# 0️⃣ Docker installation

- https://docs.docker.com/install/

# 1️⃣ Verify docker

Command:

```
docker version
```

Output example:

```
Client:
 Cloud integration: v1.0.35+desktop.10
 Version:           25.0.3
 API version:       1.44
 Go version:        go1.21.6
 Git commit:        4debf41
 Built:             Tue Feb  6 21:13:26 2024
 OS/Arch:           darwin/arm64
 Context:           desktop-linux

Server: Docker Desktop 4.27.2 (137060)
 Engine:
  Version:          25.0.3
  API version:      1.44 (minimum version 1.24)
  Go version:       go1.21.6
  Git commit:       f417435
  Built:            Tue Feb  6 21:14:22 2024
  OS/Arch:          linux/arm64
  Experimental:     false
 containerd:
  Version:          1.6.28
  GitCommit:        ae07eda36dd25f8a1b98dfbf587313b99c0190bb
 runc:
  Version:          1.1.12
  GitCommit:        v1.1.12-0-g51d5e94
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0
```

# 2️⃣ Docker Login

Command:

```
docker login
```

You have to create Docker account via: https://docker.com

After logging in with your created account. Here is an output:

```
Authenticating with existing credentials...
Login Succeeded
```

✅ It is not required to create docker account to start learning. But it is
recommdation for long term working on container pattern.

# 3️⃣ Running your first container

Command:

```
docker run -p <destination_port:source_port> <docker_image>

- destination_port: port will run on your computer
- source_port: listening port on your container (8080 in my containers)
- docker_image: your container docker on https://docker.com
```

Example:

```
docker run -p 8081:8080 vietaws/arm:v1
```

Output example:

```
Unable to find image 'vietaws/arm:v1' locally
v2: Pulling from vietaws/arm
1e92f3a395ff: Already exists
374850c6db17: Already exists
421c44fab18b: Already exists
b9717a38adec: Already exists
a4516e430499: Already exists
83507c165f45: Already exists
72c79ca077cb: Already exists
763e0db536d0: Already exists
bc759a21b60e: Already exists
2a245d6e960c: Already exists
37af4eb023df: Already exists
21c8d0826208: Already exists
b7e67d57d35e: Already exists
84c17beff7da: Already exists
Digest: sha256:743dd61dfa2581792ffd83526b1111e5e5784eb3e575aa3b033c919183c54c7b
Status: Downloaded newer image for vietaws/arm:v1
f8a273d8a2522d412b27eccad596939ccf70449396004c75ee910c9602dbb2b7
> simple-app@1.0.0 start
> node index.js

Server is running on port 8080
```

💡 Docker will download image if it is not existed on your local computer

‼️ Your container is running on port 8080, but you exposed to port 8081. Try to
access http://localhost:8081 and see the website.

<img src="../images/img1.png" alt="Your first website" style="width: 300px;display: block;margin-left:auto;margin-right:auto;" />

💡I am using Macbook M1 chip, so my container image is `vietaws/arm:v1`, but you
may use `vietaws/eks:v1` container image. In next chapter, we will learn Amazon
EKS and `vietaws/eks:v1` will be a main choice.

Other docker run command options:

- `docker pull <container_image>` - download image only
- `docker run -d -p 8081:8080 vietaws/eks:v1` - `-d` run in detach mode
- `docker run -d --name miracle -p 8082:8080 vietaws/arm:v2`
- `docker run -d -e bgColor=green,color=pink -p 8085:8080 vietaws/arm:v1` - run
  with env variables

```
CONTAINER ID   IMAGE            COMMAND                  CREATED          STATUS          PORTS                    NAMES
ef69ef3cc9b8   vietaws/arm:v2   "docker-entrypoint.s…"   3 seconds ago    Up 2 seconds    0.0.0.0:8082->8080/tcp   miracle
be6801cc6364   vietaws/arm:v1   "docker-entrypoint.s…"   42 minutes ago   Up 42 minutes   0.0.0.0:8081->8080/tcp   elegant_hellman
```

