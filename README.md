# D4rwin
**D4rwin** is a Perl v5.4 pipeline for downloading, clustering, and analyzing sequences from GenBank or local databases. It streamlines sequence database assembly with ortholog markers for phylogenetic studies, featuring BLAST-based clustering, sequence refinement, and detailed marker summaries, including taxonomic coverage and accession data.

## Key Features

### 1. **Download and Clustering**
- **Taxonomic Database Assembling Phase**: Automatically create a custom database using GenBank data for specific taxonomic groups, based on a user-defined configuration file.
- **BLAST Phase**: Perform local alignments using **BLAST** on large sequence databases, including data from GenBank or custom local datasets.
- **Sequence Clustering Phase**: Group orthologous sequences based on taxonomic coverage, enabling efficient data handling and phylogenetic analysis.

### 2. **Refinement and Post-Processing**
- **Cluster Renaming**: Renames sequence clusters for better clarity using a user-provided list, ensuring easy identification and reusability.
- **Taxonomic Level Filtering**: Filter sequences by taxonomic level and number of sequences, allowing for high-quality marker selection.
- **Post-Processing**: Optional steps to convert and reformat sequence data as required.

### 3. **Molecular Marker Summary**
- **Summary Table Generation**: Create a summary table of molecular markers, including species names, accession numbers, and taxonomic coverage, which can be included in publications.

Further details on each phase are provided in the **manual**.

## Requirements
- **Perl 5 or higher**  
- **BLAST** ([NCBI BLAST+](https://www.ncbi.nlm.nih.gov/books/NBK569861/))  
- NCBI tools for database search and download ([Entrez Direct](https://ftp.ncbi.nlm.nih.gov/entrez/entrezdirect/install-edirect.sh))  
- **Python 3 or higher** (optional, for generating the configuration file or using the pipeline via a graphical interface).
- **Unix-like system** (e.g., **Linux** or **macOS**).  
  - This pipeline is designed to run on **Unix-based operating systems** and is **not supported on Windows** natively.

## License

This project is licensed under the **AGPL-3.0 License**. 

### **Key Points:**
- You may use, modify, and distribute the software freely, but any modified versions must also be released under the **AGPL-3.0**.
- If you modify and deploy the software (even as a service), you must provide the **source code** for your modifications and ensure users can access it.
- You must provide appropriate **attribution** to the original authors and include a copy of the license when redistributing the software.

For more information on the AGPL-3.0 license, visit: [GNU AGPL-3.0 License](https://www.gnu.org/licenses/agpl-3.0.html).

## Acknowledgements

- This project uses **BLAST** for sequence comparison.
- Special thanks to **NCBI** for providing access to GenBank and tools for downloading and searching databases.

## Contact

For questions or contributions, feel free to open an issue or submit a pull request.
