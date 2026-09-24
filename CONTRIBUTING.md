# Adding a row

One row per project. A row says: this person is working on this game, this is the code. It
reserves nothing, if two people want the same game, both get a row.

## Rules

1. **A public repo is required**, and it has to exist already. Checked when the row lands and
   again every day.
2. **Silence expires.** A row goes Quiet after 3 months (90 days) without a commit and
   Dormant after 6 months (180 days), whatever its status. Automatic, no judgement involved.
3. **`recomp` and `decomp` are separate.** Different work, different rows, separate filters.
4. **Anyone can add a row, the owner decides.** A row added for someone else lands *unclaimed*
   and is built only from public repo data. The repo owner can claim or correct it;
   until then, anyone may fix its details.
5. **Duplicates are fine.** Knowing who does what is the point; gatekeeping isn't.
6. **No ROMs, ISOs, game assets or links to them.** Submissions carrying one are rejected by
   CI and removed on sight.

## The short way: a file in your own repo

Commit `.recomp-board.json` at the root of your project:

```json
{
  "game": "Wave Race 64",
  "system": "N64",
  "type": "recomp",
  "status": "playable",
  "targets": ["PC", "Steam Deck"],
  "help": ["Audio timing"],
  "notes": "What works, what's next."
}
```

The daily job reads it, claims your row and fills it in. On GitHub it also finds the file in a
repository that isn't listed yet and creates the row, as long as the file sets `game`,
`system` and `type`. Every field is optional on a row that already exists, editing the file
edits the row, and deleting the file returns the row to unclaimed. GitHub, GitLab, Codeberg,
Gitea and Forgejo all work.

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

Required: `id`, `game`, `system`, `type`, `project`, `status`, `repo`.

| Field | Notes |
| --- | --- |
| `id` | Lowercase slug, unique on the board. |
| `system` | `N64`, `PS1`, `PS2`, `GameCube`, `Saturn`, `Dreamcast`, … Reuse an existing spelling so the filter stays tidy. |
| `type` | `recomp` or `decomp`. |
| `status` | `exploring`, `in-progress`, `playable`, `released`, `paused`. Self-reported, and separate from activity. |
| `targets` | Where your build runs: `["PC", "Steam Deck", "Switch"]`. Different from `system`, which is the platform the game shipped on. |
| `toolchain` | Recompiler or toolkit: `N64recomp`, `Xenonrecomp`, `Rexglue`, `Psxrecomp`, `Snesrecomp`, `Decomp-toolkit`, `Splat`... Capital first letter, the rest lowercase. |
| `approach` | Method, one line. |
| `repo` | Public repository, any host. See below. |
| `maintainers` | `name` plus any https link where you can be reached. |
| `links` | Devlog, Discord invite, thread. Never a game file. |
| `help` | Short phrases, one per thing you'd take a hand with. Highlighted on the board. |
| `notes` | Up to 600 characters. Long notes are fine, the table expands the cell. |
| `updated` | `YYYY-MM-DD`, set for you on intake. |

**Filled in automatically, leave them out:** `version` (latest release tag), `lastCommit`,
`repoStatus`, `claimed`, `addedBy`. The daily job overwrites whatever you put there.

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

Two ways to make a row yours: commit a `.recomp-board.json` to the repository (any host, any
account, and the way for repositories owned by an organisation), or send the
[add or correct form](../../issues/new?template=2-add-project.yml) from the GitHub account
that owns the repository. Only you can edit it from then on, and the periodic scan stops
filling it in. To hand it back: delete the file, or tick "Release my claim" in the same form.
