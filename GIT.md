# VISITORS.md in git and markdown

> Version 0.7.0 · Does [SPEC.md](SPEC.md) with plain git and markdown files. (In standards talk: a binding.) Until 0.5.0 this text was the spec itself; section numbers are unchanged, so an older "SPEC §8" is §8 here.

One way to do the framework, with plain git and markdown. It is not the only way. Where this document and the framework disagree, the framework wins and this document has a bug.

The key words MUST, MUST NOT, SHOULD, SHOULD NOT, and MAY are used as described in RFC 2119.

## 1. Purpose and scope

`AGENTS.md` is about interaction: what an agent may do in a space and how it does it. `VISITORS.md` is about who is in a space, what they bring in, what they take out, and what is recorded. Nothing else.

> Agents forget; assistants remember. This spec makes every session answer to one identity, and lets every space decide what it may carry away.

Four boundaries:

1. **In.** What may be brought into a space (carry-in, board messages).
2. **Out.** What may leave it (carry-out).
3. **About.** What is recorded about who was there and for whom (owner, members, the chain, visits, signing).
4. **Home.** What passes between an assistant and its home (the identity load, what is written back, the carried set).

The test for any rule: if it governs information crossing one of those boundaries, it belongs here. If it governs doing work, it belongs in `AGENTS.md`, even when the worker is an assistant.

**Beside `AGENTS.md`.** This spec never defines the contents of `AGENTS.md` and never overrides them. What a worker may do in a space is whatever `AGENTS.md` lets any agent do. A front desk grants no abilities and removes none, except the information ones. The one thing this spec asks of `AGENTS.md` is a pointer line (§7).

## 2. Terms

- **Person.** A human. The only thing that can be held accountable.
- **Identity.** An account that names one person: `alice@github.com`. All a space ever sees.
- **Agent.** An AI runtime (model plus harness) for one session. It carries the identity of whoever runs it, for that session. Agents are mortal: nothing survives the session unless it is written down.
- **Assistant.** A persistent AI bound to exactly one identity. It has an ID, a memory, and a persona, and it uses agents as its medium. It acts only when its person invokes it, or under a standing permission its person has recorded. It owns nothing.
- **Space.** Anything an agent can work in: a folder, a repository, a server, a device, a served API. A space has its own conventions and goals. It is either solo or shared.
- **Owner.** The one identity accountable for a space. The owner sets its policy.
- **Worker.** A session started in a space by an identity that may work there. It may change the space.
- **Visitor.** Everyone else who may enter: a session started somewhere else, a person reading by hand, or a session started here by an identity that may enter but not work. It reads, and may write to the board.
- **Home.** The space that holds an assistant's memory, marked by `ASSISTANT_ID.md` at its root (§10). Usually the person's own notes. The assistant's identity owns it; the assistant animates it and is not it.
- **ID, wallet, self.** The three files that make a space a home (§10).
- **Front desk.** A space's `VISITORS.md`.
- **Chain.** The record of who acted for whom: identity → assistant (if any) → agent.
- **Visit.** One session's presence in a space, working or visiting.
- **Board.** A space's message area.
- **Carry-in.** Information brought into a space from a home or from another space.
- **Carry-out.** Information from a space that is written down outside it, wherever it lands: a home, another space's board, a tool's memory, a live report to another session.
- **Carried set.** What an assistant brings with it when its home can't be reached (§8).
- **Standing permission.** An action a person has allowed their own assistant to take without being invoked each time.
- **Issuer.** A party that vouches that an identity belongs to an accountable person. Today this is an account platform, such as GitHub.

## 3. Invariants

