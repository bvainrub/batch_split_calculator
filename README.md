# Batch Calculator

A small, single-file calculator for supplement production. It helps plan how a base
powder is mixed and split when an order is too large for one mix: the production scale
cannot weigh more than about 5 kg at a time, and the capsule machine cannot run more
than 15,000 capsules in one batch. The calculator works out how many mixes (splits)
are needed, how much of the base comes from stock already on hand and how much has to
be prepared in new batches, and what proportion (RDD) of each mix comes from the old
base and from the new base.

The whole tool is one HTML file. There is nothing to install and no server: open the
file in a browser and start calculating.

---

## The two tabs

The calculator has two tabs, one for each stage of the work:

| Tab | What it is for |
| --- | --- |
| **Base batch size Calculation** | Sizing a plain compounding batch: given a Product Sizing Factor and a weight per factor, how many batches of at most 5,010 gr are needed, and how is the factor divided between them? |
| **Base Split for Capsule** | Planning a full capsule order: an amount of old base is already on hand, the rest must be made in new batches, and the total has to be split into mixes that respect both the scale limit (5,010 gr) and the capsule limit (15,000 caps). This tab calculates the splits, their composition, and the RDD for every split. |

### Terms used in the calculator

- **PSF (Product Sizing Factor)** — the size of the order (or of one mix) expressed in
  product units. Every quantity in the calculator is built from the PSF.
- **Quantity per unit** — how many grams of base one PSF unit requires. The old base
  and the new base can have different quantities per unit.
- **Batch** — one compounding mix of base, at most **5,010 gr**.
- **Split** — one mix of a capsule order, limited by **5,010 gr** of base and by
  **15,000 capsules** at the same time.
- **RDD (Requested Daily Dose)** — the proportion of old base and new base in a mix,
  expressed as two numbers that add up to 1. For example, RDD old 0.0785 / RDD new
  0.9215 means 7.85% of the mix is old base and 92.15% is new base.

---

## Tab 1 — Base batch size Calculation

This tab answers a simple question: *I need this much base in total — how do I divide
it into batches the scale can handle?*

**Inputs**

- **Product Sizing Factor (PSF)** — the total factor for the batch.
- **Weight** — grams of base per one PSF unit.

**How it works**

1. Total weight = PSF × weight.
2. If the total is 5,010 gr or less, one batch is enough.
3. Otherwise the total is divided into batches of at most 5,010 gr. The number of
   batches is rounded **up**, and the factor per batch is rounded **up** as well, so
   no batch ever exceeds the limit. The last batch takes whatever factor remains.

**Example** — PSF 331, weight 68.783 gr:

| Step | Result |
| --- | --- |
| Total weight | 331 × 68.783 = 22,767.173 gr |
| Batches needed | 22,767.173 / 5,010 = 4.54 → **5** |
| Factor per batch | 331 / 5 = 66.2 → **67** |
| Full batches | 4 × 67 = 268 |
| Remaining factor | 331 − 268 = 63 |

Result: *Make 4 batches with Product Sizing Factor: 67, and 1 batch with Product
Sizing Factor: 63.*

---

## Tab 2 — Base Split for Capsule

This is the main planning tab. A capsule order rarely starts from zero: some old base
is usually already on hand, and the rest has to be produced in new batches. This tab
combines both and splits the order into mixes the equipment can actually run.

**Inputs**

- **Old Base on Hand (gr)** — how much old base is in stock right now.
- **Old Base Quantity per unit** — grams of old base per one PSF unit.
- **Old Base Quantity is the same for the new base?** — *Yes* if the new base uses the
  same quantity per unit, *No* if it is different. The Old Base Quantity field always
  stays visible; choosing *No* adds the field for the new base next to it.
- **New Base Quantity per Unit** — grams of new base per one PSF unit (only when the
  quantities differ).
- **Product Sizing Factor (PSF)** — the size of the order. Must be a whole number.
- **Quantity Caps per Bottle** — how many capsules one PSF unit produces.
- **Manual adjust # of splits (default — automatic)** — optional. Leave it empty and
  the calculator decides the number of splits itself; enter a number and that number
  is used instead (see "Manual control" below).
