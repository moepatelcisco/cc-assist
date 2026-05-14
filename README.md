# CC Assist for Cisco Catalyst Center Log and Data Collection

CC Assist is a Catalyst Center support collection utility for engineers and TAC. It helps gather the most commonly needed logs, database table exports, tailing output, and SPR data in one place so troubleshooting can start faster and with a more consistent bundle.

The main goal of this tool is to reduce the manual effort required during case handling. Instead of collecting data from multiple commands and services one by one, CC Assist provides a guided menu that packages the output into easy-to-share archives. It supports both predefined collection bundles and targeted collections such as custom tables, custom service logs, and SPR extraction.

Technology stack:
- Python
- Linux executable distribution for Catalyst Center environments
- Intended to run as a standalone support utility

Status:
- Internal support utility

## Use Case

CC Assist is useful when an engineer or TAC needs to quickly collect troubleshooting data from a Catalyst Center node.

Typical use cases include:
- collecting a predefined bundle for IPAM, LAN-Automation, Wireless, SDA-Wired, SWIM, or Stuck tasks
- collecting custom database tables and selected service logs for focused troubleshooting
- tailing live service logs and saving them as an archive
- exporting SPR data from `serializedsnapshot` for a specific namespace and version

This helps standardize data collection and reduces time spent manually gathering logs and table dumps across multiple services.

## Installation

Download the Linux executable: `cc-assist`

This executable needs to be downloaded onto Cisco Catalyst Center. 

There are three ways of doing this, depending on access from Catalyst Center to the internet

## Option 1. git clone direct
If you have access to the internet from Catalyst Center, you can clone the repository (containing the executable) directly from Catalyst Center cli:

```text
maglev@x.x.x.x (maglev-master-x-x-x-x) ~
$ git clone https://github.com/CiscoDevNet/cc-assist
```

## Option 2. git clone via proxy
If Catalyst Center needs a proxy to get to the internet, you will need to provide a proxy for git command. 

NOTE: please do not set a permanent environment variable as this will stop you from accessing some Catalyst Center commands like maglev.

The example below uses an inline environment variable, just for the git command. Make sure to put in the correct proxy url (including port)
```text
maglev@x.x.x.x (maglev-master-x-x-x-x) ~
$ https_proxy=https://<your proxy> git clone https://github.com/CiscoDevNet/cc-assist
```

## Option 3. Isolated environment
You will need to clone (using method 1 or 2) to an intermediate machine and copy to Catalyst Center, using scp. Remember to use port 2222 with the -P option to scp.
```text
scp -P 2222 ./cc-assist  maglev@x.x.x.x:
```
x.x.x.x is CatC IP adddress

## Configuration

There is no separate configuration file required for normal usage.

At startup, the tool asks the user to select the correct Catalyst Center release profile:

```text
1 -> 2.3.7.11 and earlier (maglev commands)
2 -> 3.1.x and later (kubectl commands)
```

This selection controls which backend commands the tool uses for collection.

## Usage

Run the executable:

```bash
chmod +x cc-assist
./cc-assist
```

Main menu:

```text
1 -> Log Collection
2 -> Utilities
3 -> Exit
```

Log Collection menu:

```text
1 -> IPAM
2 -> LAN-Automation
3 -> Wireless
4 -> SDA-Wired
5 -> SWIM
6 -> Custom Tables and Logs
7 -> Stuck tasks
8 -> START Tailing logs / STOP Tailing logs
9 -> Main Menu
10 -> Exit
```

Utilities menu:

```text
1 -> SPR
2 -> Main Menu
3 -> Exit
```

Output location:

```text
./cc_assist_output/
```

Expected bundle names:

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
```

## Things to know before you use:

- The tool must be run in a compatible Catalyst Center environment.
- The correct release profile must be selected at startup, or command execution may fail.
- Active tailing must be stopped before exiting the application.
