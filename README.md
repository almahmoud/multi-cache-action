# Restore Cache from a list of keys
This action allows users to restore GitHub Actions cache from a list of keys, which can be programmatically generated in the workflow. This action is notably used for restoring a list of dependencies in a federated build system.

## Usage
The action has two required inputs, `keys` and `paths`, which are expected as comma-separated lists of quoted strings. These represent the keys and paths for which to restore the cache. At this time, no custom pairings can be made, and all paths will be used to restore each key successively.
```
      - uses: almahmoud/multi-cache-restore-action@main
        name: Multi-cache
        with:
          keys: "'pkg1','pkg2'" # required
          paths: "'/tmp/test'" # required
```
An optional boolean input `install-node` allows you to choose whether the `actions/setup-node` action should be run. This defaults to `true` if not specified.

```
      - uses: almahmoud/multi-cache-restore-action@main
        name: Multi-cache
        with:
          keys: "'pkg1','pkg2'" # required
          paths: "'/tmp/test'" # required
          install-node: "false" # optional, defaults to true
```

## Example script
```
name: Test cache
on:
  push:
  workflow_dispatch:
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - shell: bash
        run: |
          mkdir -p /tmp/test/pkg1
          echo '1' > /tmp/test/pkg1/1

      - name: Cache test
        uses: actions/cache/save@v4
        with:
          path: |
            /tmp/test
          key: pkg1
  
      - shell: bash
        run: |
          rm -rf /tmp/test
          mkdir -p /tmp/test/pkg2
          echo '2' > /tmp/test/pkg2/2

      - name: Cache test
        uses: actions/cache/save@v4
        with:
          path: |
            /tmp/test
          key: pkg2

      - run: rm -rf /tmp/test

      - uses: almahmoud/multi-cache-restore-action@main
        name: Multi-cache
        with:
          keys: "'pkg1','pkg2'"
          paths: "'/tmp/test'"

      - run: ls /tmp/test
```
