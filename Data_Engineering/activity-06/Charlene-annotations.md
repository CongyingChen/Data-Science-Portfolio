# My annotations, Assignment 6


## Here I'm opening the directory that has my docker-compose file in it.
  501  cd /Users/chenyingcharlene/w205

## Here I'm getting the data.
  502  curl -L -o assessment-attempts-20180128-121051-nested.json https://goo.gl/f5bRm4

## I'm go to the file kafka and spinning up my kafka, zookeeper, mids cluster, since I already put a docker-compose.yml in the file of kafaka.
  503  cd kafka
  504  docker-compose up -d

## Then I'm watching it come up. (Detach with Ctrl-C)
  505  docker-compose logs -f kafka

## After I successfully spin the cluster up, we can use it now. Firstly, I'm creating  a topic "foo". It shows "Created topic "foo"."
  506  docker-compose exec kafka kafka-topics --create --topic foo --partitions 1 --replication-factor 1 --if-not-exists --zookeeper zookeeper:32181

## Then, I'm checking the topic, and the result shows it is normal.
  507  docker-compose exec kafka kafka-topics --describe --topic foo --zookeeper zookeeper:32181

## After created the topic foo, I can publish the test message now. Firstly, I'm checking out the message from pulled data, a jason file in three different formats.
  508  docker-compose exec mids bash -c "cat /w205/assessment-attempts-20180128-121051-nested.json"
  509  docker-compose exec mids bash -c "cat /w205/assessment-attempts-20180128-121051-nested.json | jq '.'"
  510  docker-compose exec mids bash -c "cat /w205/assessment-attempts-20180128-121051-nested.json | jq '.[]' -c"

## Then I'm publishing some test messages to that topic with the kafka console producer.
  511  docker-compose exec mids bash -c "cat /w205/assessment-attempts-20180128-121051-nested.json | jq '.[]' -c | kafkacat -P -b kafka:29092 -t foo && echo 'Produced 100 messages.'"

## After that, I can consume the messages now, in three different ways. I'm starting a consumer to read from topic foo.
  512  docker-compose exec kafka kafka-console-consumer --bootstrap-server kafka:29092 --topic foo --from-beginning --max-messages 42
  513  docker-compose exec mids bash -c "kafkacat -C -b kafka:29092 -t foo -o beginning -e"
  514  docker-compose exec mids bash -c "kafkacat -C -b kafka:29092 -t foo -o beginning -e" | wc -l

## I'm taking the cluster down.
  515  docker-compose down
