## Master feature matrix (`complete_protein_features.csv`) — complete observation

The master matrix is:

- `cache/complete_protein_features.csv`

This is the single file intended to be merged into DTI datasets by UniProt ID.

---

### Shape (observed)

From the cached run:

- **Rows**: 3 proteins
  - `P69905`, `P68871`, `P00734`
- **Columns**: 247

---

### Column groups (what you get per protein)

#### 1) Curated UniProt summary (metadata + annotations + counts)

Includes:

- Sequence-level: `sequence_length`, `molecular_weight`, `recommended_name`
- Functional annotation identifiers: `go_terms`, `keywords`, `reactome`, `ec_numbers`
- Text fields (optional / sparse): `function`, `pathway`, `enzyme_regulation`, `tissue_specificity`, `developmental_stage`
- UniProt feature-table counts: `ft_*_count` (transmem, domains, binding, PTM, variants, mutagenesis)
- PDB cross-reference summary: `pdb_count`, `pdb_ids`

**Observation**: many long-text fields are sparse; several were empty across all 3 proteins in your run.

#### 2) Sequence descriptors (numeric, dense)

Includes:

- **AAC** (`aac_*`)
- **PAAC** (`paac_*`)
- **CTD** (`ctd__*`)

**Observation**: this block is the majority of columns and is generally fully populated for valid sequences.

#### 3) Structure descriptors (numeric + provenance)

Includes:

- Provenance: `has_experimental_structure`, `has_alphafold_structure`, `best_pdb_id`, `structure_source`
- Experimental metadata: `pdb_resolution`
- Geometry: `n_residues`, `n_chains`, `radius_gyration`
- B-factor stats: `mean_bfactor`, `std_bfactor`
- Secondary structure fractions: `helix_pct`, `sheet_pct`, `coil_pct`

**Observation**:
- For these proteins, PDB features are populated; AlphaFold is missing (404).
- Secondary structure fractions are NaN without DSSP.

---

### Sparsity / “missingness” notes (observed)

From the notebook’s printed “Top missing columns”:

- `pathway` was missing for all 3 proteins
- `sequence_checksum` missing for all 3
- `developmental_stage` missing for all 3
- `enzyme_regulation` missing for all 3

This is expected because UniProt records vary, and those fields are not guaranteed.

---

### Practical ML guidance

- Use `cache/complete_protein_features.csv` as your **protein feature table**.
- When merging into DTI datasets, perform a **left join** from your DTI dataset onto this table using UniProt IDs.
- Consider dropping high-cardinality “reference” columns (like `pdb_ids`) for modeling; keep them for provenance/debugging.

