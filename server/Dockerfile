FROM node:lts-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install && npm update tar --depth 10

COPY . .
EXPOSE 9000
CMD ["npm", "start"]
