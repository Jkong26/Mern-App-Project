# server/Dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./

# Runtime stage
RUN npm ci --only=production
COPY . .
ENV NODE_ENV=production \
    PORT=5000
EXPOSE 5000
CMD ["npm", "start"]