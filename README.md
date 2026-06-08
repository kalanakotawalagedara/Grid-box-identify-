# Grid Box Identify

An automated tool for identifying and analyzing ligand binding sites in protein structures from PDB files. This tool generates docking grid parameters for AutoDock Vina by analyzing protein-ligand interactions and detecting pockets in apo-proteins.

## Features

### Method 1: Co-crystallized Ligand-Based Detection
- **Automated PDB Fetching**: Downloads PDB files directly from RCSB Protein Data Bank
- **Ligand Detection**: Automatically identifies and filters ligand molecules (excluding common solvents and ions)
- **Binding Site Analysis**:
  - Calculates ligand centroid positions
  - Computes protein-ligand contact residues
  - Analyzes hydrophobic interactions
  - Classifies binding sites (orthosteric vs. allosteric)
- **Docking Grid Generation**: Creates AutoDock Vina configuration files with optimized search space parameters
- **Multiple Output Formats**: Generates JSON and TXT reports for easy integration with docking workflows

### Method 2: Fpocket Pocket Detection (for Apo-Proteins)
- **Fpocket Integration**: Uses open-source Fpocket software for pocket detection
- **Apo-Protein Support**: Works with protein structures without co-crystallized ligands
- **Druggability Scoring**: Ranks pockets by druggability score
- **Volume Analysis**: Provides pocket volume measurements
- **Automated Centroid Calculation**: Generates grid box centers for detected pockets

## Usage

### Running in Google Colab

1. Open the `Grid_Box_Prep.ipynb` notebook in Google Colab

#### Method 1: Co-crystallized Ligand Analysis
2. Run the first code cell and enter your PDB ID when prompted:
   ```python
   pdb_id = input("Enter PDB ID (e.g., 3ERT): ") or "3ERT"
   ```
3. The script will download and analyze all ligands in the structure

#### Method 2: Fpocket Pocket Detection
2. Run the Fpocket installation cell (installs via Conda)
3. Run the pocket detection cell and upload your PDB file when prompted
4. Fpocket will detect and rank all pockets by druggability score

### Output Files

#### Method 1 Output:
- `master_report_{PDB_ID}.txt` - Summary report for all ligands
- `master_report_{PDB_ID}.json` - JSON format for automation
- `ligand_grid_coordinates_{PDB_ID}.csv` - CSV with grid coordinates for all ligands
- `grid_{PDB_ID}_{LIGAND}_{CHAIN}_{RESSEQ}.txt` - Per-ligand grid report
- `grid_{PDB_ID}_{LIGAND}_{CHAIN}_{RESSEQ}.json` - Per-ligand metadata
- `vina_conf_{PDB_ID}_{LIGAND}_{CHAIN}_{RESSEQ}.conf` - AutoDock Vina configuration

#### Method 2 Output:
- `fpocket_pockets_{FILENAME}.csv` - CSV with all detected pockets, ranked by druggability
- Individual pocket PDB/PQR files in the output directory

## Configuration Parameters

### Method 1 Parameters:
```python
pdb_id = "8T3V"                              # Target PDB ID
output_dir = "/mnt/data"                     # Output directory
recommended_default_box = (30.0, 30.0, 30.0) # Default box size (Å)
vina_exhaustiveness = 50                     # Vina exhaustiveness parameter
contact_cutoff = 4.5                         # Protein-ligand contact distance (Å)
```

#### Customizable Parameters:
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
/mnt/data/ligand_grid_coordinates_8T3V.csv
/mnt/data/vina_conf_8T3V_CLR_R_301.conf
/mnt/data/grid_8T3V_HXA_R_302.txt
```

## Requirements

- Python 3.x
- Google Colab (or local Python environment)
- Libraries: `requests`, `json`, `math`, `statistics`, `collections`, `textwrap`, `pandas`
- **Fpocket** (installed automatically via Conda in the notebook)

## How It Works

### Method 1: Co-crystallized Ligand Analysis
1. **Download PDB**: Fetches the structure from files.rcsb.org
2. **Parse Structure**: Separates protein atoms (ATOM records) from heteroatoms (HETATM records)
3. **Filter Ligands**: Excludes common solvents (HOH, WAT) and ions (NA, CL, etc.)
4. **Analyze Each Ligand**:
   - Computes centroid and bounding box
   - Identifies protein residues within contact distance
   - Calculates hydrophobic interaction fraction
   - Classifies binding site type
5. **Generate Reports**: Creates text, JSON, CSV, and Vina config files

### Method 2: Fpocket Pocket Detection
1. **Install Fpocket**: Automatically installs Fpocket via Conda
2. **Upload PDB**: User uploads apo-protein structure
3. **Run Fpocket**: Detects all pockets in the structure
4. **Parse Results**: Extracts pocket coordinates, volumes, and druggability scores
5. **Rank Pockets**: Sorts pockets by druggability for prioritization
6. **Export CSV**: Generates downloadable CSV with all pocket information

## Notes

- The script automatically filters common solvents and ions (HOH, WAT, NA, CL, etc.)
- Box sizes are automatically adjusted based on ligand dimensions with 8 Å padding
- Classification is heuristic; consult literature and SITE records for authoritative annotations
- **Method 1** works best for holo-proteins with co-crystallized ligands
- **Method 2** (Fpocket) is ideal for apo-proteins or when exploring alternative binding sites
- Fpocket coordinates are automatically calculated from PQR files if not found in info.txt

## License

MIT License - Feel free to adapt and use for your research

## Author

Software-engineer style automated docking grid preparation tool

## Citation

If you use this tool in your research, please cite the relevant software:

### AutoDock Vina:
- Trott, O., & Olson, A. J. (2010). AutoDock Vina: improving the speed and accuracy of docking. *Journal of computational chemistry*, 31(2), 455-461.

### Fpocket:
- Le Guilloux, V., Schmidtke, P., & Tuffery, P. (2009). Fpocket: An open source platform for ligand pocket detection. *BMC bioinformatics*, 10(1), 168.
- GitHub: https://github.com/Discngine/fpocket

### PDB Structures:
Please also cite the original PDB entries used in your analysis.
