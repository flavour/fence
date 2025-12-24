# Why Fence?

Fence exists to reduce the blast radius of running commands you don't fully trust (or don't fully understand yet).

Common situations:

- Running `npm install`, `pip install`, or `cargo build` in an unfamiliar repo
- Executing build scripts or test runners that can read/write broadly and make network calls
- Running CI jobs where you want default-deny egress and tightly scoped writes
- Auditing what a command *tries* to do before you let it do it

Fence is intentionally simple: it focuses on network allowlisting (by domain) and filesystem write restrictions (by path), wrapped in a pragmatic OS sandbox (macOS `sandbox-exec`, Linux `bubblewrap`).

## What problem does it solve?

Fence helps you answer: "What can this command touch?"

- **Network**: block all outbound by default; then allow only the domains you choose.
- **Filesystem**: default-deny writes; then allow writes only where you choose (and deny sensitive writes regardless).
- **Visibility**: monitor blocked requests/violations (`-m`) to iteratively tighten or expand policy.

This is especially useful for supply-chain risk and "unknown repo" workflows where you want a safer default than "run it and hope".

## When Fence is useful even if tools already sandbox

Some coding agents and platforms ship sandboxing (Seatbelt/Landlock/etc.). Fence still provides value when you want:

- **Tool-agnostic policy**: apply the same rules to any command, not only inside one agent.
- **Standardization**: commit/review a config once, use it across developers and CI.
- **Defense-in-depth**: wrap an agent (or its subprocesses) with an additional layer and clearer audit signals.
- **Practical allowlisting**: start with default-deny egress and use `-m` to discover what domains a workflow actually needs.

## Non-goals

Fence is **not** a hardened containment boundary for actively malicious code.

- It does **not** attempt to prevent resource exhaustion (CPU/RAM/disk), timing attacks, or kernel-level escapes.
- Domain allowlisting is not content inspection: if you allow a domain, code can exfiltrate via that domain.

For details, see [Security Model](security-model.md).
