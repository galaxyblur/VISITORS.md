# Changelog

Follows [Semantic Versioning](https://semver.org/).

## 0.7.0 (2026-09-26)

**New: `HANDOFF.md`, the third framework page.** `AGENTS.md` says how work is done, `VISITORS.md` who may be in a space, `HANDOFF.md` how a person's intent becomes a plan in a space and how the result comes back. A person asks; a worker in the space plans; the person approves; the worker executes; a director relays, records and verifies. Nobody outside a space plans its work. Roles: person, director, desk, ask, plan, worker, report, transport, and cross-space work through one primary space. It uses `SPEC.md`'s terms: the worker is its worker, the director is a visitor outside its desk, and an ask or plan reaches a worker as its person's direction, not through the board. Tool-agnostic, on the same version line as `SPEC.md`.

**Changed in `HANDOFF.md` before release,** from writing the first binding: anything a tool inlines into an ask or a plan (instructions, memory) is part of the text the person gave or approved (it said only plans, and tools inline at ask time); and the approved plan is kept unchanged, apart from the report, where a fresh worker can read it (reports are rewritten whole, so a plan kept only in one is lost).

**New: `HANDOFF-herdr-projects.md`, the first binding.** How [herdr-projects](https://github.com/eliasstravik/herdr-projects) carries each role, the way `GIT.md` binds `SPEC.md`: the coordinator is the director, the project folder is the desk (its root inside a space the person owns, one project per space), a thread is the worker, and its first report is the plan, approved with an `Approve plan` line in `## Next` and kept in `library/plan.md`. Ends with where the tool and the framework don't meet: provenance of typed-in text, reports copied home whatever the carry-out, and the coordinator answering a worker's prompts.

**Templates** pin `visitors-spec: 0.7.0`. 0.7.0 changes nothing in a front desk or the home files, so an upgrade from 0.6.0 is only the pin.

## 0.6.0 (2026-09-22)

**Renamed: `VISITORS.md`.** The file every space carries is now `VISITORS.md`, its pin key `visitors-spec`, and the repo `galaxyblur/VISITORS.md`. The subject is whoever is in a space, so the name says so. The `ASSISTANT_*` home files keep their names: they are about an assistant. `templates/VISITORS.md` and `templates/VISITORS.home.md` replace the old templates. `assistants-visit` keeps its name (it wakes assistants), reads the new names, still reads the old ones, and no longer reads the pre-0.5 `resident` block. Entries below keep the old name.

**The spec is now a framework, and the old spec is `GIT.md`.** `SPEC.md` is one page of plain statements: person, identity, agent and assistant, space, policy, board, home, whoever is in a space, and the limits. It opens with the whole model in four sentences and states cardinalities as 1-to-1 and 1-to-many. It names no file formats and no tools. Everything concrete (identifiers, the chain's git trailers, visit records, front desk fields, the home files, the carried set, signing, conformance) lives in `GIT.md`, the spec done in git and markdown, with its section numbers kept. Where the two disagree, the framework wins.

**Worker and visitor.** Where a session started decides what it may do. Started in a space, as an identity that may work there: a *worker*, which may change the space. Started anywhere else: a *visitor*, which reads and may write to the board, nothing else. This replaces 0.4's knowledge-layer rule for sessions visiting from the home: such a session now drafts and posts to the board, and a session started in the space does the work. `GIT.md` invariant 7. The README's adoption prompt follows suit.

**Identity** is a new word: the account that names a person, and all a space ever sees. A person has identities 1-to-many; an identity owns spaces 1-to-many; identity to assistant is 1-to-1 and optional; assistant to home is 1-to-1, and the identity owns the home. Neither an agent nor an assistant has an identity of its own: an agent carries whoever runs it for one session, an assistant carries its identity always. This closes a hole where two assistants of one person could both enter a space as that person. `GIT.md`'s invariants are rewritten to this model and renumbered.

**Policy fields.** Who may enter and who may work are separate: `members` is who may work (unchanged in meaning), new `visitors` is who may enter beyond them (`none`, a list, `@issuer`, `any`), read-only plus the board. `any` admits a session with no identity, read-only; it replaces `unattributed`. **Visit log level** is one field, `visit-log: none | visit | file` (replaces `log-reads` and `visits: none`); `visits` now says only where the record goes. **Carry-out has three values:** `open`, `with-attribution` (was `attributed`), `none`. A space that states no policy has the strictest one, now spelled out: owner only, log `file`, carry-out `none`, no board. The board's form is the space's choice, an outside system by URL included. The visit record carries the arrival declaration: `memory`, `logs`, `spec`, `role`.

**Carry-out has no assumed destination, and `none` has one exception.** Carry-out is information written down anywhere outside a space, wherever it lands, under one rule. `none` means nothing, except what the owner releases by name; the release is a board message, which is its record. A home's carry-out is `none`, and the identity load (ID, public self, one wallet entry) is the owner's standing release by name. Under the earlier draft `none` admitted no exception at all, which forbade the identity load it relied on.

**Removed: the understanding check** (0.4.0's invariant 7 and §11 *Before a decision*). Briefing a person before they decide is one person's practice with their own assistant. It belongs in that person's `ASSISTANT_SELF.md`. Standing grants also leave the framework: they are about work. `GIT.md` keeps both. Taken out of the framework and kept in `GIT.md`: the chain as a word, "whether assistants may enter" as something a space states, and "two rules in conflict means stop and ask".

**Removed: idiocorpus and idiocortex.** "Home" was doing all the work; the coinages named a knowledge space with and without an assistant, which no rule depends on. Entries below keep them.

**`assistants-visit`** now refuses at the door under `assistants: none` as well as `min-spec`. It does not check `members`; the wallet entry is the record that the space admitted the identity.

**Upgrading 0.5 → 0.6** (`UPGRADING.md`): rename the file and key, move fields to their new names with no change of policy, and report that sessions started elsewhere are visitors now. New Example 10: a visit from home.

## 0.5.0 (2026-09-20)

**Scope.** `ASSISTANTS.md` is about the exchange of information; `AGENTS.md` remains the document about interaction (§1). The spec covers four boundaries: what comes into a space, what leaves it, what is recorded about who was there and for whom, and what passes between an assistant and its home. It no longer says what an assistant may do in a space or how. Cut or made non-normative on that test: §11's git cadence (pull, commit, push), and the knowledge-layer rule for sessions visiting from the home. `unattributed` is reworded as an information rule with the same behavior.

**Owner** (new invariant 8, new `owner` field): every space has exactly one owner, a person, inside an organization too. A pre-0.5 front desk with one member: that member is the owner.

**Idiocorpus and idiocortex** (§2, §10): a person's own knowledge space is an idiocorpus; with a resident assistant it is an idiocortex, and "home" is the role it plays. One assistant per home and one home per assistant (invariant 9). A space appears in at most one of a person's wallets (invariant 10).

**Breaking: the home files.** A home is marked by three fixed files at its root, replacing the front desk's `resident` block: `ASSISTANT_ID.md` (small, safe to show), `ASSISTANT_WALLET.md`, `ASSISTANT_SELF.md`. ID and self are now separate. Optional `ASSISTANT_SELF_PUBLIC.md` is the person-approved extract of self that may be seen outside the home. `tools/assistants-visit` reads the new files and still reads a `resident` block when they are absent, until 0.6. To migrate: move the self and wallet pages to the root names, add `ASSISTANT_ID.md`, delete `resident`.

**The carried set** (§8, §11): the home must be reachable, or the assistant brings a dated read-only snapshot: its ID, its public self, and the one wallet entry for the space being visited. Never the full self or the full wallet. `assistants-visit --pack` writes one. With neither home nor carried set, the session is a plain agent.

**Policy is the space's:** `visits: none` and `board: none` are allowed. The assistant still records every visit, at home when the space keeps no record (§6). A space with no front desk is treated as `carry-out: none` with no board (§7). Board messages flow between spaces in both directions: the sender's person approves sending, and the receiving owner's policy decides acceptance (§8). §9 now uses RFC 2119 keywords.

**Versions and upgrading** (§7, new `UPGRADING.md`): a front desk may set `min-spec`; an assistant whose `ASSISTANT_ID.md` declares an older `assistants-spec` stays out and the session goes on as a plain agent. `assistants-visit` enforces it at the door. A visitor reads an older front desk's missing fields by stated defaults, and a newer one's unknown fields carefully. Only the owner changes a front desk. `UPGRADING.md` gives agent-followable steps between versions behind one instruction: *adopt the latest ASSISTANTS.md spec here.* New `EXAMPLES.md`: nine user stories.

Wallet: standing permissions take a `scope` (`home`, `all`, or a repo), which also decides what travels in a carried entry; `principal` moves to the ID file. New templates for the ID, self and public self. README lists all ten rules (it had omitted 7).

## 0.4.1 (2026-09-20)

A space receives through its board (§8, §9): to move something between a person's own spaces, the assistant writes a message to the receiving space's board, with approval and within the sender's `carry-out`. A session does not reach into another space to fetch, and the home is no exception beyond `self` and `wallet`. Follows from 0.4.0's closed home: a space that can't read the home still needs a way to be told things.

## 0.4.0 (2026-09-20)

The person decides (new invariant 7): an assistant can hold memory for its person but not understanding, so before a decision it SHOULD brief the state and check understanding with specific questions (§11). Only decisions are gated, the person may waive, and the brief follows the person's recorded preference, which now lives in `self` (§10). The home stays home (§8): a visiting assistant reads only its `self` and `wallet` from home and MUST NOT raise home matters in a space unless asked. `self` holds how to work with the person, never what is going on at home, and standing requests tied to home matters are scoped to home sessions. `tools/assistants-visit` now says so in its wake lines. Visiting from the home (§11): a home session that walks into a space keeps its identity and takes on the space's conventions, reads the space's `AGENTS.md` and `ASSISTANTS.md` in full before writing, stays in the space's knowledge layer, and hands anything else to a session started in the space; a conflict between home and space rules stops the work until the person rules. Found in use: a standing reminder written into `self` without a scope fired inside an unrelated space on the day it was written.

## 0.3.1 (2026-09-18)

`tools/assistants-visit --id [dir]` prints the resident's ID when `dir` is its home or a wallet space, and nothing elsewhere. README: showing who's working, with a status line badge and a herdr pane label as examples. No normative changes.

## 0.3.0 (2026-09-18)

Git history as the visit record (§6): a git space may set `visits: git`. Commits carrying the chain record write visits. A visit that commits nothing makes one empty commit carrying the chain. Not combinable with `log-reads: file`. The front desk table (§7) now splits `visits` from `board`. Found in the first two code-space adoptions (Daybreaker, dotfiles), where a per-visit record file only duplicated the commits.

## 0.2.1 (2026-09-18)

README: a standard prompt for adding a space through your assistant. You give it from the home, and it covers spaces that already have a front desk. No normative changes.

## 0.2.0 (2026-09-18)

Waking in a space (§11): an assistant whose session starts inside a space MUST wake from its home first, and where the home lives is the person's per-machine configuration. The arrival procedure (§7) now starts there. The wallet's `spaces` entries (`repo`, `role`) are named as the fields tools read (§10). New reference tool `tools/assistants-visit` for git spaces, run from a session-start hook.

## 0.1.1 (2026-09-18)

Third-party data: SHOULD `carry-out: none`, no longer SHOULD `assistants: none`. Retention was the concern, and carry-out already governs it. `assistants: none` is now for owners who refuse assistants outright.

## 0.1.0 (2026-09-18)

Initial draft. Terms, six invariants, identifiers, the chain and its git binding, visit records (visit-level floor, per-file reads only when the owner requires them), the front desk file, carry rules, board, home and wallet, session discipline, optional SSH signing, conformance, relation to existing standards.
