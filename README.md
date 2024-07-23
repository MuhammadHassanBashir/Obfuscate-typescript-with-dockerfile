# Obfuscate-typescript-with-dockerfile

## Obfuscating TypeScript Files with Docker

## Introduction
This guide will help you create a Docker container to obfuscate TypeScript files. You will learn how to set up a Dockerfile, install necessary dependencies, and obfuscate your TypeScript code.

## Prerequisites
Docker installed on your local machine.
A project containing TypeScript files that you want to obfuscate.

    # Stage 1: Build Stage
    FROM node:18-alpine AS builder
    
    # Set working directory
    WORKDIR /app
    
    # Copy package.json and package-lock.json to leverage Docker cache
    COPY package*.json ./
    
    # Install all dependencies including TypeScript and obfuscator
    RUN npm install
    
    # Copy the rest of the application code
    COPY . .
    
    # Install TypeScript and the obfuscator globally
    RUN npm install -g typescript javascript-obfuscator
    
    # Compile TypeScript to JavaScript
    RUN tsc
    
    # Obfuscate the compiled JavaScript code
    RUN javascript-obfuscator ./dist --output ./obfuscated
    
    # Stage 2: Final Stage
    FROM node:18-alpine
    
    # Set working directory
    WORKDIR /app
    
    # Copy the obfuscated code from the builder stage
    COPY --from=builder /app/obfuscated ./dist
    
    # Copy package.json and package-lock.json to install production dependencies
    COPY package*.json ./
    
    # Install only production dependencies
    RUN npm install --only=production
    
    # Set the entry point for the application
    CMD ["node", "dist/index.js"]
    
    # Expose the port the app runs on
    EXPOSE 8080
    
      
      




