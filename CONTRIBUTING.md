# Adding a row

One row per project. A row says: this person is working on this game, this is the code. It
reserves nothing, if two people want the same game, both get a row.

## Rules

1. **A public repo is required**, and it has to exist already. Checked when the row lands and
   again every day.
2. **Silence expires.** A row goes Quiet after 3 months (90 days) without a commit and
   Dormant after 6 months (180 days), whatever its status. Automatic, no judgement involved.
   One exception: a row its owner marks `complete` has no clock.
3. **`recomp`, `port` and `decomp` are separate.** Different work, different rows, separate filters.
   A recomp is the original binary translated by a tool into code that runs natively. A source
   port (`port`) is the game built for today's machines from source code people wrote back: a
   decompilation, a disassembly or a rewrite. A decomp is that source itself, in C or in assembly,
   rebuilding the original binary; a disassembly counts as one.
4. **Anyone can add a row, the owner decides.** A row added for someone else lands *unclaimed*
   and is built only from public repo data. The repo owner can claim or correct it;
   until then, anyone may fix its details.
5. **Duplicates are fine.** Knowing who does what is the point; gatekeeping isn't.
6. **No ROMs, ISOs, game assets or links to them.** Submissions carrying one are rejected by
   CI and removed on sight.

## A decomp that publishes a progress report

A decomp that publishes an objdiff progress report needs no file and no form to show its
progress: the board reads the report every day. Upload `build/report.json` as a GitHub Actions
artifact named `VERSION_report` (for example `SLUS_203.88_report`), from a workflow that runs on
your default branch. The game page then shows the share of matched code, and the status In
progress until it reaches 100%, then Complete, unless you have set a status yourself. When the
repository publishes several versions of the game, a figure appears only if one of them is the
release your row works from (the `original` field of the file) or if they agree within half a
point. A file or the form still adds what a report cannot say: who maintains the project, whether
it is paused or only exploring, notes, the help you want and the release you work from.

## The short way: a file in your own repo

Commit `.recomp.json` at the root of your project:

```json
{
  "$schema": "https://recomp.fyi/schema/v1.json",
  "game": "Wave Race 64",
  "wikidata": "Q3142278",
  "system": "N64",
  "type": "recomp",
  "original": { "region": "USA", "revision": "1.0" },
  "status": "playable",
  "targets": ["PC", "Steam Deck"],
  "help": ["Audio timing"],
  "notes": "What works, what's next."
}
```

The daily job reads it, claims your row and fills it in. On GitHub it also finds the file in a
repository that isn't listed yet and creates the row, as long as the file sets `game`,
`system` and `type`. Every field is optional on a row that already exists, editing the file
edits the row, and deleting the file returns the row to unclaimed (it keeps the `status`, the
`targets`, the `approach` and the `toolchain` the file last named, the rest of what the file said
is cleared).
GitHub, GitLab, Codeberg,
Gitea and Forgejo all work. A file committed as `.recomp-board.json`, its first name, keeps
working.

The format is open (CC BY 4.0) and described at https://recomp.fyi/spec, so any other list
or tool can read the same file. The `$schema` line lets editors check it as you type.

To be listed right away, or on a host other than GitHub, open the
[manifest form](../../issues/new?template=1-manifest.yml): it asks only for the repository
URL. Any account can send it, the file in the repository is the proof.

## The other routes, same result

**Issue form.** [Add or correct a project](../../issues/new?template=2-add-project.yml):
guided fields, no JSON. A bot validates it, writes the row and closes the issue, usually
within a few minutes. If it fails, it comments with the exact reason; edit the issue and it
runs again. For someone else's repository only the facts are kept (game, system, kind of
work, name); the owner writes the rest when they claim the row.

## Fields

Every value the board accepts, with its limits, is listed on the site under
[Every field and the values it takes](https://recomp.fyi/add.html#values). That list is built from the
code that reads the file, so it is the one to trust if this page ever lags behind.

Required: `id`, `game`, `system`, `type`, `project`, `status`, `repo`.

| Field | Notes |
| --- | --- |
| `id` | Lowercase slug, unique on the board. |
| `system` | The platform of the original binary your project works from, whether you recompile it, decompile it or port the game from its decompiled source: `N64`, `PS1`, `PS2`, `GameCube`, `Saturn`, `Dreamcast`, … Reuse an existing spelling; a usual other one (`PlayStation`, `Nintendo 64`) is read as the board's name. For a port of source code its authors released, the platform that source was written for. |
| `type` | `recomp`, `port` (a source port, an engine rewritten from the game's file formats included) or `decomp`. A repository that does two of them writes a list in its manifest, `["decomp", "recomp"]` or `["decomp", "port"]`, and gets one row of each. |
| `status` | `exploring`, `in-progress`, `playable`, `released`, `complete` (finished: a decomp whose source fully rebuilds the original, or a recomp or source port with no further work planned; the activity clock stops), `paused`. Self-reported, and separate from activity. |
| `targets` | Where your build runs: `["Windows", "Steam Deck", "Switch"]`. A console is written with its `system` name. Different from `system`, which is the platform of the original binary. Until you set it, the board shows the platforms your latest release's file names name (`win-x64`, `.AppImage`, `.apk`), marked as such. |
| `toolchain` | Recompiler, decomp toolkit, or the engine or layer a source port is built on: `N64recomp`, `Xenonrecomp`, `Rexglue`, `Psxrecomp`, `Snesrecomp`, `Decomp-toolkit`, `Splat`... Capital first letter, the rest lowercase. In the file, one name or a list of up to 4 when the project is built on several. The decompilation a source port is built from is not a toolchain and is left out. |
| `approach` | Method, one line. |
| `wikidata` | The game's Wikidata item (`Q` and digits), from its wikidata.org address. File only. |
| `original` | The release you work from: `region`, `revision`, `serial`, `sha1` of the file you expect, each optional; a list when you support several. File only. |
| `repo` | Public repository, any host. See below. |
| `maintainers` | `name` plus any https link where you can be reached. |
| `links` | Devlog, Discord invite, thread. Never a game file. |
| `help` | Short phrases, one per thing you'd take a hand with. Highlighted on the board. |
| `notes` | Up to 600 characters. Long notes are fine, the table expands the cell. |
| `updated` | `YYYY-MM-DD`, set for you on intake. |

**Filled in automatically, leave them out:** `version` (latest release tag), `builds`
(platforms named in your latest release's file names), `lastCommit`, `repoStatus`, `claimed`,
`addedBy`. The daily job overwrites whatever you put there.

## Repos that aren't on GitHub

Any public HTTPS repository is accepted. GitHub, GitLab and Codeberg (and other
Gitea/Forgejo instances) have their commit dates and release tags read through their APIs;
other hosts are checked for being reachable, and their dates stay as reported.

Submitting happens through this repo. The issue form needs a free GitHub account; the
manifest needs none. What is not accepted is a row with no public code
behind it anywhere, that's rule 01, and it's the only thing keeping the table honest.

## Who can edit an unclaimed row

Whoever added it, and the owner of the repository. A row that came from the repository scan
has no human parent, so anyone may correct it. Everything else goes through the "Something else" form,
in the open. Every submission carries the GitHub account that sent it, which is what keeps
third-party rows accountable.

## Claiming, editing, releasing

Two ways to make a row yours: commit a `.recomp.json` to the repository (any host, any
account, and the way for repositories owned by an organisation), or send the
[add or correct form](../../issues/new?template=2-add-project.yml) from the GitHub account
that owns the repository. Only you can edit it from then on, and the periodic scan stops
filling it in. To hand it back: delete the file, or tick "Release my claim" in the same form.
