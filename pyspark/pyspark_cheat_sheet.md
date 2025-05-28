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
                    
          
