FROM node:18.12.0-alpine AS development

WORKDIR /usr/src/app

COPY package*.json ./

RUN npm isntall --only=development

COPY . .

RUN npm run build

FROM node:18.12.0-alpine as production

# Set the timezone
RUN apk add --no-cache tzdata

ARG NODE_ENV=prod
ARG ENV_FILE=.env.prod
ENV NODE_ENV=${NODE_ENV}
ENV ENV_FILE=${ENV_FILE}
ENV TZ=Asia/Tehran

WORKDIR /usr/src/app

COPY package*.json ./

RUN npm install --only=production

COPY . .

COPY $ENV_FILE ./.env

COPY --from=development /usr/src/app/dist ./dist

# Add Entrypoint script to run migrations and start the app
CMD ["npm", "run", "start:prod"]