# stele-sink-cloud

`stele-sink-cloud` is the [stele](https://github.com/lictor-ai/stele) plugin
that sends your project's stele log to [stele Cloud](https://app.stele.lictorlabs.com).
stele Cloud shows which of your rules your team's agents follow, across every
project.

The full guide is [stele Cloud](https://stele.lictorlabs.com/docs/cloud) in
the stele docs.

## Install

1. Download the archive for your system from the
   [Releases](https://github.com/lictor-ai/stele-sink-cloud/releases/latest)
   of lictor-ai/stele-sink-cloud.
2. Check it against `checksums.txt`, in the folder that holds both:
   - Linux: `sha256sum -c checksums.txt --ignore-missing`
   - macOS: `grep <archive> checksums.txt | shasum -a 256 -c`
   - Windows (PowerShell): `Get-FileHash <archive> -Algorithm SHA256`, and
     compare the hash with the archive's line in `checksums.txt`.
3. Unpack it and put `stele-sink-cloud` (`stele-sink-cloud.exe` on Windows)
   in a folder on your `PATH`.

## Use

List the sink in your project's `stele.json`:

    { "sinks": [ { "name": "cloud" } ] }

Then, in the project, on each machine that sends:

    export STELE_CLOUD_URL=https://app.stele.lictorlabs.com
    export STELE_CLOUD_TOKEN=<your token from stele Cloud>
    stele-sink-cloud on
    stele push

- `STELE_CLOUD_URL`: the stele Cloud server, https://app.stele.lictorlabs.com.
- `STELE_CLOUD_TOKEN`: your token. Make one in stele Cloud under
  **Settings → Tokens**: it can push to all projects of the organization, or
  to the ones you choose. On your own machine, one token for all projects
  covers every repository you work in; in CI, use a token for that one
  project. The server takes the project from what `stele push` sends (the
  `project` id in `stele.lock`), not from the token. A token is yours: it
  stops working if an owner removes you from the organization. It is read
  from the environment only, never from a file.

## Commands

For the project around the current directory, on this machine:

    stele-sink-cloud on | off          send this project's lines from this folder, or hold them back
    stele-sink-cloud rules on | off    send the rule text too, or rule ids only
    stele-sink-cloud status            both switches, the server and the token
    stele-sink-cloud version

Sending is off until you run `stele-sink-cloud on`. Until then stele keeps
the lines for later: from stele v1.6.0, `stele push` says they are waiting
and exits 0; v1.5.1 and earlier exit 1. Rule text is
on until you turn it off.
Rule text needs stele v1.3.0 or later. While stele says its list of rules is
incomplete (shared rules not compiled yet: run `stele compile`) or stele.md
does not parse, the push sends the lines but no rule text, and says why.
