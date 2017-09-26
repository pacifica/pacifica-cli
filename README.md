# Pacifica CLI Uploader

[![Build Status](https://travis-ci.org/pacifica/pacifica-cli-uploader.svg?branch=master)](https://travis-ci.org/pacifica/pacifica-cli-uploader)
[![Code Climate](https://codeclimate.com/github/pacifica/pacifica-cli-uploader/badges/gpa.svg)](https://codeclimate.com/github/pacifica/pacifica-cli-uploader)
[![Test Coverage](https://codeclimate.com/github/pacifica/pacifica-cli-uploader/badges/coverage.svg)](https://codeclimate.com/github/pacifica/pacifica-cli-uploader/coverage)
[![Issue Count](https://codeclimate.com/github/pacifica/pacifica-cli-uploader/badges/issue_count.svg)](https://codeclimate.com/github/pacifica/pacifica-cli-uploader)


Python CLI Uploader for Pacifica Core Services. This uploader wraps the
[Pacifica Python Uploader](https://github.com/pacifica/pacifica-python-uploader)
library for Windows or Linux command line.

## Installation

To install the CLI uploader set up your python environment and then use
pip to install.

```
pip install git+git://github.com/pacifica/pacifica-python-uploader.git#egg=PacificaUploader
pip install git+git://github.com/pacifica/pacifica-cli-uploader.git#egg=PacificaCLIUploader
```

## CLIUploader

This is the main executable program it consists of a main program from
`uploader_cli` module. Users should be able to access the program from
the bin or scripts directories in their python environment.

## Configure Sub-Command

The `configure` subcommand generates a local configuration file for the
user. It will read the system configuration to preseed its defaults and
asks the user to enter the values required. An example configuration is
located [here](config/example.ini).

The system configuration is processed first and the two directories are,
`/etc/pacifica-cli/config.ini` then
`PYTHON_PREFIX/pacifica-cli/config.ini`. Which ever is found first the
client uses that as the system default.

The user configuration is processed second, if found. The directory the
client looks in by default is `~/.pacifica-cli/config.ini`. The `~`
translates to the users home directory on any platform.

### System Metadata Configuration

The metadata is managed by a JSON configuration file referenced by an
environment variable `UPLOADER_CONFIG`. By default the environment
variable is set to `uploader.json`. However, it could be managed at a
system level or changed on the command line by the `--config` option.

The contents of the metadata configuration file is complex and should
be read from
[here](https://github.com/pacifica/pacifica-python-uploader/blob/master/METADATA_CONFIGURATION.md).

### Example Interaction

```
$ CLIUploader configure
Generating New Configuration.

Endpoints are an HTTP URL that looks similar to a website but
are designed for an uploader to interact with.

What are the endpoint URLs for the following...

Upload URL (https://ingest.example.com/upload):
Status URL (https://ingest.example.com/get_state):
Policy URL (https://policy.example.com/uploader):

There are three kinds of authentication types supported.

- clientssl - This is where you have an SSL client key and cert
- basic     - This is a username and password
- None      - Do not perform any authentication

Authentication Type (None): basic
Username (None): jdoe
Password (None): password
```

## Upload Sub-Command

There are two sets of options to the upload sub-command. The first comes
from the missing values in the metadata configuration json file. The
second are common for all uploads and will be documented further.

### `--follow-links`

This will follow symlinked directories as you walk a directory tree
building the list of files to upload.

### `--nowait`

The uploader will not wait for the successful or failed ingest of the
upload. Instead you will have to query for this information later.

### `--local-retry`

Upload an already generated bundle by the `--local-save` option. This
option will honor the `--local-save` and `--tar-in-tar` options as
well.

### `--local-save`

The uploader will save off a copy of the uploaded bundle of data. The
path is defined as the argument to the option.

### `--tar-in-tar`

A second bundler will be running generating a TAR in a TAR for upload.

### `--dry-run`

Do just the query portion of the upload and print off what the metadata
will be set to. This will not upload or generate a local save or retry.

### `--interactive`

Interact with the results of the query engine to manually select the
values for each metadata entry requested.

### Example Interaction

```
$ CLIUploader upload --interactive config.cfg

Instrument ID - Select an ID
=====================================

54 Nittany Liquid Probes - NMR PROBES: Nittany Liquid Probes
104 Transmogriscope - Nanoscale Transmogriscope
Select ID (54): 54

Proposal ID - Select an ID
=====================================

1234a - Pacifica Development (active no close)
1235 - Pacifica Development (no close or end)
1236e - Pacifica Development (expired closed and end)
1237 - Pacifica Development (expired closed no end)
1238 - Pacifica Development (pre-active)
Select ID (1234a): 1234a
Done 10240.
Waiting job to complete (1).
Done.
{
    "created": "2017-09-26 01:32:34",
    "exception": "",
    "job_id": 1,
    "state": "OK",
    "task": "ingest metadata",
    "task_percent": "100.00000",
    "updated": "2017-09-26 01:32:36"
}
```