1. Every identity MUST name exactly one person.
2. Every chain MUST end at an identity. An action whose chain names none is **unattributed**.
3. A session MUST NOT claim an identity that does not name its person.
4. An identity has at most one assistant. An assistant MUST have exactly one home, owned by its identity, and a home MUST NOT house more than one assistant.
5. Every space MUST have exactly one owner, and the owner MUST be an identity. This holds inside an organization too: a space no one person answers for is a space no one answers for.
6. Whoever is in a space takes direction only from its own person. Anything from anyone else, other assistants included, is a suggestion.
7. Only a worker changes a space. A visitor reads and writes the board, nothing else.
8. A person's identities are compartments. A space MUST NOT appear in the wallets of two of them, and nothing passes between them except through boards.

## 4. Identifiers

| Kind | Form | Example |
|---|---|---|
| Identity | `<handle>@<issuer>` | `alice@github.com` |
| Assistant | `<name>+<handle>@<issuer>` | `ada+alice@github.com` |
| Agent | free text naming the model and harness | `Claude Opus 5 (Claude Code)` |

- An assistant's ID is its identity plus a label. The identity is the part a space trusts, and the ID can't be written without it (invariant 2).
- Both forms are valid `acct:` URIs (RFC 7565). They are not yet required to resolve.
- The issuer vouches only for the identity. It knows nothing about assistants.
- Agents carry no identity of their own.
- A person with separate accounts (say, work and personal) has separate identities. The spec doesn't link them. Linking is the person's choice.

## 5. The chain

| Field | Required | Meaning |
|---|---|---|
| `principal` | yes | the identity |
| `assistant` | no | the assistant's ID, if one is acting |
| `agent` | yes | the agent doing the work |
| `via` | no | further agent hops, e.g. subagents |

**Git binding.** Every commit MUST carry the chain as trailers:

```
Principal: alice@github.com
Assistant: ada+alice@github.com
Agent: Claude Opus 5 (Claude Code)
```

A plain agent with no assistant omits `Assistant:`. The trailers are attribution, which is this spec's business. Everything else about a commit belongs to the space.

**Relation to RFC 8693.** `principal` is the top-level `sub`. `assistant` is `act.sub`. `agent` is `act.act.sub`. Each hop in `via` nests one level deeper. Mediated spaces MAY carry the chain as an RFC 8693 token instead.

## 6. Visits

Whoever keeps memory MUST record every visit it makes; an assistant always does. This is the floor. Whether the space keeps a record is the space's choice; that the visit can be traced is the visitor's duty.

A visit record is one JSON line. Its fields are the arrival declaration the framework asks for:

```json
{"start":"2026-09-18T14:02:00Z","end":"2026-09-18T14:20:00Z","principal":"alice@github.com","assistant":"ada+alice@github.com","agent":"Claude Opus 5 (Claude Code)","memory":"github.com/alice/notes","logs":"visit","spec":"0.7.0","role":"worker","mode":"write"}
```

- `memory` names where the session keeps memory (an assistant's home; a harness's store), or `none`. Never the person.
- `logs` is what the session records for itself: `none`, `visit`, or `file`.
- `spec` is the version of this spec the session follows.
- `role` is `worker` or `visitor`. `mode` is `read` or `write`; a visitor writes only to the board.
- Records go in `<visits>/<principal>.jsonl`, one file per identity, so two people never conflict. Here `<visits>` is the directory the front desk names.
- If `visit-log: file` is set, the record adds `"reads": [paths]`.
- A session SHOULD write its record before it ends.
- **Recorded at home.** When a space sets `visit-log: none`, has no front desk, or gives the visitor no write access, an assistant MUST record the visit in its own home. That record holds when, where and in what mode, and never what the space contained, so it is not carry-out.
- In a solo space whose only member is the owner, the space's existing event log MAY serve as the visit record.
- **Git history as the record.** A git space MAY set `visits: git`. Every commit already carries the chain (§5), so a worker's visit is recorded by its commits and writes nothing else. A visit that would commit nothing records itself with one empty commit carrying the chain; a visitor with no push access records it at home instead. `visit-log: file` needs a record file, so it can't be combined with `visits: git`.

