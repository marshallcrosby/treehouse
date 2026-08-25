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
| 105.5 | top of the 6x6 posts (1¾ in below the rim top) |
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
- Bays either side become 16.6 and 15.3 in (were 15.9 and 16.0)

Two options that do *not* work, for the record: trimming the post tops to 100
leaves the 2x10 rim with no bolting surface, and notching the joist means
removing 5½ in from a 9¼ in member.

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

**Screw spec — Grip Fast Premium #16 × 6 in.** Sold at Menards as "¼ × 6",
model 30927-32. The ¼ is nominal; the T30 drive identifies it as the **#16** in
DrJ TER 2101-03, not a repackaged lag.

| | |
|---|---|
| Diameter | 0.283 major · 0.205 minor · 0.181 shank |
| Z⊥ / Z∥ | **265 / 225 lb** (HF/SPF and DF) · 265 / 245 (SP) |
| Report requires | 1½ in side member, 1½ in min main-member penetration |
| Our condition | rim is exactly 1½ in ✓ · 6 in screw gives 4½ in penetration ✓ |
| Coating | TRIPLE MAX-SHIELD, stated equivalent to HDG ASTM A153 Class D and rated for ground-contact treated lumber, AWPA UC1–UC4A |
| Install | no pilot hole, head driven flush |

That coating rating is what makes these usable — an ordinary black-coated screw
would not survive ACQ/CA. Check the treatment on your lumber tags; the report
names MCA specifically, though A153 Class D equivalence is the general
requirement for ACQ and CA too.

**Spacing governs, not capacity.** In a 5½ × 7½ in contact area the #16 wants
3⅛ in between screws in a row parallel to grain, 2⅛ in perpendicular, and ⅝ in
edge distance. That allows roughly 2 columns × 3 rows, so **about 6 per post
face is the practical ceiling**. Ten faces, ~60 screws, three 24-packs.

**Why the screws cannot be the load path.** Six screws at the conservative 225
lb is 1,350 lb, against a post demand somewhere around 3,000–4,000 lb. Neither
these screws nor the two ½ in bolts they replace could ever have carried
gravity. That arithmetic is the clearest statement that **the cleats are
structural, not insurance.**

**Two caveats.**

- The report is from **DrJ, not ICC-ES**. DrJ is ANAB ISO/IEC 17065 accredited
  so it is a real evaluation, but some engineers and building departments work
  only from ICC-ES. Confirm before buying in bulk.
- **#16 stops at 6 in.** At the front corner posts the cross rim is a doubled
  2x10 — a 3 in side member, outside the tested 1½ in configuration, and a 6 in
  screw leaves only 3 in of penetration (still above the 1½ in minimum). The
  **#18** in the same report runs to 16 in and jumps to **410 / 370 lb**. Use it
  at the front corners, or throughout if you want fewer screws.

**The layout is in the model** (section 2c, `fasteners` layer, its own toggle).
Eighty fasteners: 60 screws and 20 bolts. Drawn as real cylinders on the real
spacing, because the spacing is what runs out first and it is easier to see
than to argue about. Verified against the report's own numbers:

