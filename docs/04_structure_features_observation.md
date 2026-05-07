## Structure-derived features (RCSB PDB + AlphaFold DB)

The notebook attempts to enrich each protein with 3D structure-derived descriptors using:

- **RCSB PDB** experimental mmCIFs: `https://files.rcsb.org/download/{pdb_id}.cif`
- **AlphaFold DB** predicted mmCIFs: `https://alphafold.ebi.ac.uk/files/AF-{uniprot_id}-F1-model_v4.cif`

The output structure feature table is cached to:

- `cache/structure_features.csv`

and failures are logged to:

- `cache/structure_fetch_errors.json`

---

### Selection logic (observed behavior)

Per UniProt ID:

1. Extract all linked PDB IDs from the UniProt record (`uniProtKBCrossReferences`).
2. Download/cached those mmCIFs (best-effort).
3. Compute structure features for available PDB CIFs and choose:
   - **best resolution** when resolution is available in mmCIF metadata
   - otherwise, first available CIF
4. If no PDB CIF is usable, try AlphaFold CIF.
5. If neither exists, fill structure fields with NaN and source `"None"`.

---

### Observed structure columns (from `cache/structure_features.csv`)

Provenance:

- `has_experimental_structure` (bool)
- `has_alphafold_structure` (bool)
- `best_pdb_id`
- `pdb_file_path`
- `alphafold_file_path`
- `structure_source` (`"PDB" | "AlphaFold" | "None"`)

Experimental metadata:

- `pdb_resolution` (Å; NaN if unavailable or AlphaFold)

Basic structure metrics:

- `n_residues`
- `n_chains`
- `radius_gyration`

Flexibility proxy (B-factor; meaningful mainly for experimental structures):

- `mean_bfactor`
- `std_bfactor`

Secondary structure composition:

- `helix_pct`
- `sheet_pct`
- `coil_pct`

---

### Key observations from your run

AlphaFold cache status:

- `cache/alphafold_structures/` is empty because AlphaFold DB returned **404** for:
  - `P69905`, `P68871`, `P00734`
  - These failures are explicitly recorded in `cache/structure_fetch_errors.json`.

PDB availability:

- All 3 proteins used **experimental PDB** (`structure_source = "PDB"`).
- Selected best structures (as observed in the CSV):
  - `P69905` → `best_pdb_id = 2W72`, `pdb_resolution = 1.07`
  - `P68871` → `best_pdb_id = 2W72`, `pdb_resolution = 1.07`
  - `P00734` → `best_pdb_id = 5AFY`, `pdb_resolution = 1.12`

Secondary structure:

- `helix_pct/sheet_pct/coil_pct` are empty (NaN) because DSSP is not available by default.
  - This is expected and acceptable per the notebook requirements.

