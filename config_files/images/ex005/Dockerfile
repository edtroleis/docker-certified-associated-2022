# docker image build -t healthcheck .
# docker container run -it --rm healthcheck sh
# docker container run -it --rm healthcheck -c 10 google.com
FROM busybox
HEALTHCHECK --interval=5s CMD ping -c 1 localhost

ENTRYPOINT [ "/bin/ping" ]
# CMD ["/bin/sh"]