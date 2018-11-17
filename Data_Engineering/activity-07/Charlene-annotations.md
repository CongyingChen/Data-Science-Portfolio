# My annotations, Assignment 7

## Activity 1

## I'm opening the directory and copying the yml file to spark-with-kafka.
  501  cd /Users/chenyingcharlene/w205
  502  mkdir ~/w205/spark-with-kafka
  503  cd ~/w205/spark-with-kafka
  504  cp ~/w205/course-content/07-Sourcing-Data/docker-compose.yml

## I'm starting the docker cluster.
  505  docker-compose up -d

## Then I'm watching it come up. (Detach with Ctrl-C)
  506  docker-compose logs -f kafka

## I'm creating a topic called foo in the kafka container using the kafka-topisc utility. It shows "Created topic "foo"."
  507  docker-compose exec kafka kafka-topics --create --topic foo --partitions 1 --replication-factor 1 --if-not-exists --zookeeper zookeeper:32181

## Then I'm checking the topic in the kafka container.
  508  docker-compose exec kafka kafka-topics --describe --topic foo --zookeeper zookeeper:32181


## Now since I have the topic created in kafka, I can publish the numbers from 1 to 42 to that topic, by using the kafka container with the kafka-console-producer utility. It shows "Produced 42 messages."
  509  docker-compose exec kafka bash -c "seq 42 | kafka-console-producer --request-required-acks 1 --broker-list kafka:29092 --topic foo && echo 'Produced 42 messages.'"

## In the spark container, I'm running the python spark command line utility called pyspark.
  510  docker-compose exec spark pyspark

## By using pyspark, I'm writing some python spark code to consumer from the kafka topic. (Not showing in the terminal history)
  - numbers = spark.read.format("kafka").option("kafka.bootstrap.servers", "kafka:29092").option("subscribe","foo").option("startingOffsets", "earliest").option("endingOffsets", "latest").load()

  - Print the schema for the RDD: numbers.printSchema()

  - Create a new RDD which stores the numbers as strings: numbers_as_strings=numbers.selectExpr("CAST(key AS STRING)", "CAST(value AS STRING)")

  - Display the entire RDD: numbers_as_strings.show()

  - Display the schema for the RDD: numbers_as_strings.printSchema()

  - Display the number of items in the RDD: numbers_as_strings.count()

  - Finally, exit pyspark using: exit()

## Lastly, I'm tearing down the docker cluster and verify it is down.
    511  docker-compose down
    512  docker-compose ps -a

## Activity 2

## I'm opening the directory that has the yml file.
  513  ~/w205/spark-with-kafka

## Here I'm getting the data.
  514  curl -L -o assessment-attempts-20180128-121051-nested.json https://goo.gl/f5bRm4

## I'm go to the file kafka and spinning up my kafka, zookeeper, mids cluster.
  515  docker-compose up -d

## Then I'm watching it come up. (Detach with Ctrl-C)
  516  docker-compose logs -f kafka

## After I successfully spin the cluster up, we can use it now. Firstly, I'm creating  a topic "foo". It shows "Created topic "foo"."
  517  docker-compose exec kafka kafka-topics --create --topic foo --partitions 1 --replication-factor 1 --if-not-exists --zookeeper zookeeper:32181

## Then, I'm checking the topic, and the result shows it is normal.
  518  docker-compose exec kafka kafka-topics --describe --topic foo --zookeeper zookeeper:32181

## After created the topic foo, I can publish the test message now. Firstly, I'm checking out the message from pulled data, a jason file in three different formats.
  519  docker-compose exec mids bash -c "cat /w205/assessment-attempts-20180128-121051-nested.json"
  520  docker-compose exec mids bash -c "cat /w205/assessment-attempts-20180128-121051-nested.json | jq '.'"
  521  docker-compose exec mids bash -c "cat /w205/assessment-attempts-20180128-121051-nested.json | jq '.[]' -c"

## Then I'm publishing some test messages to that topic with the kafka console producer. It shows "Produced 100 messages."
  522  docker-compose exec mids bash -c "cat /w205/assessment-attempts-20180128-121051-nested.json | jq '.[]' -c | kafkacat -P -b kafka:29092 -t foo && echo 'Produced 100 messages.'"

## Then I'm running spark using the spark container to consume messages this time.
  523  docker-compose exec spark pyspark

## Now I can read from kafka. (Not showing in the terminal history)
  messages = spark.read.format("kafka").option("kafka.bootstrap.servers", "kafka:29092").option("subscribe","foo").option("startingOffsets", "earliest").option("endingOffsets", "latest").load()

  - See the schema: messages.printSchema()

  - See the messages: messages.show()

  - Cast as strings: messages_as_strings=messages.selectExpr("CAST(key AS STRING)", "CAST(value AS STRING)")

  - Display the message: messages_as_strings.show()

  - Display the schema: messages_as_strings.printSchema()

  - Display the number of items: messages_as_strings.count()

  - Unrolling json:
      messages_as_strings.select('value').take(1)
      messages_as_strings.select('value').take(1)[0].value
      import json
      first_message=json.loads(messages_as_strings.select('value').take(1)[0].value)
      first_message
      print(first_message['started_at'])

  - Finally, exit pyspark using: exit()

## Finally, I'm taking the cluster down.
  515  docker-compose down
