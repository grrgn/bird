# ==============================================================================
# STAGE 1: Build 
# ==============================================================================
FROM gradle:jdk25-alpine AS builder

WORKDIR /build

COPY build.gradle settings.gradle ./

RUN gradle dependencies --no-daemon

COPY src ./src

RUN gradle clean build -x test --no-daemon

# ==============================================================================
# STAGE 2: Runtime
# ==============================================================================
FROM eclipse-temurin:25-jre-alpine

COPY lab.home.crt /usr/local/share/ca-certificates/lab.home.crt

RUN update-ca-certificates

RUN addgroup -S appgroup && adduser -S appuser -G appgroup

WORKDIR /app

ENV JAVA_TOOL_OPTIONS="-Xms512m -Xmx512m"
ENV JAVA_OPTS=""

EXPOSE 9001

COPY --from=builder --chown=appuser:appgroup /build/build/libs/twitter-2.0.jar /app/twitter-2.0.jar

USER appuser

ENTRYPOINT ["java", "-jar", "/app/twitter-2.0.jar"]
