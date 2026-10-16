# ---------------- STAGE 1 ----------------

# base image 
FROM maven:3.9-eclipse-temurin-21-alpine AS builder

WORKDIR /app

# copying pom.xml to optimize layer caching for dependencies
COPY pom.xml ./

# dowloads all the necessary dependencies ( batch mode )  
RUN mvn -B dependency:go-offline

# get the code in 
COPY . . 

# build the executable JAR ( batch mode ) 
RUN mvn -B clean package

# ------------------------- STAGE 2 --------------------------
FROM eclipse-temurin:21-jre-alpine AS runner

WORKDIR /app

# creating an non-root system user and group for security 
RUN addgroup -g 1001 -S spring && \
adduser -S spring -u 1001 -G spring     # ---- 1001 : Group ID , -S : system group/system user -------

COPY --from=builder /app/target/inventory-0.0.1-SNAPSHOT.jar ./app.jar

# changing ownership of the application to non-root user 
RUN chown spring:spring /app/app.jar 

# switch to non-root user 
USER spring

EXPOSE 8080

# run the application 
ENTRYPOINT ["java", "-jar", "app.jar"]

