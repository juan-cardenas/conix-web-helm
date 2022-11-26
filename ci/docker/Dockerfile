# FROM nginx:1.21.5-alpine
# COPY ci/docker/nginx.conf /etc/nginx/nginx.conf
# COPY /warContent /usr/share/nginx/html

# Stage 0, "build-stage", based on Node.js, to build and compile the frontend
FROM node:10-alpine as build-stage
LABEL version="1.0"
LABEL maintainer="klopez@ec.krugercorp.com"

WORKDIR /app

COPY package*.json /app/
COPY ci/docker/nginx.conf /app/

#ARG NPM_TOKEN
# COPY .npmrc .npmrc

RUN npm install -f --no-package-lock --verbose -d --quiet
RUN npm install -g @angular/cli
RUN rm -f .npmrc

COPY ./ /app/
RUN npm run build:stage
#-- --output-path=./dist/warContent

# Stage 1, based on Nginx, to have only the compiled app, ready for production with Nginx
FROM nginx:alpine
COPY --from=build-stage /app/warContent/ /usr/share/nginx/html

# Copy the default nginx.conf provided
COPY --from=build-stage /app/nginx.conf /etc/nginx/conf.d/default.conf

# Run nginx
#CMD ["nginx", "-g", "daemon off;"]
CMD ["/bin/sh",  "-c",  "envsubst < /usr/share/nginx/html/assets/env.template.js > /usr/share/nginx/html/assets/env.js && exec nginx -g 'daemon off;'"]
