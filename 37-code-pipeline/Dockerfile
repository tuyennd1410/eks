# for intel, amd
FROM --platform=linux/amd64 public.ecr.aws/docker/library/node:lts-alpine3.19

# for macos
# FROM --platform=linux/arm64 public.ecr.aws/docker/library/node:lts-alpine3.19  



WORKDIR /app 


COPY package*.json /app/

RUN npm install

#bundle app source code

COPY . ./

EXPOSE 8080 
CMD ["npm", "start"]