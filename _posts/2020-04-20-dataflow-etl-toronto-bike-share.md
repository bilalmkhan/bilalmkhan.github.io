---
layout: post
title: Using Dataflow to Extract, Transform, and Load Bike Share Toronto Ridership Data into BigQuery
subtitle: Notes on building an ETL pipeline for loading Bike Share Toronto ridership data into BigQuery to serve as a source for Data Studio visualizations
---

In my [previous blogpost](https://bilalmkhan.github.io/toronto-bike-share-covid-bigquery-datastudio/), I used Google Data Studio to analyze [BikeShareTO ridership data](https://ckan0.cf.opendata.inter.prod-toronto.ca/tr/dataset/bike-share-toronto-ridership-data) with BigQuery as the data source. In this blogpost, I will demonstrate step-by-step some of the problems you run into when trying to load BikeShareTO ridership data directly into BigQuery from Cloud Storage, and how to solve them by carrying out the ETL process with Apache Beam using Dataflow.

## Download data
Let’s start by opening a session in Cloud Shell and downloading the 2020 Bike Share Toronto ridership data in a separate folder `'2020'`.

```
wget https://ckan0.cf.opendata.inter.prod-toronto.ca/dataset/7e876c24-177c-4605-9cef-e50dd74c617f/resource/5f5d78c4-d810-4048-9dac-c18273abffac/download/files-1.zip -O temp.zip

unzip temp.zip -d 2020

rm temp.zip
```

The first command above downloads the zip file under the name `temp.zip`, the second command unzips it to a folder named `2020`, and the third command deletes the downloaded zip file.

2. Let’s take a look at the unzipped contents of the file by running the following command.

```
ls 2020
```

We see that the ridership data for the year 2020 is divided into twelve CSV files all starting with '2020':

`2020–01.csv 2020–02.csv 2020–03.csv 2020–04.csv 2020–05.csv 2020–06.csv 2020–07.csv 2020–08.csv 2020–09.csv 2020–10.csv 2020–11.csv 2020–12.csv`

3. We can read the column names by reading the header of the CSV:

```
head -n 1 2020/2020–01.csv
```

This outputs the following columns names:

`Trip Id,Trip Duration,Start Station Id,Start Time,Start Station Name,End Station Id,End Time,End Station Name,Bike Id,User Type`

## Copy files to a Cloud Storage bucket
Next, let’s copy the files to a Cloud Storage bucket. This will allow us batch load multiple files into a BigQuery table with a single command by making use of wildcard support for Cloud Storage URI.

4. First, set a project variable with your project ID and set the project property.
```
export PROJECT=my-project-id
gcloud config set project $my-project-id
```

5. Create a new bucket within your project by using the make bucket `gsutil mb` command.
```
gsutil mb -l northamerica-northeast1 gs://my-bucket-name
```
6. Use the `gsutil` command to copy files to the Cloud Storage bucket we just created.

```
gsutil cp 2020/* gs://my-bucket-name
```
7. Check if the files have been successfully copied.
```
gsutil ls gs://my-bucket-name/
```
8. Delete the folder from Cloud Shell once the data has been successfully copied to the Cloud Storage bucket.
```
rm -r 2020
```
## Create a BigQuery dataset
9. Before we can load our data to be BigQuery table, we need to create a BigQuery dataset for the table.
```
bq --location=northamerica-northeast1 mk mydataset
```
## Errors while trying to load the data to BigQuery directly from Cloud Storage
10. Next, let’s try to load the data into a BigQuery table using the `bq load` command and using a wildcard by appending an asterisk (\*) to the base
```
bq load --autodetect --source_format=CSV mydataset.biketrips2020 gs://my-bucket-name/*
```
However, we get an error:
`- gs://my-bucket-name/2020-10.csv: Error while reading
data, error message: CSV table references column position 9, but
line starting at position:3401930 contains only 9 columns.`

The error message suggests the load job failed because at least one row has fewer columns than the automatically detected schema dictates.
11. To find the source of these errors, we can inspect the CSV files in the vicinity of the byte locations of the error. To do this, we use the `gsutil cat` command.
```
gsutil cat -r 3401700–3402200 gs://my-bucket-name/2020–10.csv
```
From this, we find that there are many rows in which the values for `Trip_Id` and `Trip_Duration` are erroneously concatenated. For example, the second row below should begin with `10000084,625,7120,…`

`10000083,720,7239,10/03/2020 13:28,Bloor St W / Manning Ave — SMART,7160,10/03/2020 13:40,King St W / Tecumseth St,5563,Annual Member`

`10000084625,7120,10/03/2020 13:28,Gerrard St E / River St,7120,10/03/2020 13:38,Gerrard St E / River St,5250,Annual Member`

`10000085,1526,7239,10/03/2020 13:28,Bloor St W / Manning Ave — SMART,7544,10/03/2020 13:53,Foster Pl / Elizabeth St — SMART,3956,Annual Member`

We need to find all such concatenated values and split them. This is a data transformation that cannot be accomplished in BigQuery. Hence, we need to build an Apache Beam pipeline to transform the data and load into BigQuery.

In addition, we see that the `Start_Time` and `End_Time` columns in the above rows are not in a BigQuery compatible datetime format. We will also need to make that transformation with Dataflow.

12. One option to deal with errors is to set the `max_bad_records` flag in `bq load`. This ignores bad rows and does not load them into the table. For example, we can set the max_bad_record flag to 100:
```
bq load \
--autodetect \
--max_bad_records=100 \
--source_format=CSV \
mydataset.biketrips2020 \
gs://my-bucket-name/*
```
However, this still gives us errors, albeit of a different kind.

`Could not parse ‘NULL’ as INT64 for field Bike_Id (position 8) starting at location 5061057 with message ‘Unable to parse’`

This error arises because in BigQuery the standard way to represent nulls in CSV is to use empty fields. We can deal with this error by setting the flag `null_marker=NULL`in `bq load` command thereby specifying that this particular file uses the string NULL to mark nulls. For example:
```
bq load \
--autodetect \
--max_bad_records=100 \
--source_format=CSV \
--null_marker=NULL \
mydataset.biketrips2020 \
gs://my-bucket-name/*
```
This deals with the error due to parsing NULL values. However, we will also include this transformation in our Apache Beam pipeline so that a single pipeline carries out all the necessary transformations.
## Build a Dataflow Pipeline
13. Next, we code an Apache Beam pipeline which extracts the files, carries out the transformations and loads the data into BigQuery. The python file `etl_pipeline.py` contains the Python code for the pipeline. Three functions carry out the main transformations: `deconcat()`, `replace_nulls()`, `format_datetime_bq()`. We can upload the python file using the Cloud Shell Editor.
## Set up the Python environment
14. Run the following commands in the Cloud Shell to set up the virtual environment to run our code:
```
sudo pip install virtualenv 
virtualenv -p python3 venv 
source venv/bin/activate 
pip install apache-beam[gcp]==2.24.0
```
## Run the pipeline
15. Running the python file etl_pipeline.py creates a Dataflow job which runs the DataflowRunner. We need specify a Cloud Storage bucket location for staging and storing temporary data while the pipeline is still running, and the Cloud Storage bucket containing our CSV files.
```
python etl_pipeline.py \
--project=$PROJECT \
--region=northamerica-northeast1 \
--runner=DataflowRunner \
--staging_location=gs://my-bucket-name/test \
--temp_location gs://my-bucket-name/test \
--input gs://my-bucket-name/*.csv 
--save_main_session
```
16. When the Dataflow job is complete, we can navigate to Cloud Dataflow in the Gooogle Cloud Console and see the Job Graph and other job completion details:
