# Use a slim Node.js image for a lighter build
FROM node:21.2.0-slim

# Create app directory
WORKDIR /frontend

# Install dependencies only when needed
COPY package*.json ./
RUN npm install && npm cache clean --force

# Copy the rest of the app's source code
COPY . .

# Expose port for development server
EXPOSE 3000

# Start the React app
CMD ["npm", "run", "start-watch"]