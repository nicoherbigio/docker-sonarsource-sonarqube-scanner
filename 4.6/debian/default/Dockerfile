FROM nicoherbigde/debian:buster

LABEL maintainer="Nico Herbig <nico@nicoherbig.io>"


# Add SonarSource SonarQube Scanner user and group to ensure that the IDs (UID and GID)
# are consistently assigned regardless of which additional dependencies are added.

ENV RUN_USER_ID=1000 RUN_USER=sonarqube
ENV RUN_GROUP_ID=1000 RUN_GROUP=sonarqube

RUN groupadd -g ${RUN_GROUP_ID} ${RUN_GROUP} && useradd -g ${RUN_GROUP} -m -u ${RUN_USER_ID} ${RUN_USER}


# Install additional required software packages

RUN set -ex \
    && apt-get update \
    && apt-get install -y --no-install-recommends ca-certificates curl gnupg unzip \
    && rm -rf /var/lib/apt/lists/*


# Install Node for scanning of CSS, JavaScript and Typescript files

RUN set -ex \
    && curl -fSL https://deb.nodesource.com/setup_12.x | bash - \
    && apt-get install -y --no-install-recommends build-essential nodejs \
    && rm -rf /var/lib/apt/lists/*


# Install SonarSource SonarQube Scanner

ARG SONARQUBE_SCANNER_VERSION=4.6.1.2450-linux
ARG SONARQUBE_SCANNER_DOWNLOAD_URL=https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-${SONARQUBE_SCANNER_VERSION}.zip

ARG SONARQUBE_SCANNER_HOME_DIR=/opt/sonarqube/sonar-scanner
ARG SONARQUBE_SCANNER_PROJECT_DIR=/app

RUN set -ex \
    && mkdir -p ${SONARQUBE_SCANNER_HOME_DIR} \
    && mkdir -p ${SONARQUBE_SCANNER_PROJECT_DIR} \
    && chown -R ${RUN_USER}:${RUN_GROUP} ${SONARQUBE_SCANNER_PROJECT_DIR} \
#
    && curl -fSL ${SONARQUBE_SCANNER_DOWNLOAD_URL} -o /tmp/sonar-scanner-cli.zip \
    && unzip /tmp/sonar-scanner-cli.zip -d /tmp \
    && mv /tmp/sonar-scanner-${SONARQUBE_SCANNER_VERSION}/* ${SONARQUBE_SCANNER_HOME_DIR} \
    && rm -rf /tmp/sonar-scanner-*

ENV PATH=${SONARQUBE_SCANNER_HOME_DIR}/bin:${PATH}

WORKDIR /app

USER ${RUN_USER_ID}

CMD ["sonar-scanner"]
