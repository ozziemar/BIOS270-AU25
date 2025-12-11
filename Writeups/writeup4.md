# Write-up 0: template

**Name:** Ozzie Martinez 
**Student ID:** ozzie  
**Date:** 12/11/2025

---

## Notes

How could one add a differential expression analysis step to the rnaseq_pipeline_array_depend.sh script such that DESeq2 runs only after all salmon jobs for all samples have completed?

You could use the salmon job IDs from SALMON_QUANT to make a dependency list and submit a single DESeq2 job on all of them, so DESeq2 only runs after every salmon quantification has finished.

 SALMON_INDEX Process:

process SALMON_INDEX {

    tag "$transcriptome_fa"

    input:
    path transcriptome_fa

    output:
    path "salmon_index"

    """
    salmon index \
      -t ${transcriptome_fa} \
      -i salmon_index
    """
}

Had difficulties trying to modify the pipeline, so was unable to go farther. 
