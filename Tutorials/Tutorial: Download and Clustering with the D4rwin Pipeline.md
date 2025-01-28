### **Tutorial: Download and Clustering with the D4rwin Pipeline**

In this tutorial, we will guide you through the steps to download and cluster sequence data using the **D4rwin** pipeline, focusing on the **Trogoniformes** order. This tutorial demonstrates the pipeline's ability to perform efficient marker selection for phylogenetic analyses.
 
#### **Step 1: Install the D4rwin Pipeline**

Before beginning, make sure that the D4rwin pipeline is installed on your system. Follow the instructions in the **README** file for installation.

---

#### **Step 2: Create the Configuration File**

Before you can run the pipeline D4rwin, you need to create the configuration file, which defines all the parameters for downloading, filtering, and clustering the sequences.

To generate the configuration file through a graphical interface, use the Python script `Configure_D4rwin_Builder.py`. Running this script will prompt you to enter various parameters, which will be stored in a configuration file (`D4rwin_cfg.txt`). This file will then guide the rest of the pipeline.

Execute the following command in your terminal to start the process:

```bash
python3 Configure_D4rwin_Builder.py
```

#### **Step 3: Enter Parameters for Configuration**

Once you run the script, you will be prompted to input the necessary parameters. Here's an overview of the parameters you'll define for your project related to *Trogoniformes* (Txid: 56308):

- **File Name**: This will be the name of your project (e.g., `Trogoniformes`).
- **Taxonomic Identification (Txid)**: This is the NCBI Taxonomy ID for *Trogoniformes* (56308).
- **NCBI Taxonomic Table**: Choose whether to use the NCBI Taxonomic Table.
- **Exclude Terms**: List of terms to exclude from your sequence search (e.g., `complete_genome`, `partial_genome`).
- **Sequence Length Range**: Define the acceptable length of sequences (e.g., between 250 and 100,000,000 bases).
- **Batch Size**: How many sequences to process per batch (e.g., 300).
- **BLAST Parameters**: Parameters for the BLAST search, such as E-value (`1e-10`), query coverage (`51`), and the number of threads (e.g., `7`).
- **Clustering Parameters**: Set how strict the clustering should be, including the minimum number of sequences per cluster, the minimum number of species per cluster, and so on.

#### **Step 3: Example of Configuration File Output**

Here is an example of a configuration file (`D4rwin_cfg.txt`) generated after you input all the necessary parameters:

```plaintext
################### Core information ###################

File Name=Trogoniformes;
Taxonomic Identification of Interest (Txids)=56308;
Use NCBI Taxonomic Table=Yes;

################### Assembling Taxonomic Database Phase ###################;
NCBI Database Type=nucleotide;
Targeted Search=No;
Flexible Search=No;
Exclude Terms=Yes;complete_genome partial_genome predicted whole_genome_shotgun whole_genome unverified genome hybrid sp b-like;
Exclude Txids=No;
Exclude Records=No;
Additional NCBI Search Terms=;
Length=AND 250[SLEN]:100000000[SLEN];
Batch Size=300;
Delete NCBI Batches After Assembling Database Phase=No;

################### BLAST Stage ###################

BLAST Tool=blastn;
E-value=1e-10;
Query Coverage=51;
Threads=7;
BLAST Database Type=nucl;
Strand=both;
Dust=no;
Additional BLAST Parameters=;
Executes BLAST Sequences Per Batches=Yes;
Delete Batches After BLAST Stage=Yes;

################### Clustering Stage ###################

Filtering Parameter=6;
Minimum Parameter Value=80;
Delimit All Values For Each Taxonomic Coverage=Manual;
Minimum Sequences Per Cluster=6;
Minimum Classes Per Cluster=1;
Minimum Orders Per Cluster=1;
Minimum Families Per Cluster=1;
Minimum Genus Per Cluster=6;
Minimum Species Per Cluster=6;
Threads Available For Clustering=6;

################### Filtering per Top Sequences Option ###################

Concatenate Clusters From The Same Marker Prior to Filtering=Yes;
Taxonomic Level of Choice=Species;
Top Sequences Per Taxonomic Level=1;
```

