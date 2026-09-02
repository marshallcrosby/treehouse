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
| Grade to peak | 253 in (21 ft 1 in) |
| Total pieces | 177 boards + panels |

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
X  length   0 → 189    along the 2x10x189 side rims (front rim at X = -3)
Y  height   0 = grade, positive up
Z  width    0 → 120    across the 2x10x120 cross rims
```

Key elevations:

| Y (in) | What |
|---|---|
| -42 | bottom of the concrete piers |
| -38.5 | bottom of the 6x6 posts |
| 0 | grade |
| 2.25 – 11.5 | lower rim, side and cross, both 2x10 |
| 59.34 | foot of the knee braces |
| 98 – 107.25 | deck-level rim *and* floor joists, side and cross, all 2x10 |
| 105.5 | top of the 6x6 posts **and the rim** — flush now, see §4.11 |
| 107.25 – 108.25 | 1 in OSB room floor, full footprint (see §5) |
| 108.25 | wall base — bottom plates land on top of the floor, not the bare rim |
| 109.75 – 198.5 | side wall studs (88¾ in — grew from 87, see §4.7) |
| 109.75 – 191.25 | gable wall studs (81½ in) |
| 200 – 203.5 | 4x4 eave beams |
| 241.5 – 247 | 4x6 ridge beam |
| 253 | roof peak |

Post centres: **X = 2.75, 94.5, 186.25** · **Z = 4.5, 115.5**
(middle post centred — equal 91¾ in spans; see §4.1)

---

## 4. Discrepancies found in the source drawings

**These are unresolved and matter. Do not silently "fix" them — they need
Marshall's call.**

### 4.1 The middle posts — resolved by centring

The two source drawings disagreed by 4 in. The floor plan put the middle posts
**96½ in** from the landing end; the side elevation put them **92½ in**. Both
agreed the post was off-centre by 4 in but disagreed on the direction, because
**the side elevation is mirrored** relative to the plan.

**Resolved: the posts are now dead centre at X = 94.5**, giving equal 91¾ in
spans. That is exactly halfway between the two conflicting values, so it is
consistent with both drawings being 2 in off in opposite directions.

Alternative reading, if it ever matters: centring on the *overall* deck
(X −3 to 190.5) would put them at 93.75, but that gives unequal spans of 91.0
and 92.5. Equal spans is the structurally meaningful choice.

**Consequence — see §4.6.**

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

### 4.6 A room joist on the centre-post line — resolved

The 16 in o.c. room joist run put a joist at X 93.05 – 94.55, and the centred
posts occupy X 91.75 – 97.25. The joist sat inside the post, overlapping 5½ in
vertically (post tops out at 105.5; the joist zone is 98 – 107.25).

The clash existed at every candidate post position:

| Post position | Overlap |
|---|---|
| elevation, 92.5 | 1.50 in |
| plan, 96.5 | 0.80 in |
| plan as measured, 97.2 | 0.10 in |
| centred, 94.5 | 1.50 in |

**Resolved by hanging that joist off the posts.** Both centre posts sit on the
same line (X 94.5, Z 4.5 and 115.5), so the joist was shifted onto the post
centreline and cut to the clear span between their inner faces:

- **2x10x105.5**, spanning Z 7.25 → 112.75, at X 93.75 – 95.25
- Both ends bear on 6x6 rather than a hanger on a rim — the strongest joist in
  the deck
- Still cut from a 10 ft board, so the purchase list is unchanged
- Bays either side are now **exactly 16**, because the whole run is pulled
  from this joist rather than from a rim — see below

Two options that do *not* work, for the record: trimming the post tops to 100
leaves the 2x10 rim with no bolting surface, and notching the joist means
removing 5½ in from a 9¼ in member.

**The joist run is anchored on this joist.** It cannot move, so it is the only
sane datum: pull 16 in o.c. from X 94.5 and every interior bay lands at exactly
16, with the remainder split between the two ends — 14¼ in at the carrier, 14½
at the back rim, both comfortably under 16. Pull from the back rim instead and
the sixth mark falls at X 93, inside the posts; shuffling it clear opens a 17½
in bay beside it, which is worse than the problem it solves.

The numbers this replaced were scaled off the drawing and ran 16.6 and 15.3
either side of the centre joist, with 15.9s further along. All four room-floor
OSB seams still land on framing after the change: X 46 on the carrier, 94.5 on
the hung joist, 142 on the joist at 142.5, and 190.5 on the back rim.

**Confirmed by the revised plan** (`drawings/floor-plan-rev2.png`), which draws
the centre posts dead centre and the joist running between them. Measured against
the model: posts within **0.16 in** on both axes, room joists within **0.22 in**,
landing joists within **0.14 in**. Two deliberate differences remain:

- The revised plan draws the centre joist at X 93.92; the model puts it at
  **94.5**, exactly on the post centreline. That alignment is the whole point of
  hanging it off the posts, so the model's value is the intentional one.
- The doubled carrier — see §4.4, unchanged.

Note the drawing's own centre post is 0.31 in off equal spans (91.99 / 91.68).
The model is exactly equal at 91.75 / 91.75.

A landing joist was also grazing the far post by 1/16 in. That is inside the
plan's 0.22 in error bar, so it was nudged from Z 112.07 to 111.9 to clear.
The model now has **zero** joist/post clashes.

### 4.7 The king post lands inside the window header, not on a support — resolved

The ridge beam is a single 4x6 spanning the full 192 in roof length, carried
at each end by a king post that bears straight down onto that gable wall's
header. The king post has to sit at Z 58.25 – 61.75 — dead centre on the
wall's width, because that's where the ridge has to be for a symmetric gable
roof. It can't move.

The front window (Z 8.5 – 70.5, from the elevations) is off-centre on that
same wall. The king post's Z position falls inside the window's span, only
**~9–11 in from the window's far jack stud** and over 50 in from the near
one — the single heaviest point load on the wall was riding on the window
header, off to one side, uncaught until it reached that jack stud.

Three fixes were on the table:

- Narrow the window so its far jack stud lands under the king post (~62 in
  → ~50 in) — no new pieces, but a smaller window than the elevations draw.
- Add a support stud directly under the king post — structurally the most
  direct fix, but it lands inside the glass and splits the sill into a
  ~50 in pane and an awkward ~9 in sliver.
- Upsize the header so it doesn't need a dedicated support at all.

**Resolved by upsizing the header** — doubled 2x6 → doubled 2x8, window and
sill untouched. That didn't just drop in: a doubled 2x6 exactly filled the
5.5 in gap between the gable top plate and the eave beam with zero slack, so
the extra 1.75 in depth pushes everything above the wall studs up by that
much. Consequence, all derived from one shift:

- Side wall studs grew 87 → 88¾ in, so the eave stays level all the way
  round (the gable wall now reaches the same height a different way —
  shorter studs, deeper header — and the side walls have to match it)
- Eave beams, ridge beam, and king post height all ride up 1.75 in
  (king post itself is still 41½ in — both ends of its span moved together)
- Peak moved from 250¼ to 252 in (21 ft 0 in, up from 20 ft 10¼)

---

### 4.8 Nothing bears on the post tops — mitigated, not resolved

The posts stop at Y 105.5. The deck-level rims run Y 98 – 107.25 and lap the
**outside** face of each post, giving 5½ in wide × 7½ in tall of contact
(Y 98 – 105.50) per face. Nothing sits on a post top — not the rims, not the
joists, not the centre joist, which butts the post faces rather than crossing
them. As originally drawn, every pound of floor, wall, roof, snow and occupant
load reached the posts **through fasteners in shear**.

A rough tributary check puts on the order of 3,000 – 4,000 lb at the middle
posts, which is more than two ½ in bolts want to carry in this grain
orientation (load is perpendicular to grain in the rim, parallel in the post).
That number swings hard on snow load and lumber species — neither of which the
drawings state — and it ignores whatever the knee braces pick up, so it is an
order-of-magnitude figure, not a design value.

**Mitigated with bearing cleats** (section 2b): a 2x6 block cut to the post's
5½ in width, fastened flat to the post face with its top on the rim underside
at Y 98, so the rim lands on wood. Projection is 1½ in — exactly the rim
thickness, full bearing.

**Each cleat is mirrored by a backer on the opposite post face**, and that is
what earns the detail its keep: bolt through cleat, post and backer and the
fastener picks up **two shear planes instead of one**. Double shear is worth
roughly twice single shear per bolt, for the price of one extra block. Twenty
four blocks all told — twelve cleats, twelve backers, every one a 2x6 cut to
5½ in.

Ten bolt lines, verified continuous end to end with a cleat count matching the
backer count on every one:

| Where | Make-up | Material to bolt through |
|---|---|---|
| 6 side rims, one per post | 1 cleat + 1 backer | 1½ + 5½ + 1½ = **8½ in** |
| 2 back corner posts, cross rim | 1 cleat + 1 backer | 1½ + 5½ + 1½ = **8½ in** |
| 2 front corner posts, cross rim | 2 cleats + 2 backers | 3 + 5½ + 3 = **11½ in** |

The front pair is deeper because the front cross rim is a doubled 2x10 and both
plies want bearing. Its outer cleat is the one block in the model that does not
touch a post — it sits alongside the inner cleat and the bolt runs through both,
so the pair acts as one 3 in side member. Buy ½ in × 10 in for the eight shallow
lines; the two front ones are ½ in all-thread cut to length rather than an
off-the-shelf carriage bolt.

**Why not notch the posts.** Cutting a 1½ in × 7½ in notch so the rim bears
directly on the shoulder puts *no* fastener in the gravity path, which is
strictly better — but it lost on this build because the posts are already set:

- Ten notches, cut freehand eight feet up, all ten shoulders needing to land on
  one plane at Y 98. One shoulder ⅛ in low and that post carries nothing while
  its neighbours take its share. Cleats are set independently to a laser line
  and shimmed; notches are not.
- The four corner posts take notches on two adjacent faces, leaving a 4 × 4 in
  L-section over the top 7½ in — 47% off 30¼ sq in. Capacity is fine (16 sq in
  of DF in compression carries well over ten times the corner load) but an
  L-notch splits more readily than a flat one.
- The rim moves inboard 1½ in per side, so the nine 116½ in room joists become
  113½ (still out of 10 ft stock — a recut, not a reorder), the room narrows
  3 in, and about four more 2x10s go in to restore the outside face.
- A notch shoulder on an exterior PT post is a horizontal surface holding water
  against end grain. A cleat has the same exposure while being cheap, and
  replaceable.

### 4.9 Fasteners — bolts carry gravity, screws carry restraint

Two jobs, two fasteners. Once the cleats bear the rim, the rim's own fasteners
stop being the gravity path, so they do not need to be bolts.

**Cleats and backers — the gravity path. Bolt these.**
½ in through-bolts in double shear, washers both ends. Eight lines need 1½ +
5½ + 1½ = 8½ in of material, so ½ in × 10 in. The two front corner lines need
3 + 5½ + 3 = 11½ in — ½ in all-thread cut to length, not an off-the-shelf
carriage bolt. At the four corner posts a side-rim line and a cross-rim line
land on adjacent faces: offset them vertically so the bolts pass rather than
intersect inside the post.

**Rims to posts — restraint, not gravity. Screw these.**
The screws hold the rim tight to the post, resist lateral and racking load, and
stand as backup if a cleat bolt slips. Screws beat bolts at withdrawal, which
is what this now needs, and they avoid putting a second ½ in hole through the
post. Net: two through-holes per post instead of four.

**Screw spec — SPAX PowerLag XF, 5/16 × 6 in, T-Star washer head.** Menards
model XFP312P600-BX1, sold singly. Replaces the Grip Fast #16 this section
originally specced.

| | |
|---|---|
| Diameter | 0.315 outside thread · 0.217 shank · 0.189 minor |
| Drive | T-40, no pilot hole |
| Report requires | 2⅜ in minimum penetration into the main member |
| Our condition | 4½ in at the side rims, 3 in at the doubled front cross rim — **both clear it** |
| Coating | HCR, an ICC-recognised alternate to HDG ASTM A153 Class D, **explicitly evaluated for ACQ-D treated wood** (Exposure Condition 3) |

**This closes the approval caveat.** The Grip Fast was covered by a DrJ report —
accredited, but not what every engineer or building department will work from.
SPAX PowerLags are **ICC-ES listed (ESR-1782)**, the format nobody argues with.
That is worth more here than the extra diameter.

**One screw length now does everything.** The Grip Fast needed a #18 × 8 at the
two front corner posts, because a 6 in screw reached only 3 in past the doubled
cross rim. 3 in clears the SPAX minimum of 2⅜, so the whole job is one part.

**But the spacing got tighter, and it costs two screws a face.** ESR-1782
Table 6 wants **1¾ in edge distance** for the 5/16 series against ⅝ for the
Grip Fast — nearly three times:

| | Grip Fast #16 | SPAX 5/16 |
|---|---|---|
| Edge distance | 5/8 | **1 3/4** |
| Between fasteners in a row (⊥ grain) | 2 1/8 | 2 3/16 |
| Between rows, in-line | 1 1/8 | 1 1/8 |

On a 5½ in post face, 1¾ in a side leaves **2 in of usable width**. Two columns
at the 1⅛ in between-rows spacing fit; a third would breach the edge distance.
Three rows fit in the 9¼ in lap. So **six per face, not eight** — 60 screws
across the ten faces instead of 80.

As built: columns 2³⁄₁₆ in off each post edge, rows 2⁷⁄₁₆ off the rim's top and
bottom, every Table 6 minimum met with margin. The layout is derived from those
figures, so it re-solves if the lap or the post width ever change.

**A bigger screw is not automatically fewer screws.** Both products publish
lateral values in the same ballpark at a 1½ in side member, because what governs
is the thin rim, not the fastener. What the SPAX buys is the ICC-ES listing, the
single part number and a stiffer fastener — not a licence to use fewer. The
count still comes from the engineer.

**Still open — get the counts sized.** The detail is ordinary; a face-mounted
ledger carrying load in fastener shear is how every deck bolted to a house
works. What is unsized is *how many* of each, because the snow load and the
lumber species are both absent from the drawings. What the model draws is the
geometric maximum the spacing rules allow, not a design — if the engineer comes
back wanting fewer, delete rows; if more, the answer is a bigger screw, not a
tighter grid.

### 4.10 The ridge is structural, and the 4x6 was undersized

The rafters bear **on top** of the ridge and there are no ceiling joists, rafter
ties or collar ties anywhere in the model. That makes it a true structural ridge
beam carrying half the roof, not a ridge board. It spans **11.46 ft clear**
between the two king posts with a 55.6 sq ft tributary, and as drawn it was a
4x6 — which only works if total load stays under about 25 psf.

| Option | Actual | S in³ | Max total load |
|---|---|---|---|
| 4x6, as drawn | 3.5 × 5.5 | 17.6 | ~25 psf (10 dead + 15 snow) |
| **doubled 2x8 — built** | 3 × 7.25 | 26.3 | **~34 psf (10 + 24)** |
| doubled 2x10 | 3 × 9.25 | 42.8 | ~51 psf (10 + 41) |
| doubled 2x12 | 3 × 11.25 | 63.3 | ~69 psf (10 + 59) |

**Built as a doubled 2x8** (Marshall's call). Two plies rather than a solid
timber is the right shape for this build anyway: you lift one 56 lb ply, land it
on the king posts, then bring the second up alongside — no single heavy piece
20 ft in the air.

**Depth, not width.** S = bd²/6, so depth counts twice. A 7 in wide × 7.25 deep
build-up gives 61.3 in³; 3 in wide × 11.25 deep gives 63.3 for barely half the
wood. Width is capped anyway — see below.

**Why the ridge can't get wider.** The rafter seat notch is cut from the ridge's
near face, so ridge width drives notch depth:

| Ridge width | Plumb cut at z | Notch ⊥ | % of a 2x6 |
|---|---|---|---|
| 3.0 in (built) | 58.50 | 1.57 in | **28%** |
| 3.5 in (the old 4x6) | 58.25 | 1.71 in | 31% |
| 7.0 in (two 4x8s) | 56.50 | 2.76 in | 50% — over |

At 3 in the notch is *better* than what the 4x6 gave. Anything near 7 in breaks
the one-third rule and would force hung rafters instead of seated ones — a
different roof. It would also overhang the 4x4 king posts by 1¾ in a side.

**Geometry-neutral, the same trick as the rim.** Depth grows downward from
`RIDGE_TOP` at Y 247, so rafters, walls, sheathing and the 253 in exterior peak
do not move. Only the king posts shorten, 41.5 → **39.75**, and their length is
derived from `RIDGE_BOT` now rather than typed. Interior peak goes 11 ft 1 in →
**10 ft 11½ in**.

**Still open — confirm against a real snow load.** 24 psf of roof snow is four
feet of dry powder or ten inches of wet, and the case that breaks roofs is the
second one: a settled pack that gets rained on. This structure is unheated, so
it holds snow longer than a house roof does — codes assign unheated buildings a
*higher* snow load for exactly that reason. Call the county building department
for the design ground snow load; that one number settles it.

**If it comes back over 24 psf**, the upgrade is trivial and already sized above:
same 3 in width, same seat detail, deeper plies. 2x10 buys 41 psf, 2x12 buys 59.
Only `RIDGE_D` and the king post length change, and both are derived.

### 4.11 The deck now sits on the post tops, and three typed elevations became derived

The deck used to sit at Y 107.25 while the posts topped out at 105.5 — 1¾ in
proud. Not a consequence of any change we made; the rim top has always been
107.25, and 105.5 is just `144 − 38.5`, a 12 ft post buried 38½ in. The two
numbers came from different places and never met.

That gap cost two things: the rim lapped only **7½ in** of the post instead of
the 9¼ in it has, and the post tops sat as bare end grain in a pocket with
nothing over them — the worst place to leave treated end grain outdoors.

**`DECK_TOP` is now `POST_TOP`**, itself derived as `POST_LEN − POST_BURY`.
Everything above drops 1¾ in as one piece; peak 253 → **251¼**. The lap becomes
the full **9¼ in**, which fits a fourth row of screws (3 → 4 per post face, 80
screws instead of 60), and the OSB floor now covers the post tops.

**Measure your posts before trusting 105.5.** They are already set, so the real
tops are whatever they are. Reference the deck to the *lowest* one.

#### The knee braces were wrong, and it was the same bug

A true 45° needs `BR_FOOT_Y + BR_RUN === BR_TOP`. It did, when the rim was a 2x8
and `BR_TOP` was 100. Widening the rim to 2x10 (change 13) dropped `BR_TOP` to
98 and left the foot typed at 59.34 — **off by exactly the 2 in the rim bottom
moved.** Every brace was 43.56°, the long edge 56.10 in against a 57½ label, and
the saw panel still said 45°. Twelve braces cut to that would not have fitted.

Fixed by deriving `BR_FOOT_Y = BR_TOP − BR_RUN`. Now a true **45.000°**, long
edge **57.502**, short edge **46.499** — the labels became right by themselves.

#### Three hardcoded elevations, one bug

This is the third time a typed elevation has drifted after something below it
moved: the joist Y (change 18), the knee-brace top (change 13), and now the
rafter baseline. `U(z)` had a typed 201.604 that had been hand-edited twice, and
`RIDGE_TOP` a typed 247 — which is why lowering the deck used to collapse the
birdsmouth from 1.90 in to 0.15 in. Both now derive:

| Was | Now |
|---|---|
| `U(z) = 201.604 + TT·z` | `U(z) = EAVE_TOP − BIRDSMOUTH + TT·z` |
| `RIDGE_TOP = 246 + FLOOR_T` | `RIDGE_TOP = U(RIDGE_Z0) + RIDGE_NOTCH` |
| `SEAT_END = (EAVE_TOP − 201.604)/TT` | `SEAT_END = BIRDSMOUTH/TT` |

Both notch depths held at 28% of rafter depth through the move, which is the
check that the derivation is right.

#### Two things the re-verification turned up

**OSB panels were modelled 1½ in thick.** `sheet()` never passed a thickness, so
every wall and gable panel took `prism()`'s default instead of the ¼ in it
should be. Invisible because the skin renders translucent, and older clash
checks only compared boxes to boxes. Fixed — it now passes `OSB`.

**The gable sheathing needs notching, and always did.** The ridge and both eave
beams run X 11.5 → 203.5, straight through both gable walls, to carry the rake
overhangs. The model draws the gable panels as full rectangles, so the exact
clash check reports the notch as an overlap. Cut when you get there:

- **3 × 7¼ in** for the ridge, twice per gable
- **3½ × 3½ in** for each eave beam, twice per gable

### 4.12 Front wall openings — 36 × 48 window, 36 × 80 door, evenly spaced

The model had a **62 × 50** window R.O. taking nearly the whole bay, and a
**34 × 83** doorway. Both replaced, and the pair laid out so the front reads
evenly (Marshall's call).

| | Was | Now |
|---|---|---|
| Window unit | — | 36 × 48 |
| Window R.O. | 62 × 50 | **37 × 49** (Z 15 – 52) |
| Door unit | — | 36 × 80 |
| Door R.O. | 34 × 83 | **38 × 83** (Z 67 – 105) |
| Sill height above floor | 33 | **34** |

**Rough openings.** Window at unit + 1 in each way — ½ in of shim per side.
Generous but standard, and generous is the right direction: you can shim a gap,
you cannot stretch a frame. **Confirm against the unit's own literature before
cutting**; manufacturers publish the required R.O. and it varies (+½ and +¾ are
both common). Door at unit + 2 in wide, which is the usual pre-hung allowance.

**Even spacing, measured across the face.** Three pieces of solid wall — corner
to window, window to door, door to corner — all at **exactly 15 in**:

```
|--15--| window 37 |--15--| door 38 |--15--|      = 120
```

Measured across the **120 in face as seen from outside**, not the 113 in of
front-wall framing. The face is what reads as even from the yard, and it happens
to divide cleanly: (120 − 37 − 38) / 3 = 15.

**Heads line up by construction.** Both openings top out at the underside of the
2x4 top plate, Y 189.5, and neither needs a header of its own — the doubled 2x8
gable header already spans the full 113 in wall above them (§4.7). The
consequence is that the door R.O. comes out **83 in against the 82½ a 36 × 80
pre-hung normally wants**. Shim the half inch at the head. Do *not* add a door
header to get 82½ exactly: that drops the door head below the window head and
loses the alignment that was the point.

**Framing.** Six studs where there were seven: two corners plus a king each side
of both openings, at Z 3.5, 13.5, 52, 65.5, 105, 115. No solid bay exceeds 16 in
(8½ / 12 / 8½ of clear framing). Doubled 2x4x37 sill; two cripples at 16 o.c.,
centred, giving 9¾ / 14½ / 9¾. Bottom plate runs in two pieces broken by the
doorway, 63½ and 11½.

**This also fixes what §4.7 was worried about.** The king post at Z 58.25 – 61.75
used to land over the 62 in window opening, with only the doubled header
carrying it. It now sits over **solid wall between studs 12 in apart**. The
header upsize from §4.7 stays — it is doing other work — but the point load it
was sized for is no longer hanging over a hole.

**Watch the sheathing layout.** The gable sheathing seams sit at Z 24 and Z 96,
which now fall inside the window and the door respectively. The model draws the
gable panels as full pentagons without opening cutouts, so it will not flag
this — re-pick the seams onto studs when you lay the panels out. Related: §4.11
already notes the panels need notching around the ridge and eave beams.

**Everything derives from `WIN_W` and `DOOR_W`,** so a different window or door
is a two-number change and the 15 in spacing re-solves itself.

### 4.13 Floor sheathing seams have to land on a joist *centreline*

The room floor's sheet seams were picked from the 48 in sheet module: breaks at
`RM_X0+96` = 142 and `RM_X0+48.5` = 94.5. The second was fine. The first was
not — the joist there is centred on **142.5**, so a seam at 142 left the sheet
on its low side with **0.25 in of bearing**. Touching a joist is not the same as
landing on one; a quarter inch is nothing to nail into.

**Cause.** The joist run is anchored on the hung centre joist at X 94.5 (§4.6)
and the room starts at X 46. 94.5 − 46 = **48.5**, so every joist centreline
sits half an inch off the 48 in sheet module. No full sheet edge can ever land
on one.

**Fix: move the seams, not the joists.** Only three centrelines work as a
two-piece course, i.e. with neither piece over 96 in:

| Seam | Pieces |
|---|---|
| **94.5** | 48.5 + 96 |
| 110.5 | 64.5 + 80 |
| **126.5** | 80.5 + 64 |

Courses 1 and 3 break on **94.5**, course 2 on **126.5**. Every seam now sits
dead centre on a joist — 0.75 in of bearing each side — the outer edges land
flush on the carrier at X 46 and the back rim at X 190.5, and no four sheet
corners meet. Still six pieces from five sheets; the purchase list does not
move. Seams derive from the joist list now, so they cannot drift apart again.

**Both seams are carried along their whole 120 in run**, which is worth
recording because the 94.5 one leans on §4.11:

| Z | What carries the 94.5 seam |
|---|---|
| 0.25 – 1.75 | side rim |
| 1.75 – 7.25 | **6x6 post top** |
| 7.25 – 112.75 | the hung centre joist |
| 112.75 – 118.25 | **6x6 post top** |
| 118.25 – 119.75 | side rim |

The hung joist stops at the post faces, so for 11 in of that seam the bearing is
the post tops themselves — which only became usable when the deck dropped flush
with them (§4.11). Before that they sat 1¾ in low and the seam would have been
unsupported there. Two 0.25 in slivers at Z 0 – 0.25 and 119.75 – 120 have
nothing under them; both sit under the side wall bottom plate.

### 4.14 The room is 144 in, not 144.5 — and that half inch was costing a sheet

Marshall's observation: a full 48×96 plus a 48×48 is exactly 144, so why is the
floor being cut with a 48.5 in rip? Because the room front face was at X 46 and
the back at 190.5, making it **144.5**. Every sheet module therefore landed half
an inch off the framing:

| Sheet edge | Room = 144.5 | Room = 144 |
|---|---|---|
| `RM_X0 + 48` | X 94.0 — misses, joist is at 94.5 | X 94.5 — **joist centreline** |
| `RM_X0 + 96` | X 142.0 — misses, joist is at 142.5 | X 142.5 — **joist centreline** |
| `RM_X0 + 144` | half an inch short of the back | **the back face exactly** |

**Front face moved X 46 → 46.5.** The back stays put: the back wall sits at
187 – 190.5 and needs the floor under its full width. §4.4 records that the
drawings disagree about the front face anyway — the plan scales it at 45.3, the
elevations at 46.0, and the model had split the difference. 46.5 is another half
inch past the elevations, inside the same noise. The landing gains ½ in.

**What it bought:**

| | Was | Now |
|---|---|---|
| Room floor sheets | 5 | **4** |
| Floor waste | 25% | **6%** |
| Ripped floor pieces | 4 | **0** |
| Side wall plates | 2x4x144.5, forcing 14 ft stock | **2x4x144 from 12 ft** |

Every floor piece is now a full 48×96 or a half 48×48. Courses run 96 + 48 and
48 + 96, staggered, seams on X 142.5 and 94.5 — both joist centrelines with ¾ in
of bearing each side.

**It also fixed the side walls, which had the same defect.** Their sheathing
broke at X 46, 142 and 94.5 against studs at 46, 70, 94… — the 142 seam had the
same quarter-inch bearing problem the floor did (§4.13), and nobody had looked.
Side wall studs now run **flush at both corners with the 24 in o.c. marks pulled
from the wall end**, which is how you would lay it out on the deck anyway:

```
near edges  46.5  69.75  93.75  117.75  141.75  165.75  189.0
centrelines 47.25 70.50  94.50  118.50  142.50  166.50  189.75
```

A 48 in sheet edge lands on 94.5 and a 96 in one on 142.5 — both stud
centrelines, ¾ in each side. Same rule as the floor, and both derive from
`RM_X0` now rather than being typed.

**Joist spacing is untouched.** 62.5, 78.5, 94.5, 110.5, 126.5, 142.5, 158.5,
174.5 — exactly as before. Only the datum they are measured from moved.

### 4.15 Openings v2 — 32×80 door, a side window, and king/jack at every edge

Three changes, all Marshall's call.

**Door 36×80 → 32×80**, rough opening **34 × 83** (unit + 2 wide, head still at
the top plate). **Front window stays 36×48**, R.O. **37 × 49**. **New 60×48
window on the Z = 0 side wall**, R.O. **61 × 49**.

**Front spacing.** Equal solid wall at both ends and between the openings,
measured across the 120 in face: `(120 − 37 − 34)/3` = **16⅓ in** each. Exact,
but not a round number — that is what exact rough openings and equal margins
give you together. If a round margin matters more than the standard +2 door
R.O., a 35 in door opening lands it on 16 in flat; say the word.

**King and jack at every opening edge**, which is what Marshall asked for. Worth
being precise about what the header is here, because the front wall is unusual:

> The gable studs are **81.5** tall and the door R.O. is **83**. The opening is
> *taller than the studs*, so it necessarily runs to the top plate and there is
> no room for a header beneath it. The doubled 2x8 that spans the whole 113 in
> wall above the plate (§4.7) **is** the header for both openings.

So on the front wall the jack does not stop under a header the way it would in a
normal wall — king and jack are both full height, and together they put **3 in
of bearing** under the plate at each edge instead of 1.5. Four kings, four
jacks, two corner studs.

**The side wall can take a real header, and does.** Its studs are 88.75 against
the gable wall's 81.5, so putting the head at 189.5 — level with the front
window and door — leaves exactly `STUD_TOP − 189.5` = **7.25 in**, which a
doubled 2x8 fills dead flush to the top plate with no cripples needed above.
Jacks stop under it at 81.5 and carry it; kings run the full 88.75. The two
plies sit at Z 0–1.5 and 2–3.5, the ½ in gap being the usual plywood spacer that
makes a doubled 2x8 match a 3½ in wall.

All three openings now line up **top and bottom**: heads at 189.5, both window
sills at 140.5.

**Sills bear now.** This was the actual complaint — a doubled 2x4 sill spanning
between jacks with only mid-span cripples is end-nailed, not supported.
`crippleRun()` always puts a cripple hard against each end so the sill sits on
something, then fills any gap over 16 in. Front window: 4 cripples. Side window:
6, because it also needs one under each sheathing seam.

**The side window swallows both side-wall sheathing seams.** `RM_X0+48` = 94.5
and `RM_X0+96` = 142.5 both fall inside a 61 in opening. Above the head the
header, plate and eave beam back them; below the sill nothing would, so two of
the six cripples are placed at exactly those centrelines. Same rule as §4.13.

**Lumber.** Six more studs on the front, plus the side window's kings, jacks,
header, sill and cripples. 2x4 goes 32 → 36 boards, 2x8 6 → 7.

**Note on the renders that prompted this.** Both were stale — the ridge shown as
a 4x6 (it is a doubled 2x8 since §4.10), studs 2 in short throughout, and the
side plates at 144.5 (they are 144 since §4.14). The framing above came from the
model's own geometry.

### 4.16 The wall height is derived from the door, and §4.7 is superseded

Marshall's question — *what rough opening should the door actually be, and can
we then shorten the studs to suit?* — is the right way round, and following it
dissolved the "front wall can't have a header" problem entirely.

**Door R.O.: 34 × 82½** for a 32 × 80 exterior pre-hung. +2 wide is the standard
pre-hung allowance; +2½ tall covers head jamb, threshold and shim. The 2 in
figure that also gets quoted is the *interior* number — no threshold. Erring
loose is shimmable; erring tight means cutting a header out of a built wall, so
on something framed once, take 2½. **If the plan is a slab in a site-built jamb,
2 in is right and `DOOR_RO_H` is a one-number change.**

**The wall height now derives from that**, instead of 88.75 and 81.5 being typed:

```
floor                        106.50
+ bottom plate 1.5           108.00   <- studs start
+ door R.O. 82.5   -> head   189.00   <- all three openings head here
+ doubled 2x8 header 7.25    196.25   <- studs end
+ top plate 1.5              197.75   =  PLATE_TOP
```

`STUD_LEN` = 88.25, `JACK_LEN` = 81.0. **One stud length for every wall** —
sides and gables both — where before the gables were 7.25 shorter to make room
for the wall-length beam.

| | Was | Now |
|---|---|---|
| Door R.O. height | 83 (½ proud) | **82.5** |
| Side wall studs | 88.75 | **88.25** |
| Gable wall studs | 81.5 | **88.25** |
| PLATE_TOP | 198.25 | 197.75 |
| Peak | 251.26 | **250.76** |

Everything above the plate is derived, so the roof simply follows the half inch
down. Nothing above it needed touching.

**Every opening now has a real header.** Doubled 2x8, bearing on jacks, filling
exactly to the top plate so no cripples are needed above — the same detail on
all three. Kings run the full 88.25, jacks stop at 81.0 under their header. This
is what Marshall was asking for from the start; it was blocked only because the
old 81.5 in studs were shorter than the 83 in door opening, leaving nowhere to
put one.

**§4.7 is superseded.** It put a wall-length doubled 2x8 above the plate because
the king post landed inside the old 62 in window. Its own preferred fix was:

> *"Add a support stud directly under the king post — structurally the most
> direct fix, but it lands inside the glass."*

The openings moved in §4.15, so the king post now sits over solid wall and that
fix is available. A **4x4 post** runs from the bottom plate to the top plate
directly under each king post, aligned in Z, separated only by the 1.5 in plate.
Load path is now ridge → king post → 4x4 → bottom plate → doubled carrier joist
→ side rims → 6x6 posts: a continuous column rather than a point load spread
through a beam. The four `2x8x113` wall beams are gone.

**Lumber.** 2x4 +1 board, 4x4 +1 (a 14 footer for the two 88.25 posts). 2x8
unchanged — the four 113 in beams are replaced by two 40 in and two 37 in
headers, which fit the boards already on the list.

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
253 — see §4.7 and §5's floor change, both unrelated later changes).

**Joist depth = 2x10, matching the rim.** A top-down view cannot show depth and
there are no labels, so this is still a read rather than a measurement —
**confirm before buying.**

The joists were 2x8 when the rim was 2x8. Widening the rim to 2x10 (change 13)
left them behind: tops flush on the DECK_TOP line, bottoms 2 in shy of the rim
bottom. Structurally that was fine — 2x8 at 16 in o.c. over 116½ in is well
inside deck span tables for any common species, and the wall loads never land
mid-span (the side walls at Z 0 and Z 120 sit over the side rims, the back wall
over the cross rim, the front wall over the doubled carrier at X 46/47.5). But a
rim deeper than the joists is the unusual case in ordinary framing; it normally
only appears when the rim is doubling as a beam or a ledger was upsized for bolt
room, and neither applies here. Matched at 2x10 the floor is stiffer under a
room that carries walls and a roof, the underside is a flat plane if it is ever
soffited, and there is one hanger size instead of two.

Tops stay on the DECK_TOP line, so the extra depth extends downward and nothing
above moves. Joist depth is now derived from `DECK_TOP` in code rather than
hardcoded at 100.0 — the same trap the knee-brace top height fell into when the
rim moved. Verified against every other layer: **zero clashes**, and the knee
braces still top out at exactly Y 98, butting the joist bottoms rather than
crossing them.

**Order 2x10 hangers, not 2x8** (LUS210 in place of LUS28) for the sixteen
hung joists — the centre joist at X 94.5 bears on the posts and needs none.

**Gable infill studs added.** The gable triangle above the header was empty —
top plate, doubled 2x8 header, and the 4x4 king post, with nothing between the
king post and the corners. Four 2x4s per gable now fill it, at Z 24, 48, 72, 96:
24 in o.c. and symmetric about the ridge at Z 60.

These are non-bearing. The ridge beam's end reaction goes down the king post;
the infill studs back the sheathing and take wind on the gable face. The layout
was chosen so the outer pair sits under the gable sheathing's vertical joints
(Z 24 in course 2, Z 96 in course 3) and the inner pair clears the king post by
9½ in each side, with symmetry meaning both slopes share the same two lengths.

Lengths are derived from the rafter underside, not typed in — each stud runs
from the header top up to `UND(z)`, which is why the roof line (`U`, `TT`,
`EAVE_TOP`) now sits above section 5 in the file rather than in section 6. They
come out at **20 in** and **37⅞ in** measured to the long point, rounded up to
the next 1/8 so the piece is cut proud and trimmed to fit.

**The top cut is a bevel across the 1½ in thickness, not the 3½ in face.** The
slope runs in Z, which is the stud's thin dimension, so the board goes through
the saw *on edge* — 3½ face against the fence, 1½ on the table — with the miter
at the pitch (36.6°). Flat on the table bevels the wrong dimension. Foot stays
square, bearing on the header. One bevel per stud, same setting for both slopes.

**Room floor is 1 in OSB, not decking** (Marshall's call). The landing keeps
its 1x5½ decking boards; the enclosed room gets 1 in OSB sheathing instead —
laid as real 4x8 sheets, three courses, seams staggered, the same way the
wall/roof skin is. Six cut pieces come out of five sheets; see §6 and §7.

**The walls stand on top of the floor, not beside it** (Marshall's call —
this replaces the original "decking runs inside the walls" read of the
elevations, where the wall bottom plates sat directly on the rim at 107.25
with nothing beneath, and the 1 in decking that used to fill the room ended
up ½ in below the top of the 1½ in bottom plate — a lip around the room
perimeter). The OSB now runs the full 144.5 × 120 room footprint, under the
wall lines too, and the wall bottom plates land on top of it (WALL_BASE =
108.25) instead of on the bare rim (107.25). Consequence: everything from the
studs up — plates, eave beams, ridge, king posts, peak — moved up 1 in on
top of the §4.7 shift. The rafter notch geometry itself is untouched; the
whole roof just translated up with the walls.

**¼ in OSB is thin for this frame.** Studs and rafters are both 24 in o.c.,
where sheathing is normally 7/16 in. Drawn at ¼ as requested. Sheet count is
identical either way, so stepping up costs only the price difference. Flagged,
his call.

**Deck board gap = ¼ in**, giving a 5¾ in pitch. Not specified anywhere.

**Rake-ladder blocks stand on edge.** The 2x4x33 blocks that frame the gable
overhang over the landing are rotated to the roof pitch like the rafters, and
now stand on edge (3.5 in vertical, 1.5 in horizontal) so it's the narrow 1½
in face that follows the slope — the same way the rafters themselves meet the
roof plane, rather than lying flat with the wide 3½ in face against it. Two
per side, evenly spaced 17 in apart from the wall line and from each other
(were 16.4/33.9 — close to even but not exact).

---

## 6. Member schedule

Framing, as drawn:

| Size | Qty | Role |
|---|---|---|
| 6x6x144 | 6 | posts, 38½ in below grade |
| 2x8x192 | 2 | ridge beam — doubled 2x8, structural (§4.10) |
| 4x4x192 | 2 | eave beams |
| 4x4x39.75 | 2 | king posts under the ridge (shortened for the deeper ridge, §4.10) |
| 2x10x189 | 4 | side rims, deck and lower level (widened from 2x8, top kept flush) |
| 2x10x120 | 5 | cross rims — front pair, back, two low (widened from 2x8, top kept flush) |
| 2x10x116.5 | 9 | room floor joists + doubled wall carrier (widened from 2x8 to match the rim) |
| 2x10x105.5 | 1 | centre joist, hung off the 6x6 posts (§4.6) |
| 2x10x46.5 | 7 | landing joists, turned 90° |
| 2x8x40 | 2 | front window header, doubled |
| 2x8x37 | 2 | front door header, doubled |
| 2x6x92 | 20 | rafters, 24 in o.c. (see §4.3 — really 93.1) |
| 2x6x57.5 | 12 | 45° knee braces |
| 2x6x5.5 | 24 | 12 bearing cleats + 12 double-shear backers, at the posts (§4.8) |
| 2x4x144 | 4 | side wall plates (§4.14) |
| 2x4x113 | 3 | gable top plates + back bottom plate (see §4.2) |
| 2x4x88.25 | 25 | wall studs — sides, gables, and every opening king (§4.16) |
| 2x4x81 | 6 | jacks — 4 front openings + 2 side window |
| 4x4x88.25 | 2 | posts under the king posts (§4.16) |
| 2x8x64 | 2 | side window header, doubled (§4.15) |
| 2x4x61 | 2 | side window sill, doubled |
| 2x4x66.125 | 1 | front bottom plate, corner to the doorway |
| 2x4x37 | 2 | front window sill, doubled — 36×48, 37×49 R.O. (§4.15) |
| 2x4x37.875 | 4 | gable infill studs, inner pair — bevelled top, long point |
| 2x4x33 | 4 | rake-ladder blocks |
| 2x4x29 | 10 | cripples — 4 front window, 6 side window |
| 2x4x20 | 4 | gable infill studs, outer pair — bevelled top, long point |
| 2x4x12.875 | 1 | front bottom plate, doorway to the corner |
| 1x5.5x120 | 9 | landing boards, turned 90° |
| OSB ¼ | 22 sheets | walls, gables, roof |
| OSB 1 | 6 panels, cut from 5 sheets | room floor sheathing, full footprint (was 1x5.5x137.5 decking) |

Layout positions:

- Rafters, X: 11.5, 46, 59.5, 83.5, 107.5, 131.5, 155.5, 179.5, 188.5, 201.5
- Side wall studs, near edges X: 46.5, 69.75, 93.75, 117.75, 141.75, 165.75,
  189 — flush at both corners, 24 o.c. marks pulled from the wall end so the
  centrelines land at 47.25, 70.5, 94.5, 118.5, 142.5, 166.5, 189.75 (§4.14)
- Front gable studs, Z: 3.5, 13.33, 14.83, 53.33, 54.83, 66.67, 68.17, 103.67,
  105.17, 115 — two corners plus a king AND jack each side of both openings
  (§4.15)
- Back gable studs, Z: 3.5, 27.5, 51.5, 75.5, 99.5, 115 (24 o.c.)
- Room joists, X: 62.5, 78.5, 110.5, 126.5, 142.5, 158.5, 174.5 — a **true**
  16 o.c. run pulled from the hung centre joist at 94.5 (§4.6), plus a doubled
  carrier at 46.0 / 47.5
- Landing joists, Z: 16.87, 32.79, 48.72, 64.64, 80.65, 96.66, 111.9 (16 o.c.,
  last bay is a 7 in half-bay)
- Gable infill studs, Z: 24, 48, 72, 96 (24 in o.c., symmetric about the
  ridge at Z 60) — four per gable, both gables the same
- Front window R.O. Z 16.33 – 53.33, Y 140.5 – 189.5 (37 × 49); doubled sill
  137.5 – 140.5; 4 cripples at Z 16.33, 28.17, 40.0, 51.83
- Door R.O. Z 69.67 – 103.67, Y 106.5 – 189.0 (34 × 82.5); doubled 2x8x37
  header 189.0 – 196.25 (§4.16)
- Side window R.O. X 88 – 149, Y 140.5 – 189.5 (61 × 49) on the Z = 0 wall;
  kings at X 85 and 150.5, jacks 86.5 and 149, doubled 2x8x64 header
  189.5 – 196.75, doubled sill 137.5 – 140.5, 6 cripples at X 88, 93.75,
  109.75, 125.75, 141.75, 147.5

---

## 7. Purchase list

**Framing — 105 boards, 1,092 linear feet**

| Size | Buy |
|---|---|
| 2x4 | 17 @ 8' · 4 @ 10' · 7 @ 12' · 2 @ 14' · 7 @ 16' |
| 2x6 | 22 @ 8' · 6 @ 10' |
| 2x8 | 4 @ 10' · 1 @ 12' · 2 @ 16' |
| 2x10 | 4 @ 16' · 15 @ 10' · 4 @ 8' |
| 4x4 | 1 @ 8' · 1 @ 14' · 2 @ 16' |
| 6x6 | 6 @ 12' |

*(Two of the three 113 in 2x4 plates were missing from the rail, and the buy
list was built off that wrong count — a packing check showed two 144½ in plates
with nowhere to go. Fixed above with two more 10 ft 2x4s, one plate each. The
twenty-four cleats and backers are 132 in of stock all told — an 8 ft 2x6
yields seventeen, so budget two boards and keep the offcuts. It was 90
boards / 936 lf before the gable infill studs — those added
three 8 ft 2x4s: one yields the four 20 in outer studs, two yield the four
37⅞ in inner studs, two per board.)*

**Fasteners — 80, all HDG-equivalent or better.** Six screws per face rather
than eight: the 5/16 SPAX needs a 1¾ in edge distance where the Grip Fast
wanted ⅝, and that is what a 5½ in post face runs out of (§4.9).

| Item | Qty | For |
|---|---|---|
| SPAX PowerLag XF 5/16 × 6, T-Star washer head | 60 | every rim-to-post face — six per face (§4.9) |
| ½ × 10 HDG bolt | 16 | eight cleat lines, 8½ in of material each |
| ½ all-thread, ~14 in | 4 | two front corner cleat lines, 11½ in of material |
| ½ washers / nuts | 40 / 20 | two washers per bolt |

**Decking — 9 boards, 90 linear feet:** 9 @ 10 ft, landing only. The room
floor is OSB now, see below — it used to be 20 more boards at 12 ft.

**Sheathing — 620.3 sq ft:** the model computes this from the actual clipped
panels; 19.40 sheets of straight area, drawn as **30 cut pieces**. The layout
figure of 22 sheets (buy 23) is *not* derived from the model — it is a hand
estimate and the gable triangles are where the waste goes. Re-derive it if the
wall heights move again; the area above already grew from a stale 614½ that
predated the §4.7 and §5 shifts.

**Room floor — 1 in OSB, 4 sheets:** the floor now runs the full 144.5 × 120
room footprint (walls stand on top of it, see §5). Six pieces and **not one of
them ripped in X** (§4.14): 96×48, 48×48, 48×48, 96×48, 96×24, 48×24 — 120.0
sq ft actual. 160 sq ft bought against
that, about 25% waste — the room's 144.5 in length doesn't divide evenly by
the 96 in sheet, so every course leaves a 48.5 in offcut. This is a different
product than the ¼ in wall/roof sheathing above — don't combine the two on
the lumber order.

Add 5–10% on the 2x4s and 2x6s for crooked stock rejected at the yard.

**Watch at the yard:**
- The four side wall plates are now **144 in exactly**, cut from 12 ft stock
  with **zero trim** (§4.14). Pick four clean, square-ended boards, or buy 14 ft
  for those and accept the offcut.
- The 189 in 2x10 side rims leave only 3 in of slack in a 16 ft board. Pick
  clean ends.
- 6x6 @ 144, 4x4 @ 192, the 2x8x192 ridge plies, and the 2x10x120 cross rims
  (10 ft stock) are cut at exact stock length — no trim allowance at all. The
  4x6 is gone from the order entirely; the ridge is 2x8 now (§4.10).
- The four 37⅞ in gable infill studs cut two to an 8 ft board with 20 in
  left over — enough for one of the 20 in studs if a board comes up short.
- Upsizing the joists to 2x10 moved 132 linear feet from 2x8 to 2x10 and left
  the totals alone: still 90 boards, 936 linear feet, because every piece fell
  in the same stock length it did before. Only the price per foot and the
  weight change — roughly 25–30% more per foot, and about 40% heavier per
  board to hoist 9 ft up.

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

1. **Confirm joist depth** — modelled as 2x10 to match the rim, unstated in the drawings (§5)
2. **Get the fastener counts sized** (§4.8, §4.9) — cleats, backers and the
   bolt/screw split are settled; what is missing is how many of each, which
   needs a snow load and a species
3. **Confirm the ridge against a real snow load** (§4.10) — built as a doubled
   2x8, good for ~24 psf of roof snow; deeper plies are a drop-in if not
4. **Re-derive the 22-sheet sheathing layout** (§7) — the area is computed, the
   sheet count is still a hand estimate
5. **No way up.** There is still no ladder or stair to a platform nearly 9 ft in
   the air. Nothing in any drawing or cut list.
6. **Decide the OSB thickness** — ¼ as drawn, or 7/16 as the 24 in o.c. framing
   wants (§5)
7. Fix the 92 → 93.1 rafter length on the source drawing (§4.3). The three
   missing 113 in plates (§4.2) are now correct in the model, the rail and the
   buy list; only the source drawing still shows one.

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
11. Centred the middle posts on equal spans, resolving §4.1
12. Hung the centre joist off the posts, resolving §4.6
13. Widened the deck rim from 2x8 to 2x10 (side rims, then the front/back
    cross rims to match), top kept flush so nothing built on top moved;
    caught and fixed the knee-brace level cut, which had a hardcoded top
    height and no longer butted the deeper rim
14. Evenly spaced the rake-ladder blocks over the landing overhang (were
    16.4/33.9, now 17/34) and stood them on edge so the narrow 1½ in face
    follows the roof pitch, matching how the rafters themselves meet the
    slope
15. Caught the king post landing inside the window header instead of on a
    support (§4.7) and resolved it by upsizing the gable headers to doubled
    2x8, which pushed the side wall studs to 88¾ in and everything above
    them up 1.75 in — eave beams, ridge, king posts, and the peak (now
    252 in / 21 ft 0 in)
16. Swapped the room floor from 1x5½ decking to 1 in OSB, laid as real 4x8
    sheets with staggered seams — the landing kept its decking (Marshall's
    request)
17. Moved the walls onto the top of that floor instead of beside it — the
    OSB now runs the full room footprint, and everything from the wall
    plates up (studs, eaves, ridge, peak) moved up another 1 in to sit on
    top of it (253 in / 21 ft 1 in, on top of the §4.7 shift)
18. Upsized the floor joists from 2x8 to 2x10 so they match the rim (§5).
    Tops stayed on the DECK_TOP line, bottoms dropped 100 → 98 and are now
    flush with the rim bottom; nothing above moved. Joist depth is derived
    from DECK_TOP in code now instead of hardcoded, so the next depth change
    carries the joists with it. Clash-checked against every other layer —
    zero hits, and the knee braces still land exactly on the joist bottoms
    at Y 98. Purchase totals unchanged at 90 boards / 936 linear feet; 132
    linear feet just moved from the 2x8 pile to the 2x10 pile. Hangers step
    up to the 2x10 size.
19. Filled the empty gable triangle with infill studs — four per gable at
    Z 24, 48, 72, 96, 24 in o.c. and symmetric about the ridge (§5). Tops are
    bevelled to the rafter underside and the lengths derive from U(z) rather
    than being typed in, so they follow the roof if it ever moves; the roof
    line moved above section 5 in the file to make that possible, and
    `prism()` gained an optional depth so a 3½ in board could be extruded
    (every previous prism was a 1½ in one). Verified: stud tops sit on the
    rafter underside to 0.00 in, zero clashes against every other member,
    9½ in clear to the king post. Adds three 8 ft 2x4s — 93 boards, 960 lf.
20. Caught that nothing bears on the post tops — the rims lap the post faces
    and the whole platform was hanging on fasteners in shear (§4.8). Added
    twelve 2x6 bearing cleats, one under each rim/post landing, tops on the
    rim underside at Y 98 so the rim sits on wood. Verified with an exact
    polygon test rather than bounding boxes, since the knee braces pass close
    enough that a bbox check gives false positives: zero clashes, and every
    rim/post bearing in the model has a cleat under it. Redundancy only — the
    connection still needs sizing, now logged as §9 item 2.
21. Took the cleats to double shear rather than notching the posts, which were
    already set (§4.8 records why the notch lost). Every cleat now has a
    matching backer on the opposite post face, so the through-bolt works on two
    shear planes instead of one — twelve pairs, 24 blocks, one extra 8 ft 2x6.
    Verified by walking all ten bolt lines: each is continuous from outer cleat
    through post to outer backer with cleat count equal to backer count, and
    the exact polygon clash test stays at zero. 95 boards, 976 lf.
22. Split the fastener spec by job (§4.9): ½ in through-bolts carry gravity at
    the cleats, structural screws carry restraint at the rims. Two ½ in holes
    per post instead of four. Specced against the actual published values for
    the Grip Fast #16 x 6 (DrJ TER 2101-03) — 265/225 lb, 1½ in side member and
    1½ in penetration both satisfied, coating rated HDG-equivalent for treated
    lumber. The spacing rules, not the capacity, cap it at ~6 screws per face.
23. Audited every board against the cut list and fixed what it found. The three
    113 in 2x4 plates were listed as one, and the buy list had been built off
    that wrong count — a bin-packing check proved two 144½ in plates had
    nowhere to go, so the lumber order was genuinely two boards short. Added
    two 10 ft 2x4s (97 boards, 996 lf). Also made the OSB ¼ rail row a piece
    count like every other row (30 pieces from 22 sheets) and refreshed the
    sheathing area from the model, 614½ -> 620.9 sq ft, stale since the §4.7
    and §5 shifts. Re-verified: model, rail and §6 table agree on all 25 lumber
    sizes, and every piece now cuts from the listed boards.
24. Drew the fasteners (section 2c, `fasteners` layer with its own toggle and
    explode step). 80 cylinders: 48 #16 x 6 screws, 12 #18 x 8 at the front
    corner posts, 16 half-inch x 10 bolts and 4 lengths of all-thread. Needed a
    `cyl` kind in the renderer — the first non-prismatic piece in the model, so
    it also got its own steel colours and skips the wireframe edges the boards
    get. Positions are on the report's real spacing and verified against it:
    3.125 in columns, 2.125 in rows, 4.5 in penetration, every bolt crossing the
    full cleat/post/backer sandwich. Fasteners are excluded from the cut-list
    rail (it is a list of boards you cut) and from the clash checks (they are
    meant to pass through lumber); both still reconcile exactly.
25. Caught that the ridge is structural — rafters bear on top, no ties anywhere
    — and that the 4x6 was undersized for it (§4.10). Rebuilt as a doubled 2x8,
    depth growing downward from Y 247 so nothing above moves; king posts 41.5 ->
    39.75, derived from RIDGE_BOT rather than typed. The rafter seat notch
    improved to 28% of depth from 31%, because the ridge got narrower as well as
    deeper. Verified: plies 3.00 in wide centred on 60, top still 247, king post
    tops meeting the ridge underside, ridge bearing fully on the 4x4, both
    notches inside the 1/3 rule, zero lumber clashes. The 4x6 leaves the order
    entirely — 98 boards, 1,012 lf.
26. Dropped the deck 1.75 in so the rim top is flush with the post tops, and
    made POST_TOP/DECK_TOP derive from the post length and burial depth
    (§4.11). Everything above moved as one piece; peak 253 -> 251.25. The
    rim/post lap went 7.5 -> 9.25 in, which fits a fourth screw row (60 -> 80
    screws). Required deriving the rafter baseline and RIDGE_TOP, which had
    been typed — the third instance of that bug.
27. Caught the knee braces were 43.56 deg, not the 45 their cut list and saw
    panel both claimed, and 56.10 in long against a 57.5 label. Same root cause
    as the joist Y: change 13 moved BR_TOP and left BR_FOOT_Y typed. Derived
    the foot; now a true 45 at 57.502 / 46.499, so the existing labels are
    correct again. Twelve braces would have been cut wrong.
28. Fixed OSB panels being modelled 1.5 in thick — sheet() never passed a
    thickness, so they took prism()'s default. Pre-existing and invisible,
    since the skin is translucent and the old clash checks were box-only.
    Rewrote the clash check to use the model's own clipPoly/polyArea, which
    handles the non-convex notched rafter profiles that SAT could not: box-box
    0, prism-prism 0, and the only box-prism hits are the gable sheathing
    notches around the ridge and eave beams, which are real and now documented.
29. Made the room joists a true 16 in o.c. run, anchored on the hung centre
    joist at X 94.5 rather than on a rim (§4.6). Every interior bay is now
    exactly 16.00 — they had been 16.6 and 15.3 either side of the centre and
    15.9 further along, scaled off the drawing. Remainder sits at the two ends,
    14.25 in at the carrier and 14.5 at the back rim, both under 16. Joist
    count, cut list and purchase list all unchanged; the four room-floor OSB
    seams still land on framing.
30. Swapped the rim screws to the SPAX PowerLag XF 5/16 x 6 that Marshall
    actually bought (§4.9). The win is the ICC-ES listing — ESR-1782, where the
    Grip Fast had a DrJ report — plus HCR coating explicitly evaluated for
    ACQ-D, and one part number covering both the 1.5 in rims and the 3 in
    doubled cross rim. The cost is spacing: ESR-1782 wants 1.75 in edge
    distance against the Grip Fast's 0.625, which on a 5.5 in post face leaves
    only 2 in of usable width. Two columns of three, so six per face rather
    than eight, 60 screws instead of 80. Layout derived from Table 6 and
    verified against every minimum.
31. Resized the front window to a 36 x 48 unit — 37 x 49 rough opening (§4.12).
    It had been framed at 62 x 50, which was most of the wall bay. Head stays at
    the top plate, since the doubled 2x8 gable header already spans the whole
    wall, so only the sill moved. The two studs that edged the old opening moved
    to Z 19.5 and 58.0 as the new kings; stud count unchanged. Sills 2x4x62 ->
    2x4x37, cripples 2x4x28.5 x3 -> 2x4x29.5 x2. Window framing now derives from
    WIN_W and WIN_H.
32. Added a 36 x 80 door (38 x 83 R.O., up from 34 x 83) and spaced both
    openings evenly across the front (§4.12): 15 in of solid wall at each end
    and between them, measured across the 120 in face. Both heads land on the
    top plate underside so they align by construction, which leaves the door
    R.O. half an inch tall against a standard pre-hung — shim it rather than
    adding a header. Front wall drops to six studs. Side effect worth having:
    the king post no longer sits over the window opening (§4.7) but over solid
    wall between studs 12 in apart.
33. Fixed the room-floor sheet seams, which had been picked from the 48 in
    sheet module rather than from the joists (§4.13). The break at X 142 sat
    half an inch off the joist centred on 142.5, leaving one sheet 0.25 in of
    bearing. Seams now derive from the joist centrelines: 94.5 for courses 1
    and 3, 126.5 for course 2. Every seam centred with 0.75 in each side, outer
    edges flush on the carrier and back rim, no four corners meeting, still six
    pieces from five sheets. Joist spacing untouched.
34. Moved the room's front face X 46 -> 46.5 so the room is exactly 144 in
    (§4.14), on Marshall's observation that a 48x96 plus a 48x48 is 144 and the
    model was making him rip a 48.5. Every sheet module now lands on a joist or
    stud centreline. Room floor drops from 5 sheets to 4, waste 25% -> 6%, and
    no floor piece is ripped in X at all. Side wall plates become 144 from 12 ft
    stock, which §7 had flagged as available "if the design tolerates 144".
    Turned up the same seam defect in the SIDE WALL sheathing that §4.13 had
    just fixed in the floor — its 142 seam had a quarter inch of bearing too.
    Side wall studs now derive from RM_X0, flush at the corners with the o.c.
    marks pulled from the wall end. Joist spacing untouched; only the datum
    moved. 98 boards, 1,004 lf.
35. Openings v2 (§4.15): door 36x80 -> 32x80 (R.O. 34 x 83), front window
    unchanged at 36x48 (37 x 49), and a new 60x48 window (61 x 49) on the Z = 0
    side wall. Front margins equal at 16-1/3 in across the 120 in face. Added a
    king AND jack at every opening edge; on the front wall both are full height,
    because the 83 in door R.O. is taller than the 81.5 in studs and the wall's
    doubled 2x8 IS the header. The side wall is 88.75 tall so it takes a real
    doubled 2x8x64 header, filling exactly to the top plate with jacks carrying
    it. Every sill now has a cripple hard against each end so it bears rather
    than being end-nailed — the complaint that started this. Two of the side
    window's six cripples sit under the sheathing seams the 61 in opening
    swallows. 103 boards, 1,070 lf.
36. Derived the wall height from the door instead of typing it (§4.16), on
    Marshall's question about the right rough opening. Door R.O. 83 -> 82.5 for
    a 32x80 exterior pre-hung, which sets OPENING_HEAD 189.0, STUD_TOP 196.25,
    STUD_LEN 88.25 and PLATE_TOP 197.75 — the roof follows half an inch down to
    a 250.76 peak. Gable studs go 81.5 -> 88.25, the same as the sides, which
    finally leaves room for a real doubled 2x8 header over each opening bearing
    on jacks. Supersedes §4.7: the wall-length 2x8x113 beams are replaced by a
    4x4 post under each king post, which §4.7 wanted but could not have while
    the old window was in the way. 105 boards, 1,092 lf.