- **New Batch 1…k Amount (gr)** — the calculator works out how many new batches are
  required (one field per batch) and the amounts are entered manually. Each amount
  must be greater than 0 and at most 5,010 gr.

**How the calculation runs**

1. **Totals.** Total capsules = PSF × caps per bottle. Total weight needed =
   PSF × quantity per unit.
2. **Two limits at the same time.** A split may not exceed 15,000 capsules and may
   not exceed 5,010 gr of base:
   - Max PSF per batch (by capsules) = floor(15,000 / caps per bottle)
   - Max PSF per split (by weight) = floor(5,010 / quantity per unit)

   "Floor" means rounding **down**, so an automatic split never crosses either limit.
   Whichever limit is tighter decides how many splits are made.
3. **Splits.** The PSF is divided between the splits as evenly as possible (for
   example, PSF 150 in 4 splits becomes 38 / 38 / 37 / 37). The weight of a split is
   its PSF × quantity per unit.
4. **Composition.** Bases are consumed in order: the old base on hand first, then
   Batch 1, then Batch 2, and so on. Whatever is left of a batch carries into the
   next split. The **Split Composition** section shows, for every split, exactly how
   many grams come from each source.
5. **RDD.** Proportions are calculated for the first split and then separately for
   every following split, because the proportions change as leftovers move from
   split to split (see the next section).
6. **New batches.** New base needed = total weight needed − old base on hand. The
   number of new batches is the new base needed divided by 5,010 gr, rounded up.
   The **Batch Usage for Split** section shows how much of each entered batch is
   used and how much is left over.

If the old base on hand covers the whole order, no new batches are needed: the
calculator shows one batch and the old base left over.

**Example** — old base 1,200 gr on hand, quantity per unit 101.91 (same for the new
base), PSF 150, 90 caps per bottle, three new batches of 4,964 gr, splits set
manually to 3:

- Total caps: 150 × 90 = 13,500. Total weight needed: 150 × 101.91 = 15,286.5 gr.
- Split Composition:
  - Split 1 (PSF 50, Caps 4500) — Old base 1,200 gr + Batch 1 3,895.5 gr
  - Split 2 (PSF 50, Caps 4500) — Batch 1 1,068.5 gr + Batch 2 4,027 gr
  - Split 3 (PSF 50, Caps 4500) — Batch 2 937 gr + Batch 3 4,158.5 gr
- Batch Usage for Split: new base needed 14,086.5 gr; Batch 1 and Batch 2 are used
  completely; Batch 3 is used 4,158.5 gr with 805.5 gr left over.

---

## The first split and the RDD rules

The RDD needs care, because the first split is the only one that mixes the **old**
base with the new base. From the second split on, a mix is built from the new batches
and their leftovers only.

**The first-split pair** is defined once, in the Calculation section under
"First Split with Old base amount:":

```
RDD (old base) = old base on hand / (quantity per unit × PSF)
RDD (new base) = 1 − RDD (old base)
```

The Result repeats this pair for Split 1 exactly as calculated. For every following
split, the Result shows that split's own proportions, calculated from the grams that
actually go into it: the leftover carried in from the previous batch counts as the
"old" part, and base taken from a batch opened for the first time in that split
counts as the "new" part.

If a split is covered completely by base that is already on hand, there is no new
base in it at all. Instead of printing "RDD new base: 0", the Result prints:

```
Requested Daily Dose (RDD old base): 1
Note you have enough to complete order from new lot
```

**Different quantities per unit (the average rule).** When the new base has a
different quantity per unit than the old base (the "same quantity" question answered
*No*), the first split mixes two bases that do not weigh the same per PSF unit. In
that case:

- If the old base on hand is **more than one old unit** (on hand > Old Base Quantity
  per unit), the first split's RDD is calculated with the **average** of the two
  quantities:

  ```
  Average quantity = (old quantity per unit + new quantity per unit) / 2
  RDD (old base)   = old base on hand / (average quantity × PSF)
  RDD (new base)   = 1 − RDD (old base)
  ```

  Example: on hand 600 gr, old quantity 101, new quantity 91.5 → average =
  (101 + 91.5) / 2 = 96.25, and RDD old = 600 / (96.25 × 150) = 0.04156.
