# run: docker build -t generalmeow/kafka:<TAG>
# run: docker run -d -p 9092:9092 --name kafka --link zookeeper:zookeeper generalmeow/kafka
FROM anapsix/alpine-java:8
MAINTAINER Paul Hoang 2016-12-26
RUN ["mkdir", "-p", "/home/kafka"]
ADD ./kafka_2.11-0.10.1.0 /home/kafka/kafka_2.11-0.10.1.0
WORKDIR /home/kafka
EXPOSE 9092
RUN ["chmod", "-R", "700", "/home/kafka/kafka_2.11-0.10.1.0/bin"]
ENTRYPOINT ["/home/kafka/kafka_2.11-0.10.1.0/bin/kafka-server-start.sh", "/home/kafka/kafka_2.11-0.10.1.0/config/server.properties"]
