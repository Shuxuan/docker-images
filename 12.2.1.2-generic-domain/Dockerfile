# Pull base image
# ---------------
FROM weblogic:12.2.1.2-generic

# Maintainer
# ----------
MAINTAINER Shuxuan Nie <shuxuan.nie@oracle.com>

# WLS Configuration (editable during build time)
# ------------------------------
ARG ADMIN_PASS
ARG DOMAIN_NAME
ARG ADMIN_PORT
ARG CLUSTER_NAME
ARG DEBUG_FLAG
ARG PRODUCTION_MODE

# WLS Configuration (editable during runtime)
# ---------------------------
ENV ADMIN_PASSWORD=$ADMIN_PASS \
    ADMIN_HOST="wlsadmin" \
    NM_PORT="5556" \
    MS_PORT="7001" \
    DEBUG_PORT="8453" \
    CONFIG_JVM_ARGS="-Dweblogic.security.SSL.ignoreHostnameVerification=true"

# WLS Configuration (persisted. do not change during runtime)
# -----------------------------------------------------------
ENV JAVA_HOME=/usr/java/default \
    DOMAIN_NAME="${DOMAIN_NAME:-base_domain}" \
    DOMAIN_HOME=/u01/oracle/user_projects/domains/${DOMAIN_NAME:-base_domain} \
    ADMIN_PORT="${ADMIN_PORT:-8001}" \
    CLUSTER_NAME="${CLUSTER_NAME:-DockerCluster}" \
    debugFlag="${DEBUG_FLAG:-false}" \
    PRODUCTION_MODE="${PRODUCTION_MODE:-prod}" \
    PATH=$PATH:/u01/oracle/oracle_common/common/bin:/u01/oracle/wlserver/common/bin:/u01/oracle/user_projects/domains/${DOMAIN_NAME:-base_domain}/bin:/u01/oracle

# Add files required to build this image
USER oracle
COPY container-scripts/* /u01/oracle/

# Configuration of WLS Domain

RUN echo "Admin Password" $ADMIN_PASSWORD && \
    /u01/oracle/wlst /u01/oracle/create-wls-domain.py && \
    mkdir -p /u01/oracle/user_projects/domains/$DOMAIN_NAME/servers/AdminServer/security && \
    echo "username=weblogic" > /u01/oracle/user_projects/domains/$DOMAIN_NAME/servers/AdminServer/security/boot.properties && \ 
    echo "password=$ADMIN_PASSWORD" >> /u01/oracle/user_projects/domains/$DOMAIN_NAME/servers/AdminServer/security/boot.properties && \
    echo ". /u01/oracle/user_projects/domains/$DOMAIN_NAME/bin/setDomainEnv.sh" >> /u01/oracle/.bashrc 

# Expose Node Manager default port, and also default for admin and managed server 
EXPOSE $NM_PORT $ADMIN_PORT $MS_PORT $DEBUG_PORT

WORKDIR $DOMAIN_HOME

# Define default command to start bash. 
CMD ["startWebLogic.sh"]
