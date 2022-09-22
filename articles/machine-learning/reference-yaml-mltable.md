---
title: 'CLI (v2) mltable YAML schema'
titleSuffix: Azure Machine Learning
description: Reference documentation for the CLI (v2) MLTable YAML schema.
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
ms.topic: reference
ms.custom: cliv2, event-tier1-build-2022

author: xunwan
ms.author: xunwan
ms.date: 09/15/2022
ms.reviewer: s-polly
---

# CLI (v2) data YAML schema

[!INCLUDE [cli v2](../../includes/machine-learning-cli-v2.md)]

The source JSON schema can be found at https://azuremlschemas.azureedge.net/latest/MLTable.schema.json.



[!INCLUDE [schema note](../../includes/machine-learning-preview-old-json-schema-note.md)]

## YAML syntax

| Key | Type | Description | Allowed values | Default value |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | string | The YAML schema. If you use the Azure Machine Learning VS Code extension to author the YAML file, including `$schema` at the top of your file enables you to invoke schema and resource completions. | | |
| `type` | const | `mltable` to abstract the schema definition for tabular data so that it is easier for consumers of the data to materialize the table into a Pandas/Dask/Spark dataframe | `mltable` | `mltable`|
| `paths` | array | Paths can be a `file` path, `folder` path or `pattern` for paths. `pattern` specifies a search pattern to allow globbing of files and folders containing data. Supported URI types are `azureml`, `https`, `wasbs`, `abfss`, and `adl`. See [Core yaml syntax](reference-yaml-core-syntax.md) for more information on how to use the `azureml://` URI format. |`file`, `folder`, `pattern`  | |
| `transformations`| array | Defined sequence of transformations that are applied to data loaded from defined paths. |`read_delimited`, `read_parquet` , `read_json_lines` , `take` to take the first N rows from dataset, `take_random_sample` to take a random sample of records in the dataset approximately by the probability specified, `skip` to skip the first N records from top of the dataset, `drop_columns`, `keep_columns`,... || 

## Examples

Examples are available in the [examples GitHub repository](https://github.com/Azure/azureml-examples/tree/main/sdk/assets/data). Several are shown below.

## MLTable paths: file
```yaml
type: mltable
paths:
  - file: https://dprepdata.blob.core.windows.net/demo/Titanic2.csv
transformations:
  - take: 1
```

## MLTable paths: pattern
```yaml
type: mltable
paths:
  - pattern: ./*.txt
transformations:
  - read_delimited:
      delimiter: ,
      encoding: ascii
      header: all_files_same_headers
```

## MLTable transformations
These are transforms that all mltable-artifact files support:

- `take`: Takes the first *n* records of the table
- `take_random_sample`: Takes a random sample of the table where each record has a *probability* of being selected. The user can also include a *seed*.
- `skip`: This skips the first *n* records of the table
- `drop_columns`: Drops the specified columns from the table. This transform supports regex so that users can drop columns matching a particular pattern.
- `keep_columns`: Keeps only the specified columns in the table. This transform supports regex so that users can keep columns matching a particular pattern.


## MLTable transformations: read_delimited

```yaml
paths:
  - file: https://dprepdata.blob.core.windows.net/demo/Titanic2.csv
transformations:
  - read_delimited:
      infer_column_types: false
      delimiter: ','
      encoding: 'ascii'
      empty_as_string: false
  - take: 10
```

## Delimited files transformations
Following transformations are specific to delimited files.
- infer_column_types: Boolean to infer column data types. Defaults to `True`. Type inference requires that the data source is accessible from current compute. Currently type inference will only pull first 200 rows. If the data contains multiple types of value, it is better to provide desired type as an override via set_column_types argument
- encoding: Specify the file encoding. Supported encodings are `utf8`, `iso88591`, `latin1`, `ascii`, `utf16`, `utf32`, `utf8bom` and `windows1252`. Defaults to `utf8`.
- header: user can choose one of the following options: `no_header`, `from_first_file`, `all_files_different_headers`, `all_files_same_headers`. Defaults to `all_files_same_headers`.
- delimiter: The separator used to split columns.
- empty_as_string: Specify if empty field values should be loaded as empty strings. The default (`False`) will read empty field values as nulls. Passing this as `True` will read empty field values as empty strings. If the values are converted to numeric or datetime then this has no effect, as empty values will be converted to nulls.
- include_path_column: Boolean to keep path information as column in the table. Defaults to `False`. This is useful when reading multiple files, and want to know which file a particular record originated from, or to keep useful information in file path.
- support_multi_line: By default (support_multi_line=`False`), all line breaks, including those in quoted field values, will be interpreted as a record break. Reading data this way is faster and more optimized for parallel execution on multiple CPU cores. However, it may result in silently producing more records with misaligned field values. This should be set to `True` when the delimited files are known to contain quoted line breaks.

## MLTable transformations: read_json_lines
```yaml
paths:
  - file: ./order_invalid.jsonl
transformations:
  - read_json_lines:
        encoding: utf8
        invalid_lines: drop
        include_path_column: false
```

## MLTable transformations: read_json_lines, convert_column_types
```yaml
paths:
  - file: ./train_annotations.jsonl
transformations:
  - read_json_lines:
        encoding: utf8
        invalid_lines: error
        include_path_column: false
  - convert_column_types:
      - columns: image_url
        column_type: stream_info
```

### Json lines transformations
Below are the supported transformations that are specific for json lines:

- `include_path` Boolean to keep path information as column in the MLTable. Defaults to False. This is useful when reading multiple files, and want to know which file a particular record originated from, or to keep useful information in file path.
- `invalid_lines` How to handle lines that are invalid JSON. Supported values are `error` and `drop`. Defaults to `error`.
- `encoding` Specify the file encoding. Supported encodings are `utf8`, `iso88591`, `latin1`, `ascii`, `utf16`, `utf32`, `utf8bom` and `windows1252`. Default is `utf8`.

## MLTable transformations: read_parquet
```yaml
type: mltable
traits:
  index_columns: ID
paths:
  - file: ./crime.parquet
transformations:
  - read_parquet
```
### Parquet files transformations
If user doesn't define options for `read_parquet` transformation, default options will be selected (see below).

- `include_path_column`: Boolean to keep path information as column in the table. Defaults to False. This is useful when reading multiple files, and want to know which file a particular record originated from, or to keep useful information in file path.

## Next steps

- [Install and use the CLI (v2)](how-to-configure-cli.md)
