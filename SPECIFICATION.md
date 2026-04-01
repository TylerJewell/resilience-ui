# Akka Resilience Demo — Application Specification

## 1. Overview

A single-page React application rendered in a single HTML file (`resilience.html`) that visualizes Akka's distributed systems capabilities across two demos:

1. **Akka Clustering** — Agent workload distribution across a 3-node cluster with node failure/recovery simulation
2. **Akka HA/DR** — Active-active multi-region replication across two regions with region outage/recovery simulation

The application is built with React 18 (CDN), Babel standalone (JSX transpilation in-browser), and inline styles. No build step required.

---

## 2. Technology Stack

| Dependency | Version | Source |
|---|---|---|
| React | 18.3.1 | CDN (production UMD) |
| ReactDOM | 18.3.1 | CDN (production UMD) |
| Babel Standalone | 7.27.0 | CDN (in-browser JSX transform) |
| Instrument Sans | — | Google Fonts |
| Roboto Mono | — | Google Fonts |

Favicon sourced from `https://doc.akka.io/_/img/favicon.ico`.

---

## 3. Style Guide

### 3.1 Color Palette (CSS Custom Properties)

| Variable | Hex | Usage |
|---|---|---|
| `--bg` | `#1a1a1a` | Page background |
| `--fg` | `#f5f5f5` | Primary text |
| `--card` | `#2f2f2f` | Card backgrounds |
| `--card-fg` | `#e5e5e5` | Card text |
| `--muted` | `#434343` | Borders, dividers |
| `--muted-fg` | `#c9c9c9` | Secondary/muted text |
| `--border` | `#434343` | Card borders, section dividers |
| `--primary` / `--cyan` | `#00d8dd` | Primary accent (starting state, read actions, badges) |
| `--primary-fg` | `#1a1a1a` | Text on primary background |
| `--accent` / `--yellow` | `#ffce4a` | Secondary accent (buttons, write actions, active tab indicator) |
| `--accent-fg` | `#1a1a1a` | Text on accent background |
| `--yellow-dk` | `#f5b60b` | Button hover state |
| `--cyan-dk` | `#02a4a7` | SVG border stroke color |
| `--destructive` | `#ff5400` | Destructive/warning actions |
| `--destructive-fg` | `#ffffff` | Text on destructive background |
| `--secondary` | `#313131` | Secondary badge background |
| `--secondary-fg` | `#e5e5e5` | Secondary badge text |
| `--radius` | `0.625rem` (10px) | Global border radius |

### 3.2 Semantic Status Colors

| Status | Hex | Used For |
|---|---|---|
| Active / Available | `#72d35b` | Healthy nodes/regions |
| Starting | `#00d8dd` | Nodes/regions coming online |
| Stopping | `#d45a00` | Nodes/regions shutting down |
| Failed / Down / Unavailable | `#d70023` | Offline nodes/regions |

### 3.3 Data Visualization Colors

| Metric | Hex | Usage |
|---|---|---|
| Read operations | `#00d8dd` | Read latency, READ entries in logs |
| Write operations | `#ffce4a` | Write latency, WRITE entries in logs |
| Tokens / Replication | `#d2a8ff` | Token counts, replication strand, replicated entries |
| Active agents | `#72d35b` | Agent count indicators |

### 3.4 Typography

- **Primary font**: `'Instrument Sans', -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif`
- **Monospace font** (transaction logs): `Consolas, 'Courier New', monospace`
- **Line height**: 1.5 (body default)

| Class | Size | Weight |
|---|---|---|
| `.text-3xl` | 30px | — |
| `.text-2xl` | 24px | — |
| `.text-lg` | 18px | — |
| `.text-sm` | 14px | — |
| `.text-xs` | 12px | — |
| `.font-bold` | — | 700 |
| `.font-semibold` | — | 600 |
| `.font-medium` | — | 500 |

### 3.5 Component Styles

**Cards**: `background: var(--card)`, `border: 1px solid var(--border)`, `border-radius: var(--radius)`

**Badges**: Pill-shaped (`border-radius: 9999px`), 12px font, 500 weight
- `.badge-default`: cyan background, dark text
- `.badge-secondary`: dark background, light text
- `.badge-destructive`: orange background, white text

