# FROM python:3.9
FROM continuumio/miniconda3:4.10.3p0

ENV BASH_ENV ~/.bashrc
ENV PYTHONPATH=/app

SHELL ["/bin/bash", "-c"]

# install java
RUN mkdir -p /usr/share/man/man1
RUN apt-get update && \
    apt-get install -y openjdk-11-jre-headless && \
    apt-get install -y cmake build-essential libssl-dev tini && \
    apt-get clean;

# support arm build
ARG TARGETPLATFORM
RUN case ${TARGETPLATFORM} in \
    "linux/amd64")  JDK_ARCH=amd64  ;; \
    "linux/arm64")  JDK_ARCH=arm64  ;; \
    esac
RUN export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-${JDK_ARCH}

# install scala
RUN wget https://downloads.lightbend.com/scala/2.12.3/scala-2.12.3.deb
RUN dpkg -i scala-2.12.3.deb

# install via conda
COPY environment.yml environment.yml
RUN conda init
RUN conda env create -f environment.yml
RUN echo "conda activate 5003-project" >> ~/.bashrc
RUN conda activate 5003-project

# install drivers for pyspark
RUN mkdir /opt/drivers
RUN wget https://repo1.maven.org/maven2/org/postgresql/postgresql/42.2.19/postgresql-42.2.19.jar -P /opt/drivers

WORKDIR /app/

COPY src/pipeline /app

ENTRYPOINT ["/usr/bin/tini", "--"]

CMD /opt/conda/envs/5003-project/bin/spark-submit --packages org.apache.spark:spark-sql-kafka-0-10_2.12:3.1.2 \
    --driver-class-path /opt/drivers/postgresql-42.2.19.jar /app/pipeline.py
