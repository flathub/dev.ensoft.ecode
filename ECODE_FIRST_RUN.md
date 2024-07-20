# ecode Flatpak

You are running flatpak version of ecode. This version is running inside
of a container and is therefore not able to access tools on your host
system.

You can open issues related to this flatpak at
https://github.com/flathub/dev.ensoft.ecode/issues

## Recommendation

Please consider running ecode from the official packages distributions published [here](https://github.com/SpartanJ/ecode/releases).

## Running Commands on the Host System

To run commands on the host system from inside of the sandbox, you
can use flatpak-spawn:

```sh
flatpak-spawn --host <COMMAND>
```

## Adding Language Servers Installed on the Host

If you want to use a language server, linter or formatter installed on your host system, you
will need to add an entry to your configuration file file. For example for a Linter:

Configuration will be located at:
`~/.var/app/dev.ensoft.ecode/config/ecode/plugins/linters.json`

```json
{
    "config": {
        "delay_time": "0.5s"
    },
    "linters": [
      {
        "file_patterns": ["%.js$", "%.ts$"],
        "warning_pattern": "[^:]:(%d+):(%d+): ([^%[]+)%[([^\n]+)",
        "warning_pattern_order": { "line": 1, "col": 2, "message": 3, "type": 4 },
        "command": "flatpak-spawn --host eslint --no-ignore --format unix $FILENAME"
      }
    ]
}
```

This will allow you to use eslint installed on your host system from within the Flatpak container.
Flatpak version will need to configure manually any external application. The documentation on how
to create each configuration file is located at the [ecode repository](https://github.com/SpartanJ/ecode).

## Installing Flatpak SDK Extensions

By default, this flatpak has access to standard development environment
provided by `org.freedesktop.Sdk` with programs such as GCC and Python.
However, if you need support for additional languages (including
language servers), you will need to install SDK extensions. For example:

```sh
flatpak install flathub org.freedesktop.Sdk.Extension.rust-stable
flatpak install flathub org.freedesktop.Sdk.Extension.llvm16
```

This will install the Rust and LLVM SDK extensions. After this you will
have your extensions at `/usr/lib/sdk/<extension-name>`. But they won't
be added to `$PATH`.

## Enabling SDK Extensions

To enable SDK extensions you will need to set `FLATPAK_ENABLE_SDK_EXT`
environment variable to a comma-separated list of extension names.
For example:

```sh
FLATPAK_ENABLE_SDK_EXT=rust-stable,llvm16 flatpak run dev.ensoft.ecode
```

This will add compilers, language servers, debug adapters, and other
tools to your `$PATH`.

## Checking Language Server Detection

To check which language servers have been detected, run ecode with the
`--health` option:

```sh
FLATPAK_ENABLE_SDK_EXT=rust-stable,llvm16 flatpak run dev.ensoft.ecode --health
```

You can also get information about a specific language:

```sh
FLATPAK_ENABLE_SDK_EXT=rust-stable,llvm16 flatpak run dev.ensoft.ecode --health-lang=rust
```

## Making Changes Persistent

To set environment variables persistently, use `flatpak override`:

```sh
flatpak override --user dev.ensoft.ecode --env=FLATPAK_ENABLE_SDK_EXT="rust-stable,llvm16"
```

## Finding Other SDK Extensions

To find other SDK extensions, use `flatpak search`:

```sh
flatpak search <TEXT>
```
