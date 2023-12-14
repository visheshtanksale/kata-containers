# `kata-log-parser`

## Introduction

`log-parser-rs` is a tool that combines logfiles generated by the various
system components, sorts them by time stamp, and re-displays the log entries.

The tool is also able to check the validity of all log records, can re-format the
logs, and output them in a different format.

For more information on the `kata-log-parser` tool, use the help command:

```
$ kata-ctl log-parser --help
```

> **Note** this is a rewrite of the go-based `kata-log-parser` tool, and will eventually replace it.

## Log Format

Kata's `runtime-rs` logs are JSON objects in the following format:

```json
{"msg":"message","level":"INFO","ts":"1970-01-01T00:00:00.000000000Z","name":"kata-runtime","version":"0.1.0","pid":"0","source":"source","subsystem":"subsystem"}
```

However, if `--ignore-missing-fields` is set, a log missing one or more of the following fields may be omitted: 

- `level`
- `name`
- `version`
- `pid`
- `source`
- `subsystem`

> **Note** a log entry must be on one single line, and a line must contain only one log entry.

## Command line opts

The most valuable command line options are listed below:

- `-o, --output-file <OUTPUT_FILE>` File to output to. If not set, sends to stdout.
- `--output-format <OUTPUT_FORMAT>` Sets the format of the output. Defaults to `json`, and can be set to `csv`, `json`, `ron`, `text`, `toml`, `xml`, and `yaml`.
- `-q, --quiet` Will not print invalid log entry errors to stderr.
- `-s, --strict` Any invalid log entry will halt the program.
- `-h, --help`, Displays a listing of all CLI options.

For a comprehensive (and guaranteed up to date) list, please run `log-parser-rs --help`.

## Usage

1. Make sure containerd is in [debug mode](https://github.com/kata-containers/kata-containers/blob/main/docs/Developer-Guide.md#enabling-full-containerd-debug)
1. Make sure you are running runtime-rs:
   ```
   $ containerd-shim-kata-v2 --version|grep -qi rust && echo rust || echo golang
   ```
1. Collect the logs (alternatively to journal clearing you may consider constraining collected logs by adding `--since=<container creation time>`).
   ```
   $ sudo journalctl -q -o cat -a -t kata | grep "^{" > ./kata.log ./kata.log
   ```
1. Ensure the logs are readable:
   ```
   $ sudo chown $USER *.log
   ```
1. Process the logs:
   ```
   $ kata-ctl log-parser kata.log -o out.log
   ```