# Stage 1: Build the React app
FROM node:18-alpine AS build

WORKDIR /app

COPY package*.json ./

# Build context मा proxy config हटाउने (यदि proxy config छ भने)
RUN npm config delete proxy || true
RUN npm config delete https-proxy || true

RUN npm install --ignore-scripts

ARG REACT_APP_SERVER_URL
ENV REACT_APP_SERVER_URL=$REACT_APP_SERVER_URL

COPY . .

RUN npm run build

# Stage 2: Serve the build with nginx
FROM nginx:stable-alpine

COPY --from=build /app/build /usr/share/nginx/html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
