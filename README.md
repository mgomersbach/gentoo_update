# Gentoo Updater

`gentoo-update` is a tool that automates updates on Gentoo Linux.
By default it only installs security updates from [GLSA](https://security.gentoo.org/glsa/),
but it can also be used to update all packages on the system, i.e. `@world`.

This project
[originates](https://wiki.gentoo.org/wiki/Google_Summer_of_Code/2023/Ideas/Automated_Gentoo_system_updater)
from 2023 Google Summer of Code, more about it can be found in the
[blog post](https://blogs.gentoo.org/gsoc/2023/08/27/final-report-automated-gentoo-system-updater/) and
[Gentoo Forums](https://forums.gentoo.org/viewtopic-p-8793827.html#8793827).

`gentoo_update` has 3 main modules - updater, parser and notifier. Updater runs the
update script and creates a log file. Parser reads the log file and composes a post-upgrade
report which notifier then sends via email, IRC bot or
[mobile app](https://github.com/Lab-Brat/gentoo_update_flutter).

## Features

- updater:
  - [x] update security patches from GLSA by default, and optionally update `@world`
  - [x] insert additional flags to `@world` update
  - [x] do not start the update if available disk space is lower than a certain threshold
  - [ ] estimate update time and show package list before the update
  - parser:
  - [x] show update status (success/failure) in the report
  - [x] show package info after successful update: ebuilds, blocks, uninstalls etc.
  - [ ] detect different errors during an update
    - [x] blocked Packages
    - [ ] USE flag conflicts
    - [ ] issues with Licenses
    - [ ] network issues during an update
    - [ ] OOM during an update
  - [x] show disk usage before/after an update
  - notifier:
    - [x] send update report via IRC bot
    - [x] send update report via email using SendGrid
    - [ ] send update report via email using local relay
    - [x] send update report via mobile app
    - [x] send a short report with only the update status instead of a full report
  - general:
    - [x] ebuild in GURU repository
    - [ ] CI/CD pipeline that will run `gentoo_update` on newly published stage3 Docker containers
    - [ ] comprehensive set of unit tests

## Installation

`gentoo-update` is in [GURU](https://wiki.gentoo.org/wiki/Project:GURU)
overlay, and can be installed using `emerge`. First, enable the overlay:

```bash
emerge --ask app-eselect/eselect-repository
eselect repository enable guru
emerge --sync
```

and then install it:

```bash
emerge --ask app-admin/gentoo_update
```

Alternatively, it can be installed with pip in a virtual environment:

```bash
python -m venv gentoo_update
source gentoo_update/bin/activate
python -m pip install gentoo_update
```

## Usage

The updater creates a subdirectory in Portage's default `PORTAGE_LOGDIR` located at `/var/log/portage/gentoo-update`.
However, if this variable is set to a different value in `make.conf`, it will use the new location instead of the default.

Here are some usage examples:

- Basic security update

```bash
gentoo-update
```

- Full system update with extra update parameters

```bash
gentoo-update --update-mode full --args "color=y keep-going"
```

- Full system update, show elogs and news

```bash
gentoo-update --update-mode full --read-logs --read-news
```

- Read last update report:

```bash
gentoo-update --report
```

- Send the last update report to an IRC channel:

```bash
export IRC_CHANNEL="#<irc_channel_name>"
export IRC_BOT_NICKNAME="<bot_name>"
export IRC_BOT_PASSWORD="<bot_password>"
gentoo-update --send-report irc
```

## Help

The detailed explanation of command flags can be found in CLI's help message:

```bash
gentoo-update --help
```

Information on testing can be found in tests directory [readme](tests/README.md).

To get help or request additional features feel free to create an issue in this GitHub repo.
Or just contact me directly via email at [labbrat_social@pm.me](mailto:labbrat_social@pm.me) or on IRC.
I am also in most of the #gentoo IRC groups and my nick there is #LabBrat.
