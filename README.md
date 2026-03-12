# cubespec-tvac-conf

Configuration files for CubeSpec TVAC testing, used by [cubespec-tvac-ts](https://github.com/IvS-KULeuven/cubespec-tvac-ts) via the [CGSE](https://github.com/IvS-KULeuven/cgse) framework.

## Structure

```
data/
  KUL/conf/                   # Site-specific setup files
    SETUP_KUL_00000_*.yaml    # Initial setup
    SETUP_KUL_00001_*.yaml    # Full setup (PSUs, DAQ6510, LabJack T7,...)
    ...
  common/telemetry/           # Shared telemetry dictionary
    tm-dictionary_v1.csv
    ...
```

## Linking to the CGSE framework

Point the `CUBESPEC_CONF_DATA_LOCATION` environment variable at the site conf directory:

```bash
export PROJECT=CUBESPEC
export SITE_ID=KUL
export CUBESPEC_CONF_DATA_LOCATION=/path/to/cubespec-tvac-conf/data/KUL/conf
```

Then from Python:

```python
from egse.setup import load_setup
setup = load_setup()  # Load the latest (i.e. with the highest identifier) from the $CUBESPEC_CONF_DATA_LOCATION folder
```

## Adding a new setup

```python
from egse.setup import load_setup, submit_setup
setup = load_setup  # Load the latest (i.e. with the highest identifier) from the $CUBESPEC_CONF_DATA_LOCATION folder

# Modify an entry
setup.branch.subbranch.leaf = object

# Add a new entry
setup.branch.subbranch = {}
setup.branch.subbranch.leaf = object


response = submit_setup(setup, "Description of the changes (potentially with reference to GitHub issue)")
print(response)

# If `response` looks ok:
setup = load_setup()
```

Also possible, but discouraged (certainly during the actual test campaign):

Copy the latest YAML, increment the ID, update the `history` section, and adjust parameters as needed. The filename convention is `SETUP_<SITE>_<ID>_<YYMMDD>_<HHMMSS>.yaml`.
