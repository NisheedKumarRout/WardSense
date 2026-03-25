# 🏥 Hospital Bed & Resource Allocation — AI System
### Complete Project Presentation Guide

---

## 1. Project Overview

**Full Title:** Hospital Bed & Resource Allocation — AI System (SDG 3)

**Tagline:** An intelligent, animated simulation that uses Artificial Intelligence to automatically allocate hospital patients to the correct wards — in real time, with full visual transparency of every algorithmic decision.

**SDG Alignment:** This project directly supports **United Nations Sustainable Development Goal 3 — Good Health and Well-being**, by demonstrating how AI can optimize critical healthcare resource allocation to ensure patients receive timely and appropriate care.

---

## 2. Problem Statement

Hospitals face a constant challenge: limited beds across specialized wards must be allocated to patients with vastly different medical needs. Incorrect assignments can:
- Expose vulnerable patients to infection
- Fail to provide life-critical care to critical patients
- Lead to inefficient use of scarce medical resources

**This project solves this problem using AI** — specifically, a combination of Constraint Satisfaction Problem (CSP) modeling and DFS Backtracking, visualized step-by-step so medical and administrative staff (or students/professors) can understand _exactly_ how and why each decision was made.

---

## 3. Tech Stack

| Category | Technology | Purpose |
|---|---|---|
| **Language** | HTML5 | Structure & markup |
| **Language** | Vanilla JavaScript (ES6+) | All AI logic, animation, DOM management |
| **Rendering** | HTML5 `<canvas>` API | Real-time 2D visualization engine |
| **Typography** | Google Fonts — *Orbitron* | Futuristic UI headers & labels |
| **Typography** | Google Fonts — *Inter* | Clean body text & algorithm log |
| **Styling** | Vanilla CSS (Custom Properties) | Full dark-mode neon design system |
| **Design Theme** | Dark-mode Neon Medical Dashboard | Sci-fi inspired hospital UI |
| **AI Concepts** | CSP + DFS Backtracking + Intelligent Agent | Core algorithmic engine |
| **Physics** | Custom Canvas physics (lerp + gravity) | Orb movement & bounce animation |
| **HiDPI** | `devicePixelRatio` scaling | Crisp rendering on Retina/high-DPI screens |

