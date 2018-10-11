FROM nicoherbigio/debian:stretch

LABEL maintainer="Nico Herbig <nico@nicoherbig.io>"


# Add SonarQube Sonar Scanner user and group to ensure that the IDs (UID and GID)
# are consistently assigned regardless of which additional dependencies are added.

ENV RUN_USER_ID=1000 RUN_USER=sonar
ENV RUN_GROUP_ID=1000 RUN_GROUP=sonar

RUN groupadd -g ${RUN_GROUP_ID} ${RUN_GROUP} && useradd -g ${RUN_GROUP} -m -u ${RUN_USER_ID} ${RUN_USER}


# Install additional required software packages

RUN set -ex \
    && apt-get update \
    && apt-get install -y --no-install-recommends ca-certificates curl gnupg unzip \
    && rm -rf /var/lib/apt/lists/*


# Install Node for scanning of CSS, JavaScript and Typescript files

RUN set -ex \
    && curl -fSL https://deb.nodesource.com/setup_8.x | bash - \
    && apt-get install -y --no-install-recommends build-essential nodejs \
    && rm -rf /var/lib/apt/lists/*


# Install Sonarqube Sonar Scanner

ARG SONAR_SCANNER_VERSION=3.2.0.1227-linux
ARG SONAR_SCANNER_DOWNLOAD_URL=https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-${SONAR_SCANNER_VERSION}.zip

ARG SONAR_SCANNER_HOME_DIR=/opt/sonarqube/sonar-scanner
ARG SONAR_SCANNER_PROJECT_DIR=/app

RUN set -ex \
    && mkdir -p ${SONAR_SCANNER_HOME_DIR} \
    && mkdir -p ${SONAR_SCANNER_PROJECT_DIR} \
    && chown -R ${RUN_USER}:${RUN_GROUP} ${SONAR_SCANNER_PROJECT_DIR} \
#
    && curl -fSL ${SONAR_SCANNER_DOWNLOAD_URL} -o /tmp/sonar-scanner-cli.zip \
    && unzip /tmp/sonar-scanner-cli.zip -d /tmp \
    && mv /tmp/sonar-scanner-${SONAR_SCANNER_VERSION}/* ${SONAR_SCANNER_HOME_DIR} \
    && rm -rf /tmp/sonar-scanner-*

ENV PATH=${SONAR_SCANNER_HOME_DIR}/bin:${PATH}

WORKDIR /app

USER ${RUN_USER_ID}

CMD ["sonar-scanner"]
