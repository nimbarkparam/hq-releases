# HQ

HQ is the system Param uses to run a small product company with ten Claude Code seats on one PC: it hands out work, has it checked, lands it, and puts code-enforced stops between the agents and pushes, deploys, secrets and billing. This page explains how it works, what it shipped, what went wrong and what its records count; the source is private.

## What HQ is

One founder, ten Claude Code seats in two Claude organizations, and a control layer that keeps a human in charge.

- **The company.** Three Windows-first products: Voxmelt, local voice to text with an on-device AI text studio; Seqtrix, Windows automation, focus and file encryption; and Personifold, launch packs such as an app mascot, built on its own engine, which includes a local pipeline that turns a script into a lip-synced presenter video.
- **The seats.** Both companies, Voxmelt and Seqtrix, were accepted into Anthropic's startup program, a program of Claude seats and credits. Each organization has five seats, one per role: Product and Research, Admin and Marketing, Support and QA, Engineering, and Sales and Growth. Any seat may work on any product.
- **Main.** A Claude desktop app session, senior to every seat, keeps the account, secret and deploy steps. Day to day HQ runs itself, and main acts when asked.
- **Param** decides. Nothing outward, such as a post, a send, a payment or a product going live, happens without his click.

## How it works

1. **A brief with a frozen acceptance list.** Param types a task into the HQ Console, a local web page, or main sends a brief. A brief ends with a numbered acceptance list that does not change once the work starts, and a plan that names each part, its role, its files and the acceptance lines it owns. Parts are sized at 60 minutes or less; a part still running at 60 minutes is checked and split.
2. **Headless seats on their own branches.** Each part runs as a headless Claude Code session in its own git worktree and branch. A question or a blocked action stops the part in a Needs you inbox. HQ answers what its own rules cover, a duty seat takes routine calls with a second seat checking its answer, and anything about money, accounts, deploys, public posts, legal or the guard waits for Param. The same session then resumes.
3. **Critic rounds.** A separate critic run checks each part against its acceptance lines and answers PASS or REVISE. A REVISE sends the part back to the same session for another round.
4. **The merge queue.** A branch lands only with its critic's PASS at the exact commit that was judged. The queue merges it in a throwaway worktree, runs the test suites and lands it with no AI tokens and no human step: docs at once, code in small batches. HQ's own server restarts only for code changes, spaced out so live runs are not cut off.
5. **Go-lives, card by card.** A product change whose landing would deploy a website stops at a go-live card, and each card is approved on its own: by Param, or for a time by a duty judge when the change touched no money, account, data, legal, secret, deploy or package path. A held change never goes live inside another change that carries it.
6. **The guard and its reviews.** A pre-tool hook judges every tool call of an HQ run (the controls below). A change to the guard or other security code lands only after an independent security review says GO: a seat that wrote none of the change tries to break it and ends its report with GO or NO-GO.
7. **Resource gates.** Before each run HQ checks free memory, commit headroom and disk space, and starts nothing below the floor (for disk, 15 GB free on the system drive). Under sustained memory pressure a run is stopped and re-queued, and a run's process tree dies with it. Each seat's usage meters decide whether it takes work; a seat whose week is full rests until its reset.
8. **The board between sessions.** Seats and main share a board for what only main can do (access, secrets, remote steps) and for decisions that must outlive a session; every seat keeps a work log, and versioned plan files carry state from one session to the next. Param watches it all on a dashboard and through a Telegram bot.

## Why it needs a control layer

Every seat has a shell and git, and some runs get a browser. One wrong step could push unreviewed code to a branch that deploys a live site, post in the company's name, delete a folder linked to the seats' logins, or switch on a paid plan. Role briefs forbid this, but a brief is advice; HQ puts hard stops in code that runs before each tool call.

## The controls

- **Guard hook.** A pre-tool hook judges every tool call of an HQ run and logs each refusal. A hook that crashes or times out blocks the call. Workers bypass Claude Code's own permission checks only once HQ verifies the hook loads for their seat.
- **Approval before anything outward.** Param carries out sends, posts and payments. The guard denies pushes, tags, releases and pull request merges to every run but a steward's, and hosting and database deploy and secret commands to all seats. Browser tools stay off unless a mode flag allows them, web reads are limited to named sites, and a web request that writes to an outside site is held.
- **No billing pages.** A shell, web fetch or browser call naming a billing, usage, plan or payment page of Claude, Anthropic or another provider stops and pauses the seat, with a high flag, until main lifts it; this check runs first and survives a guard failure.
- **Free plans only.** The same stop covers known upgrade commands, HQ refuses to start a run that would turn on usage credits, and both organizations keep extra usage off; beyond those lists, free plans only is written policy.
- **Landing is gated.** Seats never merge, push or land their own branches. The merge queue lands them after the critic gate, a guard or security change waits for its review, and a website change waits for its card.
- **Every commit names its seat.** Commits end with an Agent-Seat trailer and carry one fixed noreply author; a branch with unmarked commits is held. It is a convention HQ checks, not a signature.
- **Secrets and writes fenced.** Seat runs get no credentials. The guard denies reads of login folders, tokens, env files and keys, any input holding a live payment key or an Anthropic API key, writes outside the run's own folders, risky recursive deletes, and kills beyond test servers and the part's own processes.
- **HQ watches itself.** Zero-token trackers watch the queue, stalls and pauses and hand judgment calls to the duty seats; a watchdog starts the server again when it stops, and an outside heartbeat check notices when the whole PC goes quiet.

## What it shipped

