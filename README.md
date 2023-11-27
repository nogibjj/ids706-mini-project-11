# Data Pipeline with Databricks

This repository contains a Databricks notebook demonstrating a simple data pipeline that performs data extraction, transformation, and loading (ETL) operations.

## Pipeline Overview

The data pipeline consists of the following steps:

1. **Exploring Available Datasets**: The notebook begins by listing available datasets in the Databricks environment to select a suitable data source.

```bash
dbutils.fs.ls("/databricks-datasets")
```

2. **Reading Data from Data Source**: Data is read from the selected dataset, which is stored in a CSV format in the Databricks-datasets collection.

```bash
file_location = "dbfs:/databricks-datasets/flights/"
file_type = "csv" 

df = spark.read.format(file_type) \
  .option("inferSchema", "true") \
  .option("header", "true") \
  .load(file_location)

df.show()
```

3. **Transforming the Data**: Several transformations are applied to the data, including filtering and aggregation. This step involves manipulating the data to derive meaningful insights.

```bash
from pyspark.sql import functions as F

less_than_500_miles = df.filter(df["distance"] < 500)
avg_delay_less_than_500 = less_than_500_miles.groupBy("origin").agg(F.round(F.avg("delay"), 2).alias("average_delay"))

```
   
4. **Writing to Data Sink**: The transformed data is then written to a data sink, which, in this case, is a location within Databricks File System (DBFS).

```bash
%fs mkdirs /mnt/de-mini-project-11
```
```bash
avg_delay_less_than_500.write.parquet("/mnt/de-mini-project-11/avg_delay_less_than_500", mode="overwrite")
```

5. **Checking the Written Data**: After writing the data, the notebook includes a check to ensure that the data is written to the DBFS as expected.

```bash
%fs ls /mnt/de-mini-project-11
```

```bash
data_path = "/mnt/de-mini-project-11/avg_delay_less_than_500/"
df = spark.read.format("parquet").load(data_path)
df.show()
```

## Usage

To run this notebook:
- Import the `.ipynb` or `.py` file into your Databricks workspace.
- Attach the notebook to a Databricks cluster.
- Run the cells in sequence from top to bottom.

## Additional Notes

- This pipeline is set up as an example and uses the flights data available in the Databricks-datasets.
- The notebook is configured to run on Databricks runtime environments.

Feel free to clone this repository and adapt the code to fit your data processing needs.
