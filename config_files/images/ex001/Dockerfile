FROM debian

LABEL app=debian
LABEL maintainer="troleis"

ENV SUBSCRIPTION_NAME="dev"

RUN apt-get update && apt-get install -y stress && apt-get clean

CMD stress --cpu 2 --vm 1 --vm-bytes 64M