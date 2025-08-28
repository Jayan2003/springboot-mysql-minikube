# Use a Java runtime as the base image
FROM openjdk:17-jdk-slim

# Set the working directory inside the container
WORKDIR /app

# Copy the packaged JAR file from your target directory to the container
# The name of the JAR file is demo-0.0.1-SNAPSHOT.jar based on your pom.xml
COPY target/demo-0.0.1-SNAPSHOT.jar app.jar

# Expose the port your Spring Boot app runs on
EXPOSE 8080

# The command to run your application when the container starts
CMD ["java", "-jar", "app.jar"]