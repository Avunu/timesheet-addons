---
title: "Avunu timesheet addons"
description:
  "The running timesheet timer, ticking, in the Odoo systray, with a stop button that
  works from any screen."
---

The running timesheet timer, ticking, in the Odoo systray, with a stop button that works
from any screen. It is an Odoo 18.0 module built and maintained by
[Avunu LLC](https://avunu.net) for our own client work and published for anyone to use.
The repository is named after [OCA/timesheet](https://github.com/OCA/timesheet), where
OCA keeps the timer module it builds on from 19.0, as `hr_timesheet_time_control`; on
18.0 that module is `project_timesheet_time_control`, in
[OCA/project](https://github.com/OCA/project).

## Modules

| Module                                                                      | What it does                                                                                              | Licence           |
| --------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------- | ----------------- |
| [`hr_timesheet_time_control_systray`](hr_timesheet_time_control_systray.md) | Shows the running timesheet timer, ticking, in the systray, with a stop button that works from any screen | AGPL-3.0-or-later |

Each module's README explains why it exists and how it works.

## Branches

`develop` is the default branch and the one pull requests go to. Its code targets Odoo
18.0, the series named in `.odoo-series`. `18.0` follows `develop` automatically (a
workflow fast-forwards it after every push) and is the branch to install or pin. When a
newer Odoo series is maintained, it gets its own branch (`19.0`, …) and changes flow up
to it from `develop`.

## Install

Requires Odoo 18.0 and a PostgreSQL database. Installing a module pulls in the modules
it depends on (see below), and those must be on your `addons_path` first.

With a plain Odoo checkout, clone this repository on its `18.0` branch and add it to the
addons path:

```sh
git clone --branch 18.0 https://github.com/Avunu/timesheet-addons.git
odoo-bin --addons-path=odoo/addons,/path/to/timesheet-addons,<your other addons paths> \
  -d <database> -i hr_timesheet_time_control_systray --stop-after-init
```

With [odoo-nix](https://github.com/Avunu/odoo-nix), add the repository from the dev
shell. `odoo module add` accepts an `owner/repo` and a branch, adds it as a submodule,
and the addons path is regenerated from the folders present (run `direnv reload`
afterwards):

```sh
odoo module add Avunu/timesheet-addons 18.0
```

OCA dependencies can be added by module name, which also adds the OCA repository that
holds them: `odoo module add project_timesheet_time_control`.

## Dependencies

Each module's `__manifest__.py` is the source of truth. Besides the Odoo core module
`bus`, `hr_timesheet_time_control_systray` depends on the following. The OCA repository
listed is the 18.0 branch; clone it, along with whatever its modules need in turn (their
own manifests list it).

| Module                              | Needs from OCA or others                                                         | Python packages |
| ----------------------------------- | -------------------------------------------------------------------------------- | --------------- |
| `hr_timesheet_time_control_systray` | `project_timesheet_time_control` ([OCA/project](https://github.com/OCA/project)) | none            |

## Development

The repository carries its own development environment, built by
[odoo-nix](https://github.com/Avunu/odoo-nix) in addons mode. OCB and the repositories
these modules depend on are flake inputs pinned in `flake.lock`, and the Python
environment is locked in `nix/`. You need Nix with flakes, and optionally direnv.

```sh
git clone https://github.com/Avunu/timesheet-addons.git && cd timesheet-addons
direnv allow                  # or: nix develop --no-pure-eval
devenv up                     # PostgreSQL, Odoo and Mailpit
odoo db provision             # a database with every module here installed
odoo test hr_timesheet_time_control_systray
```

After `nix flake update`, or when a module gains a `depends` or a Python requirement,
run `odoo project relock` and commit `flake.lock` and `nix/`. A new dependency on
another repository also needs its input and an `externalSources` entry in `flake.nix`.
See odoo-nix's
[A repository of addons](https://github.com/Avunu/odoo-nix/blob/main/docs/scaffolding/addons-repository.md).

## Tests

Odoo runs a module's tests when you install or update it with `--test-enable`. Select a
module's tests with a `--test-tags` filter and use a throwaway database:

```sh
odoo-bin -c odoo.conf -d <testdb> -i hr_timesheet_time_control_systray \
  --test-enable --test-tags /hr_timesheet_time_control_systray --stop-after-init
```

`hr_timesheet_time_control_systray` has Python tests and a browser tour, all run after
install; the tour checks that the timer ticks in the navbar and can be stopped from
there. Odoo runs tours in a headless Chrome or Chromium and skips them when it cannot
find one.

## Licence

Every module is licensed under the
[GNU Affero General Public License v3.0 or later](https://github.com/Avunu/timesheet-addons/blob/18.0/LICENSE),
as its `__manifest__.py` also states (`AGPL-3`, Odoo's identifier for version 3).

## Provenance

`hr_timesheet_time_control_systray` moved here, with its history, from
[Avunu/avunu-odoo-addons](https://github.com/Avunu/avunu-odoo-addons) in November 2026,
when Avunu split that repository into one repository per OCA area.
