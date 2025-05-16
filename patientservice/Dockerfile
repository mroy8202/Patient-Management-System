FROM openjdk:21-jdk

WORKDIR /app

COPY target/patientservice-0.0.1-SNAPSHOT.jar app.jar

EXPOSE 4000

ENTRYPOINT ["java", "-jar", "app.jar"]
