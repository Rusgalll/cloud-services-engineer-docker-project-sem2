ARG NODE_VERSION=22
ARG NGINX_TAG=stable-alpine

FROM node:${NODE_VERSION}-alpine AS builder

WORKDIR /app

COPY package.json package-lock.json ./
RUN npm ci --no-audit --no-fund

COPY public/ ./public/
COPY src/ ./src/
COPY babel.config.js tsconfig.json vue.config.js ./

ARG VUE_APP_API_URL=http://localhost:8081
ENV VUE_APP_API_URL=${VUE_APP_API_URL}

RUN NODE_OPTIONS=--openssl-legacy-provider npm run build

FROM nginxinc/nginx-unprivileged:${NGINX_TAG} AS runtime

COPY nginx.conf /etc/nginx/conf.d/default.conf
COPY --from=builder /app/dist/ /usr/share/nginx/html/momo-store/

USER 101:101

EXPOSE 8080

HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
    CMD wget -q -O /dev/null http://127.0.0.1:8080/momo-store/index.html || exit 1

ENTRYPOINT ["nginx"]
CMD ["-g", "daemon off;"]
