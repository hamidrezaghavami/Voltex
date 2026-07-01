# Specifies the Node.js production multi-stage build environment.
FROM node:20-alpine

WORKDIR /app

COPY package*.json ./

# install dependencies
RUN npm install

# Copy the rest of your application code (including your schema.prisma)
COPY . .

# generate prisma client
RUN npx prisma generate

# the port experess app gonna run on it
EXPOSE 3000

# commands to start the application
CMD [ "node", "app.js" ]