**Per-file reads** are logged only when the front desk requires it. The front desk states this before entry. A visitor that doesn't accept it MUST leave without reading.

## 7. The front desk

`VISITORS.md` sits at the space root, next to `AGENTS.md`. `AGENTS.md` SHOULD contain a pointer:

> Whoever works here for a person: read VISITORS.md.

The file opens with YAML frontmatter:

```yaml
visitors-spec: 0.7.0
owner: alice@github.com
members: [alice@github.com, bob@github.com]
visitors: none             # none | [identities] | @issuer | any
issuers: [github.com]
assistants: allowed        # allowed | none
min-spec: 0.7.0            # optional. the oldest spec a visiting assistant may follow
visit-log: visit           # none | visit | file
visits: visits/            # a path | git
board: board/              # a path | a URL | none
carry-out: with-attribution   # open | with-attribution | none
```

| Field | Meaning |
|---|---|
| `owner` | the one identity accountable for this space (invariant 5). Always a member |
| `members` | who may work here: identities that may change the space. The framework's *who may work* |
| `visitors` | who may enter beyond members, read-only plus the board. `none`, a list, everyone from an issuer (`@corp.example`, which must be in `issuers`), or `any`. `any` admits a session with no identity, read-only. The framework's *who may enter* |
| `issuers` | issuers this space trusts to vouch for identities |
| `assistants` | `allowed`: members and visitors may come through their assistant. `none`: no session that remembers; plain agents only |
| `min-spec` | optional. An assistant whose `ASSISTANT_ID.md` declares an older `visitors-spec` does not enter as an assistant (see *Versions*) |
| `visit-log` | what the space records about visits: `none`, `visit`, or `file` (every file read). The framework's *visit log level* |
| `visits` | where the record goes: a directory path, or `git` (§6). Ignored under `visit-log: none` |
| `board` | a directory path, a URL for an outside system, or `none` if the space receives no messages |
| `carry-out` | what may be written down outside this space. `open`: anything. `with-attribution`: anything, citing this space by name. `none`: nothing, except what the owner releases by name (§8) |

Every field is a rule about information. None says what work may be done here; that is `AGENTS.md`.

The body is human-readable and MUST include:
- a one-line statement of the file's purpose
- the arrival procedure
- any house rules beyond the frontmatter

**Arrival procedure.** An assistant first wakes from its home (§11). Then read `AGENTS.md`, then `VISITORS.md`, then open board messages addressed to your principal or to `any`. Then begin the visit record, which is the declaration (§6). A worker then works; a visitor reads.

**Versions.** Two version numbers meet at the door, and both are `visitors-spec` fields. The front desk's says which spec the space's policy is written in. The visitor's, in its `ASSISTANT_ID.md`, says which spec the assistant follows.

- *The space restricts the visitor.* A front desk MAY set `min-spec`. An assistant whose declared version is lower MUST NOT enter as an assistant. The session MAY go on as a plain agent, which carries nothing away. It SHOULD tell its person why, and that upgrading the home would fix it. Versions compare as SemVer. A space has a reason to ask: a rule it relies on, such as the carried set or the closed home, exists only from some version on.
- *The visitor meets an older front desk.* It follows the front desk as written. A field the front desk lacks takes the default in [UPGRADING.md](UPGRADING.md). One case matters today: a front desk below 0.5 has no `owner`. If it lists one member, that member is the owner. If it lists several, the space has no declared owner, and a visitor SHOULD say so to its person.
- *The visitor meets a newer front desk.* It MUST treat a field it doesn't know as the more careful reading, and SHOULD tell its person that its home is behind.

**Changing the front desk.** The front desk is the owner's policy, so only the owner changes it, or a worker acting for the owner. That includes upgrading its pin. Anyone else proposes a change through the board. [UPGRADING.md](UPGRADING.md) gives the steps from each version to the next, written so that an agent can follow them. The instruction is one line: *adopt the latest VISITORS.md spec here.*

