# Plugin License Manager

License management system for FDS plugins — currently supporting **ETABS** and **Revit**. It controls which users and machines are authorized to run specific plugin versions and features.

## Overview

This system uses CSV files data to manage:

- **Allowed plugin versions** — defines which plugin version is currently approved for use
- **License records** — maps named users to their computer, expiry date, and permitted plugin features

When a plugin starts, it reads these files to verify that the current machine and user have a valid, non-expired license for the requested feature.

## Repository Structure

```
├── etabs_powerapps_allowed_versions.csv   # Approved ETABS plugin version(s)
├── etabs_powerapps_lic.csv                # ETABS user license records
├── revit_powerapps_allowed_version.csv    # Approved Revit plugin version(s)
├── revit_powerapps_lic.csv               # Revit user license records
└── README.md
```

## CSV File Format

### Allowed Versions file

```
Allowed Versions,
<version_string>
```

Only one version is active at a time. Plugins on a different version will be blocked.

### License file

| Column | Description |
|--------|-------------|
| `Person name` | Display name of the licensed user |
| `Computer name` | Machine hostname the license is tied to |
| `Expiry date (dd/mm/yyyy)` | Date after which the license is no longer valid |
| `Allowed Plugins` | Comma-separated list of permitted plugin buttons/features, or `all` |

## Adding or Updating a License

1. Open the relevant `_lic.csv` file (`etabs_powerapps_lic.csv` or `revit_powerapps_lic.csv`).
2. Add a new row with the user's name, computer hostname, expiry date, and allowed plugins.
3. Commit the change — the plugin will pick it up automatically on next launch.

To grant access to all features, set `Allowed Plugins` to `all`. To restrict a user, list only the specific plugin button names (comma-separated).

## Updating the Allowed Version

To roll out a new plugin version:

1. Open the relevant `_allowed_versions.csv` file.
2. Replace the version string with the new version number (e.g. `2.1.0`).
3. Commit. All clients on older versions will be blocked until they update.

## License Validation Logic

On startup, the plugin checks:

1. Is the running version listed in the allowed versions file? If not → blocked.
2. Is the current computer name present in the license file? If not → blocked.
3. Has the license expiry date passed? If so → blocked.
4. Is the requested feature listed in `Allowed Plugins` (or is it `all`)? If not → blocked.

## Contributing

Changes to license files should be reviewed before merging to avoid accidental lockouts. When extending to a new software package, follow the same two-file pattern: one allowed versions file and one license file.
