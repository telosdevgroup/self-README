# 🛠️ Skills

I don't keep a laundry list of buzzwords. Here is what I actually use day to day, why I chose it, and where you can see it working.

---

## 🤖 Directing AI

I use AI all day to write software, but I treat it like a fast junior team that needs clear boundaries.

- **Antigravity**: my primary workbench for directing agents. I run it with cross-project visibility enabled so agents have full context on everything I'm building.
- **Models**:
  - **Gemini Flash (Low)**: my workhorse 99% of the time. Fast, cheap on tokens, and sharp enough for code and day-to-day edits.
  - **Gemini Pro / Ultra**: stepped up when a problem needs deep reasoning or heavy refactoring.
  - **Claude Sonnet (Low)**: what I bring in for documentation and prose. It has the best natural, jargon-free voice.
- **Workflow habits**:
  - **Short files (<500 lines)**: keeps agent context small, fast, and dirt-cheap.
  - **Fresh agents for small tasks**: instead of dragging one agent through a messy 50-turn chat, I spin up a new agent for a specific job and close it.
  - **[AGENTS.md](../AGENTS.md)**: plain ground rules in every project so agents know what not to touch and how to write.

---

## 💻 Linux & Hardware

I run all my own hardware at home instead of paying cloud bills.

- **OS**: Linux Mint across three different laptops.
- **Hardware**: a mix of everyday laptops and a Raspberry Pi.
- **Control**: simple Bash and Python scripts talking straight to Linux kernel interfaces (`sysfs`, systemd) to manage thermals, fans, and batteries ([ChangeState](../projects/changestate.md), [avabatt](../projects/avabatt.md)).

---

## 🌐 Building Web Apps & APIs

When I build something for the web, I keep the stack simple, lightweight, and fast.

- **Python & FastAPI**: clean backend APIs with minimal fuss.
- **MongoDB**: flexible document storage.
- **In-memory cache & NVMe**: keeping active data right in RAM and fast local storage so pages load in milliseconds without expensive infrastructure ([AvaScry](../projects/avascry.md)).

---

## 📊 Business & Operations

Before directing AI to build software, I spent years in operations and inventory systems.

- **NetSuite & ERP**: business logic, general ledger rules, and supply chain tracking ([Background](erp-and-operations.md)).
- **Integrations**: gluing systems together so data flows automatically between inventory, orders, and accounting without people retyping things into spreadsheets.
