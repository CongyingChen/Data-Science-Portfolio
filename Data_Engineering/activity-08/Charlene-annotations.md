# My annotations, Assignment 8

## Activity 1

## I'm connecting to and logging into my droplet
  ```
  ssh science@165.227.0.11
  ```
 
## Then, I'm creating a directory for spark with kafka and hadoop hadfs
  ```
  science@w205s4-crook-1:~$ mkdir ~/w205/spark-with-kafka-and-hdfs
  science@w205s4-crook-1:~$ cd ~/w205/spark-with-kafka-and-hdfs
  ```

## I'm copying the yml file to spark-with-kafka-and-hdfs, and change the file directory by vi.
  ```
  science@w205s4-crook-1:~/w205/spark-with-kafka-and-hdfs$ cp ~/w205/course-content/08-Querying-Data/docker-compose.yml .
  science@w205s4-crook-1:~/w205/spark-with-kafka-and-hdfs$ vi docker-compose.yml
  ```
 
## I'm starting the docker cluster.
  ```
  science@w205s4-crook-1:~/w205/spark-with-kafka-and-hdfs$ docker-compose up -d
  ```

## Then I'm watching it come up. (Detach with Ctrl-C)
  ```
  science@w205s4-crook-1:~/w205/spark-with-kafka-and-hdfs$ docker-compose logs -f kafka
  ```

## I'm viewing a directory listing of the hdfs directory /tmp.
  ```
  science@w205s4-crook-1:~/w205/spark-with-kafka-and-hdfs$ docker-compose exec cloudera hadoop fs -ls /tmp/
  ```

## I'm creating a topic called players in the kafka container. It shows "Created topic "players"."
  ```science@w205s4-crook-1:~/w205/spark-with-kafka-and-hdfs$ docker-compose exec kafka kafka-topics --create --topic players --p
  ```

## Then, I'm downloading the dataset for world cup players in json format.
  ```
  science@w205s4-crook-1:~/w205/spark-with-kafka-and-hdfs$ cd ..
  science@w205s4-crook-1:~/w205$ curl -L -o assessment-attempts-20180128-121051-nested.json https://goo.gl/f5bRm4
  science@w205s4-crook-1:~/w205$ cd spark-with-kafka-and-hdfs/
  ```

## I'm using kafkacat to publish the world cup players json data to the kafka topic players.
  ```
  science@w205s4-crook-1:~/w205/spark-with-kafka-and-hdfs$ docker-compose exec mids bash -c "cat /w205/assessment-attempts-20180128-121051-nested.json | jq '.[]' -c | kafkacat -P -b kafka:29092 -t players"
  ```

## Then, I'm starting a spark pyspark shell in the spark container. Remember that pyspark is the python interface to spark.
  ```
  science@w205s4-crook-1:~/w205/spark-with-kafka-and-hdfs$ docker-compose exec spark pyspark
  ```

## Then, I'm writing python code in pyspark to consume from the kafka topic.
  ```
  >>> raw_players = spark.read.format("kafka").option("kafka.bootstrap.servers", "kafka:29092").option("subscribe","players").option("startingOffsets", "earliest").option("endingOffsets", "latest").load()
  ```

## I'm caching the spark data structure.
  ```
  >>> raw_players.cache()
  ```

## I'm printing the schema.
  ```
  >>> raw_players.printSchema()
  ```

## I'm converting the kafka schema to strings so humans can read it.
  ```
  >>> players = raw_players.select(raw_players.value.cast('string'))
  ```

## I'm writing the players data frame to a parquet file in hadoop hdfs
  ```
  >>> players.write.parquet("/tmp/players")
  ```

## Then, I'm opening another command line window to see the directory and files we just created. 
  ```
  science@w205s4-crook-1:~$ cd w205
  science@w205s4-crook-1:~/w205$ cd spark-with-kafka-and-hdfs/  
  science@w205s4-crook-1:~/w205/spark-with-kafka-and-hdfs$ docker-compose exec cloudera hadoop fs -ls /tmp/
  science@w205s4-crook-1:~/w205/spark-with-kafka-and-hdfs$ docker-compose exec cloudera hadoop fs -ls /tmp/players/
  ```

## Then, I'm going back to our pyspark, let's look at the data I wrote. 
  ```
  >>> players.show()
  ```

## I'm seting standard output to write data using utf-8 instead of unicode.
  ```
  >>> import sys
  >>> sys.stdout = open(sys.stdout.fileno(), mode='w', encoding='utf8', buffering=1)
  ```

## I'm taking a look at the players data formatted for json.
  ```
  >>> import json
  >>> players.rdd.map(lambda x: json.loads(x.value)).toDF().show()
  ```

