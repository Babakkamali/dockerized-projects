FROM node:20.3-alpine3.18 AS builder

WORKDIR /home/node

ENV NODE_ENV build

RUN npm install -g pnpm

COPY package*.json pnpm-lock.yaml* ./

RUN pnpm install

COPY . .

RUN pnpm run build

FROM node:20.3-alpine3.18 as runner

# Set the timezone
RUN apk --no-cache add tzdata curl

ARG NODE_ENV=prod
ARG ENV_FILE=.env.prod
ENV NODE_ENV=${NODE_ENV}
ENV ENV_FILE=${ENV_FILE}

ENV TZ=Asia/Tehran

WORKDIR /home/node

RUN npm install -g pnpm

COPY package*.json pnpm-lock.yaml* ./
COPY .env* ./
COPY $ENV_FILE ./.env.prod


COPY --from=builder /home/node/node_modules/ ./node_modules/
COPY --from=builder /home/node/dist/ ./dist/
COPY --from=builder /home/node/src/ ./src/
COPY --from=builder /home/node/tsconfig.* ./
COPY --from=builder /home/node/nest-cli.json ./

# Add Entrypoint script to run migrations and start the app
CMD ["pnpm", "run", "start:prod"]