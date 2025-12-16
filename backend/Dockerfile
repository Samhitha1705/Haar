# Use an official OpenJDK 17 image
FROM openjdk:17.0.8-jdk-slim

# Copy your jar
COPY target/backend-0.0.1.jar app.jar

# Run the jar
ENTRYPOINT ["java","-jar","/app.jar"]
