```markdown
# PDB Ligand Site Analysis

This notebook provides a script to analyze protein-ligand binding sites from PDB (Protein Data Bank) structures. It automates the process of:

1.  **Downloading PDB files** from RCSB (files.rcsb.org).
2.  **Parsing ATOM/HETATM records** to identify protein atoms and ligand groups.
3.  **Computing key properties for each ligand** (excluding common solvents/ions), such as:
    *   Centroid coordinates.
    *   Ligand bounding extents and suggested docking box sizes for AutoDock Vina.
    *   Protein-ligand contacts within a specified cutoff (default 4.5 Å).
    *   Heuristic classification of the binding site (orthosteric, allosteric, ambiguous).
4.  **Generating output files** including a master report, a master JSON, per-ligand reports, per-ligand JSON metadata, and AutoDock Vina configuration snippets.

## Usage

1.  **Set `pdb_id`**: Modify the `pdb_id` variable in the main script cell to your desired PDB entry (e.g., `pdb_id = "5XRA"`).
2.  **Run the script**: Execute the cell containing the main processing script.
3.  **Review Outputs**: The script will print the download status, paths to generated files, and a preview of the master report.
4.  **Download Files**: Output files (reports, JSON, Vina confs) are saved to `/mnt/data/` and can be downloaded via the Colab Files sidebar or the generated download links.

## Parameters

Key parameters that can be adjusted in the script:

*   `pdb_id`: The PDB ID to analyze.
*   `output_dir`: Directory for output files (default: `/mnt/data`).
*   `recommended_default_box`: Default dimensions for the docking box (Å).
*   `vina_exhaustiveness`: Exhaustiveness parameter for Vina config.
*   `contact_cutoff`: Distance cutoff for defining protein-ligand contacts (Å).
*   `COMMON_IGNORE`: List of common solvent/ion residue names to exclude from ligand analysis.
*   `PRIORITY_LIGANDS`: (Currently unused in the script, but available for custom filtering) List of ligand residue names to prioritize.

## Example

If running with `pdb_id = "8T3V"` (as in a recent execution), the script will download the PDB, analyze its ligands, and generate reports like:

*   `/mnt/data/master_report_8T3V.txt`
*   `/mnt/data/master_report_8T3V.json`
*   `/mnt/data/vina_conf_8T3V_CLR_R_301.conf` (for a specific ligand)
*   `/mnt/data/grid_8T3V_HXA_R_302.txt` (for another specific ligand)


**Author**: assistant (software-engineer style)
**License**: MIT
```
