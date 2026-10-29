# Platform incident session: setup repository

The incident packet is in `incident/`: start with `incident/README.md` and
`incident/session-briefing.pdf`. Prepare however you like, including with AI
tools.

The rest is a placeholder for the code: the folder layout of the platform,
and every service only returns "ok". Use it to check that your editor and
your AI tool can open and read the repository.

When the session starts we replace it with the real source code. When we
tell you, update your copy with:

    git fetch --tags origin
    git reset --hard origin/main

Then `git tag` lists `release-2026-10-13` and `release-2026-10-20`. You
cannot push to this repository: during the session, post anything you want
done in the call chat for Tomas, our platform engineer (logs, runtime state,
a command, a change). Results arrive under `requested/` and applied changes
as commits on `main`; run `git pull` when we tell you they are there.

Please clone it, but do not fork it: a fork is a public copy under your
account that stays after we delete this repository right after the session.
