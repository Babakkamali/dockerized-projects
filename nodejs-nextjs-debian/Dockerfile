FROM node:18.17.0-bookworm-slim AS deps

WORKDIR /app
COPY package.json package-lock.json ./
#RUN npm set registry https://registry.npmjs.org/
RUN npm install

FROM node:18.17.0-bookworm-slim AS builder
ARG ENV_FILE=.env.prod
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
COPY $ENV_FILE ./.env


ENV NEXT_TELEMETRY_DISABLED 1

RUN npm run build

FROM node:18.17.0-bookworm-slim AS runner
WORKDIR /app

ENV NODE_ENV production
ENV NEXT_TELEMETRY_DISABLED 1

# RUN addgroup --system --gid 1001 nodejs
# RUN adduser --system --uid 1001 nextjs

# COPY --from=builder --chown=nextjs:nodejs /app/.next ./.next
COPY --from=builder /app/.next ./.next
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/public ./public
COPY --from=builder /app/.env ./.env
COPY --from=builder /app/package.json ./package.json
COPY --from=builder /app/next.config.js ./

# RUN chown -R nextjs:nodejs /app

# USER nextjs

EXPOSE 3000

ENV PORT 3000

CMD ["npm", "run", "start"]
