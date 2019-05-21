FROM debian:jessie
FROM picoded/ubuntu-base

MAINTAINER Marco Santonastaso <m.santonastaso@protonmail.com>

ENV TOMCAT_VERSION 8.5.41
ENV IIQ_VERSION 7.3

# Fix sh
RUN rm /bin/sh && ln -s /bin/bash /bin/sh

# Install dependencies
RUN apt-get update && \
apt-get install -y apt-utils wget unzip tar mysql-client

# Install JDK 8
RUN apt-get update && \
    apt-get install -y openjdk-8-jdk && \
    apt-get install -y ant && \
    apt-get clean;

# Fix certificate issues
RUN apt-get update && \
    apt-get install ca-certificates-java && \
    apt-get clean && \
    update-ca-certificates -f;

# Setup JAVA_HOME -- useful for docker commandline
ENV JAVA_HOME /usr/lib/jvm/java-8-openjdk-amd64/

# Get Tomcat
RUN wget --quiet --no-cookies http://www-eu.apache.org/dist/tomcat/tomcat-8/v${TOMCAT_VERSION}/bin/apache-tomcat-${TOMCAT_VERSION}.tar.gz -O /tmp/tomcat.tgz && \
tar xzvf /tmp/tomcat.tgz -C /opt && \
mv /opt/apache-tomcat-${TOMCAT_VERSION} /opt/tomcat && \
rm /tmp/tomcat.tgz && \
rm -rf /opt/tomcat/webapps/examples && \
rm -rf /opt/tomcat/webapps/docs && \
rm -rf /opt/tomcat/webapps/ROOT

# Add admin/admin user
ADD tomcat-users.xml /opt/tomcat/conf/
run mkdir -p /opt/tomcat/conf/Catalina/localhost
ADD manager.xml /opt/tomcat/conf/Catalina/localhost
# add IIQ
COPY src/identityiq-${IIQ_VERSION}.zip /tmp
RUN unzip /tmp/identityiq-${IIQ_VERSION}.zip identityiq.war && \
mkdir /opt/tomcat/webapps/identityiq && \
unzip identityiq.war -d /opt/tomcat/webapps/identityiq && \
chmod +x /opt/tomcat/webapps/identityiq/WEB-INF/bin/iiq && \
rm identityiq.war

RUN mkdir /opt/tomcat/webapps/ROOT
COPY index.html /opt/tomcat/webapps/ROOT

COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh

ENV CATALINA_HOME /opt/tomcat
ENV PATH $PATH:$CATALINA_HOME/bin

EXPOSE 8080
EXPOSE 8009
VOLUME "/opt/tomcat/webapps"
WORKDIR /opt/tomcat

# Launch IIQ
CMD ["/entrypoint.sh", "run"]
#CMD ["/opt/tomcat/bin/catalina.sh", "run"]
