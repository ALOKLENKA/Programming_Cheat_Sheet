# Create Spark Session
```

%python
from pyspark.sql import SparkSession

spark = SparkSession.builder\
    .appName("MyApp") \
    .getOrCreate()

```
### Note: 
- This is required from a normal python code, or thirdparthy engine like glue or emr
- But not required from data braicks notebook                    
                    
          
