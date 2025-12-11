# Write-up 5: Project 1

**Name:** Ozzie Martinez 
**Student ID:** ozzie  
**Date:** 12/11/2025

---

## Notes

Tried to get through steps 1-3, however, I kept having issues making the Bakta annotation and wasn't able to get farther. Additionally, I kept losing access to farmshare which made getting through step 1 difficult. I wrote a script for step 4 assuming it had worked.

Step 4 Script:
#
import argparse
from collections import defaultdict
import matplotlib.pyplot as plt

def main():
    parser = argparse.ArgumentParser()
    parser.add_argument("--proteins", required=True, help="assembly.faa from Bakta")
    parser.add_argument("--clusters", required=True, help="*_cluster.tsv from MMseqs2")
    parser.add_argument("--prefix", required=True, help="output prefix (e.g. ecoli)")
    args = parser.parse_args()

    # Map protein_id to protein_name
    id_to_name = {}
    with open(args.proteins) as f:
        for line in f:
            if line.startswith(">"):
                header = line[1:].strip()
                parts = header.split(None, 1)
                protein_id = parts[0]
                protein_name = parts[1] if len(parts) > 1 else protein_id
                id_to_name[protein_id] = protein_name

    # Read clusters
    clusters = defaultdict(list)
    with open(args.clusters) as f:
        for line in f:
            line = line.strip()
            if not line:
                continue
            cluster_id, protein_id = line.split("\t")[:2]
            clusters[cluster_id].append(protein_id)

    # Compute copy_number per protein
    protein_copy = {}  
    for cluster_id, proteins in clusters.items():
        copy_number = len(proteins)
        if copy_number <= 1:
            continue  
        for pid in proteins:
            protein_copy[pid] = copy_number

    # Write TSV: protein_id, protein_name, copy_number
    out_tsv = f"{args.prefix}_paralogs.tsv"
    with open(out_tsv, "w") as out:
        out.write("protein_id\tprotein_name\tcopy_number\n")
        for pid, cn in sorted(protein_copy.items(), key=lambda x: x[1], reverse=True):
            name = id_to_name.get(pid, pid)
            out.write(f"{pid}\t{name}\t{cn}\n")

   # Plot top 10 most frequent paralogs
    if protein_copy:
        top = sorted(protein_copy.items(), key=lambda x: x[1], reverse=True)[:10]
        labels = [pid for pid, _ in top]
        values = [cn for _, cn in top]

        plt.figure(figsize=(10, 5))
        plt.bar(range(len(labels)), values)
        plt.xticks(range(len(labels)), labels, rotation=45, ha="right")
        plt.ylabel("Copy number")
        plt.title("Top 10 most frequent paralogs")
        plt.tight_layout()
        out_png = f"{args.prefix}_top10_paralogs.png"
        plt.savefig(out_png, dpi=300)
        plt.close()
    else:
        print("No paralogs found (no clusters with more than one protein).")

if __name__ == "__main__":
    main()
