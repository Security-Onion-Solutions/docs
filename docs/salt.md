# Salt

From <https://docs.saltproject.io/en/latest/topics/about_salt_project.html#about-salt>:

> Built on Python, Salt is an event-driven automation tool and framework to deploy, configure, and manage complex IT systems. Use Salt to automate common infrastructure administration tasks and ensure that all the components of your infrastructure are operating in a consistent desired state.

!!! NOTE
    
    Salt is a core component of Security Onion as it manages all processes on all nodes. In a distributed deployment, the manager node controls all other nodes via salt. These non-manager nodes are referred to as salt minions.

## Firewall Requirements

Salt minions must be able to connect to the manager node on ports `4505/tcp` and `4506/tcp`.

## Checking Status

You can use salt's `test.ping` to verify that all your nodes are up:


```
sudo salt \* test.ping
```

## Remote Execution

Similarly, you can use salt's `cmd.run` to execute a command on all your nodes at once. For example, to check disk space on all nodes:


```
sudo salt \* cmd.run 'df'
```

## Node checkin

If you want to force a node to do a full update of all salt states, you can run `so-checkin`. This will execute `salt-call state.highstate -l info` which outputs to the terminal with the log level set to `info` so that you can see exactly what's happening:


```
sudo so-checkin
```

## Auto State Apply

When you save a configuration change in [Administration](administration.md) --> Configuration, or when rules are updated on the manager node, Security Onion detects the change and applies the affected state to only the nodes that need it. This typically happens within a few minutes instead of waiting for the next scheduled highstate.

Auto State Apply is configured at [Administration](administration.md) --> Configuration --> salt --> auto_apply:

| Setting | Default | Description |
|---------|---------|-------------|
| `enabled` | `true` | Enables or disables Auto State Apply. When disabled, changes are picked up at the next scheduled highstate. |
| `debounce_seconds` | `30` | How long a change must be quiet before it is applied. Multiple changes made within this window are combined into a single update. |
| `drain_interval` | `15` | How often, in seconds, the manager checks for changes that are ready to be applied. |
| `batch` | `25%` | How many nodes apply the state at once, either a number such as `10` or a percentage such as `25%`. |
| `batch_wait` | `15` | How many seconds to wait between each batch of nodes. |

Other than `enabled`, these are advanced settings, so you will only see them if you click the `Options` menu at the top of the page and then enable the `Show advanced settings` option.

## Highstate Interval

Every node also runs a scheduled highstate as a backstop. The interval is controlled by the `highstate_interval_minutes` setting at [Administration](administration.md) --> Configuration --> salt --> schedule and defaults to `120` minutes. The minimum value is `15` minutes. This is an advanced setting, so you will need to enable the `Show advanced settings` option to see it.

## Reverting to Previous Behavior

If you would like the grid to behave like previous versions of Security Onion, go to [Administration](administration.md) --> Configuration --> salt and:

- set `auto_apply` --> `enabled` to `false`
- set `schedule` --> `highstate_interval_minutes` to `15`

!!! WARNING
    
    If you disable Auto State Apply but leave the highstate interval at the default of `120` minutes, it can take up to two hours for a change to reach the rest of the grid.

## Configuration

Many of the options that are configurable in Security Onion are done by going to [Administration](administration.md) and then Configuration.

## Diagnostic Logs

Diagnostic logs can be found in `/opt/so/log/salt/`.

## Known Issues

You may see the following error in the salt-master log located at `/opt/so/log/salt/master`:


```
[ERROR   ][24983] Event iteration failed with exception: 'list' object has no attribute 'items'
```

The root cause of this error is a state trying to run on a minion when another state is already running. This error now occurs in the log due to a change in the exception handling within Salt's event module. Previously, in the case of an exception, the code would just pass. However, the exception is now logged. The error can be ignored as it is not an indication of any issue with the minions.

## More Information

!!! NOTE
    
    For more information about Salt, please see <https://docs.saltproject.io/en/latest/contents.html>.