**Buttons**: `border-radius: var(--radius)`, 14px font, 500 weight, `transition: all 0.15s`
- `.btn-primary`: yellow background, dark text; hover darkens to `--yellow-dk`
- `.btn-lg`: 14px 24px padding, 15px font
- Hover adds `box-shadow: 0 4px 16px rgba(0,0,0,0.4)`

**Tabs**: Bottom border bar with yellow underline on active tab
- `.tab-bar`: Flex row, bottom border `1px solid var(--border)`, 24px margin-bottom
- `.tab-btn`: 12px 24px padding, 15px font, 600 weight, transparent background
- `.tab-btn.active`: white text, `border-bottom-color: var(--yellow)`

**Scrollbar** (Webkit):
- Width: 6px
- Track: `#2f2f2f`, 3px radius
- Thumb: `#606060`, 3px radius; hover: `#c9c9c9`

### 3.6 Animations

| Name | Keyframes | Duration | Usage |
|---|---|---|---|
| `fadeInLeft` | translateX(-20px) → 0, opacity 0 → 1 | 0.3s ease-out | Team cards appearing |
| `scaleIn` | scale(0) → scale(1) | 0.3s cubic-bezier(0.34, 1.56, 0.64, 1) | Node/region boxes appearing |
| `pulse` | scale(1) → 1.05 → 1 | 2s ease-in-out infinite | Active/available status boxes |
| `spin` | rotate(0) → rotate(360deg) | 1s linear infinite | Loading/transition spinners |

---

## 4. Page Layout

### 4.1 Global Structure

```
┌─────────────────────────────────────────────────┐
│ Header: "Akka Resilience Demo" (h1, 30px bold)  │
│ Subtitle: muted text                             │
├─────────────────────────────────────────────────┤
│ Tab Bar: [Akka Clustering] [Akka HA/DR]          │
├─────────────────────────────────────────────────┤
│ Tab Content (conditional render)                 │
└─────────────────────────────────────────────────┘
```

- Max width: 1800px, centered with `margin: 0 auto`
- Page padding: 24px
- Background: `#1a1a1a`
- Min height: 100vh

### 4.2 Tab Bar

Two tabs with icons:
- **Akka Clustering** — ServerIcon (16px) + text
- **Akka HA/DR** — ActivityIcon (16px) + text

Active tab indicated by yellow (`--yellow`) bottom border, 2px.

Tab state persists — switching tabs does not reset data or selections.

---

## 5. Akka Clustering Tab

### 5.1 Summary Bar

7-column grid of stat cards:

| Stat | Icon | Icon Color | Background |
|---|---|---|---|
| Active Teams | ActivityIcon | `#00d8dd` | `rgba(0,216,221,0.1)` |
| Instances | ZapIcon | `#72d35b` | `rgba(114,211,91,0.1)` |
| Total Tokens | BotIcon | `#d2a8ff` | `rgba(210,168,255,0.1)` |
| Avg Read | TimerIcon | `#00d8dd` | `rgba(0,216,221,0.1)` |
| Avg Write | TimerIcon | `#ffce4a` | `rgba(255,206,74,0.1)` |
| Active Nodes | ServerIcon | `#d45a00` | `rgba(255,166,87,0.1)` |
| Cluster Health | ServerIcon | dynamic | dynamic |

**Cluster Health** values:
- "Healthy" (all nodes active): green icon, green bg
- "Degraded" (some nodes down): orange icon, orange bg
- "Failed" (all nodes down): red icon, red bg

Each card: 16px padding, icon in 8px-padded rounded container, label as `.text-sm .text-muted`, value as `.text-2xl .font-semibold`.

### 5.2 Main Grid Layout

`grid-template-columns: 3fr 9fr`, gap 24px, `align-items: start`.

### 5.3 Control Panel (Left Column)

Card with 24px padding containing three sections separated by `1px solid var(--border)` dividers:

**Team Control**:
- "Launch New Agent Team" button: `.btn .btn-primary .btn-lg`, full width
- PlayIcon (16px) inline