#### **Step 4: Download Sequences and Cluster Phase**

1. **Run the script D4rwin.py
   ```bash
   python3 D4rwin.py
   ```
2. **Run the command `txnmc_clstr_ncbi` command** to download sequences and cluster them based on the configuration file:

   ```bash
   txnmc_clstr_ncbi
   ```

   This command will:
   - Download sequences based on the defined taxonomic level (species).
   - Exclude unwanted terms such as "complete genome" or "unverified".
   - Perform clustering based on the configured parameters.

   During the process:
   - Sequences are downloaded from GenBank based on your exclusion criteria.
   - Sequences are clustered by their taxonomic level (e.g., species or genus).
   - The resulting clusters will be stored in the `0.Clusters` directory.

---

#### **Step 5: Review the Output**

After the clustering process, review the outputs:

1. **Clusters Directory**: After the clustering is complete, check the `0.Clusters` directory for the clustered sequences in FASTA format. The sequence headers will be simplified to `<species_accession_number>` for easier organization.

2. **Filtered Info**: In the `0.Filtered_info` directory, you will find two tables:
   - One table for sequences that meet the criteria.
   - Another for sequences that don't meet the criteria.
   
   These tables show taxonomic information, including the number of species, genera, families, and other taxonomic levels.

3. **Table_Clusters**: A table with full GenBank sequence headers will also be generated in the `0.Clusters` directory. Use this table to create a list and rename the sequences. This will allow you to match the sequences to their respective clusters and perform any necessary refinements.

---

**Step 6: Renaming Sequences**

Before proceeding with the post-processing, it's important to rename the sequences based on the information from the `Table_Clusters`. This will help you match sequences to their respective clusters and ensure proper identification.

1. **Review `Table_Clusters`**: Open the `Table_Clusters` file in the `0.Clusters` directory. This table contains the full GenBank sequence headers, which you'll use to create a list and rename sequences.

2. **Create a List**: Extract the necessary sequence identifiers (e.g., species name, accession number) and create a list of sequences to rename. You can use a script or manually organize the identifiers into a `list.txt` file.

1. **Use the `rnm_clstr` command** to combine sequences of the same marker, selecting the longest sequence for each species:

   ```bash
   rnm_clstr
   ```
---

#### **Step 7: Post-Processing**

After downloading and clustering the sequences, you may perform post-processing steps to refine the data:

1. **Use the `filtr_per_txnmc_lvl` command** to combine sequences of the same marker, selecting the longest sequence for each species:

   ```bash
   filtr_per_txnmc_lvl
   ```

   This command will:
   - Combine the clusters of sequences from the same marker.
   - Select the longest sequence for each species to avoid redundancy.

---

### **Conclusion**

Congratulations! You have successfully downloaded 44 clusters using the D4rwin pipeline.

The pipeline generated six  markers—three nuclear (12S, GAPDH, RAG-1) and three mitochondrial (CYTB, ND2, TGFB2), covering 38 species across all genera of Trogoniformes. After updating the taxonomic information, the species distribution is as follows: *Trogon* (69.5%, 16/23 spp.), *Priotelus* (100%, 2/2 spp.), *Pharomachrus* (100%, 5/5 spp.), *Harpactes* (100%, 10/10 spp.), *Apaloderma* (100%, 3/3 spp.), *Apalharpactes* (50%, 1/2 spp.), and *Euptilotis* (100%, 1/1 spp.).

With these results, you're well-equipped to move forward with more advanced analyses, such as constructing phylogenetic trees or exploring the biogeographical patterns of Trogoniformes.

---
### **Observation:**
If you decide to use your own taxonomic table, make sure the file is named **ALL_taxonomy_summary.tsv** and placed in the same directory where you will run your analysis. Additionally, set the parameter in your configuration file as follows:
```plaintext
Use NCBI Taxonomic Table=No;
```
---
