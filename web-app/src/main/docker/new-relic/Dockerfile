FROM frolvlad/alpine-oraclejdk8:slim
VOLUME /tmp
ADD web-app-0.1.0-SNAPSHOT.jar app.jar
RUN sh -c 'touch /app.jar'
ADD newrelic.jar newrelic.jar
RUN sh -c 'touch /newrelic.jar'
ADD newrelic.yml newrelic.yml
RUN sh -c 'touch /newrelic.yml'
ARG NEW_RELIC_APP_NAME=""
ENV NEW_RELIC_APP_NAME="MovieDBApp"
ARG NEW_RELIC_LICENSE_KEY="" 
ENV NEW_RELIC_LICENSE_KEY="d8814f588ac6488994c24e029a0b8216ebe4a3a5" 
ENV JAVA_OPTS=""
ENTRYPOINT [ "sh", "-c", "java $JAVA_OPTS -javaagent:/newrelic.jar -Djava.security.egd=file:/dev/./urandom -jar /app.jar" ]