**No front desk.** A space without a `VISITORS.md` has stated no policy, so the strictest one applies: only the owner may enter or work, `visit-log: file`, `carry-out: none`, no board. An assistant enters only when its identity is the owner, which its wallet records, and it records the visit at home.

## 8. Carry rules

Four flows cross a space's edge. Each has a gate.

| Flow | From → to | Gate |
|---|---|---|
| Identity load | home → a session in a space, read-only | the wallet lists the space, and the space sets `assistants: allowed`. This is the home owner's standing release, by name (§10) |
| Carry-out | a space → anywhere else: the home, another space | the space's `carry-out`; wherever it lands, it cites the space |
| Board message | any space → any other space's board, the home included | carry-out from the sending space, carry-in approval by the sender's person, and the receiver's policy |
| Visit record | session → the space's record, or the home | the space's `visit-log` |

- **Carry-in.** Anything from a session's home, or from another space, MUST be approved by the session's person before it enters this space. A visitor's carry-in goes on the board. A worker's goes where the work goes.
- **The home stays home.** Carry-in covers the conversation as well as the files. A session in another space reads its ID, self and wallet from its home (§11) and nothing more. It MUST NOT raise matters from its home, or from another space, unless its person asks for them. A space is aware only of itself. Where the harness allows it, the session SHOULD be denied read access to the rest of the home.
- **A space receives through its board.** Spaces pass information to each other in both directions, and the board is how. To move something into another space, an assistant SHOULD write a message to that space's board (§9), with its person's approval and as the sending space's `carry-out` allows. The sender's person approves the sending. The receiving space's owner, through its policy, decides whether it is accepted. A session SHOULD NOT reach into another space to fetch. The home is no exception: beyond the identity load, it receives through its own board.
- **Carry-out.** Set by the `carry-out` field, and the same rule for every destination. The spec doesn't assume where information ends up: an assistant may carry from a space to its home, or from one space to another. With `open`, anything may leave. With `with-attribution`, anything may leave, and wherever it lands MUST cite the source space by name. With `none`, nothing leaves on a session's own initiative or under a standing permission.
- **The owner may release, by name.** The policy is the owner's, so the owner can name an exception to it. Under `carry-out: none`, one item leaves only when the owner approves that item, as a board message (§9) which is then the record of the release. In a solo space the owner is the assistant's person. In a shared space, a member who isn't the owner asks the owner through the board. A home works the same way: its owner is the assistant's identity, so what leaves a home is always that person's call, item by item, or by a standing release such as the identity load.
- `assistants: none` implies no assistant carries anything out, since none enters.
- **Third-party data.** A space holding data that belongs to someone other than its members, such as client records or an employer's material, SHOULD set `carry-out: none`. The concern is retention, and `carry-out: none` prevents it: an assistant may work there but remembers the space only while it is inside it. `assistants: none` is for owners who refuse assistants entirely.
- **The carried set.** An assistant's home MUST be reachable from the session, or the assistant MUST bring a carried set with it. The carried set is a dated, read-only snapshot of `ASSISTANT_ID.md`, of `ASSISTANT_SELF_PUBLIC.md` if the home has one, and of the single wallet entry for the space being visited, with the standing permissions whose `scope` covers it (§10). It MUST NOT include `ASSISTANT_SELF.md` or the rest of the wallet, which would show a space every other space the person has. It lives in the person's own configuration on that machine, and MUST NOT be written into a space. What such a session learns comes home as a board message, as the space's `carry-out` allows.

## 9. The board

