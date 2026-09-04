FROM maven:3.9-eclipse-temurin-21 AS build
WORKDIR /src
COPY pom.xml .
RUN mvn -B dependency:go-offline
COPY . .
RUN mvn -B clean package -DskipTests

FROM eclipse-temurin:21-jre-alpine
WORKDIR /app
COPY --from=build /src/target/*.jar app.jar
# Explicit numeric uid — Kubernetes' runAsNonRoot check refuses to start
# a container whose USER is a name, because it cannot verify from outside
# the image that the name is non-root (2026-09, java-app-backend hit this
# with CreateContainerConfigError under our restricted profile).
RUN addgroup -S -g 10001 app && adduser -S -u 10001 -G app app
USER 10001
ENV PORT=8080
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "/app/app.jar"]
