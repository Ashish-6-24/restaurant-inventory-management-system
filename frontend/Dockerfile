# ---------------- STAGE 1 -----------------

# base image 
FROM node:22-alpine AS builder
	
# Set the working directory inside the container 
WORKDIR /app

# Copy dependency files for cache efficiency 
COPY package.json package-lock.json ./

# Install dependencies -> stores to node_modules securely and reproducibly using exact locked versions (Clean Install ) 
RUN npm ci  	

# COPY the code from src to destination 
COPY . .

# Configure the API base URL used by Vite during build time
ARG VITE_API 
ENV VITE_API=$(VITE_API) 

# Frontend Build 
RUN npm run build 
# -------------------- STAGE 1 Ends --------------
# -------------------- STAGE 2 Starts ------------


FROM node:26-alpine AS runner

WORKDIR /app

# COPY build files from builder 
COPY --from=builder /app/dist ./dist 

# COPY all node_modules from builder ( includes vite ) 
COPY --from=builder /app/node_modules ./node_modules 

COPY --from=builder /app/vite.config.js ./vite.config.js

COPY --from=builder /app/package.json ./package.json

EXPOSE 3000

# run the application 
CMD ["./node_modules/.bin/vite", "preview", "--host", "0.0.0.0", "--port", "3000"]

