FROM python:3-alpine as builder

# Alternatively use ADD https:// (which will not be cached by Docker builder)
RUN apk --no-cache add curl \ 
    && echo "Pulling watchdog binary from Github." \
    && curl -sSL https://github.com/openfaas/faas/releases/download/0.6.9/fwatchdog > /usr/bin/fwatchdog \
    && chmod +x /usr/bin/fwatchdog \
    && apk del curl --no-cache

RUN apk add --no-cache build-base

COPY index.py .
COPY requirements.txt .

WORKDIR /build

RUN apk add --no-cache build-base && \
    pip install --no-cache-dir -t . -r requirements.txt && \
    apk del --no-cache build-base

FROM python:3-alpine

# Add non root user
RUN addgroup -S app && adduser -S -g app app
RUN mkdir -p /home/app
RUN chown app /home/app

WORKDIR /home/app

COPY --from=builder /build .
COPY --from=builder /usr/bin/fwatchdog .

USER app

ENV fprocess="python3 index.py"

HEALTHCHECK --interval=1s CMD [ -e /tmp/.lock ] || exit 1

CMD ["fwatchdog"]
