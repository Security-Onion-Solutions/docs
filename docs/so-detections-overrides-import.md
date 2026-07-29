# so-detections-overrides-import

`so-detections-overrides-import` imports detection overrides (the modify, suppress, and threshold tuning that is created from the TUNING tab in [Detections](detections.md)) back into the `so-detection` index. It is intended for restoring overrides captured by the nightly `so-detections-backup` script, for example when migrating to or rebuilding a Manager.

It reads `<publicId>.<ext>` files from a source directory (one override per line, NDJSON), finds the matching detection by `publicId` and engine, validates each override against the same rules SOC enforces, dedupes against overrides that already exist on the detection, and appends any that are new.

!!! WARNING

    The nightly backup (`so-detections-backup`) does not delete a backup file when you remove an override in SOC, so the source directory may contain overrides you intentionally deleted. Review the source directory and remove any unwanted files before importing.

## Usage

Run on the manager and point `--source` at the directory containing your backup files:

```
sudo so-detections-overrides-import --source /nsm/backup/detections/repo/suricata/overrides/
```

Currently only the `suricata` (NIDS) engine is supported. The matching backup files use the `.txt` extension.

### Dry Run

Preview exactly what would be added, skipped, or rejected without writing anything to Elasticsearch:

```
sudo so-detections-overrides-import --source /path/to/overrides --dry-run
```

## Options

| Option | Description |
| --- | --- |
| `--source`, `-s` | **(Required)** Source directory containing `<publicId>.<ext>` override files. |
| `--engine`, `-e` | Detection engine. Default: `suricata`. |
| `--dry-run`, `-n` | Print what would happen without writing to Elasticsearch. |
| `--no-import-note` | Do not prepend `[Imported YYYY-MM-DD] ` to each imported override's note. |
| `--index`, `-i` | Elasticsearch index to update. Default: `so-detection`. |

## Behavior Notes

- **Validation** mirrors the checks SOC applies in the UI. Invalid overrides are reported (with file and line number) and skipped rather than imported.
- **Deduplication** compares only the operational fields of an override (for example, type, track, and IP for a suppression), so re-running an import does not create duplicates and does not depend on timestamps or enabled state.
- **Missing detections**: if no detection matches a file's `publicId` and engine, that file is skipped and counted under "no detection."
- **Import note**: unless `--no-import-note` is set, each imported override's note is prefixed with `[Imported YYYY-MM-DD] ` so imported tuning is easy to identify.
- **Custom Suricata variables**: if an imported override references a Suricata variable that is not one of the built-in variables, the tool lists it at the end. You must define any such variable in SOC Config (Suricata variables) before the affected rules will function correctly.

## More Information

For more information about tuning detections and managing NIDS rules, see the [Detections](detections.md) and [NIDS](nids.md) sections.
