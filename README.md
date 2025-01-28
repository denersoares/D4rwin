# D4rwin
**D4rwin** is a Perl pipeline for downloading, clustering, and analyzing sequences from GenBank or local databases. It streamlines sequence database assembly with ortholog markers for phylogenetic studies, featuring BLAST-based clustering, sequence refinement, and detailed marker summaries, including taxonomic coverage and accession data.

## Key Features

The pipeline can be executed using a single script. You can choose to run it through:
1. **Terminal**: Via Perl scripts, executed directly from the terminal with `perl`.
- **Terminal (Perl)**: 
  ```bash
  perl D4rwin.pl
  ```
2. **Graphical Interface**: Using a Python script (`python3`), providing a user-friendly graphical interface.
**Usage:**
- **Graphical Interface for D4rwin (Python)**: 
  ```bash
  python3 D4rwin.py
  ```
- **Graphical for Configuration File Builder (Python)**: 
  ```bash
  python3 Configure_D4rwin_Builder.py
  ```
---

## **1. Setup and Installation**

### **1.1 Prerequisites**

Before using the D4rwin pipeline, ensure that the following are installed:

- **Perl 5 or higher** for running the Perl scripts.
- **BLAST** ([NCBI BLAST+](https://www.ncbi.nlm.nih.gov/books/NBK569861/)).
- **NCBI tools** for sequence search and download ([Entrez Direct](https://ftp.ncbi.nlm.nih.gov/entrez/entrezdirect/install-edirect.sh)).
- **Python 3 or higher** (optional, for the graphical interface).

The pipeline is designed to work on **Unix-like systems** (e.g., **Linux** or **macOS**). It is not supported on **Windows** natively.

---

## **2. Workflow Overview**

The pipeline operates in three main phases:

1. **Download and Clustering**
   - Taxonomic Database Assembling
   - BLAST Alignment
   - Sequence Clustering

2. **Refinement and Post-Processing**
   - Cluster Renaming
   - Taxonomic Level Filtering
   - Post-Processing (optional)

3. **Molecular Marker Summary**
   - Generation of summary tables for markers

Each of these phases is controlled by specific commands, which are explained in detail below.

---

## **3. Command Details**

### **3.1 Download and Clustering Phase**

#### **3.1.1 Taxonomic Database Assembling (`txnmc_db_only`)**

This command automates the creation of a custom database using GenBank data for specific taxonomic groups. It reads a configuration file that includes parameters like taxonomic identifiers (TxIds), search terms (e.g., 'NOT predicted'), sequence size limits, and batch size. It uses NCBI’s esearch and efetch tools to search and download sequences in batches. 

Key Features:
- Generates a text file with accession numbers for the downloaded sequences in FASTA format.
- Includes batch processing to manage large datasets, improving computational efficiency.
- Handles download errors by retrying up to four times with increasing intervals (5 minutes, 10 minutes, 30 minutes, and 1 hour).
- Implements a one-hour time limit for each execution to ensure the task is restarted in case of server overload.
- Logs successes, failures, and retry attempts for better tracking.

This phase can optimize sequence downloads for large datasets and smaller sequence collections (without batch processing).

---

#### **3.1.2 BLAST Alignment (`blst_only`)**

This command automates the execution of BLAST on large sequence databases, enabling batch analysis of sequences partitioned by the `txnmc_db_only` command or any other FASTA files in the same directory. It also allows for direct comparisons without batch partitioning if preferred.

Key Features:
- Executes BLAST using configuration parameters like E-value, minimum sequence coverage, number of threads (processors), database type (nucleotides, proteins, etc.), use of the DUST filter, and strand orientation (positive, negative, or both).
- Includes multiple retries (up to 3 attempts) in case of errors or memory exhaustion, with increasing time intervals between attempts (10, 15, and 30 minutes).
- If the maximum retries are reached without success, the batch is skipped, and the process continues, allowing the user to split the remaining batches for further analysis.
- Generates a table file (.outfl) containing essential alignment data, such as:
  - Accession numbers for the query and reference sequences.
  - Total sequence length post-alignment.
  - E-value and coverage details (qcovs and qcovhsp).
  
This phase ensures thorough sequence alignment, quantifying the quality of sequences and aiding in the subsequent clustering phase.

---

#### **3.1.3 Sequence Clustering (`clstr_only`)**

The *clstr_only* command automates the analysis of the BLAST output file, identifying and clustering orthologous sequences based on user-defined taxonomic coverage.

Key Features:
- Allows for more complex filtering, meeting multiple simultaneous requirements to build both qualitative (ortholog-based) and quantitatively well-sampled clusters.
- Supports integration of a custom taxonomic dictionary (following the example in *ALL_taxonomy_summary.tsv* and maintaining the same name) or the widely used NCBI standard dictionary. Users are encouraged to standardize the nomenclature manually or using regular expressions before BLAST and clustering.
- Implements parallel processing for increased efficiency, supporting large taxonomic groups and high-performance computing infrastructures.
- Automatically extracts clustering criteria from the configuration file, including:
  - Minimum sequence number and thresholds for BLAST parameters (alignment length, E-value, qcovs, qcovshsp).
  - Minimum sequences per species, genus, family, order, and/or class.
  - Option for automatic or manual taxonomic coverage determination.
  - Number of processors for cluster assembly.
- The process consists of two stages:
  1. **Filtering**: Filters BLAST results to retain only those meeting the minimum criteria. Redundancies are eliminated during this step.
  2. **Taxonomic Analysis**: Sequences are evaluated based on the defined taxonomic criteria, generating two tables: one for matching sequences and one for non-matching sequences.
- Final clusters are extracted from the main sequence database in FASTA format and stored in a newly created directory (*0.Clusters*). Sequence headers are simplified to "<species_accession_number>" for easier organization.

**Additional Outputs**:
- **Two tables in the *0.Filtered_info* directory**: One for clusters that meet the criteria and another for those that don't. These tables display the number of species, genera, orders, families, classes, as well as the names of the taxa and their accession numbers.
- **A table with full GenBank sequence headers** (`Table_Clusters`) inside the *0.Clusters* directory. This table is generated to allow further refinement, such as removing sequences that don't belong to the cluster and creating a list for renaming clusters, **3.2.1 Cluster Renaming (`rnm_clstr`)**.
- **Manual cluster creation**: Users can create a text list of cluster names in the format "Cluster_markername" for further analysis.

---

#### **3.1.4 Automatization of the First Phase: Download and Clustering**

Two commands have been developed to automate the execution of the steps described above: `txnmc_clstr_ncbi` and `txnmc_clstr_local`. The `txnmc_clstr_ncbi` command automatically performs all three steps of the process, from accessing sequences in NCBI. The `txnmc_clstr_local` command, on the other hand, executes only the last two steps and is designed to handle locally obtained data from GenBank or other sources.

---

### **3.2 Refinement and Post-Processing Phase**

#### **3.2.1 Cluster Renaming (`rnm_clstr`)**

The *rnm_clstr* command renames sequence clusters to make file names more informative and organized.

Key Features:
- Renames FASTA sequence cluster files based on a list of new names provided in a text file (`list.txt`).
- Creates the `0.Renamed_Clusters` directory for storing renamed files. If the directory does not exist, it will be created automatically.
- The script reads all FASTA files from the source directory and matches them with names in the `list.txt` file.
- Renamed files are copied into the output directory, retaining the original clusters in the source directory for future use or corrections.
- Users can avoid accidental file loss by ensuring the original sequence clusters are not deleted.

---

#### **3.2.2 Taxonomic Level Filtering (`filtr_per_txnmc_lvl`)**

The *filtr_per_txnmc_lvl* command filters FASTA sequences based on their taxonomic level and user-defined sequence count. This command also allows the user to combine sequence clusters that share the same taxonomic level, which were previously renamed using the *rnm_clstr* command. The final output consists of molecular markers ready for phylogenetic analysis.

Key Features:
- **Taxonomic Level Filtering**: Filters sequences based on the specified taxonomic level, such as species, genus, family, order, or class.
- **Cluster Combination**: Users can choose to combine clusters of sequences from the same marker before filtering, ensuring that redundant sequences are handled early.
- **Hybrid Species Support**: Handles hybrid species names (e.g., *Bos indicus x Bos taurus*) for accurate filtering.
- **Multi-level Filtering**: Allows filtering at taxonomic levels higher than species, such as genus, family, order, and class, in two stages:
  1. **Stage 1**: Run the command at the species level to remove duplicates and combine identical marker clusters.
  2. **Stage 2**: Rerun the command with the desired higher taxonomic level (e.g., genus, family, order, or class) to append corresponding taxonomic information to the sequence names.

**Additional Outputs**:
- **Filtered Sequences**: Filtered sequences are saved in a directory called *0.Clusters_filtered*, with taxonomic labels added as per the chosen level.
- **Taxonomic Information**: The taxonomic labels are appended to species names, ensuring the sequences are clearly identified for further analysis.
- **Filtered Clusters by Taxonomic Level**: Filtered markers are stored in a directory named according to the selected taxonomic level (e.g., *0.Clusters_filtered_per_Genus*).

---

#### **3.2.3 Post-Processing (`cnvrt_mrkrs` and `reformat`)**

The *Post-processing* stage includes optional commands that are highly useful for users who want to adapt their data for use in the D4rwin pipeline. These commands—*reformat* and *cnvrt_mrkrs*—ensure data compatibility and enhance sequence identification. Both commands support hybrid species names, which should be formatted as "Species x Species," ensuring correct identification and filtering of these sequences.

Key Features:

- **reformat**: This command adjusts the sequence label format for all sequences in a single FASTA file, located in a single directory, simplifying and standardizing the naming convention by converting GenBank headers to a simplified format (`>Species_AccessionNumber`).
  
- **cnvrt_mrkrs**: This command follows a similar logic to *reformat*, but is used to convert final sequences or sequences of interest, preparing them for reuse in BLAST analysis. The key differences include:
  - Replaces underscores (`_`) with spaces.
  - Adds the file name to the end of the sequence header.
  - Precedes the header with the accession number (e.g., `>AccessionNumber Species Marker`).
  This process enables the identification of final markers and their corresponding species, as referenced in the *clstr_only* output.

---

### **3.3 Molecular Marker Summary (`summary_markers`)**

The *summary_markers* command is designed to generate a table summarizing sequence and taxonomic coverage information from FASTA sequence files, using data from an NCBI or custom taxonomic table. This table can be attached to publications, providing a concise summary of the diversity and taxonomic coverage sampled.

Key Features:
- Extracts scientific names and accession numbers for species to generate a summary of taxonomic diversity.
- Provides details on the sampled taxonomic levels, including species, genus, family, order, and class.
- Offers insights into the taxonomic coverage of the markers used (see Appendix 3).
- Supports hybrid species names, typically formatted as "Species x Species," ensuring proper identification.

---

## **4. Advanced Options**

- **Batch Processing**: For large datasets, the pipeline allows batch processing at every step. This is managed through the configuration file.
- **Custom Databases**: You can integrate custom sequence databases into the BLAST alignment and clustering steps.
- **Dynamic Integration**: The pipeline allows the integration of new sequence data dynamically by placing it in the same directory for future analysis.

---

## **5. Troubleshooting and FAQ**

- **Issue**: "BLAST alignment is taking too long."
  - **Solution**: Ensure that you are using appropriate batch sizes and taxonomic levels to optimize processing time.

---

## **6. Contact and Support**

For further assistance or questions, please feel free to open an issue or submit a pull request on the GitHub repository.

---

This updated manual includes the clarification about the two ways of executing the script and how the Python script can help generate the configuration file. Let me know if you need any further changes! 😊

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

For questions or contributions, feel free to open an issue, submit a pull request, or contact us via email at [denerdacosta12@gmail.com].
