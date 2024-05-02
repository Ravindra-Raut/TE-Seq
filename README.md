# RTE Analysis

- Create a project directory
- Clone this pipeline into said directory
- Copy the workflow/conf_example directory to ./conf
  ```
  cp workflow/conf_example conf
  ```
- If you are not able to use docker/singularity and/or want to be able to modify environements, you can create the environments in the envs/ directory. E.g.:
   ```
   mamba env create --file envs/rseqc.yaml
   ```
   It will take time and occupy a substantial amount of disk space to recreate all of these environments. Using a container is the prefered way to deploy this pipeline.
- Modify the contents of conf/private/sample_table. Make sure sample names do not start with numbers; add an X in front if they do.
- Modify the contents of conf/config.yaml; make sure the samples, contrast, library_type are modified properly; are all paths you have access to and don't give you permissions errors
  ```
  cat {path}
  ```
- Create, in your project directory, the rawdata directory, and move your fastqs there. Make sure the naming is consistent with the naming scheme set forth in the conf/project_config_srna.yaml, which uses sample_name from the conf/sample_table_srna.csv i.e.:
```
source1: "rawdata/{sample_name}_R1.fastq.gz" source2: "rawdata/{sample_name}_R2.fastq.gz"
```
If rawdata identifiers do not match with sample names, either you can rename them, or you can provide a mapping from sample_name to rawdata identifier. In this case you would add a column to your conf/sample_table_srna.csv titled something like 
Then you would modify the "derive" block in your conf/project_config_srna.yaml as follows, chaning {sample_name} to your new column in conf/sample_table_csv, which in this case I titled {fq_sample_name}:
```
  derive:
    attributes: [file_path_R1, file_path_R2]
    sources:
      source1: "srna/rawdata/{fq_sample_name}_R1_001.fastq.gz"
      source2: "srna/rawdata/{fq_sample_name}_R2_001.fastq.gz"
```

- peptable_srna.csv, is automatically updated each time you call snakemake, according to the rules set out in conf/project_config_srna.yaml applied to conf/sample_table.csv. This is how rawdata paths can be generated dynamically.
- Modify the contents of workflow/profile/default/config.yaml such that singularity containers have access to your data directory, or in general a directory which contains all files which are referenced in the pipeline and which contains your project directory.
  ```
  singularity-args: '--bind /users/mkelsey/data'
  becomes
  singularity-args: '--bind /users/other_user/data'
  ```

- Create a snakemake conda environment from which you can run the snakemake pipeline, and install the required snakemake executor plugins in your snamemake conda environment, e.g.
    ```
    mamba create --name snakemake snakemake snakemake-executor-plugin-slurm
    ```
- Performe a pipeline dry-run, which tells you which rules snakemake will deploy once really called
  ```
  conda activate snakemake
  #ensure you are in the pipeline directory which lives in your project folder, i.e. myproject/RTE/
  snakemake -n
  ```
  

