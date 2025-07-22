FROM openjdk:17-jdk-alpine
COPY ./springboot-app/target/*.jar app.jar
ENTRYPOINT ["java", "-jar", "/app.jar"]