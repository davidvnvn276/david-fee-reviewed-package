# DAVID: Rayls fee evidence — step-by-step reviewer guide

This package supports **Do Blockchain Transaction Fees Automatically Burn Tokens?
A Rayls Onchain Check**. It preserves the observations, source, inputs and
calculations behind the article so a reviewer can inspect each claim, rerun the
saved-data checks and, optionally, obtain fresh read-only state.

The investigation asks a narrow question: **does a transaction gas payment prove
that RLS was burned?** It distinguishes USDr fee collection from later conversion
to RLS, allocation, cross-chain delivery and canonical Ethereum destruction.

The package contains six selected mainnet transactions, two primary state
captures, seven simulation cases, separate saved reviewer captures and the
article's graphics. The observations are not a representative network sample.
No successful swap or final Ethereum burn is established by this package.

Public repository: [davidvnvn276/david-fee-reviewed-package](https://github.com/davidvnvn276/david-fee-reviewed-package).

## Choose a review route

| Route | Follow | What it answers |
|---|---|---|
| Read without installing anything | Sections 1, 6 and 11 | Where each finding comes from and what it does not establish |
| Check the delivered files and saved calculations | Sections 1–3 | Whether the package is complete and its saved data reconcile |
| Run the separate reviewer audit | Sections 4–5 | Whether selectors, request bindings, exact arithmetic, file hashes and saved rechecks agree |
| Inspect a claim manually | Sections 6–7 | How to follow a number or simulated outcome back to its inputs |
| Obtain fresh mainnet state | Section 8 | What the deployment returns at a newly pinned block |
| Send a small pinned RPC batch yourself | Section 9 | How to query selected state independently of the reproduction script |
| Diagnose a failed command | Section 10 | Whether a failure concerns setup, a missing file, unavailable state or changed configuration |

All documentation is in English. The root manuscript and reviewed claim map are
the current editorial versions. The `original-evidence` directory retains the
original investigation; explanatory documents were translated or updated and
their file inventories were regenerated. Raw observations, source and calldata
retain their original bytes. See `PROVENANCE.md` for the exact distinction.

## 1. Download, extract and locate the package root

1. Open the public repository linked above.
2. Open **`david-fee-reviewed-package.zip`** and use GitHub's download control to
   download that file. If you use **Code → Download ZIP**, you receive a repository
   wrapper; extract it and then extract the evidence ZIP inside it.
3. Extract the evidence ZIP into a normal local folder. Run the scripts from the
   extracted files, not from inside the compressed archive viewer.
4. Find the directory containing **`README.md`**, **`verify_package.py`**,
   **`original-evidence`** and **`verification`** together. This is the package root.
5. Open a terminal in that directory. All commands below assume this location.

On Windows, open PowerShell or Terminal from the extracted folder. Alternatively,
change to the actual path on your computer; replace `YOUR_NAME` and any differing
folder names in this example:

```powershell
cd "C:\Users\YOUR_NAME\Downloads\david-fee-reviewed-package"
Get-Location
Test-Path .\verify_package.py
```

`Test-Path` must print `True`. If it prints `False`, you are in the wrong folder or
downloaded an incomplete copy. Locate the package root before continuing.

On macOS or Linux, use your actual extraction path:

```bash
cd "/your/extracted/path/david-fee-reviewed-package"
pwd
test -f verify_package.py
```

The last command should exit successfully. It prints nothing on success.

## 2. Check Python and create a separate review environment

Use an installed Python 3 runtime. Python 3.11 or later is recommended for a new
setup; the saved-data and fresh-reproduction scripts use only the standard library. The delivered checks
were exercised on Linux. The Windows commands are provided for reviewers, but
this package does not claim that they were run on the author's Windows PC.

On Windows, check your installation:

```powershell
py -3 --version
```

If `py` is unavailable but `python --version` reports Python 3, use `python` in
place of `py -3`. If neither works, follow the official
[Python installation guidance for Windows](https://docs.python.org/3/using/windows.html)
and reopen the terminal after installing Python.

Create an environment **next to** the package, so dependencies do not become
unlisted files inside the sealed evidence directory:

```powershell
py -3 -m venv ..\david-fee-review-env
..\david-fee-review-env\Scripts\python.exe --version
```

On macOS or Linux:

```bash
python3 --version
python3 -m venv ../david-fee-review-env
../david-fee-review-env/bin/python --version
```

The commands below use the environment's interpreter directly. Activation and
PowerShell execution-policy changes are not needed. If this environment already
exists, reuse its interpreter or choose a different environment directory.
Do not place unrelated files into an existing environment.

## 3. Check package completeness and saved observations

These commands work without network access and require no third-party packages.
Run them separately so you can see which stage passes or fails.

**Windows / PowerShell**

```powershell
..\david-fee-review-env\Scripts\python.exe verify_package.py
..\david-fee-review-env\Scripts\python.exe original-evidence\verify_saved.py
```

**macOS / Linux**

```bash
../david-fee-review-env/bin/python verify_package.py
../david-fee-review-env/bin/python original-evidence/verify_saved.py
```

### What the package checker should report

For this complete guide edition, the significant fields are:

```json
{
  "status": "PASS",
  "reviewed_manifest_entries": 162,
  "original_evidence_manifest_entries": 80,
  "unlisted_files": []
}
```

The actual output also contains a scope explanation. The outer inventory covers
every included file except `SHA256SUMS-reviewed.txt` itself. The inner inventory
covers the 80 included files beneath `original-evidence`, excluding its own
`SHA256SUMS.txt`.

A hash mismatch or missing file is a package problem. Restore the complete
download and inspect the reported filename. Do not change an expected hash
simply to make a modified copy pass. Additional generated files may appear under
`unlisted_files`; they are outside the sealed inventory and must not be mistaken
for the original observations.

### What the saved-evidence checker should report

Its output should finish with:

```text
All six fee reconciliations and captured state/simulation checks passed.
```

It checks the three October 5 transactions, the three October 7 transactions,
the two primary state captures, runtime-code comparisons and the seven
simulation outcomes. It also checks that preserved pruning results remain
errors rather than being treated as zero balances.

These passes establish consistency of the delivered files and calculations.
They do not authenticate the RPC provider or independently reconstruct an Axyl
consensus certificate. Do not run the assertion-based scripts with Python's
`-O` optimization flag, which disables their assertions.

## 4. Install the reviewer's one pinned dependency

The separate reviewer audit uses **PyCryptodome 3.23.0** for Ethereum-style
Keccak. This dependency is separate from the standard-library original scripts.
Downloading it may require internet access; the subsequent audit is offline.

**Windows / PowerShell**

```powershell
..\david-fee-review-env\Scripts\python.exe -m pip install -r verification\requirements-audit.txt
..\david-fee-review-env\Scripts\python.exe -m pip show pycryptodome
```

**macOS / Linux**

```bash
../david-fee-review-env/bin/python -m pip install -r verification/requirements-audit.txt
../david-fee-review-env/bin/python -m pip show pycryptodome
```

The reported version should be `3.23.0`. Always install and run with the same
interpreter path; otherwise the dependency may be installed into a different
Python environment. The pinned requirement is in
`verification/requirements-audit.txt`. The optional graphics-rendering dependency
Matplotlib is not needed for this review.

## 5. Run the separate offline reviewer audit

**Windows / PowerShell**

```powershell
..\david-fee-review-env\Scripts\python.exe verification\independent_audit.py
```

**macOS / Linux**

```bash
../david-fee-review-env/bin/python verification/independent_audit.py
```

The significant output fields should be:

```json
{
  "mode": "offline_saved_response_replay",
  "checks_passed": 253,
  "checksums": 80,
  "saved_recheck_available": 21,
  "saved_recheck_unavailable": 15,
  "pinned_block": 12018889,
  "fresh_block": 12020199
}
```

The actual output additionally prints `output_directory`. Open the `audit.json`
there to inspect the named assertions and the status of each captured HTTP
observation. The generated `request.json` records the reconstructed comparison
batch. Newly generated files go outside the delivered package.

To choose an output directory, add `--output ../david-fee-review-audit-01` to the
command. That directory must not already exist. Use a new name for a later run,
or omit `--output` to let the script allocate a temporary directory.

### What is being checked

- Raw integer fee arithmetic and all twelve saved balance differences.
- Transaction, receipt, block-hash, account and block-tag bindings.
- ABI function/error selectors derived with Keccak, plus calldata and amount
  inputs for the simulated calls.
- The role hash, explicit balance override and original source-file comparison.
- All 80 inner file hashes.
- The saved same-block reviewer repeat at 12,018,889.
- The preserved separate HTTP batch's requests, response IDs, available headers,
  receipts and balance differences.

**This run makes no network requests.** It replays the batch already saved in
`verification/independent-comparison/response.json`. If that file is missing, the
script stops instead of silently fetching a different batch.

The `fresh_block` field refers to the earlier saved review capture at 12,020,199,
not a block fetched during your offline run. The 21 available and 15 unavailable
results describe the saved HTTP batch. All six headers matched; the October 7
receipts and balances were available, while the October 5 receipts returned
null and their balance states were pruned.

The 253 assertions are program checks, not 253 independent experiments. The
archived earlier `audit.json` records 252. This edition adds one request/response
binding assertion; it does not add another onchain experiment.

After the audit, rerun `verify_package.py` from Section 3. The package should still
pass unchanged. This demonstrates that the audit did not rewrite sealed evidence.

## 6. Map the article's claims to the files

Start with `claim-audit-reviewed.csv`, the reviewed claim index covering all six
fee samples. The older `original-evidence/claim-audit.csv` remains preserved as an
investigation artifact; it is not the preferred index for the revised article.

| Article finding | Start with | Then inspect | Evidence boundary |
|---|---|---|---|
| The Aggregator receives the base-fee component | `original-evidence/fee-reconciliation.csv` | October 5 raw fee responses and October 7 `repeat-fees-20261007` responses | Six selected one-transaction blocks |
| The block author receives the priority-fee component | The same CSV's `author_delta_raw` column | The matching author-balance requests/responses | Account identities are not inferred |
| Sample shares are 83.33% and 16.15% | `facts.json`, `fees` entries | Base fee and effective price in each receipt/header pair | Rounded sample shares, not network averages or method-dependent constants |
| Primary balances are 691.782528048 and 697.144028784 USDr | `facts.json`, `snapshot` and `october_7_snapshot` | `raw/snapshot-calls.json` and `repeat-state-20261007/02-response.json` | Values at two specified blocks |
| The deployed allocation is 5,000 / 0 / 5,000 basis points | The primary capture's configuration fields | `getConfig` calldata and raw result | Mutable configuration, not proof of execution |
| Nominal swap limits are 1,000–100,000 USDr | `limits_normalized_usd6` / snapshot limits | `getSwapLimits` and the verified normalization code | Six-decimal normalization truncates finer precision |
| USDr pool, recorded counters and pending buckets are zero | The captured state summaries | `getPoolConfig`, `getStats`, `getPendingDistribution` results | This proxy at the captured blocks, not every historical fee route |
| A funded simulation still reaches the pool check | `gate-tests.json`, `funded_override` | `templates/simulation-queries.json` and saved simulation responses | Counterfactual balance; no deposit |
| Swap and distribution are separate functions | `verified-FeeAggregator.sol` | `swapToRls` and `distributeEpochFees` bodies | Empty distribution's return `0` is simulated |
| The same October 7 state was repeated | `verification/independent-pinned-12018889/summary.json` | Its numbered request/response/label batches | Separate provider observations, not consensus authentication |
| Some October 5 data could not be fetched later | `historical-recheck-20261007` and reviewer batch | Preserved error/null responses | Unavailable observations, not zeros |
| A final Ethereum burn is not established | `review-report.md` and the article's scope section | The absence of a connected successful conversion/delivery/burn trace | This is not proof that Rayls has never burned RLS |

Paths in the first nine rows are relative to `original-evidence` unless already
prefixed with `original-evidence` or `verification`. The summaries are convenient
indexes; the request/response files are the underlying provider observations.

## 7. Inspect a result manually

### 7.1 Recalculate the two highlighted fees

Open `original-evidence/fee-reconciliation.csv`. Preserve the raw integer columns
when checking arithmetic; use the USDr columns for readable presentation. Each
USDr unit equals `10^18` native units.
Use a text editor, or import the raw integer columns as text. A spreadsheet can
round integers this large and silently change the evidence used in a calculation.

For any selected row:

```text
total fee     = gas_used × effective_price_raw_per_gas
base credit   = gas_used × base_fee_raw_per_gas
author credit = gas_used × (effective_price_raw_per_gas − base_fee_raw_per_gas)
base credit + author credit = total fee
Aggregator share (%) = 100 × base credit / total fee
```

Two reference rows are:

| Value | Approval, block 11,848,013 | Explorer-labelled setPrice, block 12,018,215 |
|---|---:|---:|
| Gas used | 51,378 | 38,001 |
| Base fee, raw units per gas | 48,000,000,000 | 48,000,000,000 |
| Effective gas price, raw units per gas | 57,600,000,000 | 297,203,348,275 |
| Total fee, raw units | 2,959,372,800,000,000 | 11,294,024,437,798,275 |
| Aggregator increase, raw units | 2,466,144,000,000,000 | 1,824,048,000,000,000 |
| Author increase, raw units | 493,228,800,000,000 | 9,469,976,437,798,275 |
| Total fee, USDr | 0.0029593728 | 0.011294024437798275 |
| Aggregator increase, USDr | 0.002466144 | 0.001824048 |
| Author increase, USDr | 0.0004932288 | 0.009469976437798275 |
| Aggregator share, rounded | 83.33% | 16.15% |

Repeat the same arithmetic for all six rows rather than treating one example as
the entire experiment. The percentage varies with the sampled effective gas
prices. It does not show that a method name determines the split.

### 7.2 Follow a CSV row back to RPC inputs

For the October 5 samples, open these files together:

```text
original-evidence/raw/historical-and-fee-requests.json
original-evidence/raw/historical-and-fee-results.json
original-evidence/raw/historical-and-fee-names.json
original-evidence/raw/author-balance-request.json
original-evidence/raw/author-balance-result.json
original-evidence/raw/author-balance-names.json
```

For the October 7 samples, use:

```text
original-evidence/repeat-fees-20261007/01-request.json
original-evidence/repeat-fees-20261007/01-response.json
original-evidence/repeat-fees-20261007/01-labels.json
original-evidence/repeat-fees-20261007/02-request.json
original-evidence/repeat-fees-20261007/02-response.json
original-evidence/repeat-fees-20261007/02-labels.json
```

Match each response to its request by **JSON-RPC `id`**, not response order. The
label list names the corresponding requests. Then check:

1. The requested block is the row's block, and the receipt names that block hash.
2. The block contains exactly one transaction and its hash matches the receipt.
3. The author-balance request uses the header's `miner` address.
4. The balance difference uses block `n − 1` and block `n` at the same address.
5. The raw differences match the three equations above.

The single-transaction condition limits competing same-block transaction
effects in these samples. It does not establish that arbitrary blocks can be
analysed by attributing every balance change to one transaction.

### 7.3 Inspect configuration and implementation identity

The two primary captures are:

| Block | UTC timestamp | Native/ERC-20 raw balance | USDr |
|---|---|---:|---:|
| 11,848,988 | October 5, 2026, 10:43:21 | 691782528048000000000 | 691.782528048 |
| 12,018,889 | October 7, 2026, 07:47:08 | 697144028784000000000 | 697.144028784 |

Both captured balances are below the nominal 1,000-USDr minimum. For the second
capture, `repeat-state-20261007/02-labels.json` identifies the state responses.
Check `native_USDr_balance`, `USDr_balanceOf`, `getConfig`, `getSwapLimits`,
`getPoolConfig`, `getStats`, `getPendingDistribution`, `implementation_slot` and
`implementation_code` in the matching batch.

Decode integer responses from hexadecimal. ABI-encoded integer returns use
32-byte words. `getConfig()` returns 5,000 / 0 / 5,000; `getSwapLimits()` returns
1,000,000,000 / 100,000,000,000 in normalized six-decimal units. These are
nominally 1,000 / 100,000 USDr, while USDr balances themselves use 18 decimals.

The proxy is `0x07e17e17e17e17e17e17e17e17e17e17e17e17e3`. Its captured
EIP-1967 implementation address is
`0x07e17e17e17e17e17e17e17e17e17e17e17e17e4`. Compare the RPC runtime bytes
with `deployed_bytecode` in the saved Explorer implementation metadata. The
source reviewed is `original-evidence/verified-FeeAggregator.sol`; it is not
assumed to be an arbitrary current GitHub revision.

### 7.4 Trace the simulated cases

Use `original-evidence/templates/simulation-queries.json` for the inputs,
`templates/errors.json` for error selectors and `gate-tests.json` for the compact
result index. The primary raw results are in `raw/simulation-result.json` and
`repeat-state-20261007/03-response.json` with their corresponding labels.

| Case name | Input | Captured outcome | What to inspect |
|---|---|---|---|
| `zero` | Swap 0 USDr | `ZeroAmount` | Amount check |
| `ten` | Swap 10 USDr | `BelowMinimumSwap` | Nominal minimum check |
| `minimum` | Swap 1,000 USDr | `InsufficientBalance` | Captured balance gate |
| `over_maximum` | Swap 100,001 USDr | `ExceedsMaximumSwap` | Nominal maximum check |
| `unauthorized_minimum` | Swap 1,000 USDr from a different caller | `AccessControlUnauthorizedAccount` | Caller/role boundary |
| `empty_distribution` | Distribution with empty pending buckets | Return `0` | Separate distribution function |
| `funded_override` | Simulated balance 2,000 USDr, swap 1,000 | `PoolNotConfigured` | Explicit temporary override and later pool gate |

An `eth_call` `from` field sets the simulated caller; it does not prove ownership
of that address. The saved authorized caller's keeper-role membership was
checked. The balance override does not deposit funds or alter persistent state.
The compact index's `0` and the repeat summary's `Return 0` describe the same
decoded empty-distribution return, not different transactions.

## 8. Obtain fresh read-only mainnet state

This step requires internet access to the public RPC and Explorer. It collects
new observations; it does not replay the article's data. A new block can have
different balances, configuration, roles or simulated outcomes.

**Windows / PowerShell**

```powershell
..\david-fee-review-env\Scripts\python.exe original-evidence\reproduce.py --simulate --override --output ..\david-fee-live-recheck-01
```

**macOS / Linux**

```bash
../david-fee-review-env/bin/python original-evidence/reproduce.py --simulate --override --output ../david-fee-live-recheck-01
```

Use a new output directory name each time. The script:

1. Checks chain ID 72957 and pins one block.
2. Reads the proxy's EIP-1967 implementation slot.
3. Compares runtime bytes with Explorer's published bytes.
4. Reads balances, allocation, normalized limits, pool, counters and pending RLS.
5. If requested, submits read-only `eth_call` simulations and checks the saved
   simulated keeper's current role membership.
6. Saves numbered requests, responses, labels, the header, implementation
   metadata, `summary.json` and `capture.json` in the new directory.

Open the generated `summary.json` and compare fields rather than expecting its
balance to equal 697.144028784. Record the actual block, hash, timestamp, raw
values and errors. A changed outcome is a new observation to investigate, not a
reason to edit the historical evidence.

To request block **12,018,889**, add `--block 12018889` and use a different output
directory, such as `../david-fee-historical-recheck-01`. Provider retention
determines whether it succeeds. Older receipts can return null and balances can
be pruned. An archive provider retaining the required data may be necessary.

If the implementation pointer changes, runtime bytes disagree, the recorded
keeper loses its role or the provider does not support the requested override,
the script may stop. Do not bypass those checks to preserve an old result.

The live script does not automatically collect six new transaction samples or
re-run a final Ethereum burn trace. Its live role is to capture configuration,
balances and optional gate simulations. The six historical fee reconciliations
are checked separately against their preserved data.

## 9. Optional: send the pinned state requests directly

`requests/reviewer-pinned-state-request.json` contains a small read-only batch
generated from the reviewed calldata. It targets block **12,018,889**, hexadecimal
**`0xb764c9`**, and makes no state overrides. It lets a reviewer use another HTTP
client without running the reproduction script.

**Windows / PowerShell**

```powershell
Invoke-RestMethod -Uri "https://mainnet-rpc.rayls.com/" -Method Post -ContentType "application/json" -Body (Get-Content -Raw .\requests\reviewer-pinned-state-request.json) | ConvertTo-Json -Depth 100 | Set-Content -Encoding utf8 ..\david-fee-manual-rpc-response.json
```

**macOS / Linux with curl**

```bash
curl --fail --show-error --silent --request POST --header 'Content-Type: application/json' --data-binary @requests/reviewer-pinned-state-request.json https://mainnet-rpc.rayls.com/ --output ../david-fee-manual-rpc-response.json
```

The output filename is an example; use a new filename to preserve earlier runs.
Match results by `id`. A successful HTTP request can still contain JSON-RPC
errors or null results. Inspect every result before decoding it.
The PowerShell command saves parsed JSON values rather than the original HTTP
transport bytes.

| ID | Method / inspected field | Comparison with the preserved capture, if data remain available |
|---:|---|---|
| 1 | `eth_chainId` | `0x11cfd`, decimal 72957; this identifies the endpoint, not an independent consensus certificate |
| 2 | `eth_getBlockByNumber` | Number `0xb764c9`; hash `0x059fb75716c5d6602a117405f2adc321426a638a23a737015a612e6bf2a2ba04` |
| 3 | Native Aggregator balance | Raw integer 697144028784000000000 |
| 4 | USDr `balanceOf` | The same raw integer |
| 5 | `getConfig` | ABI words 5,000 / 0 / 5,000 |
| 6 | `getSwapLimits` | ABI words 1,000,000,000 / 100,000,000,000 |
| 7 | `getPoolConfig` | Both captured ABI words are zero |
| 8 | `getStats` | Both captured ABI words are zero |
| 9 | `getPendingDistribution` | Four captured ABI words are zero |
| 10 | EIP-1967 implementation slot | Last 20 bytes identify the captured implementation ending `e4` |
| 11 | Implementation runtime code | Compare exact bytes with saved Explorer `deployed_bytecode` |
| 12 | USDr `decimals` | ABI word 18 |

This batch does not run the seven simulations, compute the six fee differences
or prove a burn. It is a direct state check. Replacing the pinned tag with
`latest` in every request is not equivalent to pinning one new block: a head can
advance between requests. Use Section 8 for a fresh capture that pins one block
and verifies implementation identity before interpreting results.

## 10. Troubleshooting and honest failure handling

| Symptom | Check or next action | What not to conclude |
|---|---|---|
| Python/`py` command is unavailable | Follow the official installation guide; reopen the terminal and check the version | The evidence has failed |
| `can't open file` or missing `verify_package.py` | Return to the extracted package root from Section 1 | A contract or network problem |
| `No module named Crypto` | Install the pinned requirement using the same interpreter that runs the audit | The saved fee arithmetic is wrong |
| Dependency installation fails | Check connectivity and that pip belongs to the chosen environment; original saved checks can still run without it | A blockchain finding |
| A SHA-256 mismatch or manifest file is missing | Restore the complete ZIP and inspect the named path | That an altered file should be accepted by updating its expected hash |
| Missing saved reviewer `response.json` | Restore the complete package; the offline audit deliberately stops | That the script performed a fresh comparison |
| Output directory already exists | Choose a new `--output` directory or use the audit's temporary-directory default | That an earlier result should be overwritten |
| A historical receipt returns null | Preserve the result and seek a provider retaining that receipt if a fresh check is required | The transaction did not exist or its fee was zero |
| A state request reports pruning | Preserve the error and use a suitable archive provider or new captured block | The balance or counter was zero |
| HTTP timeout, rate limit or JSON-RPC error | Save the available response and record the request as unavailable; retry only as a new observation | The pool was zero or a simulation reverted with an ABI-defined error |
| Implementation pointer/code differs | Review the new ABI and source before decoding that deployment | The old ABI still describes current execution |
| Keeper role or override behavior differs | Record the actual block and result; review the changed prerequisite | A simulated `from` address proves possession of a key |
| Fresh state differs from the article | Compare the new block's configuration and inputs, keeping old captures intact | The two historical captures established continuous state |

Do not remove an assertion, rewrite a saved response or substitute zero for an
error to produce a pass. A useful review records **confirmed**, **unavailable**
and **not established** separately.

## 11. Package map, supplementary observations and graphics

| Path | Purpose |
|---|---|
| `david-fee-article-reviewed.md` | Current English manuscript |
| `review-report.md` | Detailed claim review and limitations |
| `claim-audit-reviewed.csv` | Current six-sample claim index |
| `original-evidence/fee-reconciliation.csv` | Six exact fee rows with raw integer columns |
| `original-evidence/facts.json` | Primary findings and additional retained captures |
| `original-evidence/verified-FeeAggregator.sol` | Preserved Explorer-published implementation source |
| `original-evidence/README.md`, `original-evidence/david-fee-guide.md` | Original-investigation inventory and English walkthrough |
| `verification/independent-comparison` | Preserved separate HTTP comparison batch and earlier audit output |
| `verification/independent-pinned-12018889` | Same-block reviewer repeat |
| `verification/fresh-independent` | Earlier fresh reviewer capture at 12,020,199, balance 697.171389504 USDr |
| `verification/supplementary-20261009` | Additional previously recorded block 12,167,402, balance 704.728012176 USDr |
| `requests/reviewer-pinned-state-request.json` | Optional direct historical-state RPC input from Section 9 |
| `images/`, `image-guide.md` | Six PNG/SVG assets, previews, text QA and insertion guidance |
| `PROVENANCE.md`, `package-provenance.json` | File preservation and editorial/package changes |

The supplementary October 9 capture is an additional point, not a replacement
for the article's October 5/7 numbers. It records the same gates and includes its
own raw requests/responses. Its file hashes are checked by `verify_package.py`,
but the two main audit scripts do not claim to validate that extra capture.
See that directory's README before drawing conclusions from it.

Use the full-resolution PNGs for publishing, not the preview files. The new
tables correspond to the article's two highlighted transactions, its two primary
balances and its five standard swap-error rows. The separate funded override
and empty distribution remain in the text and evidence. The earlier cover and
explanatory graphics are retained as alternatives; publishing every image is
not required. Each SVG embeds the supplied Rayls mark unchanged, including its
black background. Capture dates and account watermarks are omitted from the
graphics, while block references retain their scope.

## 12. Record a review without overstating it

A reviewer can record the following checkpoints and attach their own audit
output directory or fresh capture:

| Checkpoint | A supported review statement |
|---|---|
| File inventories pass | The delivered files match the supplied manifests |
| Saved arithmetic passes | All six selected fees and twelve saved balance differences reconcile exactly |
| Separate audit passes | The named offline assertions and available saved rechecks agree |
| October 5 data are unavailable on a later query | The historical recheck could not repeat those observations; preserved responses remain the basis of that calculation |
| Fresh capture succeeds | These new fields were returned at the newly recorded block, under the checked implementation |
| Canonical burn is not traced | This package does not establish a completed final Ethereum RLS burn |

File hashes and RPC/Explorer agreement do not independently authenticate an Axyl
consensus certificate. The sampled accounts' controllers are not identified.
The two main captures do not establish continuous conditions between them.
Zero counters do not establish that Rayls has never burned RLS through any route.

For a burn attribution, a further investigation must connect successful
USDr-to-RLS conversion, RLS allocation, cross-chain delivery and the final
canonical Ethereum token destruction. The available allocation setting or a
simulation cannot substitute for that execution trace.

## 13. Publishing and external references

To publish this edition, replace the previous evidence ZIP in the repository and
use this README as its root README if desired. Keep the reviewed manuscript's
repository link. This delivered package does not claim that those GitHub updates
have already been made. A reviewer should record the downloaded ZIP version or
its own SHA-256 before comparing different copies.

Investigation source links:

- [Rayls Public Chain tokenomics](https://docs.rayls.com/docs/public-chain-tokenomics)
- [Axyl technical specification](https://docs.rayls.com/docs/about-the-axyl-consensus-algorithm)
- [Rayls Public Chain reference](https://docs.rayls.com/docs/public-chain-reference)
- [Fees Aggregator proxy](https://explorer.rayls.com/address/0x07e17e17e17e17e17e17e17e17e17e17e17e17e3)
- [Explorer implementation](https://explorer.rayls.com/address/0x07e17e17e17e17e17e17e17e17e17e17e17e17e4?tab=contract)
- RPC endpoint recorded in the package: `https://mainnet-rpc.rayls.com/`.

Setup and HTTP-client documentation:

- [Python on Windows](https://docs.python.org/3/using/windows.html)
- [Python virtual environments and packages](https://docs.python.org/3/tutorial/venv.html)
- [PowerShell Invoke-RestMethod](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/invoke-restmethod)
- [curl command-line documentation](https://curl.se/docs/manpage.html)

The Rayls links identify the investigation's sources; expanding this README does
not constitute a fresh verification of every linked page. No command here signs
or submits a transaction. No wallet, private key, signature or persistent balance
change is required for these checks. Dependency installation and fresh reads
have their own network requirements, while saved-data verification remains
offline.
