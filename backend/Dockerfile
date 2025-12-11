FROM node:18-alpine
WORKDIR /app

# Copy dependency manifest and install production deps (cached layer)
COPY package*.json ./
RUN npm install 

# Copy app source (don't copy .env; use --env-file at runtime)
COPY . .

ENV NODE_ENV=production
EXPOSE 5000

# Use the start script defined in package.json (should run your server)
CMD ["node", "server.js"]
