## UniProt-derived features (what is collected and how it looks)

UniProt data is fetched from:

- `https://rest.uniprot.org/uniprotkb/{uniprot_id}.json`

and cached to:

- `cache/uniprot_json/{uniprot_id}.json`

The notebook then parses a **curated, ML-friendly summary** into a row in the feature matrix.

---

### Observed UniProt summary columns (from `cache/complete_protein_features.csv`)

Core identifiers and sequence-level:

- `sequence_length`
- `sequence_checksum` (observed empty in your run)
- `molecular_weight` (computed from sequence via Biopython `ProteinAnalysis` in this notebook)
- `recommended_name`

Functional annotation (mostly stored as `;`-joined identifiers/text):

- `ec_numbers`
- `go_terms`
- `keywords`
- `reactome`
- `function`
- `pathway`
- `enzyme_regulation`

Expression-related notes:

- `tissue_specificity`
- `developmental_stage`

Isoforms/variants (best-effort parsing):

- `isoform_count`

Feature-table-derived counts (best-effort; depends on presence in UniProt JSON):

- `ft_transmem_count`
- `ft_topo_dom_count`
- `ft_domain_count`
- `ft_region_count`
- `ft_binding_count`
- `ft_ptm_count`
- `ft_variant_count`
- `ft_mutagen_count`

Structure cross-references:

- `pdb_count`
- `pdb_ids`

---

### Observations / caveats from your current data

- **`pdb_ids` is extremely long** for the test proteins (hundreds of PDB IDs). This is useful for provenance, but it will:
  - increase CSV size,
  - and is typically **not directly used as an ML feature** (more of a reference field).

- Several UniProt fields were **missing for all three proteins** (based on notebook output):
  - `pathway`, `developmental_stage`, `enzyme_regulation` were all empty.
  - `sequence_checksum` was empty.
  - This is expected: UniProt records vary by protein.

- The current master CSV **does not include raw sequences** (good for file size / ML readiness). Raw sequences remain in:
  - the raw UniProt JSON cache, and
  - intermediate notebook variables.

