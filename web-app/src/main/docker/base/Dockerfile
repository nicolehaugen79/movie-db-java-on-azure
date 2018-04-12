FROM frolvlad/alpine-oraclejdk8:slim
VOLUME /tmp
wget -O applicationinsights-agent-2.0.2.jar https://github.com/Microsoft/ApplicationInsights-Java/releases/download/2.0.2/applicationinsights-agent-2.0.2.jar
ADD web-app-0.1.0-SNAPSHOT.jar app.jar
RUN sh -c 'touch /app.jar'
ENV JAVA_OPTS="$JAVA_OPTS -javaagent:applicationinsights-agent-2.0.2.jar"
ENTRYPOINT [ "sh", "-c", "java $JAVA_OPTS -Djava.security.egd=file:/dev/./urandom -jar /app.jar" ]
