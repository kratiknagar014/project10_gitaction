# Development Dockerfile for Spring Boot application (localhost)
FROM openjdk:11-jdk-slim

# Set working directory
WORKDIR /app

# Install Maven and curl
RUN apt-get update && \
    apt-get install -y maven curl && \
    rm -rf /var/lib/apt/lists/*

# Copy Maven wrapper and pom.xml for dependency caching
COPY pom.xml .
COPY .mvn .mvn
COPY mvnw .
COPY mvnw.cmd .

# Make mvnw executable
RUN chmod +x mvnw

# Download dependencies (better caching)
RUN ./mvnw dependency:go-offline -B

# Copy source code
COPY src ./src

# Expose development port and debug port
EXPOSE 8084 5005

# Development command with hot reload and debug
CMD ["./mvnw", "spring-boot:run", "-Dspring-boot.run.profiles=dev", "-Dspring-boot.run.jvmArguments=-Xdebug -Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=*:5005"]
