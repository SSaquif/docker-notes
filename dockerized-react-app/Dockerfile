FROM node:14.17.5-alpine3.14
WORKDIR /app
COPY . .
RUN yarn install
# the env var is unnecessary, just for testing
ENV API_URL=http://api.myapp.com/
# read notes to see how expose works
EXPOSE 3000
RUN addgroup app && adduser -S -G app app
USER app