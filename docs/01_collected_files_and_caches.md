## Collected files and caches (observed)

This project uses a **hybrid storage** approach:

- **Raw upstream data** (JSON/mmCIF) is cached as files for reproducibility.
- **Model-ready numeric features** are stored as CSV feature matrices.

---

### Cache directories

- **UniProtKB raw JSON**: `cache/uniprot_json/`
  - One file per UniProt ID: `cache/uniprot_json/{uniprot_id}.json`

- **RCSB PDB experimental structures**: `cache/pdb_structures/`
  - One file per PDB ID: `cache/pdb_structures/{pdb_id}.cif`

- **AlphaFold predicted structures**: `cache/alphafold_structures/`
  - One file per UniProt ID: `cache/alphafold_structures/{uniprot_id}.cif`
  - **Observation in your run**: this directory is empty because all requested AlphaFold downloads returned **HTTP 404** and were logged.

---

### Cache CSV artifacts (feature matrices)

- **Sequence + UniProt features**: `cache/protein_features.csv`
  - Output of joining:
    - UniProt curated summary fields (from cached JSON)
    - Sequence descriptors (AAC/PAAC/CTD)

- **UniProt summary only**: `cache/protein_metadata_summary.csv`
  - Curated UniProt fields (includes annotation and counts) without descriptor vectors.

- **Structure-only features**: `cache/structure_features.csv`
  - Per-protein structure metrics + provenance.

- **Master matrix**: `cache/complete_protein_features.csv`
  - `complete_features_df = protein_feature_df.join(structure_features_df, how="left")`
  - Intended to be merged into DTI datasets by UniProt ID.

---

### Error logs

- `cache/structure_fetch_errors.json`
  - Records structure download failures and is the primary place to debug empty structure caches.

