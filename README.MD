# My Docker Notes

## 1. Core Concepts

* **Image vs. AMI:** An Amazon Machine Image (AMI) is a blueprint for a full virtual server. A Docker **Image** is a much lighter blueprint (minimal OS + app code/libraries) used to create containers.
* **Image vs. Container:** An **Image** is the blueprint. A **Container** is a *running instance* of that image.

## 2. Basic Docker Commands

* `docker images`: Lists all images on your local machine.
* `docker pull <image>:<tag>`: Downloads an image from a registry (like Docker Hub). It always checks locally first.
* `docker ps`: Lists all **running** containers.
* `docker ps -a`: Lists **all** containers (running, stopped, etc.).

---

* `docker create <image>`: Creates a container from an image but *does not* start it.
* `docker start <container-ID>`: Starts a stopped container.
* `docker run <image>:<tag>`: The all-in-one command. It combines:
    1.  **pull** (if the image isn't local)
    2.  **create**
    3.  **start**
* `docker run -d <image>`: Runs the container in **detached** mode (in the background).
* `docker run -d -p 80:80 nginx`: Runs `nginx` and **maps** port 80 on your host machine to port 80 inside the container. (Format: `-p <host-port>:<container-port>`).
* `docker rm <container-ID>`: Removes a container.

---

* `docker exec -it <container-name> bash`: Lets you run a command (like a `bash` shell) **inside** an already running container.
* `docker inspect <container-name/ID>`: Shows detailed low-level information about a container or image.

## 3. Creating Custom Images (The Dockerfile)

A `Dockerfile` is a text file containing a set of instructions on how to build your own custom Docker image.

* **Build Command:** `docker build -t <your-image-name>:<tag> .`
    * `-t` tags the image with a name and version.
    * The `.` tells Docker to look for the `Dockerfile` in the current directory.

## 4. Dockerfile Instructions

### FROM
Specifies the **base image** to start from. It's usually the first instruction.
`FROM almalinux:9`

### RUN
Executes commands *during the image build* (e.g., installing software).
`RUN yum install nginx -y`

### CMD
Sets the default command to run *when the container starts*.
* If you have multiple `CMD` instructions, only the **last one** will be used.
* `CMD ["nginx", "-g", "daemon off;"]`

### COPY
Copies files or folders from your local machine *into* the image during the build.
`COPY ./my-website /usr/share/nginx/html`

### ADD
Similar to `COPY`, but with two extra features:
1.  It can download files directly from a URL.
2.  It can automatically unzip compressed files (like `.tar.gz`).

### ENTRYPOINT
Also sets the command to run when the container starts.
* **Difference from `CMD`:** A `CMD` instruction is easily overridden by adding a command after `docker run`. An `ENTRYPOINT` command is not.
* **Best Practice:** Use `ENTRYPOINT` for the main command and `CMD` to provide *default arguments*. You can then easily override the default arguments.

* **Example:**
    ```Dockerfile
    ENTRYPOINT ["ping"]
    CMD ["google.com"]
    ```
    * `docker run my-image` will run `ping google.com`.
    * `docker run my-image facebook.com` will run `ping facebook.com` (overriding the `CMD`).

### ARG
Defines a **build-time variable**.
* You can set it during the build: `docker build --build-arg <varname>=<value> .`
* `ARG` variables are *not* available inside the container after it's built (unlike `ENV` variables).
* **Special Case:** You can use `ARG` *before* `FROM` to pass a variable to your base image tag (e.g., `ARG TAG=9` `FROM almalinux:$TAG`).

### ONBUILD
A "trigger" instruction. It adds an instruction to the image that will *only* run when another image uses this image as its `FROM` base.

## 5. Docker Registry & Other Notes

* **Docker Home Directory:** On Linux, Docker's main directory is `/var/lib/docker`.
* **`docker push`**: Uploads your custom image to a registry (like Docker Hub).
    * `docker push docker.io/joindevops/label:v1`