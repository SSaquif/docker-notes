FROM node:14.17.5-alpine3.14
RUN addgroup app && adduser -S -G app app

# 2 lines added to fix permission issue 
# when running yarn install
RUN mkdir /app
RUN chown -R app:app /app
# Set app as user AFTER setting permissions
USER app
WORKDIR /app
# Copy and install separtely to speed up builds
COPY --chown=app:app package*.json yarn.lock ./ 
RUN yarn install
# Give owner rights to the current user (probably unnecessary)
COPY --chown=app:app . .
# the env var is unnecessary, just for testing
ENV API_URL=http://api.myapp.com/
# read notes to see how expose works
EXPOSE 3000
CMD ["yarn", "start"]