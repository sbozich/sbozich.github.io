---
title: "Flatpak said 'up to date' — but the runtime was EOL"
date: 2026-04-25 10:00:00 +0200
categories: [linux, flatpak]
tags: [flatpak, security, runtimes, packaging]
---

At first glance, Flatpak feels predictable: you run `flatpak update`, it reports “Nothing to do”, and you assume the system is in a good state. That assumption turns out to be incomplete.

In my case, the update finished cleanly, but with a warning quietly appended at the end:

```text
runtime org.gnome.Platform 48 is end-of-life
```

Nothing was broken. Applications still launched, behaved normally, and even reported themselves as up to date. From a user perspective, there was no visible degradation. But underneath, they were still running on a runtime that no longer receives security updates.

That disconnect is where the real issue lies: Flatpak can give you a functionally healthy system that is structurally outdated.

## The misleading “up to date” state

Flatpak separates applications from the runtimes they are built on. That design solves classic Linux problems like dependency conflicts and version mismatches. In return, however, it introduces a different kind of risk that is less visible but just as relevant.

An application can be fully updated while still being pinned to an outdated base.

In practice, the state looked like this:

- the system already had newer runtimes installed (GNOME 49 was present and usable)
- the application itself had a recent build
- yet it remained bound to GNOME 48, which had already reached end-of-life

Nothing in the standard update flow forces a migration. Flatpak will happily keep both runtimes installed and continue using the older one as long as the application depends on it.

The missing link is the maintainer: the application must be rebuilt against the newer runtime. Until that happens, you stay where you are.

## Why this matters more than it seems

For a simple GUI tool, this might be a minor detail. For anything that interacts with networks, credentials, or remote systems, it becomes more significant.

In the observed case, a VPN client — Surfshark — had been updated recently via Flatpak, but still depended on an EOL runtime. From a purely technical standpoint, that means the application layer is current, but the underlying platform is not. Any vulnerabilities in that runtime remain part of your attack surface.

What makes this subtle is that nothing in Flatpak makes the situation urgent. There is no failure, no forced upgrade, and no degraded functionality. The system continues to work, which makes it easy to ignore.

This is a different class of issue: not a bug, but a lifecycle mismatch.

## How to actually check

Detecting this is straightforward, but it requires stepping outside the default workflow.

Start with:

```bash
flatpak info <app-id>
```

What matters is the runtime line. If it points to something like:

```text
org.gnome.Platform/x86_64/48
```

you are tied to that version regardless of what else is installed.

Then check what runtimes exist locally:

```bash
flatpak list --runtime
```

This shows what is available on your system, not what is actively used.

Finally, map which applications are still using the old runtime:

```bash
flatpak list --app --columns=application,runtime | grep 48
```

At this point, you have a clear picture: which apps are effectively running on unsupported components.

## What to do about it

If the maintainer has already migrated the application, a simple update will pull in the new runtime and move you forward. In that case, verification is enough:

```bash
flatpak update <app-id>
flatpak info <app-id>
```

If no migration exists, the decision becomes contextual.

For low-risk applications, waiting is reasonable. The impact is limited and often temporary.

For anything that handles network traffic, credentials, or remote access, relying on an outdated runtime is a questionable trade-off. In those cases, switching to a native package or a protocol-level setup (for example, WireGuard instead of a GUI VPN client) gives you more control over the update surface.

This is less about Flatpak being “bad” and more about choosing the right abstraction level for the job.

Once everything is moved off the old runtime, cleanup is straightforward:

```bash
flatpak uninstall org.gnome.Platform//48
flatpak uninstall --unused
```

## The structural takeaway

Flatpak replaces dependency conflicts with a different dependency: the maintainer.

Your effective update chain looks like this:

```text
application maintainer → runtime → distribution channel
```

If the application is not rebuilt in time, you inherit that delay, regardless of how up to date the rest of your system is.

This is not unique to Flatpak, but Flatpak makes it more visible because runtimes are explicit and shared.

## Closing thought

The key point is simple: “no updates available” is not a complete signal.

Flatpak works well, and for many use cases it is a net improvement. But it requires a slightly different mental model. Runtimes are not an implementation detail — they are part of your system’s security posture.

A quick audit once in a while is enough to catch this class of issues before it becomes relevant.
