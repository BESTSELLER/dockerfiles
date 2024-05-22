ARG JAVA_VERSION=21
ARG MAVEN_VERSION=3.9.6

FROM maven:${MAVEN_VERSION}-eclipse-temurin-${JAVA_VERSION}-alpine AS build
WORKDIR /app

ADD . .
RUN mvn package -DskipTests


FROM eclipse-temurin:${JAVA_VERSION}-jre-alpine AS run
WORKDIR /app

RUN chown -R nobody:nobody /opt
# RUN mkdir /app && chown -R appuser:appgroup /app

# ADD --chown=nobody:nobody ./docker-entrypoint.sh /app
COPY --chown=nobody:nobody --from=build ./target/*.jar /app/app.jar
ADD --chown=nobody:nobody https://dtdg.co/latest-java-tracer /app/dd-java-agent.jar

# 65534:65534
USER nobody

# ENTRYPOINT ["/app/docker-entrypoint.sh"]
CMD ["java", "-javaagent:/app/dd-java-agent.jar", "-Ddd.agent.host=datadog-agent.datadog", "-Ddd.agent.port=8126", "-Ddd.trace.analytics.enabled=true", "-Ddd.logs.injection=true", "-Ddd.profiling.enabled=true", "-XX:FlightRecorderOptions=stackdepth=256", "-XX:MaxRAMPercentage=75.0", "-jar", "/app/app.jar"]
