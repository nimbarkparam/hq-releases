# HQ

HQ is the system Param uses to run a small product company with a fleet of Claude Code agents on one PC: it hands out work, tracks it, and puts code-enforced stops between the agents and pushes, deploys, secrets and billing. This page explains how it works and what it has caught; the source is private.

## What HQ is

One main agent and ten Claude Code seats in two Claude organizations, five per organization, one per role: Product and Research, Admin and Marketing, Support and QA, Engineering, and Sales and Growth. Any seat may work on the three products: Voxmelt, Seqtrix and Personifold. Main, a Claude desktop app session senior to every seat, dispatches briefs and keeps the account, secret and deploy steps.

How work flows:

1. Param types a task into the HQ Console, a local web page, or main sends a brief; a planner splits big jobs into parts.
2. Each part runs as a headless Claude Code session on its own git branch and worktree; a critic checks it against its acceptance criteria.
3. A question or a blocked action stops the part in a Needs you inbox; Param approves, denies or answers, and the same session resumes.
4. A release steward run, normally a Support and QA seat, merges finished branches, runs the tests and pushes.
5. A dashboard shows token use per seat, work logs, messages, merges and flags.

## Why it needs a control layer

Every seat has a shell and git, and some runs get a browser. One wrong step could push unreviewed code to a branch that deploys a live site, post in the company's name, delete a folder linked to the seats' logins, or switch on a paid plan. Role briefs forbid this, but a brief is advice; HQ puts hard stops in code that runs before each tool call.

## The controls

- **Guard hook.** A pre-tool hook judges every tool call of an HQ run and logs each refusal. A hook that crashes or times out blocks the call; an error inside its rule engine allows the call and is logged. Workers bypass Claude Code's own permission checks only once HQ verifies the hook loads for their seat.
- **Approval before anything outward.** Param or main carries out sends, posts and payments. The guard denies pushes, tags, releases and pull request merges to every run but the steward, denies hosting and database deploy and secret commands to all seats, and keeps browser tools off unless a mode flag allows them. A plain web request that posts or signs up has no rule and rests on the briefs.
- **No billing pages.** A shell, web fetch or browser call naming a billing, usage, plan or payment page of Claude, Anthropic or another provider stops and pauses the seat, with a high flag, until main lifts it; this check runs first and survives a guard failure.
- **Free plans only.** The same stop covers listed upgrade commands; beyond those lists, free plans only is written policy.
- **The steward merges and pushes.** Without a mode flag, other runs may not write in their project's main checkout, merges included. Shipping HQ's own code merges in a throwaway worktree first, stops on a conflict or a red test suite, and refuses any seat run but that task's steward. The role comes from the run's environment, so this guards against mistakes; it is not a security boundary.
- **Every commit names its seat.** Commits end with an Agent-Seat trailer; HQ's git scan turns a branch with unmarked commits into a high dashboard item: the steward must not merge it as is. It is a convention HQ checks, not a signature.
- **Secrets and writes fenced.** The guard denies reads of login folders, tokens, env files and keys, any input holding a live payment key or an Anthropic API key, writes outside the run's own folders (a part with no worktree also gets HQ's folder and other worktrees), risky recursive deletes, and kills beyond test servers and the part's own processes.
- **RAM gate.** Before each run, HQ checks free memory and commit headroom and starts nothing below the floor, with one browser-heavy part at a time; under sustained pressure a run is stopped and re-queued; a run's process tree dies with it.
- **Patrol.** A zero-token background script wakes main when a task ends, a part asks a question, the guard pauses a part or seat, seats idle while parts wait, or the server stops answering. It works only while main keeps it armed.

## Real catches

The guard refuses calls every day, mostly writes outside a run's own folders and kills of processes the run does not own. A few moments that shaped HQ:

- **Restarts mid-run.** System updates restarted the PC during a night run, and the fleet sat idle until HQ was restarted by hand.
- **A frozen machine.** Too many parallel runs, child processes that outlived their parts and several browser-heavy jobs at once froze the PC. That produced the RAM gate, which now holds new runs until memory frees up.
- **A push from the wrong role.** A run that was not the steward tried to push straight to a product's main branch. The guard denied it and pointed the run to the steward.
- **The wrong branch.** A steward tried to ship another task's branch. The ship command refused, and the seat raised a flag instead of working around it.

## What running it taught me (Param)

I have followed this field closely since ChatGPT arrived in 2022, and building with it has changed what I think the core skill is. In an earlier project I built a browser extension that analyzed YouTube videos from their transcripts in real time, driving Claude through the API inside VS Code and Windsurf, with a lot of manual back and forth. A newer model now gets most of the way there from one plain prompt. Hand-built work keeps getting absorbed by the next model, the way hand-written assembly was absorbed by compilers. What lasts is genuinely new, carefully researched work, and the judgment about what to delegate.

HQ is built around that judgment. Every new model makes the seats more capable, and every time I decide again what can move to them. This week merges, releases and restarts moved; a product going live, anything posted in public and any spending stayed with me. When their queue is empty, the agents now pick their own work, including improvements to HQ itself, the system that sets their limits. It is a small, low-stakes version of the loop I care most about.

That loop is the question I most want to work on at frontier scale: how far AI should be allowed to improve itself, building more capable models with less human involvement each round. Which work should a system do and improve on its own, and which should stay a human decision, made with AI but not by it? And how do we hold that line once the system is more capable than the people who drew it?

## Updates

- 2026-10-17: public page opened.

Written with Claude from my notes and HQ's own docs.

HQ runs on Claude Code. Not affiliated with or endorsed by Anthropic.
