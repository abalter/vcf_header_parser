# Parse a VCF Header to JSON

# Motivation
My primary motivation to write this was to simplify uploading VCF files
to BigQuery. The GCP variant upload tool has features I don't like (such
as switching to 0-based and renaming chromosome to "reference"), and is
in fact not working at this moment at all (3/14/2022).

However, as an intermediate step I decided it could be useful to also be able 
to output a "raw" version in which the entire header is parsed to JSON
with no reformatting or assumptions for BigQuery. (See "to do")

# Base VCF Fields
Schema for the base VCF fields, _CHROM_, _POS_, _ID_, _REF_, _ALT_, and _QUAL_
are not specified in the VCF header. Therefore, they are not parsed by this
tool. I wrote a basic schema for these fields that can be included in the JSON
output using the `--include_base` or `-b` flag.


# Usage:

```
./vcf_metadata_to_json \
    --in_vcf small.vcf \
    --out_schema small.json \
    --schema_type bq \
    --metadata_sections INFO FORMAT *FILTER \
    --include_base
```

Can output to stdout and take input from stdin.

Assuming `vcf_metadata_to_json` is in your path:

```
cat tiny.vcf | vcf_metadata_to_json
```


# BigQuery Schema
The correct format for BigQuery schema is a flat list of JSON object literals
in the following format:

```
{
    "name": "Name of the variable",
    "type": "Type of the variable in all-caps. Chiefly: [INTEGER|FLOAT|STRING|BOOL|DATE|STRUCT]",
    "description": "A description of the variable",
    "mode": "[NULLABLE|REQUIRED|REPEATED]"
}
```

See [here](https://cloud.google.com/bigquery/docs/schemas) for a detailed description of the schema particularly
[type](https://cloud.google.com/bigquery/docs/schemas#standard_sql_data_types) and
[mode](https://cloud.google.com/bigquery/docs/schemas#modes)


# To Do:
- [ ] Facilitate other cloud formats (e.g. Azure, AWS) and SQL engines.
- [x] Options for "flattening" the schema because the schema to upload to
      BigQuery needs to be a simple list of key-value pairs. (see above.
- [ ] R utility to convert the schema to `bq_field` format in the [`bigrquery`
      library](https://bigrquery.r-dbi.org/)
- [ ] Option for exporting schema when uploading INFO, FORMAT, and FILTER
      sections as JSON object literals rather than an expanded set of
      columns 
