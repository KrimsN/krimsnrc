```
    █████   ████            ███                          ██████   █████
   ░░███   ███░            ░░░                          ░░██████ ░░███
    ░███  ███    ████████  ████  █████████████    █████  ░███░███ ░███  ████████   ██████
    ░███████    ░░███░░███░░███ ░░███░░███░░███  ███░░   ░███░░███░███ ░░███░░███ ███░░███
    ░███░░███    ░███ ░░░  ░███  ░███ ░███ ░███ ░░█████  ░███ ░░██████  ░███ ░░░ ░███ ░░░
    ░███ ░░███   ░███      ░███  ░███ ░███ ░███  ░░░░███ ░███  ░░█████  ░███     ░███  ███
 ██ █████ ░░████ █████     █████ █████░███ █████ ██████  █████  ░░█████ █████    ░░██████
░░ ░░░░░   ░░░░ ░░░░░     ░░░░░ ░░░░░ ░░░ ░░░░░ ░░░░░░  ░░░░░    ░░░░░ ░░░░░      ░░░░░░
        unix environment in one command: zsh · tmux · nvim · p10k
```

# .krimsnrc

*(short name: `.knrc`)*

[Русский](../README.md) | **English**

[![Ubuntu 24.04](https://img.shields.io/github/actions/workflow/status/KrimsN/krimsnrc/test-ubuntu.yml?label=Ubuntu%2024.04&logo=ubuntu&logoColor=white&labelColor=E95420&style=flat)](https://github.com/KrimsN/krimsnrc/actions/workflows/test-ubuntu.yml)
[![Debian 12](https://img.shields.io/github/actions/workflow/status/KrimsN/krimsnrc/test-debian.yml?label=Debian%2012&logo=debian&logoColor=white&labelColor=A81D33&style=flat)](https://github.com/KrimsN/krimsnrc/actions/workflows/test-debian.yml)
[![Fedora Latest](https://img.shields.io/github/actions/workflow/status/KrimsN/krimsnrc/test-fedora.yml?label=Fedora%20Latest&logo=fedora&logoColor=white&labelColor=51A2DA&style=flat)](https://github.com/KrimsN/krimsnrc/actions/workflows/test-fedora.yml)
[![CentOS Stream 9](https://img.shields.io/github/actions/workflow/status/KrimsN/krimsnrc/test-centos.yml?label=CentOS%20Stream%209&logo=centos&logoColor=white&labelColor=262577&style=flat)](https://github.com/KrimsN/krimsnrc/actions/workflows/test-centos.yml)
[![Lint](https://img.shields.io/github/actions/workflow/status/KrimsN/krimsnrc/lint.yml?label=Lint&logo=gnu-bash&logoColor=white&labelColor=4EAA25&style=flat)](https://github.com/KrimsN/krimsnrc/actions/workflows/lint.yml)

A set of scripts for quickly setting up a unix environment on Ubuntu,
Debian, Fedora and CentOS: zsh + oh-my-zsh + Powerlevel10k (+ Nerd Font
fonts), tmux (+ TPM, tmux-resurrect, tmux-continuum), the git ecosystem
(gh), docker, and a set of CLI utilities (full list —
[docs/PROJECT.md](PROJECT.md)).

## Quick start

```bash
curl -fsSL https://raw.githubusercontent.com/KrimsN/krimsnrc/master/install.sh | bash
```

The `curl` command itself is needed beforehand (it's usually already
present on the system); if not, install it manually as the first step.
`git`, however, is not required to run via `curl | bash` — the script
will install it itself if needed before cloning the repository, asking
what to install (or installing everything by default).

From an already cloned repository — the same thing:

```bash
./install.sh
```

No questions, install everything right away:

```bash
./install.sh --yes
```

Only specific modules:

```bash
DOTFILES_MODULES="base zsh tmux" ./install.sh
```

## Diagnostics

Along with the modules, `install.sh` installs the `knrc` command (into
`~/.local/bin`). To check what's actually set up on the machine and
what isn't:

```bash
knrc doctor
```

One line per check with status `ok` / `missing` / `broken`, a final
count, and a non-zero exit code on problems — the command can be used
as a CI step. It doesn't fix or install anything; to install what's
missing, run the regular `install.sh`. To narrow the set of checks:
`knrc doctor --modules=zsh,tmux`. Details —
[docs/modules/doctor.md](modules/doctor.md).

## Updating

Pull the latest version of `.knrc` and reinstall new modules:

```bash
knrc update
```

Runs `git pull --ff-only` in the local clone and reruns `install.sh`.
To see what would be pulled without changing anything: `knrc update
--dry-run`. Refuses to update if the clone has uncommitted changes.
Details — [docs/modules/update.md](modules/update.md).

## Rollback

Return the machine to the state it was in before installation:

```bash
knrc uninstall
```

The command first prints the full plan — what it will restore from
backups, what it will remove, what it will put back — and only then
asks for confirmation (you need to type `yes` in full). To see the
plan without changing anything: `knrc uninstall --dry-run`. For
automation there's `--force`; `NONINTERACTIVE=1` alone does not count
as confirmation — without the explicit flag the command refuses to
run.

Packages installed via the package manager are **not removed**: they
may have been present before us or be needed by the system. Instead,
a list of what's left is printed at the end, along with a ready-made
removal command. Your `*.local` files are also left untouched. Details
— [docs/modules/uninstall.md](modules/uninstall.md).

## SSH hardening (optional)

A separate command, not part of installation or rollback — only on
explicit request, after you've confirmed that SSH key login works:

```bash
knrc harden-ssh
```

Disables root login and password login in this machine's
`sshd_config`. Prints a plan and asks for confirmation the same way as
`knrc uninstall`; has its own `--dry-run` and `--rollback`. Details —
[docs/modules/harden-ssh.md](modules/harden-ssh.md).

## Status

All modules are implemented and tested on Ubuntu, Debian, Fedora and
CentOS (Docker/WSL). On every push/PR to `master`, GitHub Actions also
runs the full `install.sh` (twice, to check idempotency) in Ubuntu
24.04, Debian 12, Fedora (latest) and CentOS Stream 9 containers — see
the badges above for status. CentOS 7 (yum fallback) is not included
in CI due to stale mirrors for the EOL distribution — it's only
supported for manual testing via `scripts/test-module.sh`.
The full list of programs, history of decisions about what's included,
and the module coverage table are in
[docs/PROJECT.md](PROJECT.md); implementation details for each
module are in `docs/modules/`.
