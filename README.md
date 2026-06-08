# Grid Box Identify

An automated tool for identifying and analyzing ligand binding sites in protein structures from PDB files. This tool generates docking grid parameters for AutoDock Vina by analyzing protein-ligand interactions and classifying binding sites.

## Features

- **Automated PDB Fetching**: Downloads PDB files directly from RCSB Protein Data Bank
- **Ligand Detection**: Automatically identifies and filters ligand molecules (excluding common solvents and ions)
- **Binding Site Analysis**:
  - Calculates ligand centroid positions
  - Computes protein-ligand contact residues
  - Analyzes hydrophobic interactions
  - Classifies binding sites (orthosteric vs. allosteric)
- **Docking Grid Generation**: Creates AutoDock Vina configuration files with optimized search space parameters
- **Multiple Output Formats**: Generates JSON and TXT reports for easy integration with docking workflows

## Usage

### Running in Google Colab

1. Open the `gridboxprep.ipynb` notebook in Google Colab
2. Modify the `pdb_id` variable to your target PDB ID:
   ```python
   pdb_id = "8T3V"  # Change to your desired PDB ID
   ```
3. Run all cells to generate the reports

### Output Files

The script generates the following files for each analysis:

- `master_report_{PDB_ID}.txt` - Summary report for all ligands
- `master_report_{PDB_ID}.json` - JSON format for automation
- `grid_{PDB_ID}_{LIGAND}_{CHAIN}_{RESSEQ}.txt` - Per-ligand grid report
- `grid_{PDB_ID}_{LIGAND}_{CHAIN}_{RESSEQ}.json` - Per-ligand metadata
- `vina_conf_{PDB_ID}_{LIGAND}_{CHAIN}_{RESSEQ}.conf` - AutoDock Vina configuration

## Configuration Parameters

```python
pdb_id = "8T3V"                              # Target PDB ID
output_dir = "/mnt/data"                     # Output directory
recommended_default_box = (30.0, 30.0, 30.0) # Default box size (Å)
vina_exhaustiveness = 50                     # Vina exhaustiveness parameter
contact_cutoff = 4.5                         # Protein-ligand contact distance (Å)
```

### Customizable Parameters

- **`pdb_id`**: The PDB ID to analyze
- **`output_dir`**: Directory for output files (default: `/mnt/data`)
- **`recommended_default_box`**: Default dimensions for the docking box (Å)
- **`vina_exhaustiveness`**: Exhaustiveness parameter for Vina config
- **`contact_cutoff`**: Distance cutoff for defining protein-ligand contacts (Å)
- **`COMMON_IGNORE`**: List of common solvent/ion residue names to exclude from ligand analysis
- **`PRIORITY_LIGANDS`**: List of ligand residue names to prioritize (available for custom filtering)

## Binding Site Classification

The tool uses heuristic rules to classify binding sites:

- **Orthosteric**: ≥8 contacts with ≥40% hydrophobic residues (buried in hydrophobic pocket)
- **Ambiguous**: 3-7 contacts (possible allosteric or shallow orthosteric)
- **Allosteric/Peripheral**: ≤2 contacts (few protein interactions)

## Example Output

For PDB 8T3V, the script identified two ligands:

**CLR (chain R, resseq 301)**
- Binding site center: 111.276, 119.873, 97.307 Å
- Box size: 30.016 × 30.0 × 30.0 Å
- Classification: Likely allosteric or peripheral (2 contacts)

**HXA (chain R, resseq 302)**
- Binding site center: 103.045, 122.47, 79.463 Å
- Box size: 30.0 × 30.0 × 33.079 Å
- Classification: Likely orthosteric (17 contacts, 72% hydrophobic)

### Sample Files Generated

```
/mnt/data/master_report_8T3V.txt
/mnt/data/master_report_8T3V.json
/mnt/data/vina_conf_8T3V_CLR_R_301.conf
/mnt/data/grid_8T3V_HXA_R_302.txt
```

## Requirements

- Python 3.x
- Google Colab (or local Python environment)
- Libraries: `requests`, `json`, `math`, `statistics`, `collections`, `textwrap`

## How It Works

1. **Download PDB**: Fetches the structure from files.rcsb.org
2. **Parse Structure**: Separates protein atoms (ATOM records) from heteroatoms (HETATM records)
3. **Filter Ligands**: Excludes common solvents (HOH, WAT) and ions (NA, CL, etc.)
4. **Analyze Each Ligand**:
   - Computes centroid and bounding box
   - Identifies protein residues within contact distance
   - Calculates hydrophobic interaction fraction
   - Classifies binding site type
5. **Generate Reports**: Creates text, JSON, and Vina config files

## Notes

- The script automatically filters common solvents and ions (HOH, WAT, NA, CL, etc.)
- Box sizes are automatically adjusted based on ligand dimensions with 8 Å padding
- Classification is heuristic; consult literature and SITE records for authoritative annotations
- Falls back to embedded example (5XRA) if internet is unavailable

## License

MIT License - Feel free to adapt and use for your research

## Author

Software-engineer style automated docking grid preparation tool

## Citation

If you use this tool in your research, please cite the original PDB entries and AutoDock Vina:
- Trott, O., & Olson, A. J. (2010). AutoDock Vina: improving the speed and accuracy of docking. *Journal of computational chemistry*, 31(2), 455-461.
