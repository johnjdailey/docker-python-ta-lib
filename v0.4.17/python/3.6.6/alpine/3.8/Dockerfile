# 9fevrier/python-ta-lib:0.4.17_python3.6.6-alpine3.8_20190114
# ============================================================

FROM alpine:3.8 as python-builder
MAINTAINER contact@9fevrier.com

ENV PYTHON_VERSION 3.6.6-r0
ENV PYTHON_TA_LIB_VERSION 0.4.17

USER root
WORKDIR /tmp

RUN apk add --no-cache python3=${PYTHON_VERSION} \
    && apk add --no-cache --virtual .build-deps \
        alpine-sdk \
        python3-dev=${PYTHON_VERSION} \
        curl \
    && cd /tmp \
    && curl -L -O http://prdownloads.sourceforge.net/ta-lib/ta-lib-0.4.0-src.tar.gz \
    && tar -zxf ta-lib-0.4.0-src.tar.gz \
    && cd ta-lib/ \
    && sed -i 's/^#define TA_IS_ZERO(v).*$/#define TA_IS_ZERO(v) (((-0.000000000000000001)<v)\&\&(v<0.000000000000000001))/' src/ta_func/ta_utility.h \
    && sed -i 's/^#define TA_IS_ZERO_OR_NEG(v).*$/#define TA_IS_ZERO_OR_NEG(v) (v<0.000000000000000001)/' src/ta_func/ta_utility.h \
    && ./configure --prefix=/usr \
    && make \
    && make install \
    && pip3 install setuptools numpy \
    && pip3 install ta-lib==${PYTHON_TA_LIB_VERSION} \ 
    && apk del .build-deps \
    && rm -rf /root/.cache \
              /tmp/* \
              /var/cache/apk/* \
              /var/lib/apk/lists/*

WORKDIR /root

FROM python:3.6.6-alpine as base-python-talib
MAINTAINER contact@9fevrier.com

COPY --from=python-builder ["/usr/lib/libta*", "/usr/lib/"]
COPY --from=python-builder ["/usr/lib/python3.6/site-packages/numpy", "/usr/local/lib/python3.6/site-packages/numpy"]

FROM scratch 
MAINTAINER contact@9fevrier.com

COPY --from=base-python-talib ["/", "/"]
COPY --from=python-builder ["/usr/lib/python3.6/site-packages/talib", "/usr/local/lib/python3.6/site-packages/talib"]

CMD ["python3"]
