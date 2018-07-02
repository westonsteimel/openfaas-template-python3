FROM python:3-alpine as builder

WORKDIR /build

COPY index.py .
COPY requirements.txt .
COPY function function/

RUN touch ./function/__init__.py

COPY function/requirements.txt	.

RUN apk --no-cache add --virtual .build-dependencies \
    curl \
    build-base \
    && echo "Pulling watchdog binary from Github." \
    && curl -sSL https://github.com/openfaas/faas/releases/download/0.8.0/fwatchdog > /usr/bin/fwatchdog \
    && chmod +x /usr/bin/fwatchdog \
    && pip install --no-cache-dir -t ./function/ -r requirements.txt \
    && pip install --no-cache-dir -t ./function/ -r ./function/requirements.txt \
    && apk --no-cache del --purge .build-dependencies \
    && rm -rf /var/cache/*

FROM python:3-alpine

# Add non root user
RUN addgroup -S app && adduser -S -g app app
RUN mkdir -p /home/app
RUN chown app /home/app

USER app

WORKDIR /home/app

COPY --from=builder /build .
COPY --from=builder /usr/bin/fwatchdog /usr/bin/fwatchdog

ENV fprocess="python3 index.py"

HEALTHCHECK --interval=1s CMD [ -e /tmp/.lock ] || exit 1

CMD ["fwatchdog"]
