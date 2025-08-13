# Create Spark Context
```

from pyspark import SparkConf, SparkContext

# Create SparkConf object
conf = SparkConf().setAppName("MyApp").setMaster("local[*]")

# Create SparkContext
sc = SparkContext(conf=conf)

# Example: Create an RDD
rdd = sc.parallelize([1, 2, 3, 4, 5])
print("RDD Sum:", rdd.sum())

# Stop the context
sc.stop()

```
- API- Low-level (RDD) API
- Usage - RDD operations
- Introduced in Spark1.0
- Access via sc = Spark.SparkContext

# Create Spark Session
```

%python
from pyspark.sql import SparkSession

spark = SparkSession.builder\
    .appName("MyApp") \
    .getOrCreate()

```
- API - High Level (Data Frame, Sql) API
- Usage - DataFrame, SQL, Hive, Streaming, ML
- Introduced in Saprk2.0
- Access directly
### Note: 
- Required when Running PySpark scripts outside Databricks (e.g., local dev, EMR, etc.)
- But not required from data braicks notebook                    

# Create spark session in AWS glue
```
sc = SparkContext()
glueContext = GlueContext(sc)
spark = glueContext.spark_session

```

# Create data frame in Pyspark from a list of tuples
 ```

data1 = [('1','kad'),
        ('2','sid')]
schema1 = 'id STRING, name STRING' 

df1 = spark.createDataFrame(data1,schema1)

 ```

# Create data frame in pyspark by reading a csv file
```
%python
df_sales=spark.read.format('csv')\
            .option('header', True)\
            .option('inferschema', True)\
            .load('dbfs:/FileStore/tables/Sales.csv')

```
# Create data frame using ddl schema
```
%python
my_ddl_schema = '''
                    Item_Identifier STRING,
                    Item_Weight STRING,
                    Item_Fat_Content STRING, 
                    Item_Visibility DOUBLE,
                    Item_Type STRING,
                    Item_MRP DOUBLE,
                    Outlet_Identifier STRING,
                    Outlet_Establishment_Year INT,
                    Outlet_Size STRING,
                    Outlet_Location_Type STRING, 
                    Outlet_Type STRING,
                    Item_Outlet_Sales DOUBLE 

                ''' 
  df = spark.read.format('csv')\
            .schema(my_ddl_schema)\
            .option('header',True)\
            .load('/FileStore/tables/BigMart_Sales.csv')

```

   # Crate data frame using StructType
```
   %python
   from pyspark.sql.types import * 
   from pyspark.sql.functions import *  

my_strct_schema = StructType([ 
StructField('Item_Identifier',StringType(),True), 
StructField('Item_Weight',StringType(),True), 
StructField('Item_Fat_Content',StringType(),True), 
StructField('Item_Visibility',StringType(),True), 
StructField('Item_MRP',StringType(),True), 
StructField('Outlet_Identifier',StringType(),True), 
StructField('Outlet_Establishment_Year',StringType(),True), 
StructField('Outlet_Size',StringType(),True), 
StructField('Outlet_Location_Type',StringType(),True), 
StructField('Outlet_Type',StringType(),True), 
StructField('Item_Outlet_Sales',StringType(),True)
])

df = spark.read.format('csv')\
.schema(my_strct_schema)\
.option('header',True)\
.load('/FileStore/tables/BigMart_Sales.csv')

```

# Write data frame to csv file
```

df.write.format('csv')\
        .save('/FileStore/tables/CSV/data.csv')

```
# Write data frame to csv file in append mode
```

df.write.format('csv')\
        .mode('append')\
        .save('/FileStore/tables/CSV/data.csv')

```
# Write data frame to csv file in overwrite mode
```

df.write.format('csv')\
        .mode('append')\
        .save('/FileStore/tables/CSV/data.csv')

```
# Write data frame to csv in error mode
```

df.write.format('csv')\
.mode('error')\
.option('path','/FileStore/tables/CSV/data.csv')\
.save()

```
# Write data frame to csv in ignore mode
```

df.write.format('csv')\
.mode('ignore')\
.option('path','/FileStore/tables/CSV/data.csv')\
.save()

```
# Write data frame to parquet 
```

df.write.format('parquet')\
.mode('overwrite')\
.option('path','/FileStore/tables/CSV/data.csv')\
.save()

```
# Write data frame to table
```

df.write.format('parquet')\
.mode('overwrite')\
.saveAsTable('my_table')

```
# Write data frame to a temp view 
```

df.createTempView('my_view')

```
# Spark Streaming
## 	📘 Sample PySpark Code using CloudFiles
	
```python
	from pyspark.sql.types import StructType, StringType, IntegerType
	# Define schema
schema = StructType() \
    .add("id", IntegerType()) \
    .add("name", StringType()) \
    .add("city", StringType())
	# Read streaming data from cloud files (e.g., S3 or ADLS)
df = (spark.readStream
    .format("cloudFiles")
    .option("cloudFiles.format", "csv")         # or 'json', 'parquet', etc.
    .option("cloudFiles.inferColumnTypes", "true")  # or provide schema
    .schema(schema)
    .load("/mnt/raw-data/")                     # path to your cloud mount
)
# Write to Delta table (Bronze layer)
(df.writeStream
    .format("delta")
    .option("checkpointLocation", "/mnt/checkpoints/raw-data/")
    .outputMode("append")
    .start("/mnt/bronze-data/")

```

## 📜 PySpark Code (Kafka → Delta)

```python
from pyspark.sql.functions import col, from_json
from pyspark.sql.types import StructType, StringType, IntegerType
### 1. Define the schema for your Kafka value (JSON payload)
schema = StructType() \
    .add("id", IntegerType()) \
    .add("name", StringType()) \
    .add("city", StringType())
# 2. Read streaming data from Kafka topic
kafka_df = (spark.readStream
    .format("kafka")
    .option("kafka.bootstrap.servers", "localhost:9092")  # change to your broker
    .option("subscribe", "input-topic")
    .option("startingOffsets", "latest")  # or 'earliest' for dev/testing
    .load())
# 3. Parse the Kafka 'value' (binary) to string and extract JSON fields
parsed_df = kafka_df.selectExpr("CAST(value AS STRING) as json_string")
json_df = parsed_df.select(from_json(col("json_string"), schema).alias("data")).select("data.*")
# 4. Optional: Filter or transform
clean_df = json_df.filter("id is not null and name is not null")
# 5. Write to Delta Table
query = (clean_df.writeStream
    .format("delta")
    .outputMode("append")  # use "append" for streaming inserts
    .option("checkpointLocation", "/mnt/checkpoints/kafka_to_delta/")  # use DBFS, S3, ADLS, etc.
    .start("/mnt/bronze/kafka-data/"))

```