**Node Management**:
- Header: "Node Management" (semibold)
- Description: muted text explaining simulation
- 3 toggle buttons (one per node), stacked with 10px gap
- Each button shows: PowerIcon/PowerOffIcon + "Node N" + status pill badge
- Button colors match node status (see Status Colors)
- Transitioning buttons: `opacity: 0.85`, `cursor: not-allowed`

**About Akka Clusters**:
- ServerIcon + header
- Description text (12px, muted, line-height 1.6)
- Content: "This demo shows how Akka clusters distribute agent workloads and memory across nodes. When a node fails, agents automatically migrate to healthy nodes. State is sharded in-memory across nodes with embedded routing and load balancing. Workflow state is managed with durable execution and memory recovery is from event sourced state. The combination reduces token waste by avoiding full workflow restarts. Akka's largest customers run clusters of 300K nodes with petabytes of data."

### 5.4 Agent Teams Area (Right Column)

**Empty state**: Card with centered muted text "No teams running. Launch a team to get started."

**With teams**: Flex container with two sub-columns:

#### 5.4.1 Team List (Left, 280px fixed width)

- Header: UsersIcon + "Agent Teams"
- Min height: 600px
- Each team card (stacked, 12px gap):
  - When **selected**: transparent background, no border, no border-radius (merges with SVG shape)
  - When **unselected**: card background, card border, standard radius
  - Click toggles selection (click selected card to deselect)
  - Hover on unselected: `box-shadow: 0 4px 16px rgba(0,0,0,0.4)`
  - Content: team name (14px, medium weight), runtime counter ("Xm Ys"), status badge, 2x2 stats grid (agents ratio, tokens, read latency, write latency)
  - Ref stored per card for SVG border shape calculation

#### 5.4.2 SVG Border Shape Overlay

An SVG path overlay that creates a visual connection between the selected team card and the detail panel:

- Positioned absolutely over the main area
- Fill: `rgba(0,216,221,0.06)` (very subtle cyan tint)
- Stroke: `#02a4a7`, 2px width
- Shape adapts based on selected card position:
  - **Near top**: Notch merges with top edge
  - **Near bottom**: Notch merges with bottom edge
  - **Middle**: Full notch shape with indentation at card position
  - **Spans full height**: Simple rounded rectangle
- Recalculated on: selection change, team count change, tab switch, container resize
- Uses double `requestAnimationFrame` for layout settling
- `ResizeObserver` on the main area container

#### 5.4.3 Agent Grid (Right, Flex 1)

- Constrained to left column height via `maxHeight` (tracked by `ResizeObserver`)
- Padding: `8px 24px 21px 32px`
- `overflow: hidden`

Content (from `AgentGrid` component):

**Header**: Team name (18px semibold) + "N instance cluster" subtitle

**Node Boxes**: CSS Grid, `repeat(N, 1fr)`, 32px gap
- Each node column (centered, staggered animation 80ms delay):
  - **Latency row above**: ReadIcon + "Xms" (cyan), WriteIcon + "Xms" (yellow)
  - **Node box**: 100% width, min-width 120px, height 80px, border-radius 8px
    - Background: status color
    - Icon: CheckCircle (active), RefreshIcon spinning (starting/stopping), AlertCircle (failed)
    - Label: "Node N" (13px, bold, white on dark statuses)
    - Active nodes get `animate-pulse-slow`
    - Hover: elevated box-shadow
  - **Token count below**: BotIcon + "N tokens" (12px, muted)

**Legend**: Horizontal flex, 24px gap, border-top separator
- Four items: Active (green), Starting (cyan), Stopping (orange), Down (red)
- Each: 16x16 rounded square + label

**Transaction Log**:
- Header: "TRANSACTION LOG" (12px, semibold, uppercase, 0.05em letter-spacing)
- Flex: 1, min-height: 0 (fills remaining vertical space)
- Scrollbar: always visible (`overflow-y: scroll`)
- Background: `#111111`, border-radius 6px
- Monospace font (Consolas), 11px
- Table with sticky header (background `#111111`, z-index 1)
- Columns: ACTION, DATA, WRITE NODE, READ NODE, TIMESTAMP, LATENCY
- Header cells: 10px font, 700 weight, `#606060` color, 0.08em letter-spacing, bottom border `#313131`
- Row colors: READ = `#00d8dd`, WRITE = `#ffce4a`
- Cell padding: `3px 10px`, `white-space: nowrap`
- Empty state: "Waiting for transactions..." in `#434343`

