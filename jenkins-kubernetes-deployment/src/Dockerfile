# Use node:19-alpine3.16 as the parent image for building the Docker image
FROM node:19-alpine3.16

# Install dos2unix to normalize line endings
RUN apk add --no-cache dos2unix

# Create a working directory for Docker. The Docker image will be created in this directory.
WORKDIR /react-app

# Normalize line endings of files before copying them
COPY package.json .
COPY package-lock.json .

# Convert line endings to LF
RUN dos2unix package.json package-lock.json

# Install dependencies
RUN npm install

# Copy the remaining React.js application files from the local folder to the Docker working directory
COPY . .

# Convert line endings of the remaining files to LF
RUN find . -type f -exec dos2unix {} \;

# Expose port 3000 for the React.js application
EXPOSE 3000

# Command to start the React.js application container
CMD ["npm", "start"]