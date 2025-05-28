# Create Spark Session
```

%python
from pyspark.sql import SparkSession

spark = SparkSession.builder\
    .appName("MyApp") \
    .getOrCreate()

```
### Note: 
- Required when Running PySpark scripts outside Databricks (e.g., local dev, EMR, etc.)
- But not required from data braicks notebook                    
                    
# Create data frame in pyspark
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

