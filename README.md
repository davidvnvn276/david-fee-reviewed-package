# Reviewed DAVID fee evidence and images

The original evidence is preserved under original-evidence without changing its
80-entry manifest. The corrected six-sample claim map is claim-audit-reviewed.csv.
The reviewed manuscript only refines nominal-limit terminology, historical RPC
availability, and the reproduction instructions. See david-fee-review-vi.md for
the review findings and limitations.

## Check the original saved evidence

```bash
cd original-evidence
python verify_saved.py
```

Python 3 standard library is sufficient for this original script.

## Obtain fresh read-only data

```bash
python reproduce.py --simulate --override
```

Run from original-evidence. A fresh block can have different state. To request
the published repeat block instead, use --block 12018889. Historical availability
is provider-dependent; do not interpret pruning errors or null receipts as zeros.
No command signs or submits transactions. from sets a simulated caller only.
The balance override does not deposit funds or alter persistent chain state.

## Independently recheck the saved observations

From this package root:

```bash
python -m pip install pycryptodome==3.23.0
python verification/independent_audit.py
```

This separate reviewer script additionally derives ABI selectors with Keccak,
binds request methods and block tags to responses, recalculates raw integer fee
splits, checks all original manifest entries, compares the same-block repeat,
and checks the already captured independent HTTP batch in
verification/independent-comparison/response.json. It reuses that batch when the
file exists; it does not fetch a fresh historical batch on each run. The reviewer
dependency is separate from the standard-library original reproduction script.

Existing independent observations:
- Same-block recheck: 12018889, 697.144028784 USDr.
- Fresh review capture: 12020199, 697.171389504 USDr.
- All six block headers matched.
- October 7 receipts and balances rechecked directly.
- October 5 receipts returned null and balance state was pruned.

Neither offline consistency checks nor RPC/Explorer agreement independently
authenticate an Axyl consensus certificate or establish a final Ethereum burn.
The 252 passing assertions are program checks, not 252 independent experiments.

## Images

Three precise SVG-derived graphics are provided as PNG and editable SVG, with
500-pixel previews. Exact dimensions and text bounds are included. The supplied
Rayls mark is embedded unchanged, retaining its original black background.
Insertion locations are in david-fee-review-vi.md. Dates and account watermarks
are omitted from the images; block references retain their evidence boundary.

## Publishing

Upload this package or its readable files to a public location before linking it
from the article. Add a real permalink to the manuscript reproduction section.
This package has not been uploaded to a public GitHub repository by this review.

SHA256SUMS-reviewed.txt covers every included file except itself. It establishes
file identity and internal consistency, not authenticity of onchain observations.
