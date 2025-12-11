# Step 1: Use Node.js as base image
FROM node:18-alpine

# Step 2: Set working directory
WORKDIR /app

# Step 3: Copy package.json and package-lock.json (or yarn.lock)
COPY package*.json ./

# Step 4: Install dependencies
RUN npm install

# Step 5: Copy the rest of the project
COPY . .

# Step 6: Expose the port that Vite uses
EXPOSE 5173

# Step 7: Start Vite dev server on all network interfaces
CMD ["npm", "run", "dev", "--", "--host"]
