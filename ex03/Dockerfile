# for build
FROM maven:3.9.2-eclipse-temurin-17-alpine AS build
COPY . /home/maven
WORKDIR /home/maven
RUN mvn package

# for packaging container image
FROM bellsoft/liberica-openjdk-debian:17
COPY --from=build /home/maven/target/*.jar /srv/application.jar
RUN groupadd -r springboot && useradd --no-log-init -r -g springboot springboot
USER springboot
EXPOSE 8080
CMD ["java", "-jar", "/srv/application.jar"]