> **Zero dependencies.** No frameworks, no npm, no build tools. A single [.html](file:///c:/Codes/AI%20Project/hospital_allocation.html) file that runs entirely in the browser.

---

## 4. AI Concepts Implemented

This is the intellectual core of the project. Three distinct AI concepts from classical AI theory are implemented:

---

### 4.1 Constraint Satisfaction Problem (CSP)

> **Definition:** A CSP is a mathematical problem defined by: Variables, Domains, and Constraints. The goal is to find an assignment of values to all variables such that all constraints are satisfied.

| CSP Component | Project Mapping |
|---|---|
| **Variables** | Patients (what we need to assign) |
| **Domains** | Hospital beds (filtered by valid zone type) |
| **Constraints** | Zone compatibility rules (see below) |

**Constraint Rules:**
```
Critical   patient → MUST go to ICU           (needs life support equipment)
Infectious patient → MUST go to Isolation      (prevent outbreak/contagion)
General    patient → MUST go to General Ward   (cannot be near infectious)
```

Every assignment attempt is validated against these rules. A violation causes the algorithm to **reject** and **prune** that branch.

---

### 4.2 Uninformed Search — Depth-First Search (DFS) with Backtracking

> **Definition:** DFS explores a search tree by going as deep as possible down one branch before backtracking and trying another. With backtracking, failed branches are pruned and the algorithm undoes partial assignments.

```
┌─ backtrack(depth) ─────────────────────────────────┐
│  depth = current DFS tree level                     │
│  Each level handles ONE patient (CSP variable)      │
│  Iterate all beds = try each CHILD NODE             │
│  Constraints pass → RECURSE DEEPER                  │
│  Constraints fail → PRUNE branch (no recursion)     │
│  All beds exhausted → BACKTRACK (return false)      │
│  Base case: depth == numPatients → SOLUTION FOUND!  │
└─────────────────────────────────────────────────────┘
```

**Time Complexity:** [O(n!)](file:///c:/Codes/AI%20Project/hospital_allocation.html#1314-1375) in worst case, but the CSP constraints prune massive portions of the search tree, making it extremely fast in practice.

**Key Steps Visualized:**
- 🟢 **ASSIGN** — orb moves to a bed (valid placement at this depth)
- 🔴 **REJECT** — orb bounces off a bed (constraint violated, branch pruned)
- 🟠 **BACKTRACK** — orb returns to queue (no valid bed found, undo assignment)
- ⭐ **SUCCESS** — all patients assigned, solution propagates up the call stack

---

### 4.3 Intelligent Agent (AllocationAgent)

> **Definition (Russell & Norvig):** An intelligent agent perceives its environment through sensors and acts upon it through actuators to achieve a goal.

| Agent Property | Implementation |
|---|---|
| **Perceive** | Reads current assignment map + bed availability |
| **Goal** | Assign every patient to a valid, unique bed |
| **Actions** | ASSIGN, REJECT, BACKTRACK (recorded as step objects) |
| **Policy** | DFS with constraint pruning (smart backtracking) |

The [AllocationAgent](file:///c:/Codes/AI%20Project/hospital_allocation.html#816-930) class pre-computes an entire `stepQueue` — a complete trace of every algorithmic decision — which the animation engine then replays visually, step by step.

```javascript
class AllocationAgent {
  perceive()    // returns current environment state
  solve()       // entry point: kicks off DFS
  backtrack()   // recursive DFS engine
}
```

---

## 5. System Architecture

```
┌──────────────────────────────────────────────────────────┐
│                   hospital_allocation.html                │
│                                                          │
│  ┌─────────────────────┐   ┌──────────────────────────┐  │
│  │   DATA MODEL        │   │   AllocationAgent (AI)   │  │
│  │  ─────────────────  │   │  ──────────────────────  │  │
│  │  PATIENTS[]         │──▶│  checkConstraints()      │  │
│  │  BEDS[]             │   │  backtrack(depth)        │  │
│  │  CONDITION_COLORS{} │   │  solve() → stepQueue[]   │  │
│  └─────────────────────┘   └───────────┬──────────────┘  │
│                                        │                  │
│                            ┌───────────▼──────────────┐  │
│                            │   STEP PLAYBACK ENGINE   │  │
│                            │  ──────────────────────  │  │
│                            │  playNextStep()          │  │
│                            │  scheduleNext(delay)     │  │
│                            │  waitForArrival(orb, cb) │  │
│                            └───────────┬──────────────┘  │
│                                        │                  │
│  ┌─────────────────────┐   ┌───────────▼──────────────┐  │
│  │   SIDE PANEL (DOM)  │◀──│   CANVAS ENGINE          │  │
│  │  ─────────────────  │   │  ──────────────────────  │  │
│  │  Patient Queue cards│   │  draw() render loop      │  │
│  │  Algorithm Log      │   │  drawZone(), drawBed()   │  │
│  │  Status display     │   │  drawOrb(), updateOrb()  │  │
│  └─────────────────────┘   │  drawGrid(), drawECG()   │  │
│                            │  flashBed()              │  │
│  ┌─────────────────────┐   └──────────────────────────┘  │
│  │   CONFIGURE MODAL   │                                  │
│  │  ─────────────────  │                                  │
│  │  Patient editor     │                                  │
│  │  Ward capacity ctrl │                                  │
│  │  Apply → rebuild    │                                  │
│  └─────────────────────┘                                  │
└──────────────────────────────────────────────────────────┘
```

---

## 6. Data Models

### Patients (CSP Variables)
```javascript
let PATIENTS = [
  { id: 'P1', name: 'Alice Chen', condition: 'Critical',   color: '#4da6ff', glow: '...' },
  { id: 'P2', name: 'Bob Marley', condition: 'Infectious', color: '#ff2d55', glow: '...' },
  { id: 'P3', name: 'Carol Weiss', condition: 'General',   color: '#00ff8c', glow: '...' },
  // ... up to 6 default patients
];
```

### Beds (CSP Domains)
```javascript
let BEDS = [
  { id: 'B1', label: 'ICU-1',  zone: 'ICU'       },
  { id: 'B2', label: 'ICU-2',  zone: 'ICU'       },
  { id: 'B3', label: 'ISO-1',  zone: 'Isolation'  },
  { id: 'B4', label: 'ISO-2',  zone: 'Isolation'  },
  { id: 'B5', label: 'GEN-1',  zone: 'General'    },
  { id: 'B6', label: 'GEN-2',  zone: 'General'    },
];
```

Both arrays are declared with `let` so they can be **dynamically rebuilt** at runtime through the Configure modal.

---

## 7. Key Features

### 🎬 Real-Time Canvas Animation
- Entire hospital ward map drawn on `<canvas>` using the 2D Context API
- 60fps render loop using `requestAnimationFrame`
- HiDPI / Retina display aware (`devicePixelRatio` scaling)
- Dynamic layout recalculates on window resize

### 🔮 Patient Orbs with Physics
Each patient is represented by a glowing "orb" with:
- **Smooth lerp movement** toward target positions (beds or queue)
- **Motion trail effect** for a cinematic feel during movement
- **Bounce physics** on REJECT steps — gravity + friction simulation — orb visually "gets ejected" from a bed and snaps back to queue
- **Pulse animation** (idle pulsing glow) using `Math.sin` on timestamp
- **Specular highlight** for a 3D glass-ball appearance

### 🏥 Ward Visualization
Three distinct colored zones rendered with glowing borders:
| Zone | Color | For |
|---|---|---|
| ICU (Intensive Care Unit) | Cyan `#00e5ff` | Critical patients |
| Isolation Ward | Orange `#ff7b00` | Infectious patients |
| General Ward | Green `#00ff8c` | General patients |

Each bed slot displays a 🛏 bed emoji with a label. Bed slots **flash red** on REJECT and **green** on ASSIGN, with fade-out highlight animation.

### 📋 Algorithm Log (Live Feed)
A real-time scrollable log in the side panel shows every step the DFS algorithm takes:
- ✔ Green entries = ASSIGN (valid placement)
- ✘ Red entries = REJECT (constraint violation)
- ↩ Orange entries = BACKTRACK (undo)
- ★ Cyan entries = SUCCESS

### ⚙ Configure Modal
A full configuration interface allowing:
- **Edit patient names** inline
- **Change patient conditions** (Critical / Infectious / General)
- **Add new patients** dynamically
- **Remove patients** from the roster
- **Set ward capacity** (1–8 beds per ward type: ICU, Isolation, General)
- **Apply Changes** — completely rebuilds the CSP problem and re-renders everything

### 🎚 Speed Control Slider
Adjustable step delay from **50ms** (very fast) to **1200ms** (slow, great for presentations). Controls the pause between each algorithm decision during animation playback.

### 🔬 Developer/Professor Inspection
The [AllocationAgent](file:///c:/Codes/AI%20Project/hospital_allocation.html#816-930) instance is exposed on `window.agent` after solving, allowing real-time inspection of the `assignment`, `stepQueue`, and `occupiedBeds` via browser DevTools console.

---

## 8. UI/UX Design System

| Property | Value |
|---|---|
| **Theme** | Dark-mode Neon Medical Dashboard |
| **Background** | Deep navy `#080c18` |
| **Font (Headers)** | Orbitron (futuristic, monospace-feel) |
| **Font (Body)** | Inter (clean, readable) |
| **Layout** | CSS Grid — 2-column (canvas + side panel) |
| **Primary Accent** | Cyan `#00e5ff` |
| **Success Color** | Neon Green `#00ff8c` |
| **Error Color** | Neon Red `#ff2d55` |
| **Warning Color** | Neon Orange `#ff7b00` |
| **Config Accent** | Neon Purple `#bf5fff` |
| **Animations** | CSS transitions + Canvas `requestAnimationFrame` |
| **Rendering** | HiDPI canvas scaling |

**Visual Details:**
- Dot-grid background canvas overlay for depth
- ECG-style decorative lines under each ward label
- Glassmorphism modal with `backdrop-filter: blur`
- Neon glow box-shadows on all interactive elements
- All buttons use Orbitron font with uppercase letter-spacing

---

## 9. Workflow — How It Works (End to End)

```
1. Page loads
   └─ buildPatientCards()   → populates the side panel patient list
   └─ resizeCanvas()        → computes layout, places zones & bed slots
   └─ requestAnimationFrame → starts the 60fps render loop

2. User clicks "▶ Run AI Allocation"
   └─ Validates: does each patient condition have a matching zone?
   └─ Instantiates AllocationAgent(PATIENTS, BEDS)
   └─ agent.solve() runs the full DFS synchronously → builds stepQueue[]
   └─ Disables buttons, sets status to "ALLOCATING…"
   └─ scheduleNext(400) → starts step-by-step playback

3. For each step in stepQueue:
   ASSIGN  → orb moves to bed slot, bed flashes green, card updates "ASSIGNED"
   REJECT  → orb moves toward bed, then bounces back (physics), bed flashes red
   BACKTRACK → orb lerps back to queue home position, bed flashes orange
   SUCCESS → status turns green "ALLOCATION COMPLETE", buttons re-enabled

4. User can:
   └─ "↺ Reset" — clears everything, returns to initial state
   └─ "⚙ Configure" — opens modal to change patients/beds

5. Configure Modal → Apply:
   └─ Rebuilds PATIENTS[] and BEDS[] from form inputs
   └─ Triggers full re-render pipeline (same as reset)
```

---

## 10. Default Scenario

| Patient | Condition | Required Zone | Assigned Bed |
|---|---|---|---|
| Alice Chen | Critical | ICU | ICU-1 |
| Bob Marley | Infectious | Isolation | ISO-1 |
| Carol Weiss | General | General | GEN-1 |
| David Osei | Critical | ICU | ICU-2 |
| Eva Torres | Infectious | Isolation | ISO-2 |
| Frank Liu | General | General | GEN-2 |

The DFS solver assigns all 6 patients successfully with no backtracking needed in the default configuration (perfect symmetry: 2 patients per condition, 2 beds per zone).

---

## 11. Potential Questions & Answers

**Q: Why DFS and not BFS or A*?**
> DFS + Backtracking is the standard algorithm for CSP solving because it uses O(n) space (only the current path), prunes entire subtrees on constraint violations, and works well when a solution exists at a moderate depth. BFS would require exponential memory. A* requires a heuristic, which is unnecessary here.

**Q: What is the time complexity?**
> Worst case O(n! / pruned) — the constraints eliminate a huge portion of the search space. In the typical 6-patient, 6-bed case, the solver completes in microseconds.

**Q: What happens if there's no solution?**
> The DFS exhausts all possibilities and returns `false` all the way up. The system would log failures and set status to a fail state. This can be demonstrated by removing all ICU beds while having a Critical patient in Configure mode.

**Q: Why a single HTML file?**
> To keep the project zero-dependency and maximally portable — runs on any device with a browser, no server needed, no install required.

**Q: How is this an "Intelligent Agent"?**
> Using Russell & Norvig's definition: it Perceives (reads assignment state), has a Goal (all patients assigned), takes Actions (ASSIGN/REJECT/BACKTRACK), and follows a Policy (DFS with constraint pruning) to rationally achieve its goal.

**Q: How does the physics simulation work?**
> Orbs use **linear interpolation (lerp)** for smooth movement during ASSIGN and BACKTRACK. For REJECT, a velocity vector is applied (upward kick `vy = -12` + horizontal `vx` toward home), with gravity (+0.4 per frame) and air friction (×0.97 per frame) for a realistic bounce back effect.

---

## 12. Summary

| Aspect | Detail |
|---|---|
| **Project Type** | Single-page AI Simulation |
| **Lines of Code** | ~1,764 lines |
| **File Size** | ~61 KB |
| **Dependencies** | Zero (pure HTML/CSS/JS) |
| **AI Algorithms** | CSP + DFS Backtracking + Intelligent Agent |
| **Rendering** | HTML5 Canvas, 60fps |
| **SDG Goal** | SDG 3 — Good Health & Well-being |
| **Configurable** | Yes — patients and ward beds fully editable at runtime |
| **Accessibility** | ARIA roles on modal, semantic HTML |
