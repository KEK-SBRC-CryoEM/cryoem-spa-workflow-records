# Cryo-EM SPA Workflow Records

## 1. Dataset Overview
The dataset "Cryo-EM SPA Workflow Records" is a compilation of workflow records for Cryo-Electron Microscopy (cryo-EM) Single Particle Analysis (SPA). It comprehensively stores the intermediate processes from raw input dataset (Raw Micrograph Movies) to the final high-resolution 3D structures. It is specifically designed to allow developers of AI and machine learning algorithms to learn from and verify the transition of data at each stage of analysis.

## 2. Basic Bucket Configuration of Root Directory
The root directory of the Amazon S3 bucket is classified into the following four entries based on the function and nature of the data:
- **ArXiv/** : Stores the actual analysis data, which forms the core of this dataset.
- **Scripts/** : A collection of execution scripts used for automatic path generation, data archiving, and uploading to S3. This also includes code that will serve as the foundation for future Web GUI applications.
- **Documents/** : Stores dataset specifications, a mapping table for software versions (MAIN_SOFTWARE_ID_List.txt), and citation protocols.
- **README.md** : Describes the navigation for the entire bucket, dataset specifications, and terms of use.

## 3. Detailed Hierarchical Structure and Naming Conventions under "ArXiv"
Data under ArXiv/ is managed according to a strict 3-tier structure and naming convention to maximize searchability and ease of automated processing.

### 1st Tier: Origin of Measurement Data
Data is categorized according to its source:
- **For public data**: Designated as "EMPIAR".
- **For unpublished data**: Designated as "[Facility ID]_[Microscope ID]" (e.g., KEK_KriosG4, KEK_ArcticaG2).

### 2nd Tier: Dataset ID
Represents a specific measurement session unit.
- **Under "EMPIAR"**: Designated as "EMPIAR#####".
- **Under "[Facility ID]_[Microscope ID]"**: Designated as "[Facility ID] [Microscope ID] [Dataset ID]".
- **Format**: "[Measurement Date (YYMMDD)]_[User ID] Grid[Grid Number] [Free Format]".

### 3rd Tier: Process ID
Identifies an individual analysis workflow executed for a specific dataset.
- **Format**: "[Start Date (YYMMDD)]_[Analyst_ID]_[Software_ID]_[Free_Format]".
- **Requirement**: Each folder in this tier must contain a "final_maps.cvs" defining the list of final output maps (Path, Resolution, Free Format).

## 4. Tutorial: Building AI Training Datasets Using Intermediate Data on AWS
To help you get started with the Cryo-EM SPA Workflow Records dataset, we provide an interactive tutorial notebook. 

### Interactive Tutorial Notebook

| Tutorial Topic | Links |
| :--- | :--- |
| **Get to know the Cryo-EM SPA Records** | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/KEK-SBRC-CryoEM/cryoem-spa-workflow-records/blob/main/tutorials/get_to_know_cryoem_spa_records.ipynb) [![Open In SageMaker Studio Lab](https://studiolab.sagemaker.aws/studiolab.svg)](https://studiolab.sagemaker.aws/import/github/KEK-SBRC-CryoEM/cryoem-spa-workflow-records/blob/main/tutorials/get_to_know_cryoem_spa_records.ipynb) |

#### What you will learn:
- How to access the dataset using `boto3`.
- Navigating the **ArXiv Tier 3 (Process ID)** metadata structure.
- Reconstructing intermediate data from metadata records.

### Prerequisites
To access this data, use the **AWS CLI**. As this dataset is published as a public bucket, data can be accessed even by users without an AWS account using the `--no-sign-request` option. This option skips the signing process, allowing anyone to retrieve data immediately.

### Basic Commands
- **List files**: `aws s3 ls s3://[Bucket_Name]/ArXiv/ --no-sign-request`
- **Download a specific file**: `aws s3 cp s3://[Bucket_Name]/ArXiv/[Path_to_File] . --no-sign-request`
- **Synchronize an entire folder**: `aws s3 sync s3://[Bucket_Name]/ArXiv/[Path_to_Folder]/ ./local_folder/ --no-sign-request`

### Workflow Steps
1. **Identifying Target Data**: Check the hierarchy by specifying the Facility ID and Microscope ID.
2. **Extracting Intermediate Data**: Sync by filtering for specific extensions only (e.g., `--exclude "*" --include "*.star" --include "*.mrcs"`).
3. **Verifying Software Consistency**: Refer to "Documents/MAIN_SOFTWARE_ID_List.txt" for exact versions.
4. **Integration**: Load retrieved .star files in Python (using `starfile` and `pandas`) to filter and process training samples.

## 5. How Users Can Find Required Data
- **Filtering**: Scan the bucket using "[Software_ID]" or "[Free_Format]" as keys.
- **Tracking Specific Proteins**: Use protein abbreviations included in the third tier's "[Free_Format]".
- **Reviewing Details**: Each process folder contains RELION .star files or CryoSPARC .json files for full parameter overviews.

## 6. Cloud Optimization and Data Integrity
- **Exclusion of Reproducible Files**: Intermediate files that can be regenerated from metadata are filtered/deleted before uploading to effectively use the 10TB storage limit.
- **Automatic Path Generation Script**: Use tools under Scripts/ to accurately place and upload data according to naming conventions.
