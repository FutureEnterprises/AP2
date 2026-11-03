# Seven offline unknown-outcome controls

Published November 3, 2026 on this review branch. [Open the complete seven-file packet](ap2-all-seven/README.md).

All seven packet files preserve the reviewed bytes, including the README and provenance statements written before publication was approved. Those preparation-time statements describe the local packet at that time; this note records its subsequent public publication.

The controls were tested against AP2 commit `7634eac4aa479a8b42f701b13074728217dbe9e8`. The test file SHA-256 is `ce692846bd570d77b99ef1e2bc1e44fbc595ac0a16dc7388f1435c0637e7d611`. Run `sha256sum -c SHA256SUMS` inside `ap2-all-seven` and follow its reproduction instructions against the pinned baseline.

There are seven collected cases from five test functions. Re-signing equal checkout payloads deliberately still produces two sample receipts under distinct hash identities. This characterizes the existing boundary; the proposed occurrence-identity change and four future cases are not implemented here. No real payment or settlement is claimed.

This branch adds review tests and documentation only. It changes no application behavior. No upstream pull request has been opened or merged as part of this publication.
