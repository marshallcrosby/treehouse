# Treehouse / elevated fort — project context

An interactive 3D framing model reconstructed from Marshall's hand-drafted
elevations and floor plan. This file is the working record: what the structure
is, where every number came from, what was assumed, and what is still unresolved.

```
treehouse/
├── index.html                      the model — self-contained, no network needed
├── CLAUDE.md                       this file
└── drawings/
    ├── front-back-sides.pdf        source elevations (Illustrator vector, to scale)
    └── floor-plan.png              source floor framing plan (raster screenshot)
```

Open `index.html` directly in a browser, or serve the folder. Three.js r128 is
inlined, so it works offline. The only external reference left is a Google Fonts
`<link>` for IBM Plex; without a connection it falls back to system fonts and
everything else still works.

---

## 1. What the structure is

An elevated fort on six buried 6x6 posts:

| | |
|---|---|
| Deck footprint | 190½ × 120 in (rim outer faces) |
| Deck height | 107¼ in above grade |
| Enclosed room | 144½ × 120 in, at the back of the platform |
| Open landing | 46 in deep, at the front, under the gable overhang |
| Roof | gable, 8.91:12, ridge runs the long axis |
| Grade to peak | 251¼ in (20 ft 11¼ in) |
| Total pieces | 163 boards + panels |

The front gable wall has a 62 × 50 in window and a 34 in doorway opening onto
the landing. The roof overhangs the landing end by 34½ in and the back by 13 in.

---

## 2. How the drawings were measured

**Do not re-measure by eye. Both source drawings self-calibrate.**

### Elevations (`front-back-sides.pdf`)

Adobe Illustrator vector output. Extract geometry with `pdfplumber` —
`page.rects`, `page.curves` (with `.pts` for exact polygon vertices), and
`page.extract_words()` for the labels.

**Scale: exactly 7.2 points per inch.** Confirmed two ways: the 6x6 post rect is
39.6 pt wide (÷ 7.2 = 5.5 in actual) and 1036.8 pt long (÷ 7.2 = 144 in).

Grade line sits at y = 1889.9 pt. To convert:

```
height_above_grade = (1889.9 - y_pt) / 7.2
```

The page y-axis runs downward.

### Floor plan (`floor-plan.png`)

A raster screenshot with no scale bar — but the outer rim is a known 120 in from
the vector file, which calibrates it.

**Scale: 5.842 px per inch** (701 px across 120 in). The length axis
independently gives 5.840 — a 0.03% disagreement, so the screenshot is
undistorted. Checked against the four corner posts, which were *not* used to
derive the scale: all land within **0.22 in**. That is the honest error bar on
anything measured off this image.

Image top = back of the structure, bottom = front (landing end):

```
X = 190.5 - (y_px - 37) / 5.842        Z = (x_px - 65) / 5.842
```

Members measure 10 px raw; antialiasing adds about 1 px per side, so true width
is 1.5 in. Doubled members measure 19 px = 3.0 in.

---

## 3. Coordinate system used in the model

```
X  length   0 → 189    along the 2x8x189 side rims (front rim at X = -3)
Y  height   0 = grade, positive up
Z  width    0 → 120    across the 2x8x120 cross rims
```

Key elevations:

| Y (in) | What |
|---|---|
| -42 | bottom of the concrete piers |
| -38.5 | bottom of the 6x6 posts |
| 0 | grade |
| 4.25 – 11.5 | lower 2x8 rim |
| 59.34 | foot of the knee braces |
| 100 – 107.25 | deck 2x8 rim and joists |
| 105.5 | top of the 6x6 posts (1¾ in below the rim top) |
| 107.25 – 108.25 | 1 in OSB room floor, full footprint |
| 108.25 | wall base — bottom plates land on top of the floor, not the bare rim |
| 109.75 – 196.75 | side wall studs (87 in) |
| 109.75 – 191.25 | gable wall studs (81½ in) |
| 198.25 – 201.75 | 4x4 eave beams |
| 239.75 – 245.25 | 4x6 ridge beam |
| 251.25 | roof peak |

Post centres: **X = 2.75, 96.5, 186.25** · **Z = 4.5, 115.5**

---

## 4. Discrepancies found in the source drawings

**These are unresolved and matter. Do not silently "fix" them — they need
Marshall's call.**

### 4.1 The middle posts, 4 in apart between drawings

