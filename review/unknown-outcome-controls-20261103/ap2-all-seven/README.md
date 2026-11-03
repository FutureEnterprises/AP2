# AP2: seven offline unknown-outcome controls

Local review proposal for AP2 PR #347 at
`7634eac4aa479a8b42f701b13074728217dbe9e8`.
No public submission or publication has been made.

The patch adds only
`code/samples/python/tests/payment_processor_unknown_outcome_tests.py`.
It replaces the earlier one-case proposal; do not apply both versions.
No application behavior or protocol format changes.

## What the seven cases check

1. A signed receipt is created and its delivery is deliberately lost. A fresh,
   valid authorization/token for the same exact checkout JWT passes actual
   verification and constraints, then receives `mandate_already_used`. A new
   checkout positive control succeeds.
2. Corrupt SQLite replay state fails closed before any receipt.
3. A directory used as the replay-state file fails closed before any receipt.
4. A busy SQLite database fails closed after the implementation's real timeout.
5. Eight distinct, valid authorizations/tokens race one checkout: one receipt,
   seven refusals and one SQLite key.
6. Re-signing the exactly equal validated checkout payload produces a different
   legitimate ES256 JWT and AP2 transaction hash. Both valid transactions create
   receipts and distinct SQLite keys. This deliberately characterizes hash
   identity; it is not a same-hash replay bypass or a logical-order solution.
7. A fresh Python interpreter reopens the preserved database after the first
   unknown outcome and refuses the replacement authorization, with no new
   receipt.

There are five test functions; the unavailable-state function has three
parameters, producing seven collected cases. These repeat the seven already-run
controls in a portable form, not seven additional scientific observations.
The four proposed future occurrence-identity cases are not implemented or
claimed to pass here.

## Reproduce in an ordinary checkout

Use CPython 3.12 and a checkout at the exact pin above. The source is public at
<https://github.com/FutureEnterprises/AP2/tree/7634eac4aa479a8b42f701b13074728217dbe9e8>;
the upstream change is <https://github.com/google-agentic-commerce/AP2/pull/347>.
For the commands below, place this packet in `../ap2-all-seven` beside the
checkout, then run from the checkout root.

```sh
git rev-parse HEAD
python3.12 -m venv ../ap2-review-venv
../ap2-review-venv/bin/python -m pip install -r ../ap2-all-seven/requirements.txt
git apply --check ../ap2-all-seven/ap2-unknown-outcome-all-seven.patch
git apply ../ap2-all-seven/ap2-unknown-outcome-all-seven.patch
PYTHONPATH=code/sdk/python:code/samples/python/src \
  PYTHONDONTWRITEBYTECODE=1 \
  ../ap2-review-venv/bin/python -B -m pytest -q -p no:cacheprovider \
  --basetemp=../ap2-review-temp --junitxml=../ap2-review-junit.xml \
  code/samples/python/tests/payment_processor_unknown_outcome_tests.py
../ap2-review-venv/bin/ruff check --config .ruff.toml \
  code/samples/python/tests/payment_processor_unknown_outcome_tests.py
../ap2-review-venv/bin/ruff format --check --config .ruff.toml \
  code/samples/python/tests/payment_processor_unknown_outcome_tests.py
```

`requirements.txt` records the fully pinned tested environment, including Ruff.
It omits unrelated ADK, x402 and Web3 samples. Tests import the actual checkout
through ordinary `PYTHONPATH`; no installed AP2 package, private directory,
custom conftest or absolute source path is required. The child interpreter
inherits that same relative import configuration.

## Isolation and exact scope

The pytest fixture sets test-owned log/default-state directories before SDK and
server imports, redirects the SDK log and module loggers, and patches all state
and key locations to `tmp_path`. The child process does the same before import.
Keys are generated during the test; no private key material is shipped. Runtime
traces/receipts remain under pytest temporary state and are not in this packet.
IPv4/IPv6 connection attempts are refused in both parent and child.

The real SDK signer/verifier, chain constraints, hashes, SQLite reservation,
receipt creation and ES256 receipt signing execute. Spies delegate to those
functions. The merchant's used-token/order handoff is represented locally and
outbound receipt delivery is substituted. There is no PSP/bank/payment-network
call, live merchant agent, multihost test or power-loss claim. A receipt is the
sample effect proxy, not evidence of a real charge.

Replay safety assumes the SQLite marker is retained. No result proves safe
database deletion/rollback, a complete outcome ledger or authenticated provider
reconciliation. The re-signing result requires an explicit higher-level business
identity and trusted policy if two hashes must denote one payment occurrence.

## Recorded validation and provenance

The portable file passes all seven cases against the pinned public code.
Ruff check and format check pass under that snapshot's `.ruff.toml`.
`results.json` records the environment and scoped outcomes;
`PROVENANCE.json` and `SHA256SUMS` bind the exact patch and test bytes.
The local source snapshot has no Git object database, so no source-based local
commit is claimed. The immutable baseline pin plus patch digest identifies the
proposal. No application source snapshot, private keys or runtime state is
bundled.

Read `CONTRIBUTING.md` before a future upstream submission: it requires the
appropriate CLA and code review. This local packet does not sign a CLA, open a
PR, publish results or authorize either action.
