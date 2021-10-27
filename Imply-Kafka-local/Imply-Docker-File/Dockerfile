# Pull the base image
FROM ubuntu:18.04

# Set up working directory
WORKDIR /app

# Install all required packages
RUN apt-get update \
    && apt-get install -y \
        software-properties-common \
        openjdk-8-jdk \
        ant \
        wget \
        libfindbin-libs-perl \
        python \
        ca-certificates-java \
    && apt-get clean;
# Fix certificate issues
RUN update-ca-certificates -f;

# Setup JAVA_HOME -- useful for docker commandline
ENV JAVA_HOME /usr/lib/jvm/java-8-openjdk-amd64/
RUN export JAVA_HOME

# Copy the imply setup to the container. This
RUN wget https://static.imply.io/release/imply-2021.10.tar.gz \
    && tar -xzf imply-2021.10.tar.gz \
    && rm imply-2021.10.tar.gz

# Expose the port for imply 
EXPOSE 9095 8081 8090 8888 8082 8083 8091
WORKDIR /app/imply-2021.10
# Execute/start imply services
CMD ["./bin/supervise","-c","conf/supervise/quickstart.conf"]