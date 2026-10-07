# Operations, ERP, & Systems Analysis

> Before I spent my days directing AI agents or tuning Linux daemons, I ran business systems and data pipelines for a high-volume hardware refurbishing and custom server manufacturing business.

---

## The Academic Foundation

- **Degree**: Production & Operations Management
- **Minor**: Accounting
- **Core Focus**: Theory of Constraints (bottlenecks), double-entry accounting integrity, material requirements planning (MRP), and inventory cycle dynamics.

I didn't start in a traditional computer science lab. I started on the operational floor, learning how materials move, where money leaks, and how supply chains actually break down when software gets in the way.

---

## The Real-World Operation: Custom Server Manufacturing

The business model was straightforward but logistically intense:
1. Buy decommissioned data center gear by the truckload (thousands of enterprise servers at a time).
2. Diagnostic-test components on the bench and tear them down to bare chassis, motherboards, CPUs, RAM, and drives.
3. Dynamically build and configure custom servers to customer specs, selling across our web store and marketplaces.

The challenge wasn't just physical assembly—it was tracking tens of thousands of volatile components in real time across warehouse bins, online listings, customer quotes, and financial books.

If our software fell out of sync with physical stock for even an hour, we either oversold parts we didn't have or sat on expensive components while market prices dropped.

---

## Building Integrations That Worked

Instead of wrestling with off-the-shelf software or treating people like human copy-paste machines between spreadsheets, I worked directly with founders and platform APIs to wire our business together.

### 1. eBay & NetSuite: Near-Real-Time Inventory Sync (FarApp)
- **The Problem**: Managing thousands of volatile hardware parts across eBay and NetSuite using TurboLister meant constant manual uploads. We regularly ran into stockouts, oversold items, and phantom inventory.
- **What I Did**: Worked directly with Steve Greiner (founder of FarApp, later acquired by Oracle as NetSuite Connector) over 12+ months to build a near-real-time sync layer.
- **The Outcome**: When component bins ran low in the warehouse, eBay listings automatically paused or shut down before anyone could buy what wasn't there. Incoming sales orders pushed straight into NetSuite fulfillment queues without manual data entry.

### 2. Customer List & Behavioral Sync (Constant Contact & Cazoomi)
- **The Problem**: A 25,000+ subscriber list required bi-weekly spreadsheet exports and reconciliations just to keep unsubscribe lists and customer groups accurate. This was Cazoomi's largest deployment at the time.
- **What I Did**: Partnered with Clint Wilson (founder of Cazoomi) over 6+ months to automate bi-directional sync between NetSuite customer records and Constant Contact.
- **The Outcome**: Opt-ins and opt-outs stayed synchronized automatically. We also brought click-activity data directly into NetSuite customer records, letting sales reps see which parts or systems a customer was eyeing.

### 3. Live Chat Attribution (Velaro)
- **The Problem**: Sales reps taking chat inquiries on high-ticket enterprise builds spent the first few minutes flying blind, searching NetSuite for customer order history while leads cooled down.
- **What I Did**: Integrated Velaro live chat with NetSuite CRM to automatically identify returning accounts, display past orders, and log chat transcripts directly into customer profiles.
- **The Outcome**: Reps had full purchase history the second a chat opened, directly attributing \$60,000–\$100,000 in new sales while speeding up support turnaround.

---

## Custom Tooling & NetSuite Automation

Enterprise tools fail when people have to act as the glue between clunky interfaces. I wrote SuiteScripts and designed NetSuite workflows to make bad data entry impossible:

- **Server Configurator & Dynamic Kits**: Built an interactive web configurator for our storefront. Customers could pick processors, RAM configurations, drive arrays, and RAID cards with real-time compatibility checks and pricing updates. Behind the scenes, NetSuite calculated component availability so we never quoted configurations we couldn't build.
- **Smart Sales Order Validation**: Simple parts flowed straight through to pick-and-pack. High-value custom builds required automated component allocation holds and credit checks before hitting the warehouse queue. Rush orders were flagged to automatically jump fulfillment lines.
- **Morning Click-to-Close Reports**: Automated reports delivered to sales reps the morning after an email campaign. Reps could see customers who clicked high-margin server configurations but didn't finish buying. Following up with those warm leads consistently drove **\$10,000–\$20,000 in incremental sales** within 48 to 72 hours.

---

## Working with Executive Leadership

Reporting directly to the Chief Operating Officer, I turned operational data into concrete buying and inventory decisions:

- **Inventory Velocity & Aging**: Tracked how quickly specific CPUs, memory modules, and drives turned over so we could discount aging silicon before market value dropped.
- **Truckload Purchase Modeling**: Combined historical component teardown yields with marketplace clearing prices to model whether buying a decommissioned data center lot would actually turn a profit after labor and bench testing.
- **Permissions & Clean Audits**: Built role-based access in NetSuite so sales, warehouse, and accounting teams had exactly the access they needed without breaking internal controls or slowing down daily work.

---

## Why This Matters for Modern AI & Systems Work

Building AI systems isn't a break from this background—it's the exact same discipline applied to new tools:

- **In ERP systems**, the challenge was connecting APIs, warehouses, and ledgers so humans didn't waste hours shuffling spreadsheets.
- **In AI-native engineering**, the challenge is giving language models clear specs, clean tool interfaces, and solid constraints so they build useful software without hallucinating or breaking state.

The principle is identical: understand the real-world workflow, remove friction, respect the constraints, and build systems that don't need constant babysitting to run cleanly.