# 4️⃣ List all running container

Command:

```
docker ps
```

Output example:

```
CONTAINER ID   IMAGE            COMMAND                  CREATED          STATUS          PORTS                    NAMES
be6801cc6364   vietaws/arm:v1   "docker-entrypoint.s…"   34 minutes ago   Up 34 minutes   0.0.0.0:8081->8080/tcp   elegant_hellman
```

Legend:

- be6801cc6364: container id
- elegant_hellman: container name

Options:

- `docker stats` - Display a live stream of container(s) resource usage
  statistics

```
CONTAINER ID   NAME              CPU %     MEM USAGE / LIMIT     MEM %     NET I/O           BLOCK I/O        PIDS
ef69ef3cc9b8   miracle           0.00%     50.83MiB / 7.755GiB   0.64%     4.75kB / 2.48kB   20.5kB / 4.1kB   23
be6801cc6364   elegant_hellman   0.00%     53.32MiB / 7.755GiB   0.67%     2.92kB / 1.23kB   0B / 4.1kB       23
```

- `docker top <container-id or name>` - Display the running processes of a
  container

# 5️⃣ Access container's log

Command:

```
docker logs <container-name>
```

Example: `docker logs miracle`

- `miracle` is a container name

Output:

```
> simple-app@1.0.0 start
> node index.js

Server is running on port 8080
os hostname:  ef69ef3cc9b8
```

☑️ You should access website `http://localhost:8082` to get the os hostname log.

Options:

- `docker logs <container_name> -f` - live logs viewing (`f` is for `follow`)

# 6️⃣ Interact container's shell

Command:

```
docker exec -it <container_name> sh
```

Example: `docker exec -it miracle sh` Output example:

```
docker exec -it miracle sh
# ls
Dockerfile  cache  data  index.js  node_modules  package-lock.json  package.json
#
```

Options:

- `docker exec -it <container_name> env` - Output env variables

```
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=ef69ef3cc9b8
TERM=xterm
NODE_VERSION=18.20.2
YARN_VERSION=1.22.19
HOME=/root
```

# 7️⃣ Terminate container

Command:

```
docker kill <container_name/id>
```

Others:

- docker stop <container_name>
- docker start <container_name>

Example: `docker kill miracle`

# 8️⃣ Docker images command

```
docker images
docker rmi <image_id>
docker rmi $(docker images) -f
```

# 9️⃣ Docker build command

## 🍄 Docker Build

```
docker build -t vietaws/eks:v10 .
```

💡 Explain: build docker image and tagging.

- `vietaws` - docker hub alias
- `eks` - docker hub repository
- `v10` - tag.
- `.` - current directory has `Dockerfile`

Dockerfile example:

```
FROM public.ecr.aws/docker/library/node:lts-alpine3.19

# FROM --platform=linux/arm64 node:18 #for apple chip m1

#configure working directory

WORKDIR /app

RUN mkdir -p /app/data

RUN mkdir -p /app/cache

COPY package*.json /app/

RUN npm install

#bundle app source code

COPY . ./

EXPOSE 8080
CMD ["npm", "start"]
```

Options:

- `docker tag <existed_container_tag> <new_container_tag>`

Example: `docker tag vietaws/arm:v1 vietaws/arm:v8` Output:

```
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
vietaws/arm   v5        c1e1e17346b0   11 hours ago   1.1GB
vietaws/arm   v2        b988925ec8b4   11 hours ago   1.1GB
vietaws/arm   v1        a14307b91ae9   11 hours ago   1.1GB
vietaws/arm   v8        a14307b91ae9   11 hours ago   1.1GB
```

## 🍄 Docker Push

Command:

```
docker push <alias/repo:tag>
```

Example: `docker push vietaws/arm:v8`

‼️ You required to use `docker login` first before pushing container to Docker
Hub

✅ Congratulations! Let's move to next step 🚀🚀🚀
