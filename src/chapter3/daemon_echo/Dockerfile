FROM weipeng2k/ubuntu:16.04

RUN apt-get update
RUN apt-get install -y openjdk-8-jdk

WORKDIR /root/app
COPY Test.java .
RUN chmod u+x Test.java
RUN ["javac", "Test.java"]

CMD ["java", "Test"]
