#
# for build
#
FROM maven:3.6.0-jdk-11 AS build
COPY . /home/maven
WORKDIR /home/maven
RUN mvn package

#
# for packaging container image
#
FROM openjdk:11-jdk
COPY --from=build /home/maven/target/*.jar /srv/application.jar
RUN groupadd -r springboot && useradd --no-log-init -r -g springboot springboot
USER springboot
EXPOSE 8080
CMD ["java", "-jar", "/srv/application.jar"]
