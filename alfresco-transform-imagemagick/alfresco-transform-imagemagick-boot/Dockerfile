# Image provides a container in which to run ImageMagick transformations for Alfresco Content Services.

# ImageMagick is from ImageMagick Studio LLC. See the license at http://www.imagemagick.org/script/license.php or in /ImageMagick-license.txt.

FROM alfresco/alfresco-base-java:11.0.11-centos-8@sha256:91627e3fb0cce0348de66fe42c5c39a7a872a23dbf35714efa255673a3b71aaa

ARG IMAGEMAGICK_VERSION=7.0.10-59

ENV IMAGEMAGICK_RPM_URL=https://nexus.alfresco.com/nexus/service/local/repositories/thirdparty/content/org/imagemagick/imagemagick-distribution/${IMAGEMAGICK_VERSION}/imagemagick-distribution-${IMAGEMAGICK_VERSION}-linux.rpm
ENV IMAGEMAGICK_LIB_RPM_URL=https://nexus.alfresco.com/nexus/service/local/repositories/thirdparty/content/org/imagemagick/imagemagick-distribution/${IMAGEMAGICK_VERSION}/imagemagick-distribution-${IMAGEMAGICK_VERSION}-libs-linux.rpm
ENV IMAGEMAGICK_DEP_RPM_URL=https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
ENV JAVA_OPTS=""

# Set default user information
ARG GROUPNAME=Alfresco
ARG GROUPID=1000
ARG IMAGEUSERNAME=imagemagick
ARG USERID=33002

COPY target/${env.project_artifactId}-${env.project_version}.jar /usr/bin

RUN ln /usr/bin/${env.project_artifactId}-${env.project_version}.jar /usr/bin/${env.project_artifactId}.jar && \
    curl -s -S $IMAGEMAGICK_RPM_URL      -o imagemagick-distribution-linux.rpm && \
    curl -s -S $IMAGEMAGICK_LIB_RPM_URL  -o imagemagick-distribution-libs-linux.rpm && \
    curl -s -S $IMAGEMAGICK_DEP_RPM_URL  -o imagemagick-epel-dep.rpm && \
    yum localinstall -y imagemagick-epel-dep.rpm && \
    yum localinstall -y imagemagick-distribution-*linux.rpm && \
    rm -f imagemagick-distribution-*.rpm && \
    yum clean all

ADD target/generated-resources/licenses              /licenses
ADD target/generated-resources/licenses.xml          /licenses/
ADD target/generated-sources/license/THIRD-PARTY.txt /licenses/
COPY src/main/resources/licenses/3rd-party/ /

RUN groupadd -g ${GROUPID} ${GROUPNAME} && \
    useradd -u ${USERID} -G ${GROUPNAME} ${IMAGEUSERNAME} && \
    chgrp -R ${GROUPNAME} /usr/bin/${env.project_artifactId}.jar

EXPOSE 8090

USER ${IMAGEUSERNAME}

ENTRYPOINT java $JAVA_OPTS -jar /usr/bin/${env.project_artifactId}.jar
