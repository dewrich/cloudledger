# How Our Documentation is Organized

## Overview

These docs are organized on two orthogonal schemes. **C4** answers *which thing is
this about* (system → container → component); the **[Divio Documentation System](https://documentation.divio.com)**
answers *what kind of writing is this*. Every page has both coordinates. See the video
[What nobody tells you about documentation](https://www.youtube.com/watch?v=t4vKPhjcMZg)
for why this matters.

## The Quadrants

### Tutorials — learning-oriented

Take a newcomer by the hand and turn a learner into a user. Start here if you're new.

### Topic Guides / Explanation — understanding-oriented

Discussion of key concepts at a high level, with the background and the *why*. Start
here for concepts, decisions (ADRs), and discussions.

### Reference — information-oriented

Dry, factual technical reference: APIs, schemas, settings. Describes how the
component works and how to use it, assuming you know the key concepts. Start here
for the blueprints of the system.

### How-to Guides — problem-oriented

Recipes that walk you through addressing a specific problem or use-case. More advanced
than tutorials. Start here to follow a procedure and get a job done.

## Layout — C4 is the primary axis

The top-level folders name the **C4 levels**, so the folder tree itself shows the
architecture:

- `context/index.md` — the **system** node (C4 L1).
- `containers/<name>.md` — the **container** nodes (C4 L2), one file each.
- `components/<container>/<name>.md` — **component** nodes (C4 L3), when a container
  is decomposed. Absent until needed.

The **Divio quadrants** above (`tutorials/`, `how-tos/`, `reference/`, `topic-guides/`)
hold the prose and answer *what kind of writing is this*; the two schemes are
orthogonal. C4 level comes from each node's `kind:` frontmatter, not its path — the
folders are for humans. Empty folders are simply absent — no dead links to render.

See the service overview: [index.md](../../index.md).