| | modelled | #16 requires |
|---|---|---|
| column spacing, parallel to grain | 3.125 in | 3.125 |
| row spacing, perpendicular | 2.125 in | 2.125 |
| edge distance off the post face | 1.1875 in | 0.625 |
| screw penetration into the post | 4.5 in (5.0 for the #18) | 1.5 |
| bolt diagonal spacing | 2.83 in | 2.0 (4D) |

Every screw stops short of the far post face; every bolt crosses the whole
sandwich. Three rows of two per face is the ceiling — a fourth row does not fit
between the rim bottom and the post top at 2⅛ in centres.

**The clash checks skip the `fasteners` layer.** Fasteners are supposed to pass
through lumber, so including them would be noise. The lumber-only check still
reads zero.

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
| 2x10x46 | 7 | landing joists, turned 90° |
| 2x8x113 | 4 | doubled gable headers (widened from 2x6 — see §4.7) |
| 2x6x92 | 20 | rafters, 24 in o.c. (see §4.3 — really 93.1) |
| 2x6x57.5 | 12 | 45° knee braces |
| 2x6x5.5 | 24 | 12 bearing cleats + 12 double-shear backers, at the posts (§4.8) |
| 2x4x144.5 | 4 | side wall plates |
| 2x4x113 | 3 | gable top plates + back bottom plate (see §4.2) |
| 2x4x88.75 | 14 | side wall studs, 24 in o.c. (grew from 87 — see §4.7) |
| 2x4x81.5 | 13 | gable wall studs |
| 2x4x74.5 | 1 | front plate to the doorway |
| 2x4x62 | 2 | window sill, doubled |
| 2x4x37.875 | 4 | gable infill studs, inner pair — bevelled top, long point |
| 2x4x33 | 4 | rake-ladder blocks |
| 2x4x28.5 | 3 | cripples under the window |
| 2x4x20 | 4 | gable infill studs, outer pair — bevelled top, long point |
| 2x4x4.5 | 1 | front plate corner stub |
| 1x5.5x120 | 9 | landing boards, turned 90° |
| OSB ¼ | 22 sheets | walls, gables, roof |
| OSB 1 | 6 panels, cut from 5 sheets | room floor sheathing, full footprint (was 1x5.5x137.5 decking) |

Layout positions:

- Rafters, X: 11.5, 46, 59.5, 83.5, 107.5, 131.5, 155.5, 179.5, 188.5, 201.5
- Side wall studs, X: 46, 70, 94, 118, 142, 166, 189 (24 o.c.)
- Front gable studs, Z: 3.5, 7, 70.5, 75, 76.5, 112, 115
- Back gable studs, Z: 3.5, 27.5, 51.5, 75.5, 99.5, 115 (24 o.c.)
- Room joists, X: 61.9, 77.9, 109.8, 125.8, 141.8, 157.7, 173.6 (16 o.c.), the
  hung centre joist at 94.5, plus a doubled carrier at 46.0 / 47.5
- Landing joists, Z: 16.87, 32.79, 48.72, 64.64, 80.65, 96.66, 111.9 (16 o.c.,
  last bay is a 7 in half-bay)
- Gable infill studs, Z: 24, 48, 72, 96 (24 in o.c., symmetric about the
  ridge at Z 60) — four per gable, both gables the same
- Window opening Z 8.5 – 70.5, sill at 138.25 – 141.25
- Doorway opening Z 78 – 112

---

## 7. Purchase list

**Framing — 98 boards, 1,012 linear feet**

| Size | Buy |
|---|---|
| 2x4 | 16 @ 8' · 3 @ 10' · 2 @ 12' · 6 @ 14' · 5 @ 16' |
| 2x6 | 22 @ 8' · 6 @ 10' |
| 2x8 | 4 @ 10' · 2 @ 16' |
| 2x10 | 4 @ 16' · 15 @ 10' · 4 @ 8' |
| 4x4 | 1 @ 8' · 2 @ 16' |
| 6x6 | 6 @ 12' |

*(Two of the three 113 in 2x4 plates were missing from the rail, and the buy
list was built off that wrong count — a packing check showed two 144½ in plates
with nowhere to go. Fixed above with two more 10 ft 2x4s, one plate each. The
twenty-four cleats and backers are 132 in of stock all told — an 8 ft 2x6
yields seventeen, so budget two boards and keep the offcuts. It was 90
boards / 936 lf before the gable infill studs — those added
three 8 ft 2x4s: one yields the four 20 in outer studs, two yield the four
37⅞ in inner studs, two per board.)*

**Fasteners — 80, all HDG-equivalent or better**

| Item | Qty | For |
|---|---|---|
| Grip Fast #16 × 6 screw | 48 | six side-rim faces + both back cross rims (2 × 24-packs) |
| Grip Fast #18 × 8 screw | 12 | the two front corner posts — doubled cross rim (§4.9) |
| ½ × 10 HDG bolt | 16 | eight cleat lines, 8½ in of material each |
| ½ all-thread, ~14 in | 4 | two front corner cleat lines, 11½ in of material |
| ½ washers / nuts | 40 / 20 | two washers per bolt |

**Decking — 9 boards, 90 linear feet:** 9 @ 10 ft, landing only. The room
floor is OSB now, see below — it used to be 20 more boards at 12 ft.

**Sheathing — 620.9 sq ft:** the model computes this from the actual clipped
panels; 19.40 sheets of straight area, drawn as **30 cut pieces**. The layout
figure of 22 sheets (buy 23) is *not* derived from the model — it is a hand
estimate and the gable triangles are where the waste goes. Re-derive it if the
wall heights move again; the area above already grew from a stale 614½ that
predated the §4.7 and §5 shifts.

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