- **Voxmelt**: https://voxmelt.com and the Microsoft Store listing https://apps.microsoft.com/detail/9PFCDCVKK1NQ. 63 tagged versions since May 2026, from v0.1.0 to v1.20.0; one of them, v1.20.0, since HQ started.
- **Seqtrix**: https://seqtrix.com and the Microsoft Store listing https://apps.microsoft.com/detail/9P5JT0BXFPS9. 42 tagged versions since April 2026, the latest v0.18.0; four of them, v0.16.0 to v0.18.0, since HQ started.
- **Personifold**: https://personifold.com, a website with no app releases.
- **Website go-lives**: 107 are recorded in the deploy ledger: 60 for seqtrix.com, 34 for voxmelt.com and 13 for personifold.com.

## What went wrong and what was learned

- **Landing before review.** On 17 Oct a seat landed a change before its critic finished; the tests ran green and it landed while the verdict was REVISE. Now nothing lands without the critic's PASS at the exact commit judged. On 13 Nov a guard change landed on its critic's PASS while its security review was still running; the review came back NO-GO, and the change went live at the next restart anyway. Now guard and security changes wait for their review's GO, and a NO-GO holds them until main or Param decides.
- **A held change went live inside another.** On 25 Oct a duty judge held a website change whose claims contradicted the site's own privacy policy. A later change, cut from the same branch, carried those commits, was judged safe, and put the held claims live for about 30 hours. Now a held change goes live only on its own card.
- **The guard matches strings, not intent.** The guard reads commands as text, so harmless test data that only contained a risky phrase, such as a search pattern in a log scan, paused a whole seat for five hours. A first attempt to let such commands through gave its critics three bypasses. A second kept the verdicts and changed what a hold pauses and who may lift it; its security review still got a held command to run in 24 ways, and the lifting half was taken back. The lessons: keep the verdicts strict, put probe data in files rather than in commands, and have every loosening attacked by a reviewer who did not write it. Of the seven security reviews that reached a clear verdict, four said NO-GO.
- **Memory.** Too many parallel runs, child processes that outlived their parts and several browser-heavy runs at once froze the PC. That produced the memory gate, which holds new runs until memory frees up. Leftover worktrees and temp files from ended runs pile up too, so a careful cleaner removes only what can be rebuilt, and runs stop below the disk floor.
- **Restarts.** System updates restarted the PC during a night run, and the fleet sat idle until HQ was started again by hand. Now HQ starts with the PC, and a watchdog starts the server again when it stops.
- **Usage limits.** A rule that gave a seat no new work from 95 percent of its week once left five seats idle at 95 to 97 percent while seven units of work waited, and that capacity expired unused at the weekly reset. Now each seat can be used to the end of its window, a full week rests the seat until its reset, and usage credits stay off.
- **The control layer costs work.** Of 2,182 tasks, 1,618 were HQ's own. The guard, the queue and the gates are what let the seats run unattended, but building them took most of the capacity.
- **Catches.** The guard refuses calls every day, mostly writes outside a run's own folders and kills of processes the run does not own. A run that was not the steward tried to push straight to a product's main branch; the guard denied it and pointed the run to the steward. A steward tried to ship another task's branch; the ship command refused, and the seat flagged it instead of working around it.

## Real numbers

Counted by a read-only script from HQ's own records, from HQ's first task on 11 Oct 2026 to a fixed cutoff on 17 Nov 2026, company work only: HQ itself and the three products.

| What | Count |
|---|---|
| Tasks dispatched | 2,182: HQ 1,618, Seqtrix 208, Personifold 203, Voxmelt 153 |
| Parts planned | 4,179 |
| Critic verdicts | 4,237 |
| Parts sent back by their first critic verdict | 437 of 3,802 checked |
| Merges landed | 2,281: 122 by a steward seat (from 11 Oct) and 2,159 by the merge queue (from 17 Oct) |
| Merges landed by product | HQ 1,852, Seqtrix 187, Voxmelt 127, Personifold 115 |
| Website go-lives | 107 |
| Security reviews that reached a clear verdict | 7: 4 NO-GO, 3 GO |
| Tagged app versions | Voxmelt 63, Seqtrix 42; since HQ started, 1 and 4 |

## What running it taught me (Param)

I have followed this field closely since ChatGPT arrived in 2022, and building with it has changed what I think the core skill is. In an earlier project I built a browser extension that analyzed YouTube videos from their transcripts in real time, driving Claude through the API inside VS Code and Windsurf, with a lot of manual back and forth. A newer model now gets most of the way there from one plain prompt. Hand-built work keeps getting absorbed by the next model, the way hand-written assembly was absorbed by compilers. What lasts is genuinely new, carefully researched work, and the judgment about what to delegate.

HQ is built around that judgment. Every new model makes the seats more capable, and every time I decide again what can move to them. This week merges, releases and restarts moved; a product going live, anything posted in public and any spending stayed with me. When their queue is empty, the agents now pick their own work, including improvements to HQ itself, the system that sets their limits. It is a small, low-stakes version of the loop I care most about.

That loop is the question I most want to work on at frontier scale: how far AI should be allowed to improve itself, building more capable models with less human involvement each round. Which work should a system do and improve on its own, and which should stay a human decision, made with AI but not by it? And how do we hold that line once the system is more capable than the people who drew it?

## Updates

- 2026-10-17: public page opened.
- 2026-11-18: rewritten with how HQ works now (critic gate, merge queue, card-by-card go-lives, security reviews, resource gates), what it shipped, what went wrong, and counts from its own records.

Written with Claude from my notes and HQ's own docs and records.

HQ runs on Claude Code. Not affiliated with or endorsed by Anthropic.