### 5.5 Clustering Behaviors

#### Team Launch
- Creates a team with a generated name from `PREFIXES` x `SUFFIXES` arrays
  - PREFIXES: Alpha, Beta, Gamma, Delta, Epsilon, Zeta, Eta, Theta
  - SUFFIXES: Workflow, Orchestra, Coalition, Ensemble, Squad
- 3 agent instances, one per node (Node 1, 2, 3)
- Instance status matches current node status (active nodes → active instances, failed nodes → failed instances)
- Initial tokens: random 100–599 per instance
- Initial read latency: random 5–15ms per instance
- Initial write latency: random 9–25ms per instance
- Newly launched team is auto-selected

#### Node Toggle (Lifecycle Transitions)
- **Active → Stopping**: Immediate status change to `stopping` (2 second duration)
  - After 2s: transitions to `failed`, `active: false`
  - All agents on that node also transition: active → stopping → failed
- **Failed → Starting**: Immediate status change to `starting` (9 second duration)
  - After 9s: transitions to `active`, `active: true`
  - All agents on that node also transition: failed → starting → active
- Buttons are non-interactive during transitions

#### Token Accumulation (every 2 seconds)
- Active agents accumulate 20–79 tokens per tick
- Read latency fluctuates ±10ms (clamped 5–15ms)
- Write latency fluctuates ±10ms (clamped 9–25ms)
- Team averages recalculated each tick

#### Transaction Log Generation (every 400ms per team)
- **First 10 entries per team**: Always WRITE
- **After 10 entries**: 60% WRITE, 40% READ (random)
- Data names selected randomly from: user_profile, order_details, inventory, session_token, cart_items, payment_info, shipping_addr, product_catalog, account_prefs, audit_log, notification, search_index, cache_entry, config_value, metric_point
- Timestamp: HH:MM:SS.mmm (24-hour, millisecond precision)
- Read latency: random 5–15ms; Write latency: random 9–25ms
- New entries prepended (newest first)
- Logs persist per team across team selection changes

---

## 6. Akka HA/DR Tab

### 6.1 Summary Bar

7-column grid of stat cards:

| Stat | Icon | Icon Color | Background |
|---|---|---|---|
| Active Teams | ActivityIcon | `#00d8dd` | `rgba(0,216,221,0.1)` |
| Total Tokens | BotIcon | `#d2a8ff` | `rgba(210,168,255,0.1)` |
| Avg Read | TimerIcon | `#00d8dd` | `rgba(0,216,221,0.1)` |
| Avg Write | TimerIcon | `#ffce4a` | `rgba(255,206,74,0.1)` |
| Repl. Latency | ArrowRightLeftIcon | `#d2a8ff` | `rgba(210,168,255,0.1)` |
| Repl. Data | ArrowRightLeftIcon | `#ffce4a` | `rgba(255,206,74,0.1)` |
| Region Health | GlobeIcon | dynamic | dynamic |

**Region Health** values:
- "All Active" (both available): green
- "Degraded" (one unavailable): orange
- "Transitioning" (any starting/stopping): `#d45a00` orange
- "Offline" (both unavailable): red

### 6.2 Control Panel (Left Column)

Same card structure as clustering tab, three sections:

**Team Control**: Identical "Launch New Agent Team" button.

**Region Management**:
- Header: "Region Management"
- Description: "Simulate region outages to observe active-active failover"
- 2 toggle buttons (one per region: us-east-1, eu-west-1)
- GlobeIcon + region name + status pill
- Colors match region status (Available=green, Starting=cyan, Stopping=orange, Unavailable=red)
- Transitioning buttons: `opacity: 0.85`, `cursor: not-allowed`

