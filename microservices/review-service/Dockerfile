FROM maven:3.9.1-eclipse-temurin-17 AS builder
WORKDIR /app
COPY . .
RUN mvn -f util/pom.xml clean install -DskipTests
RUN mvn -pl microservices/review-service clean package -DskipTests

FROM eclipse-temurin:17.0.5_8-jre-focal AS extractor
WORKDIR /extracted
COPY --from=builder /app/microservices/review-service/target/*.jar app.jar
RUN java -Djarmode=layertools -jar app.jar extract

FROM eclipse-temurin:17.0.5_8-jre-focal
WORKDIR /application
COPY --from=extractor /extracted/dependencies/ ./
COPY --from=extractor /extracted/spring-boot-loader/ ./
COPY --from=extractor /extracted/snapshot-dependencies/ ./
COPY --from=extractor /extracted/application/ ./
EXPOSE 8080
ENTRYPOINT ["java", "org.springframework.boot.loader.launch.JarLauncher"]