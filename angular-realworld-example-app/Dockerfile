#FROM node:18-alpine AS builder

#WORKDIR /app

#COPY package*.json ./
#RUN npm install --legacy-peer-deps

#COPY . .

#RUN npm run build -- --configuration production --output-path=./dist

#FROM nginx:alpine

#COPY --from=builder /app/dist /usr/share/nginx/html

#EXPOSE 80

#CMD ["nginx", "-g", "daemon off;"]

FROM node:18-alpine AS builder

WORKDIR /app

COPY package*.json ./
COPY node_modules ./node_modules
COPY . .

RUN ./node_modules/.bin/ng build --configuration production --base-href /

FROM nginx:alpine

COPY --from=builder /app/dist /usr/share/nginx/html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]