# ------------------------------------------------------------------------
#
# Copyright 2017 WSO2, Inc. (http://wso2.com)
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
# http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License
#
# ------------------------------------------------------------------------

# set base docker image to adoptopenjdk ubuntu docker image
FROM adoptopenjdk/openjdk8:jdk8u192-b12
LABEL maintainer="WSO2 Docker Maintainers <dev@wso2.org>"

# docker image build arguments for user/group configurations
ARG USER=wso2carbon
ARG USER_ID=802
ARG USER_GROUP=wso2
ARG USER_GROUP_ID=802
ARG USER_HOME=/home/${USER}
# docker image build argument for docker image build context
ARG FILES=./files
# docker image build arguments for wso2 product installation
ARG WSO2_SERVER_NAME=wso2sp
ARG WSO2_SERVER_DESCRIPTION="Stream Processor"
ARG WSO2_SERVER_VERSION=4.4.0
ARG WSO2_SERVER_PACK=${WSO2_SERVER_NAME}-${WSO2_SERVER_VERSION}
ARG WSO2_SERVER_HOME=${USER_HOME}/${WSO2_SERVER_PACK}
# docker image build arguments for external artifacts
ARG MYSQL_CONNECTOR_VERSION=5.1.47
# docker image build argument for unix message of the day
ARG MOTD="\n\
Welcome to WSO2 Docker resources.\n\
------------------------------------ \n\
This Docker container comprises of WSO2 ${WSO2_SERVER_DESCRIPTION} product, running v${WSO2_SERVER_VERSION} GA release \n\
which is under the Apache License, Version 2.0. \n\
Read more about Apache License, Version 2.0 here @ http://www.apache.org/licenses/LICENSE-2.0.\n"

# create the user and group
RUN \
    groupadd --system -g ${USER_GROUP_ID} ${USER_GROUP} \
    && useradd --system --create-home --home-dir ${USER_HOME} --no-log-init -g ${USER_GROUP_ID} -u ${USER_ID} ${USER}

# install required packages
RUN \
    apt-get update \
    && DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends netcat \
    && rm -rf /var/lib/apt/lists/* \
    && echo '[ ! -z "${TERM}" -a -r /etc/motd ] && cat /etc/motd' >> /etc/bash.bashrc; echo "${MOTD}" > /etc/motd

# copy the wso2 product distribution to user's home directory
COPY --chown=wso2carbon:wso2 ${FILES}/${WSO2_SERVER_PACK}/ ${WSO2_SERVER_HOME}/
# copy the Kafka client jars to the product home
COPY --chown=wso2carbon:wso2 ${FILES}/kafka_2.11_0.10.0.0_1.0.0.jar ${WSO2_SERVER_HOME}/lib/
COPY --chown=wso2carbon:wso2 ${FILES}/kafka_clients_0.10.0.0_1.0.0.jar ${WSO2_SERVER_HOME}/lib/
COPY --chown=wso2carbon:wso2 ${FILES}/metrics_core_2.2.0_1.0.0.jar ${WSO2_SERVER_HOME}/lib/
COPY --chown=wso2carbon:wso2 ${FILES}/scala_library_2.11.8_1.0.0.jar ${WSO2_SERVER_HOME}/lib/
COPY --chown=wso2carbon:wso2 ${FILES}/scala_parser_combinators_2.11_1.0.4_1.0.0.jar ${WSO2_SERVER_HOME}/lib/
COPY --chown=wso2carbon:wso2 ${FILES}/zkclient_0.8_1.0.0.jar ${WSO2_SERVER_HOME}/lib/
COPY --chown=wso2carbon:wso2 ${FILES}/zookeeper_3.4.6_1.0.0.jar ${WSO2_SERVER_HOME}/lib/
# add mysql jdbc connector to server home as a third party library
ADD --chown=wso2carbon:wso2 http://central.maven.org/maven2/mysql/mysql-connector-java/${MYSQL_CONNECTOR_VERSION}/mysql-connector-java-${MYSQL_CONNECTOR_VERSION}.jar ${WSO2_SERVER_HOME}/lib/

# set the user and work directory
USER ${USER_ID}
WORKDIR ${USER_HOME}

# set environment variables
ENV WORKING_DIRECTORY=${USER_HOME} \
    WSO2_SERVER_HOME=${WSO2_SERVER_HOME}
