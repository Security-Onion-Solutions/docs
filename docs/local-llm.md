# Local LLM Hosting

!!! NOTE

    [Onion AI](onion-ai.md) is an enterprise-level feature of Security Onion. Contact Security Onion Solutions, LLC via our website at <https://securityonion.com/pro> for more information about purchasing a [Security Onion Pro](security-onion-pro.md) license.

[Onion AI](onion-ai.md) can connect to a model you host yourself through the *OpenAI Chat* adapter. This page walks through building such an endpoint on a single AMD Strix Halo machine using `llama.cpp`, which is packaged and GPU-accelerated on that platform.

The result is an OpenAI-compatible API on your own network, protected by an API key, serving a model with a context window large enough for the assistant to work with.

!!! NOTE

    Hosting your own model is optional. A local model will not match the accuracy of the proprietary foundational models available through the SOAI adapter. See [Local Model Considerations](onion-ai.md#local-model-considerations) before deciding.

## Hardware

The reference platform for this guide is an AMD Strix Halo system (Ryzen AI Max series) with 128GB of unified memory.

Strix Halo is a good fit for this task because its memory is shared between the CPU and the integrated GPU. Rather than being limited to the memory on a discrete graphics card, the GPU can address the bulk of system RAM, which is what makes it possible to hold a large model and a large context window at once on a single, relatively inexpensive machine.

This guide assumes the AMD Ryzen AI Developer Platform operating system, which is Debian-based and ships with the ROCm stack and GPU-enabled `llama.cpp` packages already available.

## Verify GPU Access

Before installing anything, confirm the GPU is visible to the ROCm backend:

```bash
llama-cli --list-devices
```

You should see a ROCm device with most of your system memory available to it:

```text
Available devices:
  ROCm0: Radeon 8060S Graphics (96454 MiB, 124565 MiB free)
```

If no ROCm device is listed, the GPU backend is not working and nothing later in this guide will run on the GPU.

### Available GPU Memory

The amount of memory the GPU may address is the GTT (Graphics Translation Table) size:

```bash
cat /sys/class/drm/card0/device/mem_info_gtt_total
```

On the reference platform this reports roughly 101GB out of 128GB of system memory, which is ample and requires no tuning. If your system reports a much smaller value, you can raise it with the `amdgpu.gttsize` and `ttm.pages_limit` kernel parameters, or by increasing the memory allocated to graphics in your system firmware.

## Install llama.cpp

```bash
sudo apt install llama.cpp
```

This is a small metapackage that pulls in `llama.cpp-tools`, which provides `llama-server` and `llama-cli`. The HIP backend that provides GPU acceleration comes from the same vendor repository, so it stays current through normal system updates.

The package also installs a `llama-server` systemd service, which is what you will configure below rather than running the server by hand.

## Choose a Model

This guide uses **Gemma 4 26B A4B**, a mixture-of-experts model with 26 billion total parameters but only about 4 billion active per token. That combination suits this hardware well: the full model must fit in memory, but the amount of data read per generated token stays small, which is what governs speed on a memory-bandwidth-limited system.

It also supports a 262,144 token context window, comfortably above the 128k minimum recommended for the assistant.

Pre-converted GGUF files are published by Unsloth. Pick a quantization based on how you want to trade accuracy against speed and memory:

| Quantization | Size | Notes |
|---|---|---|
| `unsloth/gemma-4-26B-A4B-it-qat-GGUF:UD-Q4_K_XL` | 14.2 GB | Quantization-aware trained by Google. Best quality available at 4-bit, and the fastest option. |
| `unsloth/gemma-4-26B-A4B-it-GGUF:UD-Q4_K_M` | 16.9 GB | Standard 4-bit. |
| `unsloth/gemma-4-26B-A4B-it-GGUF:Q8_0` | 26.9 GB | Effectively indistinguishable from full precision. The recommended default when you have the memory. |
| `unsloth/gemma-4-26B-A4B-it-GGUF:BF16` | 50.5 GB | Full precision. Fits, but offers no meaningful accuracy gain over `Q8_0` while running slower. |

!!! TIP

    Start with `Q8_0`. On a 128GB machine it leaves plenty of room for a full-size context window, and accuracy matters more than raw speed for security analysis. Move to the QAT 4-bit build if you need faster responses or want to leave more memory free.

## Generate an API Key

Never expose an inference endpoint without authentication. Generate a random key and store it in a file that only the service can read:

```bash
sudo install -d -m 0755 /etc/llama-server
openssl rand -hex 32 | sudo tee /etc/llama-server/api-key > /dev/null
sudo chown root:_llama-server /etc/llama-server/api-key
sudo chmod 0640 /etc/llama-server/api-key
```

Display the key when you need it for the Onion AI adapter configuration:

```bash
sudo cat /etc/llama-server/api-key
```

## Grant the Service Access to the GPU

The packaged service runs as the unprivileged `_llama-server` user. Interactive login sessions are granted access to the GPU devices automatically through an ACL, but system services are not, so the service account must be added to the `render` and `video` groups:

```bash
sudo usermod -aG render,video _llama-server
```

!!! WARNING

    This step is easy to miss. Without it the service starts and appears healthy, but silently falls back to CPU inference. The giveaway is `failed to initialize ROCm: no ROCm-capable device is detected` in the service log, followed by `no usable GPU found, --gpu-layers option will be ignored`. On this hardware, CPU-only inference is too slow to be usable.

## Configure the Server

The service reads its settings from `/etc/default/llama-server` as `LLAMA_ARG_*` environment variables, each corresponding to a `llama-server` command line option. Replace the contents of that file with:

```bash
LLAMA_ARG_HOST=0.0.0.0
LLAMA_ARG_PORT=8080
LLAMA_ARG_HF_REPO=unsloth/gemma-4-26B-A4B-it-GGUF:Q8_0
LLAMA_ARG_ALIAS=gemma-4-26b
LLAMA_ARG_CTX_SIZE=262144
LLAMA_ARG_N_GPU_LAYERS=99
LLAMA_ARG_FLASH_ATTN=on
LLAMA_ARG_N_PARALLEL=1
LLAMA_ARG_API_KEY_FILE=/etc/llama-server/api-key
```

What these do:

- **`LLAMA_ARG_HOST`** -- binds to all interfaces so the Security Onion manager can reach the endpoint. See [Network Access](#network-access) below.
- **`LLAMA_ARG_HF_REPO`** -- the model to serve. It is downloaded automatically on first start into `/var/cache/llama-server`.
- **`LLAMA_ARG_ALIAS`** -- the name the API reports for the model. This is the value you will enter as the model identifier in Onion AI.
- **`LLAMA_ARG_CTX_SIZE`** -- the context window, in tokens. `262144` is the maximum this model supports.
- **`LLAMA_ARG_N_GPU_LAYERS`** -- any value at or above the model's layer count offloads the whole model to the GPU.
- **`LLAMA_ARG_N_PARALLEL`** -- how many requests are served concurrently. The context window is divided among these slots, so leave it at `1` to give a single conversation the entire window.

!!! NOTE

    Do not set `LLAMA_ARG_SWA_FULL`. Most of this model's layers use sliding-window attention, and leaving that option off allows the server to allocate a much smaller cache for them. Enabling it would make a 262,144 token context far more expensive in memory for no benefit.

Tool calling, which the assistant depends on, requires the Jinja chat template engine. It is enabled by default, so no setting is needed, but do not disable it.

## Start the Server

```bash
sudo systemctl enable --now llama-server
```

The first start downloads the model, which takes a while. Watch its progress:

```bash
journalctl -u llama-server -f
```

Once the model has loaded, confirm the server is answering:

```bash
curl -s -H "Authorization: Bearer $(sudo cat /etc/llama-server/api-key)" \
  http://127.0.0.1:8080/v1/models
```

## Network Access

The Security Onion manager connects to this endpoint over your network, so the port must be reachable from it.

!!! WARNING

    The configuration above serves plain HTTP. The API key is sent as a bearer token in clear text and is readable by anyone who can observe the traffic, as are the prompts and responses, which will contain data from your grid.

    Restrict access to the manager and terminate TLS in front of the server before using this outside an isolated lab network.

Limit which hosts may reach the port. If the machine has no firewall configured, allowing only the manager is a reasonable starting point:

```bash
sudo nft add table inet filter
sudo nft add chain inet filter input '{ type filter hook input priority 0; }'
sudo nft add rule inet filter input tcp dport 8080 ip saddr != <MANAGER_IP> drop
```

For anything beyond a lab, put a reverse proxy such as nginx in front of `llama-server` to terminate TLS, and bind `llama-server` itself to `127.0.0.1` by setting `LLAMA_ARG_HOST=127.0.0.1`.

## Connect to Onion AI

With the endpoint running, configure Security Onion to use it. This mirrors the general instructions in [Onion AI](onion-ai.md#configuration).

### Add the Adapter

Go to Administration --> Configuration, turn on *Show advanced settings* in the Options at the top of the page, and navigate to soc --> config --> server --> modules --> assistant --> adapters. Add an adapter:

| Field | Value |
|---|---|
| Adapter Name | `local` |
| Protocol | *OpenAI Chat* |
| API Url | `http://<LLM_HOST_IP>:8080/v1/` |
| API Key | the contents of `/etc/llama-server/api-key` |
| Health Timeout Seconds | `120` |

!!! NOTE

    Use the *OpenAI Chat* adapter, not *OpenAI Responses*. `llama-server` implements the Chat Completions protocol.

    A generous health timeout is appropriate here. A locally hosted model on this class of hardware responds more slowly than a cloud provider, particularly when processing a large context.

### Add the Model

Navigate to soc --> config --> server --> client --> assistant --> availableModels and add an entry:

| Field | Value |
|---|---|
| Name | `Gemma 4 26B (local)` |
| Adapter | `local` |
| Model | `gemma-4-26b` |

The model identifier must match the `LLAMA_ARG_ALIAS` value you configured, since that is the name the server reports over the API.

Finally, make sure the assistant itself is enabled at soc --> config --> server --> client --> assistant --> enabled, then open the assistant in SOC and select your new model.

## Reasoning Output

Gemma 4 is a reasoning model. It works through a problem before committing to an answer, and `llama-server` returns that internal reasoning in a separate `reasoning_content` field, leaving `content` empty until the reasoning is finished.

The practical consequence is that a request must be allowed enough tokens to finish thinking *and* answer. If the limit is too low, the response comes back with `finish_reason` of `length`, an empty `content`, and the answer stranded mid-thought. A short question can easily spend a few hundred tokens reasoning before producing a one-sentence reply.

!!! TIP

    If you see empty responses in the assistant, this is the first thing to check. Raise the token limit rather than assuming the model or the connection is broken.

## Verify the Endpoint

Confirm authentication is enforced. A request with no key, or the wrong key, must be rejected:

```bash
curl -s -o /dev/null -w "%{http_code}\n" \
  -H "Content-Type: application/json" \
  http://127.0.0.1:8080/v1/chat/completions \
  -d '{"model":"gemma-4-26b","messages":[{"role":"user","content":"hi"}],"max_tokens":5}'
```

This should print `401`.

!!! NOTE

    The `/v1/models` endpoint is deliberately not protected by the API key and will answer without one. It exposes only the model name. Inference endpoints require the key.

Now make a real request:

```bash
curl -s -H "Authorization: Bearer $(sudo cat /etc/llama-server/api-key)" \
  -H "Content-Type: application/json" \
  http://127.0.0.1:8080/v1/chat/completions \
  -d '{"model":"gemma-4-26b","messages":[{"role":"user","content":"In one sentence, what is Zeek used for?"}],"max_tokens":2000}'
```

The response includes a `timings` object reporting `prompt_per_second` and `predicted_per_second`, which is the simplest way to measure throughput on your own hardware.

### Confirm Tool Calling

The assistant relies on tools such as `query_events` and `ack_alerts` to reach data in your grid, so tool calling has to work. Send a request with a tool definition and confirm the model responds with a `tool_calls` entry rather than plain text:

```bash
curl -s -H "Authorization: Bearer $(sudo cat /etc/llama-server/api-key)" \
  -H "Content-Type: application/json" \
  http://127.0.0.1:8080/v1/chat/completions -d '{
    "model": "gemma-4-26b",
    "messages": [{"role":"user","content":"Are there alerts from source IP 10.1.2.3 in the last 24 hours? Use the available tools."}],
    "tools": [{"type":"function","function":{
      "name":"query_events",
      "description":"Query security events from the local Security Onion instance.",
      "parameters":{"type":"object","properties":{"query":{"type":"string"},"range":{"type":"string"}},"required":["query"]}
    }}],
    "max_tokens": 2000
  }'
```

A correct result contains a `tool_calls` array naming `query_events` with populated arguments.

## Performance Expectations

The following were measured on the reference platform serving the `Q8_0` build with a 262,144 token context window. Your results will vary with hardware and quantization.

| Measure | Result |
|---|---|
| Memory in use | ~32 GB of unified memory (26.9 GB of weights, the remainder cache and buffers) |
| Generation, short context | ~41 tokens/sec |
| Generation, ~193,000 token context | ~22 tokens/sec |
| Prompt processing, bulk | ~850 tokens/sec initially, ~228 tokens/sec averaged over a 193,000 token prompt |

Two characteristics of this hardware are worth planning around.

**Prompt processing slows as the context fills.** Bulk prompt processing starts around 850 tokens/sec but falls steadily with depth. A 193,139 token prompt took roughly 14 minutes to process before generation began, and generation itself ran at about half the speed it does on a short prompt. Large contexts are practical, but they are not fast. This is the main reason to set a generous Health Timeout on the adapter.

**Memory is not the limiting factor here.** The model and a full-size context together use roughly a third of the available memory, which is why a 262,144 token window is practical on this machine. There is room for a larger model or additional concurrent slots if you need them.

The context window is usable in practice, not just allocatable: in testing, a fact placed at the very beginning of a 193,139 token prompt was retrieved correctly when asked about at the end.

!!! NOTE

    `LLAMA_ARG_N_PARALLEL` divides the context window among concurrent slots rather than adding capacity. Two slots on a 262,144 token window give each request 131,072 tokens. Raise the context size alongside the slot count if you need several analysts working at once with large contexts.

## Troubleshooting

Check the service log first:

```bash
journalctl -u llama-server -n 100 --no-pager
```

**`failed to initialize ROCm: no ROCm-capable device is detected`**, followed by `no usable GPU found, --gpu-layers option will be ignored`

The service account cannot reach the GPU devices. Confirm it is in the right groups and restart:

```bash
id -nG _llama-server
sudo usermod -aG render,video _llama-server
sudo systemctl restart llama-server
```

Note that running `llama-cli --list-devices` from your own shell may still show the GPU even while the service cannot use it, because interactive sessions get access through a separate mechanism. Always confirm against the service log.

**`request (N tokens) exceeds the available context size (262144 tokens)`**

The prompt is larger than the configured window. Either reduce what is being sent or raise `LLAMA_ARG_CTX_SIZE`, keeping in mind the model's own 262,144 token limit.

**Responses are empty and `finish_reason` is `length`**

The token limit was reached while the model was still reasoning. See [Reasoning Output](#reasoning-output).

**The service starts but the model never loads**

The first start downloads the model, which is tens of gigabytes and can take a long time. Watch the cache directory grow:

```bash
sudo du -sh /var/cache/llama-server
```

**Onion AI reports the adapter as unhealthy**

Confirm the manager can reach the port, and raise the adapter's Health Timeout. A local model can take longer to respond than the default timeout allows, particularly on a large context.
