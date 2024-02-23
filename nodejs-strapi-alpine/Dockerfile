# Creating base build stage
FROM node:20-alpine3.19 as build-base

# Set alpine package mirror
#RUN echo "https://dl-cdn.alpinelinux.org/alpine/v3.18/main" > /etc/apk/repositories && \
#    echo "https://dl-cdn.alpinelinux.org/alpine/v3.18/community" >> /etc/apk/repositories

#RUN apk update && apk add --no-cache build-base gcc autoconf automake zlib-dev libpng-dev vips-dev > /dev/null 2>&1

WORKDIR /opt/
COPY package.json ./

#RUN npm set registry https://registry.npmjs.org/
RUN npm install

ENV PATH /opt/node_modules/.bin:$PATH

WORKDIR /opt/app
COPY . .

RUN npm run build

# Creating final production image
FROM node:20-alpine3.19

RUN apk add --no-cache curl

ARG NODE_ENV=prod
ARG ENV_FILE=.env.production
ENV NODE_ENV=${NODE_ENV}
ENV ENV_FILE=${ENV_FILE}

WORKDIR /opt/
COPY --from=build-base /opt/node_modules ./node_modules
WORKDIR /opt/app
COPY --from=build-base /opt/app ./
COPY $ENV_FILE ./.env
ENV PATH /opt/node_modules/.bin:$PATH

VOLUME [ "/opt/app/public/uploads" ]
# VOLUME [ "/opt/app/public/src/api" ]

RUN chown -R node:node /opt/app
USER node
EXPOSE 1337
CMD ["npm", "run", "develop"]