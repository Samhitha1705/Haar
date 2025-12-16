# Use an official OpenJDK 17 image
FROM openjdk:17-jdk

# Copy your jar
COPY target/backend-0.0.1.jar app.jar

# Run the jar
ENTRYPOINT ["java","-jar","/app.jar"]