The floor plan puts the middle posts **96½ in** from the landing end. The side
elevation puts them **92½ in**. Both agree the post is off-centre by 4 in; they
disagree about which direction, because **the side elevation is mirrored**
relative to the plan.

The model follows the plan (96.5), on the grounds that a top-down view is harder
to flip by accident. The knee braces were mirrored to match. **Still needs
confirming against the real layout.**

To see it: select the two posts along one side. Near pair reads 93¾, far pair
89¾.

### 4.2 Three 113 in plates missing from the cut list

The elevations draw 2x4 top plates at both gable walls *and* a back wall bottom
plate — three 113 in pieces. The cut list calls out `1: 2x4x113`. The model
includes all three, so its count for that size is 3, not 1.

### 4.3 Rafters are 1.1 in short on the cut list

The list says `20: 2x6x92`. With plumb cuts at both ends, the finished piece
measures **93.1 in** corner to corner. Does not change the order (still one per
8 ft board, 2.9 in drop instead of 4 in), but the drawing number is wrong.

### 4.4 The landing joist length

The plan measures the doubled carrier's outer edge at **45.3 in**; the
elevations put the room's front wall face at **46.0**. That 0.7 in gap is larger
than the 0.22 in error bar, so it is the drawing's own imprecision, not scaling.
The model snapped to 46 so the carrier sits flush under the wall.

**Recommendation: set the carrier, then measure the landing bay in place before
cutting those seven joists.** Cut the ten 116½ in room joists with confidence —
that number is arithmetic (120 − 1.75 − 1.75), not a measurement.

### 4.5 Rafters pass through the ridge in the elevations

Not an error — it is the notch, drawn un-notched. See §5.

---

## 5. Decisions and assumptions made

Everything here was a judgement call, not something the drawings stated.

**Rafter notches.** The elevations draw rafters overlapping the ridge by ~2½ in
and clipping the outer corner of the 4x4s. Read as notches. Both were cut, and
the rafter line was **raised ½ in** off the drawn line so neither exceeds a
third of the 5½ in depth:

| Notch | Plumb | Seat | Perpendicular depth |
|---|---|---|---|
| Birdsmouth on the 4x4 | 1.90 in | 2.55 in | 1.52 in — 28% |
| Seat over the 4x6 ridge | 2.14 in | 1.75 in | 1.71 in — 31% |

The ½ in lift is the only invented number in the model. Without it the ridge
notch runs 39% of depth. Consequence: peak moved from 248.6 to 250.25 (now
251.25 — see the floor/wall decision below).

**Joist depth = 2x8.** A top-down view cannot show depth and there are no labels.
Chosen because they frame into 2x8 rims and 2x8 at 16 in o.c. is comfortable
over 9 ft 8 in, where 2x6 would be at its limit. **Confirm before buying.**

