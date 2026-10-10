# Contributing and scope of the frozen release

The two programs in `scripts/` are **frozen snapshots**. This publication does **not** open a new feature-development or patch cycle.

Useful contributions for owner review may include documentation corrections, inaccurate compatibility claims, broken links, synthetic reproducers, and safety clarifications. Do not submit real backup archives, keys, credential hashes or unredacted host logs.

If proposing changes, identify whether they affect **documentation/package only** or would alter source bytes. Source changes are outside the current development freeze and require an explicit future owner decision; do not silently update or reformat the scripts.

Repository integrity is checked against `SHA256SUMS`; changing a script requires a distinct, explicitly approved release process rather than updating the manifest to make a change appear unchanged.

## Repository author and credit

This publication is maintained as a documentation snapshot under the GitHub account [@Saugjunkie2](https://github.com/Saugjunkie2). Please keep attribution notices intact when sharing the code under MIT.
