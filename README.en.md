# gangmu-action

One step in GitHub Actions that builds an SBOM and a cryptographic BOM (CBOM) for embedded C/C++ firmware with [gangmu](https://github.com/GANGMU-SBOM/gangmu), and writes a post-quantum readiness summary.

```yaml
- uses: actions/checkout@v4
- uses: GANGMU-SBOM/gangmu-action@main
  with:
    compile-db: build/compile_commands.json   # optional, recommended
    fail-on: quantum-vulnerable               # optional; without it the job never fails on algorithms
```

Outputs (in `gangmu-out/` by default, also uploaded as an artifact): `sbom.cdx.json`, `cbom.cdx.json` (CycloneDX 1.6) and `pqc-readiness.md`, which is also added to the run's Summary page.

Inputs: `path`, `compile-db`, `link-map`, `sbom`, `cbom`, `fail-on` (space-separated `quantum-vulnerable` / `legacy`), `output-dir`, `upload`, `artifact-name`, `install-spec` (what pip installs; pin it for reproducible runs), `extra-packages`, `python-version`. See `action.yml` for defaults.

Notes:

* The readiness summary needs gangmu 0.9 or later; an older gangmu skips it with a warning and still writes the SBOM and CBOM.
* PyPI currently has gangmu-sbom 0.8.0. Until 0.9.0 is published, set `install-spec: git+https://github.com/GANGMU-SBOM/gangmu.git@main` to get the summary.
* Without `compile-db` the CBOM lists what the tree contains, not what was built.
* Algorithms are found by name; key sizes and protocols are not inspected.

Apache-2.0.
