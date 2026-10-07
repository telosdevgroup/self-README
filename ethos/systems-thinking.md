# Systems Thinking: From Balance Sheets to Linux Kernels

> An inventory warehouse, an accounting ledger, and an operating system have more in common than most people think.
> In every case, you're tracking state, managing bottlenecks, and making sure the numbers balance.

---

## The Core Insight: Everything Balances

My background didn't start in a traditional computer science curriculum. It started in **Production & Operations Management, NetSuite ERP systems, and double-entry accounting**.

In software, it's easy to get distracted by shiny frameworks or trendy UI libraries. An operational and accounting background gives you a much simpler, more grounded filter:

- **In Accounting**: Debits must equal credits. If the ledger is out of balance, something is wrong.
- **In Operations**: What comes in minus what goes out equals what's sitting on the shelf. If your physical count doesn't match your system count, your pipeline is leaking.
- **In Linux & Hardware**: Memory and heat have hard limits. If a process hogs RAM or pegs CPU clocks, the machine overheats or triggers the out-of-memory killer.

Every healthy system has baseline rules it cannot violate without breaking. If you can't describe those core rules in simple terms, you don't understand the system yet.

---

## Rules That Keep Systems Stable

When I build tools—like [ChangeState](../projects/changestate.md) to manage CPU temps, or [avabatt](../projects/avabatt.md) to protect ThinkPad battery health—the focus isn't on clever code. It's on keeping the machine in a safe, predictable state:

1. **Rely on Ground Truth**: Avoid guessing or relying on ephemeral memory that can fall out of sync. Read state straight from the actual source—whether that's a database record, a log, or a Linux virtual filesystem like `sysfs`.
2. **Smooth, Predictable Responses**: When a system drifts out of its ideal range (like a CPU running hot or inventory running low), adjustments should be calm and steady, not erratic swings that cause oscillation.
3. **Repeatable Actions (Idempotence)**: Applying the same setting or configuration twice shouldn't break anything. If the machine is already in the right state, leave it alone.

---

## The Operations Advantage in Software

Writing syntax is just the translation layer. The harder part is figuring out what actually matters in the real world:

- **Where is the real bottleneck?** Speeding up a fast step doesn't help if the whole line is waiting on a slow step downstream (Eliyahu Goldratt's Theory of Constraints).
- **Where can humans make mistakes?** Good software design makes it hard or impossible for someone to enter invalid data in the first place.
- **What does it cost to run?** Can this run quietly on modest local hardware without a monthly cloud bill?
- **Does it require babysitting?** If a network hiccups or a service reboots, does the program recover cleanly on its own, or does it demand human intervention at 3 AM?

Because my foundation is rooted in operations and business workflows, I don't build software just to write code. I build it to solve real problems simply, cleanly, and reliably.
