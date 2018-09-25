FROM openjdk:8-jre-alpine

# Initializing variables that will be used later.
ENV APP_NAME=biz_application
ENV SERVER_PORT=9601
ENV JAR_FILE=biz-application-SNAPSHOT.jar


# Copy all the required files/directories
COPY ${APP_NAME}/${JAR_FILE} /usr/src/${JAR_FILE}

# Startup Command
ENTRYPOINT java -Dserver.port=${SERVER_PORT} -Deureka.client.serviceUrl.defaultZone=http://$(ping -c1 discovery_server |  grep 'bytes from 172' | cut -d' ' -f 4 | cut -d: -f 1):7001/eureka/ -jar /usr/src/${JAR_FILE}
