FROM node:16.20.2-alpine3.18 AS builder

ARG VENDOR_NAME=novin
ENV VENDOR_NAME=${VENDOR_NAME}

RUN mkdir -p /app
WORKDIR /app

COPY package.json package-lock.json ./

#RUN npm set registry https://registry.npmjs.org/
RUN npm install --legacy-peer-deps

# Install Angular CLI globally
RUN npm install -g @angular/cli@11.2.19

COPY . .

RUN ng build --configuration=$VENDOR_NAME --outputHashing=all

FROM nginx:mainline-alpine3.18-slim AS runner

COPY --from=builder /app/dist/ngx-oipf /usr/share/nginx/html

EXPOSE 80