**About Akka HA/DR**:
- GlobeIcon + header
- Content: "This demo shows Akka's active-active multi-region replication. Agent state is replicated across regions in real time using reliable gRPC. When a region becomes unavailable, agents continue operating in the remaining region with no data loss. Writes to either region are automatically replicated to the other. When a downed region reactivates, Akka uses vector spaces to reconcile conflicts automatically during synchronization."

### 6.3 Agent Teams Area

Same structure as clustering: team list (left, 280px) + SVG border overlay + detail panel (right).

#### 6.3.1 Team List

Same visual treatment as clustering tab, with:
- 2x2 stats grid: regions (GlobeIcon, "2 regions"), tokens, read latency, write latency
- Same select/deselect behavior and SVG border shape wrapping

#### 6.3.2 Region Grid (Detail Panel)

**Header**: Team name + "Active-active replication across N region(s)" subtitle

**Region Boxes**: Horizontal flex layout with replication visualization between:

```
┌────────────┐                          ┌────────────┐
│  Rd   Wr   │                          │  Rd   Wr   │
│ ┌────────┐ │   ╭ DNA helix ╮          │ ┌────────┐ │
│ │us-east-1│ │   │ ≋≋≋≋≋≋≋≋ │          │ │eu-west-1│ │
│ │Available│ │   │REPLICATING│          │ │Available│ │
│ └────────┘ │   │ 42ms 1.2KB│          │ └────────┘ │
│  N tokens  │   ╰───────────╯          │  N tokens  │
└────────────┘                          └────────────┘
```

Each region box:
- Max width: 280px, flex: 1
- Latency indicators above (same as clustering)
- Box: 100% width, height 100px, border-radius 8px
  - Available: GlobeIcon, green, pulsing
  - Starting/Stopping: RefreshIcon spinning, cyan/orange
  - Unavailable: GlobeIcon, red
  - Status label text below name
- Token count below

**DNA Helix Replication Visualization** (between regions):
- SVG, 200x50 viewBox, max-width 200px
- Two sinusoidal paths (strands):
  - Strand A: purple (`#d2a8ff`) with gradient fade at edges
  - Strand B: cyan (`#00d8dd`) with gradient fade at edges
  - Both 2.5px stroke width, round linecap
- 7 vertical cross-links (base pairs) at x=25,50,75,100,125,150,175
  - White, 1px, 0.2 opacity
  - Positioned between the strand intersections
- 4 animated dots (2 per strand, 1s offset):
  - Purple dots flow left-to-right along Strand A (2s duration)
  - Cyan dots flow right-to-left along Strand B (2s duration)
- When replication suspended (not both available):
  - Strands turn `#606060`, 0.3 opacity
  - Cross-links 0.1 opacity
  - No animated dots
- Label below: "REPLICATING" (purple) or "SUSPENDED" (grey), 11px bold
- Metrics below: replication latency + cumulative bytes

**Legend**: Available (green), Unavailable (red)

**Replication Log**: Same structure as clustering transaction log, with different columns:
- Columns: ACTION, DATA, ORIGIN, REPLICATED TO, TIMESTAMP, LATENCY
- Row colors: READ = cyan, WRITE = yellow, WRITE → REPL = purple (`#d2a8ff`)
- Replicated entries show "WRITE ⟶ REPL" in ACTION column

### 6.4 HA/DR Behaviors

#### Team Launch
- Generated names from `HADR_PREFIXES` x `HADR_SUFFIXES`:
  - PREFIXES: Phoenix, Horizon, Cascade, Meridian, Summit, Apex, Zenith, Vanguard
  - SUFFIXES: Pipeline, Relay, Sync, Bridge, Circuit
- Deploys to a randomly selected available region as primary
- `regionData` created for both regions with:
  - Initial tokens: random 100–599
  - Initial read latency: random 5–15ms
  - Initial write latency: random 9–25ms
- Auto-selected on launch

#### Region Toggle (Lifecycle Transitions)
- **Available → Stopping**: 2 second duration, then `unavailable`, `available: false`
- **Unavailable → Starting**: 9 second duration, then `available`, `available: true`
- Non-interactive during transitions

