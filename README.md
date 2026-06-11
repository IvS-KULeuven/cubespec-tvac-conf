# cubespec-tvac-conf

Configuration files for CubeSpec TVAC testing, used by [cubespec-tvac-ts](https://github.com/IvS-KULeuven/cubespec-tvac-ts) via the [CGSE](https://github.com/IvS-KULeuven/cgse) framework.

## Structure

```
data/
  KUL/conf/                   # Site-specific setup files (used for FSM TVAC, before splitting KUL1/KUL2)
    SETUP_KUL_00000_*.yaml    # Initial setup
    SETUP_KUL_00001_*.yaml    # Full setup (PSUs, DAQ6510, LabJack T7,...)
    ...
  KUL1/conf/                  # Site-specific setup files for KUL1 (used for piezo lifetime tests)
    SETUP_KUL1_00000_*.yaml   # Initial setup
    SETUP_KUL1_00001_*.yaml   # Full setup (wave generators, LabJack T7)
    ...
  KUL2/conf/                  # Site-specific setup files for KUL2 (used for STM TVAC)
    SETUP_KUL2_00000_*.yaml   # Initial setup
    SETUP_KUL2_00001_*.yaml   # Full setup (PSUs, MEASURpoint,...)
    ...
  common/telemetry/           # Shared telemetry dictionary
    tm-dictionary_v1.csv
    ...
```

## Linking to the CGSE framework

Point the `CUBESPEC_CONF_DATA_LOCATION` environment variable at the site conf directory:

```bash
export PROJECT=CUBESPEC
export SITE_ID=<KUL, KUL1, or KUL2>
export CUBESPEC_CONF_DATA_LOCATION="/path/to/cubespec-tvac-conf/data/${SITE_ID}/conf"
```

Then from Python:

```python
from egse.setup import load_setup
setup = load_setup()  # Load the latest (i.e. with the highest identifier) from the $CUBESPEC_CONF_DATA_LOCATION folder
```

## Adding a new setup

```python
from egse.setup import load_setup, submit_setup
setup = load_setup()  # Load the latest (i.e. with the highest identifier) from the $CUBESPEC_CONF_DATA_LOCATION folder

# Modify an entry (code for a fictitious setup file)
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
