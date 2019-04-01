# docker build --build-arg SENSORS="12 10000" \
#  --build-arg MQTT_HOST=192.168.x.x --build-arg MQTT_PORT=1883 \
#  --build-arg=MQTT_USERNAME=username --build-arg=MQTT_PASSWORD=hemligt

ARG BUILD_FROM
FROM $BUILD_FROM

ENV LANG C.UTF-8

FROM alpine:edge as BUILD_ENV

COPY ./sparsnas_decode.cpp /build/

RUN \
  apk add --no-cache \
    g++ \
    mosquitto-dev \
  \
  && g++ -o /build/sparsnas_decode -O2 -Wall /build/sparsnas_decode.cpp -lmosquitto

FROM alpine:edge

RUN apk add --no-cache jq

#ARG SENSORS
#ARG MQTT_HOST
#ARG MQTT_PORT
#ARG MQTT_USERNAME
#ARG MQTT_PASSWORD
ENV SENSORS="123456 1000"
ENV MQTT_HOST=${MQTT_HOST:-localhost}
ENV MQTT_PORT=${MQTT_PORT:-1883}
ENV MQTT_USERNAME="user"
ENV MQTT_PASSWORD="pass"

RUN : "${SENSORS:?Build argument 'SENSORS' needs to be set and non-empty.}"

COPY --from=BUILD_ENV /build/sparsnas_decode /usr/bin/
COPY sparsnas.sh /

RUN apk add --no-cache --repository http://dl-cdn.alpinelinux.org/alpine/edge/testing/ --allow-untrusted \
      rtl-sdr \
      mosquitto-libs++ \
      zsh

#RUN sed -i "s/^SENSORS=.*/SENSORS=(${SENSORS})/" /sparsnas.sh

RUN chmod a+x /sparsnas.sh

ENTRYPOINT ["/sparsnas.sh"]
