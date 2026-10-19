     echo "Missing reverse reads for sample: $base" >&2
        exit 1
    fi

    echo "Trimming sample: $base"

    # Trim adapters and low-quality bases
    trimmomatic PE \
        "$infile" \
        "$reverse_file" \
        "$output_dir/${base}_1.paired.trim.fastq.gz" \
        "$output_dir/${base}_1.unpaired.trim.fastq.gz" \
        "$output_dir/${base}_2.paired.trim.fastq.gz" \
        "$output_dir/${base}_2.unpaired.trim.fastq.gz" \
        "ILLUMINACLIP:${adapter_file}:2:40:15" \
        SLIDINGWINDOW:4:20 \
        MINLEN:25

done

echo "Read trimming complete."

#--------------------------------------------------------------
   # Align reads to the reference genome
    bwa mem "$reference" "$fq1" "$fq2" > "$sam"

    # Convert SAM to BAM
    samtools view -b -o "$bam" "$sam"

    # Sort alignments by genomic coordinates
    samtools sort -o "$sorted_bam" "$bam"

    # Index the sorted BAM file
    samtools index "$sorted_bam"

    # Call variants and save a VCF file
    bcftools mpileup -Ou -f "$reference" "$sorted_bam" |
        bcftools call --ploidy 1 -m -v -Ov -o "$variants"

    echo "Finished sample: $base"

done

echo "Variant calling complete."
