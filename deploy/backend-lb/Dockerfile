ARG NGINX_TAG=stable-alpine

FROM nginxinc/nginx-unprivileged:${NGINX_TAG}

USER 0
RUN apk upgrade --no-cache

COPY nginx.conf /etc/nginx/conf.d/default.conf

USER 101:101
EXPOSE 8080

HEALTHCHECK --interval=10s --timeout=3s --start-period=10s --retries=5 \
    CMD wget -q -O /dev/null http://127.0.0.1:8080/health || exit 1

ENTRYPOINT ["nginx"]
CMD ["-g", "daemon off;"]
