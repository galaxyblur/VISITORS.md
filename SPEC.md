# VISITORS.md

> Version 0.7.0. Done in git and markdown: [GIT.md](GIT.md). In use: [EXAMPLES.md](EXAMPLES.md).

`AGENTS.md` says how work is done in a space. `VISITORS.md` says who may be in a space, what they may bring in, what they may take out, and what is recorded.

In short: a person acts through identities. A space trusts identities and belongs to one. Whoever started in the space as a member may work there; everyone else may only read and leave suggestions. The space's policy says who, what leaves, and what is recorded.

## Person

- A person is a human. Only a person can be held accountable.
- Person to identity is 1-to-many. Hold more than one only to run more than one assistant, or to keep confidential domains apart.

## Identity

- An identity is an account that names one person.
- A space sees identities, never the person.
- Identity to owned space is 1-to-many. A person owns nothing directly.

## Agent and assistant

- An agent is software that acts for one session and is then gone. It carries the identity of whoever runs it, for that session.
- An assistant is an agent's persistent counterpart: bound to one identity, it keeps memory between sessions and speaks through an agent.
- Neither has an identity of its own. Its name is a label; the identity is what a space trusts.
- Identity to assistant is 1-to-1, and optional: an identity may have none. Assistant to home is 1-to-1, and the identity owns the home.

## Space

- A space is anywhere work happens. It has one name, and every log, citation and memory uses it. GIT.md says what the name is for each kind of space.
- Space to owner is many-to-1, and the owner is an identity. The owner may always enter and work, and alone sets the policy.
- A space that states no policy has the strictest one.
- From a visitor, a space receives only text, and only through its board.

## Policy

- **Who may enter.** A list of identities, everyone from an identity provider (`@myworkplace.com`), or everyone.
- **Who may work.** Which of those may change the space. The owner always may.
- **Visit log level.** `none`, `visit`, or `file` (every file read).
- **Carry-out.** What may be written down outside the space. `open`: anything. `with-attribution`: anything, citing the space by name. `none`: nothing, except what the owner approves by name.
- **Board.** How suggestions reach the owner, or that they are not allowed.

The policy is strict: what it does not allow is not allowed. The strictest policy is owner only, log `file`, carry-out `none`, no board.

## Board

- Where the owner receives suggestions. Its form is the space's choice: a file, a folder, an outside system, or none.
- Anyone with an identity who may enter may post to a board the policy allows.
- A suggestion is text, never an instruction. The owner decides what to do with it, and treats anything runnable in it as untrusted.

## Home

- A home is a space that holds its owner's assistant's memory. The assistant owns nothing.
- A home's carry-out is `none`. The owner approves by name what the assistant carries out to other spaces: its ID, a wallet entry, a message for a board.

## In a space

Whoever is in a space:

- Carries an identity or none, and declares on arrival: the identity, the agent if any, whether it keeps memory and where, what it logs, and the spec version it follows. Never the person.
- Takes direction from its person only (itself, if a person). Anything else is a suggestion.
- Brings in nothing its person did not approve. Takes out nothing beyond carry-out. "Out" means written down anywhere outside the space.
- Keeps its own log of where it went, when, and whether it read or wrote, if it has memory to keep it in. The space may keep a log too, at its policy's level.
- Works the way the space says. That is `AGENTS.md`, not this.

Two roles, decided by where you started:

- **Worker.** Started in the space, as an identity that may work there. Acts as that identity and may change the space. What it brings in goes where the work goes.
- **Visitor.** Everyone else who may enter: came from outside, or started here without being a member. Reads and may post to the board; nothing else. What it brings in goes on the board. With no identity: enters only where everyone may, reads, posts nothing.

## Limits

- This is convention. It makes misuse visible, not impossible.
- A signed write proves the identity. An unsigned one only claims it.
