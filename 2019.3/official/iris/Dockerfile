# Dockerfile for InterSystems IRIS
# (c) InterSystems 2019

FROM $BASEOS
LABEL maintainer "InterSystems Worldwide Response Center <support@intersystems.com>"

# Arguments passed in
ARG FTP_PATH
ARG ISC_PACKAGE_PLATFORM=dockerubuntux64

# InterSystems IRIS requires several environment variables for a valid image.
# The following were set in the baseos Dockerfile:
# ISC_PACKAGE_IRISGROUP
# ISC_PACKAGE_IRISUSER
# ISC_PACKAGE_MGRGROUP
# ISC_PACKAGE_MGRUSER
# IRISSYS
# We set the last two required variables.
ENV ISC_PACKAGE_INSTANCENAME=IRIS \
    ISC_PACKAGE_INSTALLDIR=/usr/irissys


# Install InterSystems IRIS
RUN mkdir -p /tmp/work/extract \
 # COPYing the tarball leaves us with large intermediate layers.
 # Downloading it allows us to avoid multi-stage builds.
 && cd /tmp/work/extract \
 && export INT_F_NAME=iris.tar.gz \
 && wget -q ${FTP_PATH} -O ../${INT_F_NAME} \
 && tar zxf ../${INT_F_NAME} \
 && cd * \
 # These installer parameters don't need to be in the image, so we set them
 # here rather than using ENV.
 && export ISC_PACKAGE_USER_PASSWORD=SYS \
 && export ISC_PACKAGE_STARTIRIS=Y \
 && export ISC_PACKAGE_UNICODE=Y \
 && export ISC_PACKAGE_INITIAL_SECURITY=Normal \
 && ./irisinstall_silent \
 && cd /tmp \
 # Remove installer tarball
 && rm -rf /tmp/work \
 # changePassword.sh is called by iris-main and must be in our PATH.
 # configLicense.sh is used by ICM and is optional for non-ICM users.
 && ln -s ${ISC_PACKAGE_INSTALLDIR}/dev/Container/changePassword.sh ${ISC_PACKAGE_INSTALLDIR}/dev/Cloud/ICM/configLicense.sh /usr/bin/ \
 # Many tasks essential to making a sound InterSystems IRIS image are best
 # performed in IRIS directly, or in an environment with better conditionals
 # than a Dockerfile.
 && $ISC_PACKAGE_INSTALLDIR/dev/Container/imageBuildSteps.sh

# Now that we're not running irisinstall, let's stop being root
USER $ISC_PACKAGE_MGRUSER
WORKDIR /home/$ISC_PACKAGE_MGRUSER

COPY irisHealth.sh /
COPY iris-main /

HEALTHCHECK --interval=1m --timeout=10s --start-period=1m --retries=3 \
  CMD /irisHealth.sh || exit 1

ENTRYPOINT ["/iris-main"]
