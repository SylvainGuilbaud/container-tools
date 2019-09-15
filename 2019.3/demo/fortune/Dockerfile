FROM intersystems/iris:2019.3.0.304.0

USER root
RUN apt-get update \
 && apt-get install -y fortune \
 && rm -rf /var/lib/apt/lists/*

USER $ISC_PACKAGE_MGRUSER
COPY _ZSTART.xml /
RUN iris start $ISC_PACKAGE_INSTANCENAME \
 && echo 'if ($SYSTEM.OBJ.Load("/_ZSTART.xml", "ck")) {write !, "Loaded OK"  halt} else { do $SYSTEM.Process.Terminate($JOB,1)}' | iris session $ISC_PACKAGE_INSTANCENAME \
 && iris stop $ISC_PACKAGE_INSTANCENAME quietly