#### Token Accumulation (every 2 seconds)
- Available regions accumulate 20–79 tokens per tick
- Read/write latency fluctuates ±10ms (same clamps as clustering)
- When both regions available:
  - Replication latency fluctuates ±10ms (clamped 20–80ms)
  - Replication bytes accumulate 100–599 bytes per tick
  - Bytes formatted: B → KB → MB

#### Replication Log Generation (every 400ms per team)
- **First 10 entries per team**: Always WRITE
- **After 10 entries**: 60% WRITE, 40% READ
- Origin region randomly selected from available regions
- For WRITE events when both regions available:
  - Additional replication entry auto-generated
  - Shows origin → target region
  - Separate replication latency: random 20–49ms
  - Entry marked as `replicated: true`
- Newest entries first, logs persist across team selection changes

---

## 7. Shared Behaviors

### 7.1 Tab Persistence
- All state (teams, selections, logs, node/region status) persists when switching tabs
- SVG border shapes recompute on tab switch via `activeTab` dependency in effects
- Runtime counters continue ticking across both tabs (1 second interval)

### 7.2 Team Name Generation
- Index-based prefix selection (cycles through array)
- Random suffix selection
- Counter incremented per tab independently

### 7.3 Transaction Log Write Burst
- Both tabs: first 10 log entries per team are forced to WRITE
- After 10 entries: normal random distribution (60% write, 40% read)

### 7.4 SVG Border Shape
- Shared algorithm between both tabs (separate ref sets)
- Computes a notched path connecting selected team card to detail panel
- 12px corner radius on all path corners
- Four cases based on card proximity to container edges:
  - Near top (< 30px): top edge merges
  - Near bottom (< 30px): bottom edge merges
  - Both: simple rounded rect
  - Neither: full notched shape
- ResizeObserver-driven, double-rAF debounced

---

## 8. Icon Set

All icons are inline SVGs following Lucide icon conventions:
- ViewBox: `0 0 24 24`
- Fill: none
- Stroke: configurable color
- Stroke width: 2 (standard) or 2.5 (small status icons)
- Stroke linecap/linejoin: round

| Icon | Component | Default Size | Usage |
|---|---|---|---|
| Activity | `ActivityIcon` | 20px | Team counts, HA/DR tab icon |
| Zap | `ZapIcon` | 20px | Instance count stat |
| Bot | `BotIcon` | 20px | Token counts |
| Timer | `TimerIcon` | 20px | Latency metrics |
| Server | `ServerIcon` | 20px | Node/cluster stats, clustering tab icon |
| Users | `UsersIcon` | 20px | Team list headers |
| Play | `PlayIcon` | 20px | Launch button |
| Power | `PowerIcon` | 20px | Active node toggle |
| PowerOff | `PowerOffIcon` | 20px | Failed node toggle |
| CheckCircle | `CheckCircleIcon` | 12px | Active status indicator |
| Refresh | `RefreshIcon` | 12px | Transition spinner (spin prop) |
| AlertCircle | `AlertCircleIcon` | 12px | Failed status indicator |
| Globe | `GlobeIcon` | 20px | Region indicators |
| ArrowRightLeft | `ArrowRightLeftIcon` | 20px | Replication metrics |
| Read (book) | `ReadIcon` | 14px | Read latency indicators |
| Write (pencil) | `WriteIcon` | 14px | Write latency indicators |

---

## 9. Data Names Pool

Transaction log data fields are randomly selected from:

```
user_profile, order_details, inventory, session_token, cart_items,
payment_info, shipping_addr, product_catalog, account_prefs, audit_log,
notification, search_index, cache_entry, config_value, metric_point
```

---

## 10. Timing Constants

| Behavior | Interval/Duration |
|---|---|
| Runtime counter tick | 1,000ms |
| Token accumulation tick | 2,000ms |
| Transaction log generation | 400ms |
| Node/region stopping duration | 2,000ms |
| Node/region starting duration | 9,000ms |
| DNA helix dot animation cycle | 2,000ms (1,000ms offset for second dot) |
| Pulse animation cycle | 2,000ms |
| Spin animation cycle | 1,000ms |
| fadeInLeft duration | 300ms |
| scaleIn duration | 300ms |
| Node box scale-in stagger | 80ms per node |
