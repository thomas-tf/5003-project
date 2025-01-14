FROM bitnami/python:3.9.7-prod
LABEL maintainer "Bitnami <containers@bitnami.com>"

ENV HOME="/" \
    OS_ARCH="amd64" \
    OS_FLAVOUR="debian-10" \
    OS_NAME="linux" \
    PATH="/opt/bitnami/python/bin:/opt/bitnami/java/bin:/opt/bitnami/spark/bin:/opt/bitnami/spark/sbin:/opt/bitnami/common/bin:$PATH"

ENV BASH_ENV ~/.bashrc
SHELL ["/bin/bash", "-c"]

COPY ./src/backend_pipeline/spark/prebuildfs /
# Install required system packages and dependencies
RUN install_packages acl ca-certificates curl gzip libbz2-1.0 libc6 libffi6 libgcc1 liblzma5 libncursesw6 libreadline7 libsqlite3-0 libssl1.1 libstdc++6 libtinfo6 procps tar zlib1g
RUN . /opt/bitnami/scripts/libcomponent.sh && component_unpack "java" "1.8.302-0" --checksum 59c0b7440797c7110c76959dbbce5235359b52adf62f99218b2718c04f2d67b6
RUN . /opt/bitnami/scripts/libcomponent.sh && component_unpack "spark" "3.1.2-0" --checksum 0a4e7acfc0b025d99e3723f80cd3a1e1a331c9b977be9a1f05bc15e5f5b01709
RUN . /opt/bitnami/scripts/libcomponent.sh && component_unpack "gosu" "1.14.0-0" --checksum 3e6fc37ca073b10a73a804d39c2f0c028947a1a596382a4f8ebe43dfbaa3a25e
RUN . /opt/bitnami/scripts/libcomponent.sh && component_unpack "miniconda" "4.9.2-0" --checksum 35d4fd9e2c401689d9880a5cd2fb8e099202320a70f025d5f93ca5b800e45d26
RUN chmod g+rwX /opt/bitnami

COPY ./src/backend_pipeline/spark/rootfs /
RUN /opt/bitnami/scripts/spark/postunpack.sh
ENV BITNAMI_APP_NAME="spark" \
    BITNAMI_IMAGE_VERSION="3.1.2-debian-10-r118" \
    JAVA_HOME="/opt/bitnami/java" \
    LD_LIBRARY_PATH="/opt/bitnami/python/lib/:/opt/bitnami/spark/venv/lib/python3.6/site-packages/numpy.libs/:$LD_LIBRARY_PATH" \
    LIBNSS_WRAPPER_PATH="/opt/bitnami/common/lib/libnss_wrapper.so" \
    NSS_WRAPPER_GROUP="/opt/bitnami/spark/tmp/nss_group" \
    NSS_WRAPPER_PASSWD="/opt/bitnami/spark/tmp/nss_passwd" \
    SPARK_HOME="/opt/bitnami/spark" \
    PATH="/opt/bitnami/miniconda/bin:$PATH"

# install via conda
COPY environment.yml environment.yml
RUN /opt/bitnami/miniconda/bin/conda init bash
RUN /opt/bitnami/miniconda/bin/conda env create -f environment.yml
RUN echo "conda activate 5003-project" >> ~/.bashrc
RUN conda activate 5003-project

# install drivers for pyspark
RUN wget https://repo1.maven.org/maven2/org/postgresql/postgresql/42.2.19/postgresql-42.2.19.jar -P /opt/bitnami/spark/jars

# install netcat for health check
RUN apt-get update
RUN apt install net-tools

WORKDIR /opt/bitnami/spark
USER 1001
ENTRYPOINT [ "/opt/bitnami/scripts/spark/entrypoint.sh" ]
CMD [ "/opt/bitnami/scripts/spark/run.sh"]