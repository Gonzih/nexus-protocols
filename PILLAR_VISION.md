# PILLAR VISION

## What You're Looking At

**The LLM probability space as intelligence terrain.**

141 conversation anchors. 136 pathways between them. 23 pillars—regions where something valuable emerged from the exploration.

This isn't a graph. It's a **decision intelligence platform.**

---

## The Experience

### Opening View: Clean Architecture

Light background (off-white, subtle grid). Minimal chrome. Data-forward presentation.

Nodes appear as **clean geometric forms**—circles with crisp edges, professional color coding, varying sizes based on value metrics.

**Visual hierarchy:**
- Size = uniqueness/discovery density
- Color = value tier (cool gray → warm amber gradient for pillars)
- Depth rings = exploration distance from origin (subtle, not distracting)
- Labels appear on hover (no visual clutter by default)

**Professional palette:**
- Background: #F8F9FA (almost white, warm undertone)
- Regular nodes: #6C757D → #ADB5BD (cool grays)
- High-value nodes: #F59E0B → #D97706 (amber/gold spectrum)
- Edges: #DEE2E6 (light gray, subtle)
- Pillars: Stronger amber with clean border, not glowing

### The Network Structure

Edges show **reachability pathways** with varying opacity/thickness based on probability strength.

Clean, technical aesthetic—think network topology visualization, not organic web.

Hover over edge → tooltip shows probability metric, transition data

### Pillar Detail Panel

Click a pillar node → **side panel slides in from right.**

Clean white card with structured information:
- **Anchor ID** (technical identifier)
- **Value metrics** (uniqueness score, output count, depth)
- **Best output** (preview with expand option)
- **Reachability data** (how many paths lead here)
- **Connected nodes** (list of neighboring anchors)

Professional presentation—data tables, clear typography, no decoration.

### Exploration Timeline

Bottom-mounted timeline control. Minimal UI.

**Playback controls:** Play/pause, scrubber, speed selector
**Progress bar:** Shows exploration progression (nodes discovered over time)
**Depth markers:** Visual indicators when algorithm moved to new depth layer

Watch the network build itself. Professional animation—smooth, purposeful, not flashy.

### Control Panel (Top Right)

Clean toolbar with icon buttons:
- **Depth filter:** Toggle layers 0/1/2
- **Value filter:** Show all / pillars only / high-value threshold slider
- **Layout mode:** Force-directed / hierarchical / circular
- **Search:** Text input for anchor ID or content search
- **Export:** Download current view or pillar dataset

Minimal, professional, functional.

---

## The Value Proposition

**Framing for enterprise/professional audience:**

This visualization maps **LLM output quality landscape**—showing which conversation patterns reliably produce high-value results vs noise.

**Business use case:**
- Identify reusable conversation templates (pillars)
- Understand pathway dependencies (what leads to what)
- Optimize prompt engineering workflows
- Package proven patterns as operational playbooks

**Technical insight:**
The network structure reveals **reachability topology**—which starting points give you access to which solution regions, with what probability.

**ROI story:**
Most LLM usage = random walk through probability space. This maps the efficient routes to valuable outputs.

---

## Interaction Design

### Pan & Zoom
Smooth, responsive camera controls. Mouse wheel or pinch-to-zoom. Click-drag to pan.

### Node Interaction
- **Hover:** Tooltip with core metrics (anchor ID, value score, depth)
- **Click:** Open detail panel, highlight connected nodes
- **Double-click:** Center view on node, show 2-hop neighborhood

### Edge Interaction
- **Hover:** Show reachability probability
- **Click:** Highlight path, show transition details

### Filters & Controls
All controls update view in real-time. Smooth transitions between states. Professional animation curves (ease-out, no bounce).

### Multi-Select
Shift-click to select multiple nodes → compare metrics, export subset, analyze cluster properties

---

## Technical Architecture

**Stack:**
- D3.js v7 (force simulation, SVG rendering)
- Modern vanilla JS (ES6+, no framework bloat)
- CSS3 for UI components (clean, responsive)
- JSON data files (nodes, edges, metrics, timeline)

**Performance:**
- SVG for nodes/edges (141 nodes = fine, will scale to 1k+)
- RequestAnimationFrame for smooth animations
- Debounced resize handlers
- Lazy-load detail panels

**Browser targets:**
- Chrome/Edge (primary)
- Firefox, Safari (tested)
- No IE support (modern browsers only)

**Responsive:**
- Desktop-first (this is enterprise/analyst tool)
- Tablet works (with adjusted controls)
- Mobile = read-only view (no editing/interaction)

---

## The Differentiator

**Not another force-directed graph.**

**Professional execution:**
- Clean data presentation (no chart junk)
- Purposeful interaction (every click reveals insight)
- Enterprise-ready aesthetics (present to executives without cringe)
- Actionable intelligence (export what you discover)

**The feeling:**
You're using a **professional intelligence tool**, not exploring a toy demo. This is how experts analyze LLM behavior at scale.

**Trust signals:**
- Precise metrics (no hand-waving)
- Consistent design language
- Clear value hierarchy
- Technical credibility

---

## Implementation Phases

**Phase 1: Core Visualization (MVP)**
1. Load JSON data
2. Render force-directed graph (nodes + edges)
3. Basic pan/zoom
4. Hover tooltips
5. Simple color coding (gray → amber)

**Phase 2: Interaction**
6. Click detail panel
7. Depth filtering
8. Search functionality
9. Layout mode switching

**Phase 3: Timeline**
10. Timeline scrubber
11. Playback controls
12. Animated graph building

**Phase 4: Polish**
13. Responsive design
14. Export functionality
15. Performance optimization
16. Visual refinement

**Ship when Phase 2 complete.** Polish is iterative.