- If the old base on hand is **less than one old unit**, it is too small to influence
  the mix, and only the new base quantity per unit is used.
- Splits 2 and later always use the new base quantity per unit only.

The average is an approximation for a mixed first split: it treats the split as if
its quantity per unit sat halfway between the old base and the new base.

---

## Manual control: the user decides the tolerance

The automatic calculation is strict — it never lets a split exceed 5,010 gr or
15,000 capsules. In real production, though, a mix that is a few dozen grams over the
nominal limit may still be acceptable, and that judgement belongs to the operator,
not to the calculator.

That is what **Manual adjust # of splits** is for. Enter the number of splits you
want, and the calculator uses it exactly as entered:

- It **never blocks** the calculation and **never forces** extra splits.
- If a manually chosen split goes over a limit, the Result shows a plain warning,
  for example: *"Split weight 5,095.5 gr (PSF 50) is above the 5,010 gr limit."*
- The Calculation section marks the split count as "(splits set manually)" so it is
  always clear which numbers came from the automatic rules and which ones were
  decided by the user.

In the example above, the automatic rules would make 4 splits of PSF 38/37, because
a PSF 50 split weighs 5,095.5 gr. Setting the manual value to 3 keeps the practical
50 / 50 / 50 division, with the warning shown and the decision left to the user.

---

## Colours and highlights

The calculator uses colour only where a number needs attention:

- **Red — the average quantity row.** In the Calculation section, the row
  "Unit size for first split (average)" is printed in red whenever the average rule
  described above is applied, so it is immediately visible that the first split was
  calculated differently from the rest.
- **Green — the leftover that stays in stock.** In Batch Usage for Split, the
  "left over … gr" of the last batch that was actually used is printed in green.
  That is the amount of new base physically remaining on hand after the order.
  Batches used completely ("left over 0 gr") are not highlighted.
- **Warning blocks.** An orange warning appears when the entered batches are not
  enough to cover the order (the shortage is stated in grams), and when a manually
  chosen split exceeds the weight or capsule limit.
- **Bold frames.** The Result, Split Composition and Batch Usage for Split sections
  share the same design — a white panel with a bold border — so the three output
  sections are easy to find and read together.

---

## Limits and things to know

- **Hard limits used by the automatic calculation:** 5,010 gr of base per batch or
  split, and 15,000 capsules per split. Manual split counts may exceed them on
  purpose (see above); the calculator warns but does not interfere.
- **PSF must be a whole number.** Quantities per unit, caps per bottle and batch
  amounts may be decimal.
- **New batch amounts are entered manually.** The calculator decides *how many*
  new batches are required and offers an even split of the needed weight as a hint,
  but the actual amount of each batch (each between 0 and 5,010 gr) is typed in by
  the user, because in production each batch is weighed as it is made.
- **Bases are consumed strictly in order** — old base first, then Batch 1, 2, 3… —
  and leftovers always carry into the next split. There is no option to reorder or
  skip a batch.
- **The average rule is an approximation** for the first split when the old and new
  bases have different quantities per unit; see "The first split and the RDD rules".
- **Rounding.** Maximum PSF values are always rounded down (floor), so automatic
  results stay inside the limits. Proportions (RDD) are shown with 4–5 decimal
  places.
- **No data is saved.** The calculator keeps nothing between sessions: closing or
  reloading the page clears every field. It makes no network requests except loading
  its font; if the font cannot be loaded, a system font is used instead.
- **Scope.** The tool plans base mixing and splitting for supplement capsule orders.
  It does not manage inventory, orders, or production records — the amounts on hand
  and the batch amounts are entered for each calculation.

---

## How to use

1. Download `Batch_Calculator.html` and open it in any modern browser.
2. Choose a tab:
   - **Base batch size Calculation** for sizing a plain batch by factor and weight.
   - **Base Split for Capsule** for a capsule order that combines base on hand with
     new batches.
3. Fill in the fields, enter the new batch amounts when they appear, and press
   **Recalculate**. **Clear** resets the tab.
