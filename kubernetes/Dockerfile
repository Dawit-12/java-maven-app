FROM openjdk:16-alpine3.13
RUN mkdir /apps
COPY src/*.jar /apps
EXPOSE 8080
USER user1
CMD [ "java -jar /apps/*.jar" ]