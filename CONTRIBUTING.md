# Contributing to dingoElectronics

This guide covers every dingoElectronics project. Each code repo also has a short `CONTRIBUTING.md` with its own build steps:

| Repo | What it is | Build guide |
|------|------------|-------------|
| [dingoFW](https://github.com/corygrant/dingoFW) | ChibiOS firmware for dingoPDM, dingoPDM-Max, CANBoard and other dingoElectronics boards| [dingoFW/CONTRIBUTING.md](https://github.com/corygrant/dingoFW/blob/master/CONTRIBUTING.md) |
| [dingoConfig](https://github.com/corygrant/dingoConfig) | .NET / Blazor Server configuration app | [dingoConfig/CONTRIBUTING.md](https://github.com/corygrant/dingoConfig/blob/main/CONTRIBUTING.md) |
| [dingoElec](https://github.com/corygrant/dingoElec) | This repo: documentation ([site](https://corygrant.github.io/dingoElec/)) and roadmap |  |

## Before you start

- **Check [the project board](https://github.com/users/corygrant/projects/4) first.** It lists the features and bugs that are already planned or being worked on. If your idea or bug is already there, comment on that item instead of opening a new issue.
- **Bugs:** open an issue in the repo where the bug shows up. Include the firmware version, the dingoConfig version, the device and board revision, the adapter you used, and the steps to reproduce it. A CAN log or a config file helps a lot.
- **Features:** open an issue or ask on Discord (the link is in the README) before you write a large change. For longer-term plans, see [the roadmap](dev/roadmap/roadmap.md).
- **Small fixes** like typos, docs or obvious bugs can go straight to a PR.

## Branches and releases

dingoFW and dingoConfig use the same flow:

```
feature branch ──PR──> development ──> testing ──> main/master
                                         │             │
                              "testing-latest"      tagged
                               pre-release          release
```

- **Open PRs against `development`**, never against `testing` or `main`/`master`.
- When code is pushed to `testing`, CI builds it and replaces the `testing-latest` pre-release.
- When code is pushed to `main` (dingoConfig) or `master` (dingoFW), CI builds and publishes the release.
- **Don't bump version numbers.** The maintainer bumps `core/device_config.h` (dingoFW) and `<Version>` in `web/web.csproj` (dingoConfig) at release time.

## Changes that span firmware and software

The two repos are linked by the CAN protocol. If you change a message, a parameter or an enum in dingoFW, dingoConfig has to change to match. When that happens:

1. Open a PR in each repo and link them to each other.
2. Update the DBC builder in `dingoFW/dbc/dbc_builder/` if any CAN messages changed.
3. Update the matching page in `dingoElec/docs/` (for example `dingofw/can.md` or `dingoconfig/messages.md`).

## Testing the docs locally

The docs site is built with [MkDocs](https://www.mkdocs.org/) and [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/). Preview your changes locally before opening a PR.

Install Python 3, then from the root of the dingoElec repo:

```bash
# Create and activate a virtual environment (one time)
python3 -m venv .venv
source .venv/bin/activate          # Windows: .venv\Scripts\activate

# Install the same packages CI uses (one time)
pip install mkdocs-material mkdocs-glightbox mkdocs-macros-plugin mkdocs-charts-plugin

# Start the live preview server
mkdocs serve
```

Open `http://127.0.0.1:8000` in your browser. The page reloads every time you save a file in `docs/` or `mkdocs.yml`. Press `Ctrl+C` to stop the server.

Before you open a PR, run a full build and fix any warnings (broken links, missing pages):

```bash
mkdocs build
```

The site is built into `site/`, which is ignored by git. Next time, just activate the virtual environment and run `mkdocs serve`.

## Pull requests

- Keep each PR to one change. Split up unrelated fixes.
- Match the style of the code around your change, and avoid reformatting files you aren't otherwise changing.
- Say what you tested and on which device and board revision. For firmware changes, say whether you tested on real hardware or only built the code.
- Don't commit build output (`build/`, `bin/`, `obj/`, `publish/`) or IDE files.
- Update the docs if your change affects what a user sees.

## Safety

These devices switch real loads in vehicles. Bench-test firmware changes with a current-limited supply before you put them in a vehicle. Be careful with changes to output control, current limits, fault handling and sleep. Explain in the PR what happens when something goes wrong, not just what happens when it works.

## License

By contributing, you agree that your work will be released under the same license as the repo you contribute to (see that repo's LICENSE file).
