# Build after: https://github.com/apiman/apiman-docker/blob/master/on-wildfly11/Dockerfile
# Use WildFly 11 image as the base
FROM jboss/wildfly:11.0.0.Final

MAINTAINER Bogdan Mic <mic.bogdan@gmail.com>

ENV APIMAN_VERSION 1.5.5.Final

RUN cd $HOME/wildfly \
    && curl https://downloads.jboss.org/apiman/$APIMAN_VERSION/apiman-distro-wildfly11-$APIMAN_VERSION-overlay.zip | bsdtar -xvf- \
    && rm -rf $HOME/wildfly/standalone/deployments/apiman-ds.xml \
    && rm -rf $HOME/wildfly/standalone/deployments/apiman-es.war \
    && rm -rf $HOME/wildfly/standalone/deployments/apiman.war \
    && rm -rf $HOME/wildfly/standalone/deployments/apimanui.war

# Add our custom config files. These are a bit cleaned up version of the ones available in the apiman-distro-wildfly11-$APIMAN_VERSION-overlay.zip
COPY configs/$APIMAN_VERSION/standalone-apiman.xml ${JBOSS_HOME}/standalone/configuration/standalone-apiman-custom.xml
COPY configs/$APIMAN_VERSION/apiman.properties ${JBOSS_HOME}/standalone/configuration/apiman.properties

USER root

RUN chown -R jboss:0 ${JBOSS_HOME} \
    && chmod -R g+rw ${JBOSS_HOME}

USER jboss

# Set the default command to run on boot
ENTRYPOINT ["/opt/jboss/wildfly/bin/standalone.sh", "-b", "0.0.0.0", "-bmanagement", "0.0.0.0", "-c", "standalone-apiman-custom.xml"]`