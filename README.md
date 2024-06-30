# Working notes
**NOTE**:

- Folder and directory are used synonymously, however "folder" is used here when in a Windows OS context.

## "Elevated privileges" to connect
(**_Connect with local system_**)

**NOTE**: _This issue is encountered only in the Windows version of Docker Desktop_

When trying to run Docker functions such as "pull", "run" and "build", I encountered an error that began as follows:

`docker : ERROR: error during connect: in the default daemon configuration on Windows, the docker client must be run with elevated privileges to connect: ...`

This is resolved by navigating (_in the file system_) to Docker Desktop (_generally available in the Docker folder in Program Files within the chosen drive, such as C:_) and run Docker Desktop as the administrator.

To understand why this works, note that this error only occurs for commands that need to access local files, and the default configuration of the daemon (_the background control loop handling Docker_) in Windows is such that the client (_i.e. the user_) has high enough privileges. This means running the Docker Desktop application as the administrator (_the administrator having the highest privilege_) satisfies the requirements for privilege of access.

> Thanks to: https://stackoverflow.com/questions/57460393/the-docker-client-must-be-run-elevated-to-connect

## Creating a Docker image using another Docker image
Let's say we have a Docker image xyz. To make an image based on this, we need to do the following within our working or project directory...

### Dockerfile
Check for the details, but here are some keywords:

`FROM` indicates the image that serves as the basis for ours. In our case, this would look like:

`FROM xyz`

`COPY` indicates that a certain file or directory (given by a file path) within our local system (or the system that Docker is operating on) must be copied into a file or directory (also given by a file path) within the container (created from the image we are making). Its format is:

`COPY <local path> <in-container path>`

For example:

`COPY ./my-project ~/usr/local/src`
 
 (_Note the space between the two file paths_)

### Building the image
To build an image using our Dockerfile, we do...

`docker build -t <name chosen for repository> <path of Dockerfile>`
<br>OR<br>
`docker build <path of Dockerfile> -t <name chosen for repository>`

Note that the 't' in_ `-t` stands for "tag". Hence, the "repository" name is essentially a tag to group one or more images. In implementation, the repository names (i.e. tags) are associated to their respective set of image ID's in a JSON file, while the images themselves are stored together in one directory (we shall discuss later where exactly). Note that images are assigned ID's automatically.

### Creating a container from created images
To create a running instance of a container from created images, use...

`docker run <option> --name <name chosen for container> -p <port number on which to be exposed> <tag name or image ID>`

For `option`, the main options are:

- `-it`: interactive launch of container, i.e. the container will run as a foreground process which we can interact with using the CLI
- `-dit`: the container will run as a background process (non-interactive through the CLI)

Running this command will echo the container's ID. Furthermore, this command exposes the container's process through the given port. Assuming we are running Docker in our own computer ("localhost"), we can access the container's process through a browser using the URL `localhost:<port number>`, for example:

`localhost:8080`
