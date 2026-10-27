# Frame Security Scan

A GitHub Action that runs [Frame](https://github.com/lambdasec/frame) on your code. Frame follows attacker-controlled data across files, checks each path with a solver, and uploads the findings to GitHub code scanning as SARIF.

## Usage

```yaml
name: frame
on: [push, pull_request]
permissions:
  contents: read
  security-events: write
jobs:
  scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: lambdasec/frame-action@v1
        with:
          path: src
          fail-on: high
```

## Inputs

| Input | Default | What it does |
|---|---|---|
| `path` | `.` | Directory or file to scan. |
| `fail-on` | `high` | Fail the job on findings at or above this severity: `critical`, `high`, `medium`, `low`, `any` or `none`. |
| `args` | `""` | Extra arguments for `frame scan`. |
| `sarif-file` | `frame.sarif` | Where to write the SARIF report. |
| `upload-sarif` | `"true"` | Upload the report to code scanning (needs `security-events: write`). |
| `python-version` | `"3.12"` | Python used to run Frame (3.10 or later). |

Outputs: `sarif-file` (path of the report) and `exit-code` (0 clean, 1 findings at or above `fail-on`, 2 the scan could not run).

## Pull requests: fail only on new findings

Check out the full history, then scan only the files the PR changed and compare against a baseline:

```yaml
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
      - uses: lambdasec/frame-action@v1
        with:
          args: --changed origin/${{ github.base_ref }} --baseline .frame-baseline.json
```

The job fails only on findings that are not already in `.frame-baseline.json`.

## More

This repo is a thin wrapper so the action can be listed on the Marketplace. The action itself lives in [lambdasec/frame](https://github.com/lambdasec/frame); see [docs/usage.md](https://github.com/lambdasec/frame/blob/main/docs/usage.md) for every `frame scan` option.

## License

Apache-2.0
