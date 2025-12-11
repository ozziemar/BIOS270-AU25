# Write-up 3: Data

**Name:** Ozzie Martinez 
**Student ID:** ozzie  
**Date:** 12/10/2025

---

## Notes

Examine create_bacteria_db.sh:
It has 3 python scripts insert_gff_table.py, insert_protein_table.py, and insert_metadata_table.py, which will create 3 tables from the bacteria.db.

Why is try and except necessary in insert_gff_table.py?
It is necessary because multiple Slurm tasks may attempt to write to the same SQLite database at the same time, which would cause it to crash immediately if not for the try and except.

query_bacteria_db.py modification:
query = f"""
        SELECT DISTINCT protein_id
        FROM gff
        WHERE record_id = '{record_id}'
        """

Is what I wrote, but I was unable to run it, unsure if this was errors in the code or in how I was modifying the file. This means I was unable to record the runtime.

Examine the upload_bigquery.py script. Explain the role of CHUNK_SIZE and why it is necessary/
It runs a large dataset in chunks instead of all at the same time, controlling how many rows are read from the table in each batch. This means it can iterate over the table in chunks then all at once, lowering the amount of memory needed to do the full analysis. 