## I'm creating a new data frame to hold the data in json format.
  ```
  >>> extracted_players = players.rdd.map(lambda x: json.loads(x.value)).toDF()
  ```

## I'm checking the results of the new data frame.
  ```
  >>> extracted_players.show()
  ```

## I'm saving the results to a parquet forma file in hdfs.
  ```
  >>> extracted_players.write.parquet("/tmp/extracted_players")
  ```

## I'm checking the differences again between players and extracted_players.
  ```
  >>> players.show()
  >>> extracted_players.show()
  ``` 


## Activity 2

## I'm keeping the cluster and pyspark running, and go to the other terminal window to check out hdfs.
  ```
  science@w205s4-crook-1:~/w205/spark-with-kafka-and-hdfs$ docker-compose exec cloudera hadoop fs -ls /tmp/
  ```

## I'm creating a kafka topic called commits.
  ```
  science@w205s4-crook-1:~/w205/spark-with-kafka-and-hdfs$ docker-compose exec kafka kafka-topics --create --topic commits --partitions 1 --replication-factor 1 --if-not-exists --zookeeper zookeeper:32181
  ```

## Then, I'm downloading the github commits dataset in json format into the ~/w205 directory
  ```
  science@w205s4-crook-1:~/w205/spark-with-kafka-and-hdfs$ cd ~/w205
  science@w205s4-crook-1:~/w205$ curl -L -o assessment-attempts-20180128-121051-nested.json https://goo.gl/f5bRm4
  science@w205s4-crook-1:~/w205$ cd ~/w205/spark-with-kafka-and-hdfs
  ```

## I'm using kafkacat publish the github json dataset to the topic commits.
  ```
  science@w205s4-crook-1:~/w205/spark-with-kafka-and-hdfs$ docker-compose exec mids bash -c "cat /w205/assessment-attempts-20180128-121051-nested.json | jq '.[]' -c | kafkacat -P -b kafka:29092 -t commits"d
  ```
  
## Then, I'm going back to the spark window, and consume from the kafka topic commits into a kafka data frame.
  ```
  >>> raw_commits = spark.read.format("kafka").option("kafka.bootstrap.servers", "kafka:29092").option("subscribe","commits").option("startingOffsets", "earliest").option("endingOffsets", "latest").load() 
  ```
 
## I'm cacheing.
  ```
  >>> raw_commits.cache()
  ```

## I'm checking the schema and convert it into a string.
  ```
  >>> raw_commits.printSchema()
  >>> commits = raw_commits.select(raw_commits.value.cast('string'))
  ```

## I'm committing data frame to a parquet file in hdfs
  ```
  >>> commits.write.parquet("/tmp/commits")
  ```

## I'm extracting the json fields and showing the data frame after the json extraction.
  ```
  >>> extracted_commits = commits.rdd.map(lambda x: json.loads(x.value)).toDF()
  >>> extracted_commits.show()
  ```

## Since I have nested json data, I'm printing the schema.
  ```
  >>> extracted_commits.printSchema()
  ```

## I'm using spark sql to deal with the nested json.
  - Create a spark temporary table called commits based on the data frame. registerTempTable() is a method of the spark class data frame.
      ```
      >>> extracted_commits.registerTempTable('commits')
      ```
  - Issue spark sql against the temporary table commits that we just registered.
      ```
      >>> spark.sql("select commit.committer.name from commits limit 10").show()
      >>> spark.sql("select commit.committer.name, commit.committer.date, sha from commits limit 10").show()
      ```
  - Save the results of the query into another data frame.
      ```>>> some_commit_info = spark.sql("select commit.committer.name, commit.committer.date, sha from commits limit 10")
      ```
  - Write the data frame holding the results of our query to a parquet file in hdfs.
      ```
      >>> some_commit_info.write.parquet("/tmp/some_commit_info")
      ```

## Then, I'm going back to the other terminal window to see the directory and files in hdfs.
  ```
  science@w205s4-crook-1:~/w205/spark-with-kafka-and-hdfs$ docker-compose exec cloudera hadoop fs -ls /tmp/
  science@w205s4-crook-1:~/w205/spark-with-kafka-and-hdfs$ docker-compose exec cloudera hadoop fs -ls /tmp/commits/
  ```

## Then, I'm going back to the pyspark window and exit pyspark.
  ```
  >>> exit()
  ```

## Finally, I'm tearing down the docker cluster and make sure it's down.
  ```
  science@w205s4-crook-1:~/w205/spark-with-kafka-and-hdfs$ docker-compose down
  science@w205s4-crook-1:~/w205/spark-with-kafka-and-hdfs$ docker-compose ps
  ```
