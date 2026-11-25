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

Then log in once on each machine that sends, and turn the sink on in the
project:

    stele-sink-cloud login
    stele-sink-cloud on
    stele push

`login` opens stele Cloud in your browser to approve a code, and keeps the
token it gets in your system's keychain (Windows Credential Manager, the
macOS Keychain, or the Secret Service on Linux), never in a file. Logging
in sends nothing: each project folder still needs `stele-sink-cloud on`.

The token is yours: it stops working if an owner removes you from the
organization. The one `login` makes can push to every project of the
organization (`login --project`, below, makes one for a single project). The
server takes the project from what `stele push` sends (the `project` id in
`stele.lock`), not from the token.

In CI, or where there is no keychain, set `STELE_CLOUD_TOKEN` instead, to a
token made in stele Cloud under **Settings → Tokens**: it can push to all
projects of the organization, or to the ones you choose; in CI, use a token
for that one project. It is read from the environment, never from a file, and
it wins over the keychain. `STELE_CLOUD_URL` is only needed for a server
other than https://app.stele.lictorlabs.com: a token from
`STELE_CLOUD_TOKEN` goes there, or to stele Cloud, never to a server a
login saved.

## Commands

For the project around the current directory, on this machine:

    stele-sink-cloud login [--project] [--with-token] [--url URL]
                                       log in; the token goes in the OS keychain
    stele-sink-cloud logout [--project]
                                       remove that token from this machine
    stele-sink-cloud on | off          send this project's lines from this folder, or hold them back
    stele-sink-cloud rules on | off    send the rule text too, or rule ids only
    stele-sink-cloud status            both switches, the server, and which token a push uses
    stele-sink-cloud version

`login --project` keeps a token for one project, the one whose id is in
this folder's `stele.lock`, wherever that project is checked out; for that
project it wins over the one for the server. `login --with-token` reads a token from
stdin. A push uses `STELE_CLOUD_TOKEN` if set, then the project's token,
then the server's. `logout` does not revoke the token: do that in stele
Cloud.

Sending is off until you run `stele-sink-cloud on`. Until then stele keeps
the lines for later: from stele v1.6.0, `stele push` says they are waiting
and exits 0; v1.5.1 and earlier exit 1. Rule text is
on until you turn it off.
Rule text needs stele v1.3.0 or later. While stele says its list of rules is
incomplete (shared rules not compiled yet: run `stele compile`) or stele.md
does not parse, the push sends the lines but no rule text, and says why.
