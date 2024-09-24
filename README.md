Execution steps : 
 Data Sources: Ingest data from multiple sources like csv and live API.
 Storage Services: Use S3 for storing processed and unprocessed data.
 Data Processing and Ingestion: Use Amazon Kinesis for high performance real-time data streaming, Glue for processing batch data, Lambda to trigger any jobs on events.
 Data Structuring: Using AWS Glue Crawler, Glue Data Catalog to structure raw data from S3 do ETL and store the processed data in S3.
 Data Lake: Using S3 buckets to store all the processed data.
 Data Analysis: Using Athena to run queries, do analysis and build dashboards to analyze all the data.
 Data Communication: Using SNS and SMTP protocol to communicate with users and admins, to send emails to specific users etc.

 For clean the timestamp code - 
 Code:
def MyTransform (glueContext, dfc) -> DynamicFrameCollection:
df = dfc.select(list(dfc.keys())[0]).toDF()
def convert_date_format(date_str):
if '-' in date_str:
parts = date_str.split('-')
return "/".join([str(int(parts[1])), str(int(parts[0])), parts[2]])
else:
parts = date_str.split('/')
return '/'.join(parts)
convert_date_format_udf = spark.udf.register("convert_date_format", convert_date_format)
df_transformed = df.withColumn("dateofbirth", convert_date_format_udf(df["dateofbirth"]))
df_transformed = df_transformed.withColumn("accountopeningdate",
convert_date_format_udf(df_transformed["accountopeningdate"]))
transformed_dyf = DynamicFrame.fromDF(df_transformed, glueContext, "transformed_dyf")
return(DynamicFrameCollection({"CustomTransform0": transformed_dyf}, glueContext))

SQL qwerty in Athena to output the email ID and the count of visits in a separate S3 : 


bucket (max-visitor982).
SQL Query:
CREATE TABLE "capstonedata"."maxvisitor"
WITH
(
format='CSV',
external_location='s3://max-visitor982/'
) AS
SELECT
MAX(email) as email,
CAST(COUNT(*) AS VARCHAR) AS count
FROM
"capstonedata"."clean_api_data"
GROUP BY
user_id
ORDER BY
count DESC
LIMIT 1;

Twitter live Data - https://developer.x.com/en/docs/x-api/v1/data-dictionary/overview
Twitter Live Data - https://developer.x.com/en/docs/x-api/v1/data-dictionary/object-model/tweet
Facebook API - https://developers.facebook.com/docs/graph-api/guides/our-sdks

