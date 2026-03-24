java -server -jar \
  -Xms2g \
  -Xmx2g \
  -XX:MetaspaceSize=256m \
  -XX:MaxMetaspaceSize=512m \
  -XX:+UseG1GC \
  -XX:MaxGCPauseMillis=200 \
  -Dspring.profiles.active=prod \
  -XX:+PrintGCDetails \
  -XX:+PrintGCDateStamps \
  -XX:+DisableExplicitGC \
  -XX:+HeapDumpOnOutOfMemoryError \
  -XX:HeapDumpPath=/data/logs/jar/pay-api-dumpfile.hprof \
  -Xloggc:/data/logs/jar/pay-api-gc.log \
  -XX:+UseGCLogFileRotation \
  -XX:NumberOfGCLogFiles=10 \
  -XX:GCLogFileSize=100M \
  /data/jar/java-pay-api/pay-api.jar
