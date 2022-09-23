FROM nginx:mainline-alpine

RUN rm /etc/nginx/conf.d/*

ADD helloworld.conf /etc/nginx/conf.d/
ADD index.html /usr/share/nginx/html/

HEALTHCHECK --interval=30s --timeout=3s \
  CMD curl -fs http://localhost || exit 1

EXPOSE 80