ARG NODE_VERSION=22
ARG NGINX_TAG=stable-alpine

FROM node:${NODE_VERSION}-alpine AS dependencies

WORKDIR /app

COPY package.json package-lock.json ./
RUN npm ci --no-audit --no-fund

COPY public/ ./public/
COPY src/ ./src/
COPY babel.config.js tsconfig.json vue.config.js .browserslistrc ./

FROM dependencies AS dev

ENV NODE_ENV=development \
    NODE_OPTIONS=--openssl-legacy-provider \
    npm_config_cache=/tmp/npm

USER 1000:1000
EXPOSE 8080

HEALTHCHECK --interval=10s --timeout=5s --start-period=120s --retries=6 \
    CMD wget -q -O /dev/null http://127.0.0.1:8080/ || exit 1

CMD ["npm", "run", "serve", "--", "--host", "0.0.0.0", "--port", "8080"]

FROM dependencies AS builder

ARG VUE_APP_API_URL=http://localhost:8081
ENV VUE_APP_API_URL=${VUE_APP_API_URL} \
    PUBLIC_PATH=/momo-store/

RUN NODE_OPTIONS=--openssl-legacy-provider npm run build

FROM nginxinc/nginx-unprivileged:${NGINX_TAG} AS runtime

USER 0
RUN apk upgrade --no-cache libuuid \
    && mkdir -p /var/cache/nginx/catalog \
    && chown 101:101 /var/cache/nginx/catalog \
    && chmod 0700 /var/cache/nginx/catalog

COPY nginx.conf /etc/nginx/conf.d/default.conf
COPY --from=builder /app/dist/ /usr/share/nginx/html/momo-store/

USER 101:101

EXPOSE 8080

HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
    CMD wget -q -O /dev/null http://127.0.0.1:8080/momo-store/index.html || exit 1

ENTRYPOINT ["nginx"]
CMD ["-g", "daemon off;"]