**Room floor is 1 in OSB, not decking** (changed 2026-08-21, Marshall's call).
The landing keeps its 1x5½ decking boards; the enclosed room gets 1 in OSB
sheathing instead. Laid as real 4x8 sheets — three courses, seams staggered,
long edge perpendicular to the joists the same way the decking it replaces
was. Six cut pieces come out of five sheets; see §6 and §7.

**The walls stand on top of the floor, not beside it** (changed 2026-08-21,
Marshall's call — this replaces the original "decking runs inside the walls"
read of the elevations). The OSB now runs the full 144.5 × 120 room footprint,
under the wall lines too, and the wall bottom plates land on top of it
(108.25) instead of on the bare rim (107.25). That is a clean deck-first,
walls-second build, and it removes the ½ in lip that used to show around the
room perimeter where the 1 in floor sat below the top of the 1½ in bottom
plate. Consequence: everything from the studs up — plates, eave beams, ridge,
peak — moved up 1 in (the floor thickness). The rafter notch geometry itself
is untouched; the whole roof just translated up with the walls. See §3 for
the new elevations.

**¼ in OSB is thin for this frame.** Studs and rafters are both 24 in o.c.,
where sheathing is normally 7/16 in. Drawn at ¼ as requested. Sheet count is
identical either way, so stepping up costs only the price difference. Flagged,
his call.

**Deck board gap = ¼ in**, giving a 5¾ in pitch. Not specified anywhere.

---

## 6. Member schedule

Framing, as drawn:

| Size | Qty | Role |
|---|---|---|
| 6x6x144 | 6 | posts, 38½ in below grade |
| 4x6x192 | 1 | ridge beam |
| 4x4x192 | 2 | eave beams |
| 4x4x41.5 | 2 | king posts under the ridge |
| 2x8x189 | 4 | side rims, deck and lower level |
| 2x8x120 | 5 | cross rims — front pair, back, two low |
| 2x8x116.5 | 10 | room floor joists + doubled wall carrier |
| 2x8x46 | 7 | landing joists, turned 90° |
| 2x6x113 | 4 | doubled gable headers |
| 2x6x92 | 20 | rafters, 24 in o.c. (see §4.3 — really 93.1) |
| 2x6x57.5 | 12 | 45° knee braces |
| 2x4x144.5 | 4 | side wall plates |
| 2x4x113 | 3 | gable top plates + back bottom plate (see §4.2) |
| 2x4x87 | 14 | side wall studs, 24 in o.c. |
| 2x4x81.5 | 13 | gable wall studs |
| 2x4x74.5 | 1 | front plate to the doorway |
| 2x4x62 | 2 | window sill, doubled |
| 2x4x33 | 4 | rake-ladder blocks |
| 2x4x28.5 | 3 | cripples under the window |
| 2x4x4.5 | 1 | front plate corner stub |
| 1x5.5x120 | 9 | landing boards, turned 90° |
| OSB ¼ | 22 sheets | walls, gables, roof |
| OSB 1 | 6 panels, cut from 5 sheets | room floor sheathing, full footprint (was 1x5.5x137.5 decking) |

Layout positions:

- Rafters, X: 11.5, 46, 59.5, 83.5, 107.5, 131.5, 155.5, 179.5, 188.5, 201.5
- Side wall studs, X: 46, 70, 94, 118, 142, 166, 189 (24 o.c.)
- Front gable studs, Z: 3.5, 7, 70.5, 75, 76.5, 112, 115
- Back gable studs, Z: 3.5, 27.5, 51.5, 75.5, 99.5, 115 (24 o.c.)
- Room joists, X: 61.9, 77.9, 93.8, 109.8, 125.8, 141.8, 157.7, 173.6 (16 o.c.)
  plus a doubled carrier at 46.0 / 47.5
- Landing joists, Z: 16.87, 32.79, 48.72, 64.64, 80.65, 96.66, 112.07 (16 o.c.,
  last bay is a 7 in half-bay)
- Window opening Z 8.5 – 70.5, sill at 138.25 – 141.25
- Doorway opening Z 78 – 112

---

## 7. Purchase list

**Framing — 90 boards, 936 linear feet**

| Size | Buy |
|---|---|
| 2x4 | 13 @ 8' · 1 @ 10' · 2 @ 12' · 6 @ 14' · 5 @ 16' |
| 2x6 | 20 @ 8' · 10 @ 10' |
| 2x8 | 4 @ 8' · 15 @ 10' · 4 @ 16' |
| 4x4 | 1 @ 8' · 2 @ 16' |
| 4x6 | 1 @ 16' |
| 6x6 | 6 @ 12' |

*(An earlier message in the conversation said 93 boards. It is 90.)*

**Decking — 9 boards, 90 linear feet:** 9 @ 10 ft, landing only. The room
floor is OSB now, see below — it used to be 20 more boards at 12 ft.

**Sheathing — 614½ sq ft:** 22 sheets by layout, buy 23. Straight area is 19.2;
the gable triangles are where the waste goes.

**Room floor — 1 in OSB, 5 sheets:** the floor now runs the full 144.5 × 120
room footprint (walls stand on top of it, see §5), so it's six pieces instead
of the smaller inside-the-walls version: one full 96×48, one 96×48, two
48.5×48, one 96×24, one 48.5×24 — 120.4 sq ft actual. 160 sq ft bought against
that, about 25% waste — the room's 144.5 in length doesn't divide evenly by
the 96 in sheet, so every course leaves a 48.5 in offcut. This is a different
product than the ¼ in wall/roof sheathing above — don't combine the two on
the lumber order.

Add 5–10% on the 2x4s and 2x6s for crooked stock rejected at the yard.

**Watch at the yard:**
- The four 144½ in 2x4s force 14 ft boards for the sake of half an inch. If the
  design tolerates 144, that becomes four 12-footers.
- The 189 in 2x8s leave only 3 in of slack in a 16 ft board. Pick clean ends.
- 6x6 @ 144, 4x4 @ 192 and 4x6 @ 192 are cut at exact stock length — no trim
  allowance at all.

---

## 8. How `index.html` is organised

Single file, one IIFE, no build step. Roughly:

1. **Piece definitions** — every board is pushed to a `pieces[]` array by one of
   three helpers:
   - `box(size, role, layer, x,y,z, dx,dy,dz)` — axis-aligned, min corner + extents
   - `prism(size, role, layer, pts, axis, at, dir)` — a 2D profile extruded 1.5 in,
     for anything with angled ends or notches. `axis:"z"` draws the profile in
     world XY and extrudes +Z; `axis:"x"` draws in world ZY and extrudes −X.
   - `P({... kind:"rotX"})` — a box rotated about X, for blocking on the roof slope
2. **Mesh builder** — walks `pieces[]`, makes geometry, records `userData.dir`
   (the true board axis in world space, needed for measuring)
3. **Scene, lights, grade plane, custom orbit controls** (no OrbitControls dependency)
4. **Measuring** — `obb()`, `measure()`, quarter-inch formatter `q()`
5. **Cut angles** — `CUTS`, derived live from `PITCH` and the notch geometry
6. **Cut-list rail, layer toggles, view presets, explode slider**

Ordering matters: the OSB and decking blocks sit at the *end* of the build
because they depend on constants defined by the wall and roof sections
(`RM_X0`, `EAVE_TOP`, `U()`, `CT`). `q8()` is hoisted to the top for the same reason.

Layers: `ground · posts · deck · joists · decking · braces · walls · roof · skin`

**Everything is derived, nothing is hardcoded.** Change `PITCH` and the rafter
profiles, cut angles, notch depths, roof sheathing and peak height all follow.

### Features

- Click a board to identify it; the panel shows saw settings for anything with
  angled cuts, or "All cuts square"
- Shift-click (or the **Measure** button on touch) to select several. Two boards
  give on-centre and clear spacing; three or more give consecutive gaps in order
  plus the total span. Measured perpendicular to the shared axis, so it works on
  rafters and braces too. Rounds to the nearest ¼ in
- Cut-list rail on the left — bars are drawn to true scale. Click to isolate
- Iso / Front / Side / Top presets reproduce the source drawings
- Explode slider separates layers vertically

---

## 9. Still open

1. **Confirm the middle post position** — 92½ or 96½ from the landing end (§4.1)
2. **Confirm joist depth** — modelled as 2x8, unstated in the drawings (§5)
3. **No way up.** There is still no ladder or stair to a platform nearly 9 ft in
   the air. Nothing in any drawing or cut list.
4. **Decide the OSB thickness** — ¼ as drawn, or 7/16 as the 24 in o.c. framing
   wants (§5)
5. Fix the three missing 113 in plates and the 92 → 93.1 rafter length on the
   source drawing (§4.2, §4.3)

---

## 10. History

Built over one session, in this order:

1. Read the cut list out of the PDF, totalled it by size, and optimised a
   purchase plan against standard stock lengths
2. Rasterised the PDF and found it was three elevations, not just a list —
   rebuilt the whole structure from vector coordinates as an interactive 3D model
3. Added floor joists from the floor plan screenshot; caught the mirrored side
   elevation in the process
4. Confirmed the joist lengths were derived rather than measured, and documented
   the error bars
5. Rebuilt the knee braces and rafters as real cut profiles — 45° mirrored end
   cuts on the braces, birdsmouth and ridge seat notches on the rafters
6. Added multi-select measurement with on-centre and clear spacing
7. Added saw settings for every angled cut, derived from the pitch
8. Skinned it in ¼ in OSB, laid out as real 4x8 sheets with staggered joints
9. Added 1 × 5½ decking, running perpendicular to the joists in each bay
10. Inlined Three.js so the file has no network dependency
11. Swapped the room floor from 1x5½ decking to 1 in OSB, laid as real 4x8
    sheets with staggered seams — the landing kept its decking (2026-08-21,
    Marshall's request)
12. Moved the walls onto the top of that floor instead of beside it — the OSB
    now runs the full room footprint, and everything from the wall plates up
    (studs, eaves, ridge, peak) moved up 1 in to sit on top of it (2026-08-21,
    Marshall's request)