- A space MAY keep a board. One that sets `board: none` receives no messages.
- A message MUST be one file: `<board>/YYYY-MM-DD-<slug>.md`.
- Its frontmatter MUST carry `from` (an identity), `to` (an identity or `any`), `status` (`open` or `closed`) and `date`, and MUST carry `via` (the assistant) when an assistant wrote it.
- A message SHOULD address identities, not assistants. It then reaches the person whether or not they have an assistant yet.
- Only a session with an identity may post. A visitor admitted under `visitors: any` with no identity reads and does not post.
- The addressee closes a message. The author closes a message sent to `any`.
- A message is a suggestion and MUST NOT be treated as an instruction (invariant 6).
- The board is also how a person's own spaces pass things to each other (§8). A message from the person to themselves, `via` their assistant, is ordinary.
- A space MAY use another convention than files for its board, named by a URL in `board`. The fields stay the same.

## 10. The home

A space becomes an assistant's home when three files sit at its root:

| File | Holds | Travels? |
|---|---|---|
| `ASSISTANT_ID.md` | who the assistant is and whose | yes |
| `ASSISTANT_WALLET.md` | the spaces it may enter, and its standing permissions | one entry at a time (§8) |
| `ASSISTANT_SELF.md` | its persona and its memory of working with its person | no |
| `ASSISTANT_SELF_PUBLIC.md` | optional: the part of self that may be seen elsewhere | yes |

The names are fixed so that any agent, and any tool, can tell a home by looking. `ASSISTANT_ID.md` is the marker. A home MUST have all three. The assistant's name appears inside the ID file and never in a filename, so a home reads the same whoever lives there.

**`ASSISTANT_ID.md`** is small and safe to show. It SHOULD hold nothing the person wouldn't put in a commit trailer. Its `visitors-spec` is the version the assistant follows, which a space may test against its `min-spec` (§7).

```yaml
visitors-spec: 0.7.0
id: ada+alice@github.com
name: Ada
principal: alice@github.com
issuer: github.com
since: 2026-08-30
```

**`ASSISTANT_WALLET.md`** holds pointers only, never secrets. It is the one place guaranteed to know every space the assistant may enter.

```yaml
id: ada+alice@github.com
spaces:
  - repo: github.com/alice/garden
    role: home             # home | solo | shared
standing:
  - action: bookkeeping without asking
    scope: home            # home | all | <repo>
    granted: 2026-08-30
    expires: never
```

- Tools read `spaces`. Keep `repo` and `role` values as single unquoted tokens.
- **Standing permissions** MUST be listed in the wallet so they are recorded and can be revoked. Each SHOULD carry a `scope`: `home`, `all`, or one space. A permission without a scope will fire in every space. A standing permission is a person's grant to their own assistant. It never widens what a space's `AGENTS.md` allows.

**`ASSISTANT_SELF.md`** is the assistant's persona and its memory of working with its person: the positions it holds, the calls it has made and how they aged, how its person wants to be worked with and briefed. When the home is reachable it is read on every wake, in the home and in every space, so it SHOULD hold *how* to work with the person and never *what* is going on at home. A standing request tied to a home matter MUST be scoped to home sessions. The persona is an exchange between an assistant and its person. It belongs here, and in neither the front desk nor `AGENTS.md`.

**`ASSISTANT_SELF_PUBLIC.md`** is optional. It is the part of self that may be seen outside the home, and it is what travels in the carried set. `ASSISTANT_SELF.md` is the master and this file is an extract of it, never a second source. The name is the test: it MUST hold nothing the person wouldn't show in any space the assistant visits. How to speak, how to brief, the person's own practices. No people, no projects, no history. The person MUST approve every revision, and its frontmatter records the date:

```yaml
id: ada+alice@github.com
approved: 2026-09-20
```

A home's front desk SHOULD set `owner` and `members` to its identity alone, `visitors: none`, and `carry-out: none`. The identity load (§8) is the owner's standing release, by name: the ID, the public self, and one wallet entry. Everything else leaves a home only as the owner's release (§8).

## 11. Sessions

An agent session is mortal, and what it doesn't write down is lost. Two duties follow, and both are about information:

