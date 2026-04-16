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

RUN addgroup -S appgroup && adduser -S appuser -G appgroup

WORKDIR /app

ENV JAVA_TOOL_OPTIONS="-Xms512m -Xmx512m"
ENV JAVA_OPTS=""

EXPOSE 9000

COPY --from=builder --chown=appuser:appgroup /build/build/libs/ums-2.0.jar /app/ums-2.0.jar

USER appuser

ENTRYPOINT ["java", "-jar", "/app/ums-2.0.jar"]
