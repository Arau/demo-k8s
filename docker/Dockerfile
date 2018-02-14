FROM node:carbon

# Create app directory
WORKDIR /usr/src/app

# Install app dependencies
COPY package*.json ./

RUN npm install

# Upload app source
COPY . .

EXPOSE 8080
CMD [ "npm", "start" ]

