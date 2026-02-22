# gtvfx-contrib

Tools and frameworks for building reproducible, scalable application environments — built for VFX and games production pipelines.

---

## Envoy

**The core framework.** Envoy is a CLI-first environment orchestration tool that lets you define, compose, and launch complex application environments from JSON configuration — without touching system settings.

- JSON-based environment variable configuration with `+=` / `^=` operators for appending and prepending
- **Multi-bundle architecture** — aggregate commands and environments from any number of Git repositories
- **Closed environment mode** — subprocesses receive only what you define, eliminating "works on my machine" problems
- Auto-discovery of bundles via `ENVOY_BNDL_ROOTS`
- `global_env.json` for shared baseline variables across all commands in a bundle

```bash
envoy python_dev script.py        # run with a fully defined environment
envoy --list                      # discover all available commands
envoy --info python_dev           # inspect a command's configuration
```

→ [gt-envoy](https://github.com/gtvfx-contrib/gt-envoy)

---

## Repositories

| Repo | Purpose |
|---|---|
| [gt-envoy](https://github.com/gtvfx-contrib/gt-envoy) | Environment orchestration framework — the core of the stack |
| [gt-pythoncore](https://github.com/gtvfx-contrib/gt-pythoncore) | Pure-Python utility library: paths, filesystem, git, REST, Win32, and more |
| [gt-globals](https://github.com/gtvfx-contrib/gt-globals) | Shared global environment configuration and baseline Envoy bundle |
| [gt-pythonlibs](https://github.com/gtvfx-contrib/gt-pythonlibs) | Vendored third-party Python packages for controlled dependency management |
| [gt-command_aliases](https://github.com/gtvfx-contrib/gt-command_aliases) | CLI batch scripts for common Git and environment workflows |
| [gt-unreal](https://github.com/gtvfx-contrib/gt-unreal) | Unreal Engine integration tools and launcher wrapper |

---

## How It Fits Together

Every repo ships an `envoy_env/` directory, making it a self-contained **Envoy bundle**. Point `ENVOY_BNDL_ROOTS` at your checked-out repos and Envoy assembles the full environment automatically:

```
ENVOY_BNDL_ROOTS=R:/repo/gtvfx-contrib;R:/repo/gtvfx
```

```
gt-globals/          ← global baseline (PYTHONDONTWRITEBYTECODE, allowlist, etc.)
gt-pythoncore/       ← adds py/ to PYTHONPATH, registers branch_status command
gt-pythonlibs/       ← adds vendored packages to PYTHONPATH
gt-unreal/           ← adds unreal launcher command
gt-command_aliases/  ← bat scripts on PATH
```

Each bundle layers its environment on top of the previous one. No install steps, no global mutations.

---

## Design Principles

- **Reproducible environments** — define once, run anywhere with the same result
- **Single-responsibility bundles** — each repo does one thing and composes cleanly with others
- **No host dependencies** — core libraries target plain Python; DCC integrations are isolated to their own bundles
- **Escape hatches** — `--inherit-env` and `ENVOY_ALLOWLIST` let you selectively inherit the system environment when needed
