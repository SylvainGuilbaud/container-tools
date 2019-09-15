# "baseos" Dockerfile for InterSystems IRIS images.
#
# To manage the introduction of external OS-level changes, we build
# images in two steps: a "base OS" layer that encapsulates OS
# package installations, and a layer that installs InterSystems IRIS
# itself. This construction isolates external changes so that they can be
# introduced into the container in a controlled fashion.

FROM ubuntu:18.04
LABEL maintainer "InterSystems Worldwide Response Center <support@intersystems.com>"

# InterSystems IRIS requires several environment variables for a valid image.
# We set these here because they involve interacting with the OS.
ENV ISC_PACKAGE_IRISGROUP=irisuser \
    ISC_PACKAGE_IRISUSER=irisuser \
    ISC_PACKAGE_MGRGROUP=irisowner \
    ISC_PACKAGE_MGRUSER=irisowner \
    IRISSYS=/home/irisowner/irissys

# The remaining required variables can be set when we install InterSystems IRIS.
#   ISC_PACKAGE_INSTANCENAME
#   ISC_PACKAGE_INSTALLDIR

# Get basic dependencies, update OS
RUN apt-get update \
 && DEBIAN_FRONTEND=noninteractive apt-get upgrade -y \
 && DEBIAN_FRONTEND=noninteractive apt-get install -y \
 # These packages are needed for networking, ODBC, and fetching the kit tarball.
      iproute2 \
      iputils-ping \
      krb5-multidev \
      libkrb5-dev \
      net-tools \
      wget \
 && DEBIAN_FRONTEND=noninteractive apt-get install -y sysstat \
 # Whatever uids are used inside the container will also be used when writing 
 # Durable %SYS files to bind mounts.  Those uids may not have names attached
 # to them on the host, so here we make sure to use numbers associated with
 # InterSystems IRIS, as a mnemonic.
 && useradd -m $ISC_PACKAGE_MGRUSER --uid 51773 \
 && useradd -m $ISC_PACKAGE_IRISUSER --uid 52773 \
 && rm -rf /var/lib/apt/lists/*
