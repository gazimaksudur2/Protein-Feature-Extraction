## ProteinFetch (DTI) — Feature Extraction Observations

This repo contains a Jupyter notebook pipeline that builds a **master protein feature matrix** for Drug–Target Interaction (DTI) modeling by combining:

- **UniProtKB-derived protein metadata/features** (fetched via REST, cached as raw JSON)
- **Sequence-derived descriptors** (AAC, PAAC, CTD)
- **Structure-derived descriptors** from **RCSB PDB** (experimental) and **AlphaFold DB** (predicted; optional / best-effort)

The main notebook is `protein_feature_extraction.ipynb`.

---

### What was produced in the current run (observed from cached outputs)

The notebook produced (in `cache/`):

- **Master feature matrix**: `cache/complete_protein_features.csv`
  - Rows: **3 proteins** (`P69905`, `P68871`, `P00734`)
  - Columns: **247**
  - Includes UniProt summary fields, AAC/PAAC/CTD, and structure features.

- **Sequence + UniProt (no structure)**: `cache/protein_features.csv`
  - Rows: **3 proteins**
  - Columns: **232**

- **UniProt summary (no descriptors)**: `cache/protein_metadata_summary.csv`

- **Structure-only features**: `cache/structure_features.csv`
  - Rows: **3 proteins**
  - Columns: **15**

- **Raw UniProt JSON cache**: `cache/uniprot_json/*.json`

- **Structure fetch error log**: `cache/structure_fetch_errors.json`

---

### Is it OK that `cache/alphafold_structures/` is empty?

Yes. In your current run, AlphaFold DB returned **HTTP 404** for all three test UniProt IDs:

- `P69905` → 404
- `P68871` → 404
- `P00734` → 404

Those 404s are recorded in `cache/structure_fetch_errors.json`, so the notebook correctly **did not create any AlphaFold `.cif` files**.

Practical note: AlphaFold availability/URLs can vary by version and entry; the pipeline is written to be **best-effort** and to **fall back to PDB** (experimental structures) when available.

---

### High-level feature inventory (current master CSV)

In `cache/complete_protein_features.csv`, each protein row contains:

- **UniProt summary fields (curated, ML-friendly)**
  - `sequence_length`, `molecular_weight`, `recommended_name`
  - GO/keywords/pathways (as `;`-joined IDs)
  - Counts from UniProt feature table: `ft_transmem_count`, `ft_domain_count`, `ft_variant_count`, etc.
  - PDB cross-reference summary: `pdb_count`, `pdb_ids` (very long)

- **Sequence descriptors**
  - **AAC**: `aac_A ... aac_V` (20 columns)
  - **PAAC**: `paac_*` (from `propy3`; observed `paac_APAAC1..40` in your output)
  - **CTD**: `ctd__*` (composition/transition/distribution for several property groupings; wide block)

- **Structure-derived features**
  - Source flags + provenance: `has_experimental_structure`, `has_alphafold_structure`, `best_pdb_id`, `structure_source`
  - Basic structure metrics: `n_residues`, `n_chains`, `radius_gyration`
  - Experimental-only stats (when PDB used): `pdb_resolution`, `mean_bfactor`, `std_bfactor`
  - Secondary structure fractions: `helix_pct`, `sheet_pct`, `coil_pct` (NaN unless DSSP is installed)

---

### Detailed documentation

See the `docs/` folder:

- `docs/01_collected_files_and_caches.md`
- `docs/02_uniprot_features_observation.md`
- `docs/03_sequence_descriptor_features_observation.md`
- `docs/04_structure_features_observation.md`
- `docs/05_master_feature_matrix_observation.md`

---

### Cloning this repo (Git LFS required for large files)

Some folders/files in this project are tracked with **Git LFS** (see `.gitattributes`). If you clone without Git LFS, you may only get **small pointer files** instead of the real data.

#### Install Git LFS (one-time per machine)

```bash
git lfs install
```

#### Clone normally (LFS downloads automatically)

```bash
git clone https://github.com/gazimaksudur2/Protein-Feature-Extraction.git
cd ProteinFetch
git lfs pull
```

#### If you already cloned without LFS

```bash
cd ProteinFetch
git lfs install
git lfs fetch --all
git lfs checkout
```

#### Verify you have the real LFS files

```bash
git lfs ls-files
```