- Before ending, write into the home what the assistant should remember, and into the space what the space should keep, subject to the carry rules.
- Record the visit (§6).

*How* a session saves its work (when it pulls, commits and pushes) is the space's business and belongs in `AGENTS.md`. In a git space the usual advice holds: pull before the first write, and commit and push at logical boundaries, because anything uncommitted dies with the session.

**Waking in a space.** A session started inside a space, by an identity that may work there, is a worker. An assistant acting there MUST wake from its home first: read `ASSISTANT_ID.md`, `ASSISTANT_SELF.md` and `ASSISTANT_WALLET.md`, and nothing else from the home (§8), and confirm the wallet lists this space. If the home can't be reached, it wakes from the carried set (§8). With neither, there is no assistant in the session: the agent works as a plain agent and says so. Where the home is on a given machine is the person's configuration, never the space's. The space names no assistants. [`tools/assistants-visit`](tools/assistants-visit) does this for git spaces: it matches the current repo's `origin` against the wallets of the homes configured on that machine, then checks the front desk's `assistants` and `min-spec`. It prints the wake lines, or why it stays out, or nothing if no wallet lists the repo. It does not check `members`; the wallet entry is the record that the space admitted the identity. Run it from the harness's session-start hook.

**Visiting from the home.** The reverse also happens: a session starts in the home and walks into a space. It is a visitor there (invariant 7): it reads, and it may leave a board message. It changes nothing else, whatever the home's instructions say and whatever the person's role in the space. Anything the home session wants changed goes on the space's board, and a session started in the space does the work, with the space's `AGENTS.md`, skills and hooks loaded. Identity travels with the assistant; conventions belong to the space.

## 12. Signing

- Commits SHOULD be signed with an SSH key registered to the principal's issuer account (for GitHub, published at `github.com/<handle>.keys`).
- A space MAY verify signatures with git's `allowed_signers`, and MAY require them.
- An unsigned chain is **declared**. A signed chain is **verified**. Either counts as attributed. Only a verified chain is evidence.

## 13. Conformance

- **A conforming space** has a `VISITORS.md` with the §7 frontmatter, including its one `owner`, its `members`, and the spec version it is pinned to, and has the pointer in `AGENTS.md`.
- **A conforming assistant** has an ID per §4, exactly one identity, and exactly one home with the three §10 files, the ID file declaring the spec version it follows. It stays out of spaces whose `min-spec` it doesn't meet. It carries the chain on every write, records every visit, follows the carry rules, works only where it was started, and keeps home matters out of its visits.
- **A plain agent** in a conforming space follows the front desk and carries a chain with no assistant. That is full participation. Nothing needs to change when its person later gets an assistant.

## 14. Relation to existing standards

| This spec | Borrowed from |
|---|---|
| Chain fields and nesting | OAuth 2.0 Token Exchange, RFC 8693 (`sub`, `act`) |
| Identifier syntax | `acct:` URI, RFC 7565; WebFinger, RFC 7033 (future resolution) |
| Assistant identity card (future) | A2A AgentCard, with a `principal` extension |
| Session grouping (future) | A2A `contextId` |
| Visit vocabulary | W3C PROV-O (`Agent`, `Activity`, `actedOnBehalfOf`) |
| Signed delegation hops (future) | HDP, arXiv 2604.04522 |
| Front desk at a known path | `security.txt`, RFC 9116 |
| Issuer trust chosen by the space | the web's certificate-authority model (TLS) |
| Verified writes | git SSH commit signing, `allowed_signers` |
| Interaction rules, left alone | `AGENTS.md` |

## 15. Limits

This spec is a convention plus signatures. It makes misuse visible, not impossible. In a space no one mediates, compliance is on the honor system. Reads from a clone can't be observed. An issuer vouches for who is responsible, not for who is at the keyboard. See [FUTURE.md](FUTURE.md) for limitations, open questions, and planned mitigations.
