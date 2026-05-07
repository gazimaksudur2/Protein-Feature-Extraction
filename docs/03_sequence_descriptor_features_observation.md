## Sequence descriptor features (AAC / PAAC / CTD)

The notebook generates standardized descriptors from the primary amino-acid sequence.

---

### AAC (Amino Acid Composition)

- Columns: `aac_A ... aac_V` (20 columns; standard amino acids)
- Values: fractions of each residue in the sequence (sums to ~1)

---

### PAAC (Pseudo Amino Acid Composition)

In your run, PAAC columns were produced by `propy3`:

- Observed columns include: `paac_APAAC1 ... paac_APAAC40`

These are numeric descriptors capturing sequence-order information beyond simple composition.

Note:
- If `propy3` PAAC ever fails to compute, the notebook contains a fallback path to keep the pipeline runnable, but the fallback is intentionally minimal.

---

### CTD (Composition / Transition / Distribution)

CTD features appear as a wide block of columns prefixed with:

- `ctd__...`

They include **C**, **T**, and **D** components across multiple residue property groupings (e.g., polarity, charge, hydrophobicity, etc.).

---

### Observed size in your current master matrix

From the notebook outputs that are reflected in `cache/complete_protein_features.csv`:

- `features_df` (sequence descriptors only) had **207 columns** for the 3 proteins.
- Joined with UniProt summary, the sequence+UniProt table had **232 columns**.

This is normal for CTD-heavy feature sets: CTD contributes many dimensions.

