# ---------- Dependencies stage ----------
FROM node:20-alpine AS deps

WORKDIR /app

COPY package.json ./

RUN npm install --omit=dev


# ---------- Production stage ----------
FROM node:20-alpine

WORKDIR /app

COPY --from=deps /app/node_modules ./node_modules
COPY package.json ./
COPY src ./src

# Create and use a non-root user
RUN addgroup -S appgroup \
    && adduser -S appuser -G appgroup \
    && chown -R appuser:appgroup /app

USER appuser

ENV NODE_ENV=production
ENV PORT=5000

EXPOSE 5000

HEALTHCHECK --interval=30s --timeout=5s --start-period=10s --retries=3 \
    CMD wget --no-verbose --tries=1 --spider http://127.0.0.1:5000/healthz || exit 1

CMD ["node", "src/server.js"]
