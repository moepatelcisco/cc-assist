# CC-Assist

CC-Assist is a Cisco Catalyst Center support utility for collecting troubleshooting data from a Catalyst Center node. It packages commonly needed database exports, service logs, tailing logs, SPR data, audit logs, and SPF diagnostic output into timestamped archives under the current working directory.

Current user-facing version: `Version 3.0.1-08.28.26`

Document updated: September 1, 2026

## What It Collects

CC-Assist provides guided collection bundles for:

- IPAM
- LAN-Automation
- Wireless
- SDA-Wired
- SWIM
- Custom Tables and Logs
- Stuck tasks
- Live tailing logs

It also includes Utilities for:

- SPR collection for selected namespace/version data
- Audit log export from MongoDB, with JSON and XLSX output
- SPF Diagnostic collection by task ID

## Where Output Is Saved

Run output is written below the directory where `cc-assist` is executed:

```text
./cc_assist_output/
```

Each run creates a timestamped working directory and a `.tar.gz` bundle. SPF Diagnostic keeps the JSON working directory as well as the archive so the individual JSON files are easy to inspect.

Expected archive names include:

```text
ipam_<timestamp>.tar.gz
lan_automation_<timestamp>.tar.gz
wireless_<timestamp>.tar.gz
sda_wired_<timestamp>.tar.gz
swim_<timestamp>.tar.gz
custom_<timestamp>.tar.gz
stuck_tasks_<timestamp>.tar.gz
tail_logs_<timestamp>.tar.gz
spr_<timestamp>.tar.gz
audit_logs_<timestamp>.tar.gz
spf_diagnostic_logs_<timestamp>.tar.gz
```

## Installation

Download the Linux executable: `cc-assist`
This executable needs to be downloaded onto Cisco Catalyst Center.

There are three ways of doing this, depending on access from Catalyst Center to the internet

## Method 1. git clone direct

If you have access to the internet from Catalyst Center, you can clone the repository (containing the executable) directly from Catalyst Center cli:

```shel
maglev@x.x.x.x (maglev-master-x-x-x-x) ~
$ git clone https://github.com/moepatelcisco/cc-assist.git
```

## Method 2. git clone via proxy

If Catalyst Center needs a proxy to get to the internet, you will need to provide a proxy for git command.

NOTE: please do not set a permanent environment variable as this will stop you from accessing some Catalyst Center commands like maglev.

The example below uses an inline environment variable, just for the git command. Make sure to put in the correct proxy url (including port)

```css
maglev@x.x.x.x (maglev-master-x-x-x-x) ~
$ https_proxy=http://<your proxy> git clone https://github.com/moepatelcisco/cc-assist.git
```

## Method 3. Isolated environment

You will need to clone (using method 1 or 2) to an intermediate machine and copy to Catalyst Center, using scp. Remember to use port 2222 with the -P option to scp.
```
scp -P 2222 ./cc-assist  maglev@x.x.x.x:
```
x.x.x.x is CatC IP adddress



## Running On Catalyst Center

Copy the Linux executable named `cc-assist` to the Catalyst Center node and run it as the `maglev` user.

```bash
chmod +x cc-assist
./cc-assist
```

Do not run the executable with `python3`. If you see a Python `SyntaxError: Non-UTF-8 code...`, the binary was invoked as a script. Run it directly with `./cc-assist`.

If you see `cannot execute binary file: Exec format error`, the file was built for the wrong operating system or architecture. Build the Linux binary on a Linux/Catalyst Center-compatible host and copy that binary to the cluster.

## Copying To Catalyst Center

Catalyst Center SSH/SCP commonly uses port `2222` for the `maglev` user.

```bash
scp -P 2222 ./cc-assist maglev@<cluster-ip>:
ssh -p 2222 maglev@<cluster-ip>
chmod +x cc-assist
./cc-assist
```

For passwordless access, install your public key for `maglev` before copying or testing the binary.

## Release Profile

At startup, select the Catalyst Center release profile:

```text
1 -> 2.3.7.11 and earlier (maglev commands)
2 -> 3.1.x and later (kubectl commands)
```

This controls which backend command style is used when exporting data and collecting logs.

## Main Menu

```text
1 -> Log Collection
2 -> Utilities
3 -> Exit
```

## Log Collection Menu

```text
1  -> IPAM
2  -> LAN-Automation
3  -> Wireless
4  -> SDA-Wired
5  -> SWIM
6  -> Custom Tables and Logs
7  -> Stuck tasks
8  -> START Tailing logs / STOP Tailing logs
9  -> Main Menu
10 -> Exit
```

The tailing option can be started and stopped from this menu. CC-Assist warns before exit while tailing is active so the log capture can be packaged cleanly.

## Utilities Menu

```text
1 -> SPR
2 -> Audit Logs
3 -> SPF Diagnostic
4 -> Main Menu
5 -> Exit
```

## Audit Logs

Audit Logs exports audit data from MongoDB, writes the raw JSON, generates a formatted XLSX workbook, and creates an archive. Workbook generation and archive creation show a progress indicator because the audit log export can take a long time on larger systems.

Successful output includes:

- `audit_logs_<timestamp>.json`
- `audit_logs_<timestamp>.xlsx`
- `audit_logs_<timestamp>.tar.gz`

## SPF Diagnostic

SPF Diagnostic has three task selection flows:

```text
1 -> Config Preview Task
2 -> Deployed Task
3 -> Manually Enter TaskId
4 -> Utilities Menu
5 -> Exit
```

### Config Preview Task

Shows recent config preview task IDs. By default, it displays the latest 20 rows. Press Enter for 20 rows, or enter the number of latest tasks to display.

The preview shows one recent row per task ID and includes a match count when multiple related rows exist.

### Deployed Task

Shows recent deployed task IDs with readable service context when available. By default, it displays the latest 20 rows. Press Enter for 20 rows, or enter the number of latest tasks to display.

### Manually Enter TaskId

Allows up to three task IDs at a time, separated by commas. Enter `B` or `back` to return to the SPF Diagnostic menu.

## Status Colors

Terminal output uses color for quick scanning:

- Success messages are green.
- Errors and failures are red.
- Menus use the normal menu styling.

## Troubleshooting

| Symptom | Likely Cause | Fix |
| --- | --- | --- |
| `zsh: no such file or directory: ./Users/...` | The path is missing the leading `/`. | Use `/Users/...` for an absolute path, or `./file-name` only for a file in the current directory. |
| `SyntaxError: Non-UTF-8 code...` | The executable was run with `python3`. | Run `./cc-assist`. |
| `cannot execute binary file: Exec format error` | Binary was built for the wrong OS or CPU architecture. | Rebuild on Linux for the target Catalyst Center architecture. |
| `Permission denied (publickey,password)` while copying or testing | SSH key access is not installed for `maglev`. | Set up passwordless SSH or copy using an allowed authentication method on port `2222`. |
| SPF Diagnostic returns HTTP `401` or `403` | Token was missing or rejected. | Paste a valid x-auth-token when prompted. |
| SPF Diagnostic returns HTTP `404` | No SPF diagnostic record exists for that task ID. | Try a more recent supported task ID from the preview. |
