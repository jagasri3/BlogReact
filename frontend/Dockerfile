FROM node:20-alpine AS builder

WORKDIR /app

COPY package*.json ./
RUN npm ci

COPY . .
RUN npm run build


FROM nginx:1.27-alpine

COPY --from=builder /app/dist /usr/share/nginx/html
COPY default.conf.template /etc/nginx/templates/default.conf.template

RUN printf '%s\n' \
'pid /tmp/nginx.pid;' \
'events {}' \
'http {' \
'    include /etc/nginx/mime.types;' \
'    default_type application/octet-stream;' \
'    include /etc/nginx/conf.d/*.conf;' \
'}' > /etc/nginx/nginx.conf \
&& chown -R 101:101 /etc/nginx/conf.d /var/cache/nginx /var/log/nginx /var/run /tmp \
&& chmod -R u+rwX /etc/nginx/conf.d /var/cache/nginx /var/log/nginx /var/run /tmp

USER 101:101

EXPOSE 8080

HEALTHCHECK --interval=30s --timeout=5s --start-period=10s --retries=3 \
  CMD wget -qO- http://127.0.0.1:8080/ || exit 1

CMD ["nginx", "-g", "daemon off;"]
