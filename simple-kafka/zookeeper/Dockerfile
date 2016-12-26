# run: docker build -t generalmeow/kafkazookeeper:<TAG>
# run: docker run -d -p 2181:2181 --name zookeeper generalmeow/kafkazookeeper:1.0
FROM anapsix/alpine-java:8
MAINTAINER Paul Hoang 2016-12-26
RUN ["mkdir", "-p", "/home/zookeeper"]
ADD ./kafka_2.11-0.10.1.0 /home/zookeeper/kafka_2.11-0.10.1.0
WORKDIR /home/zookeeper
EXPOSE 2181
RUN  ["chmod", "-R", "700", "/home/zookeeper/kafka_2.11-0.10.1.0/bin"]
ENTRYPOINT ["/home/zookeeper/kafka_2.11-0.10.1.0/bin/zookeeper-server-start.sh", "/home/zookeeper/kafka_2.11-0.10.1.0/config/zookeeper.properties"]
