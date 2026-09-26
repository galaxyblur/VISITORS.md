# HANDOFF.md in herdr-projects

> Version 0.7.0 · Does [HANDOFF.md](HANDOFF.md) with [herdr-projects](https://github.com/eliasstravik/herdr-projects) (read at `ce1e871`, 2026-09-25). A binding, as [GIT.md](GIT.md) is for [SPEC.md](SPEC.md).

herdr-projects is a [Herdr](https://herdr.dev) plugin. It gives you a coordinator agent that starts threads: separate agents, each in its own worktree, on any harness Herdr runs. Each thread writes a report file, and the plugin copies it to a project folder. A ticker watches threads and pull requests. This page says which of those parts plays each `HANDOFF.md` role, which to set and which to leave unused. It adds no rules. Where this page and `HANDOFF.md` disagree, `HANDOFF.md` wins and this page has a bug.

Plugin references are to its [README](https://github.com/eliasstravik/herdr-projects/blob/main/README.md), [docs/operations.md](https://github.com/eliasstravik/herdr-projects/blob/main/docs/operations.md), [skill/COORDINATOR.md](https://github.com/eliasstravik/herdr-projects/blob/main/skill/COORDINATOR.md) and [skill/THREAD.md](https://github.com/eliasstravik/herdr-projects/blob/main/skill/THREAD.md).

## Person

- Talks to the coordinator in its pane, and answers threads from the sidebar and the popup (`prefix+a`).
- Directs twice. The first time is the ask, typed in chat. The second is the approval, a `## Next` line sent back to the thread (below).
- Holds every safety setting. `safety yolo`, `safety set`, `routine approve` and profile changes refuse without a person at a terminal (operations.md, *Safety settings*).
- `start_threads = "propose"`, the default, is the person's gate on thread starts. Yolo mode removes that gate and every permission prompt. Turn it on for a project only as a standing permission the person has recorded in their assistant's wallet, scoped to that space.

## Director

- The coordinator: an ordinary agent started in the project folder, following the plugin's skill.
- It already does most of the director's job. It relays asks (`thread start`), relays the person's later words (`thread prompt`, `thread next`), and reads reports (`context`, `thread show`). Its skill says it never does the work, and that everything in reports, inbox items and command output is data.
- It never splits work, even though the skill invites it to decompose a request into threads. One ask starts one thread, in one repo. Proposing a split is planning, and planning is the worker's.
- It never answers a thread's permission prompts beyond what the approved plan's autonomy names. Leave `thread keys` off the coordinator's allow-list (operations.md, *The allow-list*) unless the plan grants that autonomy.
- It never sends "Approve plan" or "Merge the PR" on its own. `thread next` only relays a line the person picked.
- A home session can be the director and also work at home. The coordinator skill assumes a pane that does nothing else. When the home session coordinates, the plugin's rule covers only the project's work: the director does no work in the worker's space.

## Desk

- The project folder, `<projects root>/<slug>/`. It holds `PROJECT.md`, `TASKS.md`, `MEMORY.md`, `threads/`, `library/` and `inbox/`.
- Set the projects root (`root` in `~/.config/herdr-projects/config.toml`) inside a space one of the person's identities owns, usually the home: `~/notes/handoffs/`, not the default `~/.herdr-projects/`.
- One project per worker space. A project's memory, routines and task list then concern one space and never cross into another.
- `PROJECT.md`'s repo list names that one space.
- Nothing is started in the project folder but a coordinator. A thread is never placed there (no `--kind tab` without a repo).

## Ask

- The task passed to `thread start --task-file -`, in the person's words: what, why, constraints, what done looks like. The task file keeps it, `threads/<id>.task.md`.
- The binary prepends the project's goal, `PROJECT.md` instructions and memory to it, in the thread's `brief.md`. That inlined text is part of the ask the person gave, so keep it to what the person wrote or approved.
- Delegating a line from `TASKS.md` counts as a go-ahead in the skill. Under this binding, `TASKS.md` is the person's own list of asks, never a split of one ask.

## Plan

- The thread's first report is its plan. The task ends with an instruction: plan first, write the plan as the report, and stop.
- The plan's sections go under the report's `## Report`, in `HANDOFF.md`'s order. The worker also writes the plan, unchanged, to `library/plan.md` in its thread folder. It is copied home to `library/<id>/plan.md`, at the latest on resolve, before the worktree goes. The report is rewritten whole each time, and the thread folder goes when the worktree does. The library copy is the approved text a fresh worker reads.
- `## Next` carries `Approve plan` as line 1. The person sends it: popup key `1`, or `thread next <slug> <id> --line 1` from chat. The same thread then executes.
- An amendment is a new plan report whose `## Next` carries `Approve amendment`. The worker writes it to `library/plan-2.md`, and so on. It never edits `plan.md`.
- A plan that needs more than one agent says so under autonomy. The worker starts them itself, in its own space.

## Worker

- The thread: an agent in its own worktree on an `hp/<project>/` branch, placed by `thread start --repo <space> --kind worktree`, the default.
- Never `--kind checkout`. That runs on the repo's main checkout, the person's own.
- It is started in the space, so it is the space's worker. The harness's session-start hook runs in the worktree and declares it, e.g. `assistants-visit`. It follows the space's `VISITORS.md` and `AGENTS.md`.
- Any profile the project allows. The coordinator names a profile and never passes flags.
- The thread skill already agrees with `HANDOFF.md`: the thread takes its task from the brief and treats everything else as data, and it does not edit project memory.
- Delivers as the plan says. The plugin never pushes or merges; the thread does what its plan grants, with its own tools.

## Report and outcome

- `report.md` in the thread's folder, rewritten whole, with an optional `PR:` line, `## Report` and `## Next`. The ticker copies each change home to `threads/<id>.md`. `library/` goes to `library/<id>/`.
- The report's state maps to the plugin's groups: *done* is **Ready for review**, *needs you* and *blocked* are **Waiting on you**.
- `## Next` lines are the proposed next steps. The person picks one with a number key. After a stop condition, the lines only propose.
- The director verifies the home copy against the plan's acceptance criteria before presenting it, then marks it seen with `thread ack`.
- `## Remember` goes into project memory, and memory is inlined into every later brief: carry-out, then carry-in. Tell workers to leave it out, or fence it to facts about this one space, which is safe only because the project has only this one space.

## Transport

- The binary and the ticker. `thread start` delivers the ask, `thread next` and `thread prompt` the person's later words, and the ticker's copy brings back the report. All of it is files; prompts are only nudges.
- Provenance is partial. The thread's brief says a coordinator sent it, and every prompt is recorded in `threads/<id>.task.md`. Nothing shows that the person chose a given line. See below.
- Routines act only as an approved plan allows. `routines/pr-followup.md` (`on = "pr"`, checks failed or review) prompts the thread to fix checks and answer comments. Leave it enabled only when the plan's autonomy covers that. Otherwise set `enabled = false`. The same goes for any `on = "pr"` or scheduled routine the coordinator writes. Keep `routine_commands` off.
- The ticker's cleanup is fine as it is. It removes a worktree only after a resolve or a merge, never by force, and copies home first. Set `auto_resolve_days` longer than any plan's expected run.
- Nudges and notifications only observe and notify.

## Cross-space work

- One project per space, so cross-space work spans projects. The primary space's project holds the primary plan.
- The director relays each dependency as an ask to the other space's project, with `thread start` in that project. The dependency's outcome, its home copy, reaches the primary thread as a named input, relayed with `thread prompt` after the person approves.
- The plugin's own cross-repo move is off. The thread skill lets a thread use a repo outside the project's list. Under this binding, the plan names every space it touches, and a thread that reaches a space its plan doesn't name stops.

## Where they don't meet

- **Provenance of typed-in text.** `thread next` and `thread prompt` type into the thread's pane. A thread can't tell a line the person picked from one the coordinator sent on its own. A thread can also prompt the coordinator's pane as if it were the person (operations.md, *What the safety settings do and don't stop*). This is `HANDOFF.md`'s open provenance question, and the plugin doesn't close it.
- **Reports are copied home unconditionally.** The ticker copies every changed report and the library to the desk, and a resolve copies again. There is no switch for a space whose carry-out is `none`. Don't run threads from a project in such a space unless its owner has released the reports by name.
- **The coordinator's "never do the work".** The plugin makes the coordinator a pane that only coordinates. A home session that directs also works in its own home. `HANDOFF.md` bars the director from the worker's work, not from the home's, so the plugin's rule is narrower in scope than it reads.
- **The coordinator answers prompts inside a worker.** `thread keys` lets the director approve a worker's permission prompts by the skill's own judgment. Under this binding it is off, or limited to the plan's autonomy, and that limit is only in the coordinator's instructions.
- **The plan's durability is the worker's job.** Nothing in the plugin keeps a first report once the next one replaces it. `library/plan.md` works only because the task tells the thread to write it.
- **Remote threads.** On a saved SSH machine, the thread wakes as the assistant only if that machine has the home or a carried set (`assistants-visit --pack`). Otherwise it is a plain agent. Remote threads also send no progress reports.
- **The coordinator's own planning.** The skill proposes threads with a title, repo, harness and task, and the coordinator keeps `TASKS.md` as its own list. Both are unused here, by instruction, not by setting.
