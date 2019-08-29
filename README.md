## API Python Wrapper
## Docker Image of live qmdb
### Step 1. Install Docker. 
Docker is avaiable on different systems including Cloud, Linux, MacOS and Windows. The complete installation document can be found [here](https://docs.docker.com/install/). For MacOS and Windows users, a Docker Desktop is required.


### Step 2. Collect qmpy docker image. 

The pre-built image is available at [Docker Hub](https://hub.docker.com/r/oqmduser/qmpy_live). You can simply pull the image by
```bash
docker pull oqmduser/qmpy_live
```
![Gif](/static/pull_image.gif?raw=true "Pull Docker Image"){:height="100%" width="100%"}

Alternatively, you can also build your own docker image. The required Dockerfile and dependencies can be found from this [Github Repository](https://github.com/wolverton-research-group/qmpy_docker). In order to build a new docker image, you can type
```bash
git clone https://github.com/wolverton-research-group/qmpy_docker.git
cd qmpy_docker
docker build -t oqmduser/qmpy_live .
```

### Step 3. Start a qmpy environment.

Once we have the qmpy docker image, we can start a docker container. The required database environment variables will be saved in the `env.list` file. 

To start a container, find out the path to your `env.list` file and then type
```bash 
docker run -it --rm --env-file <path_to_envlist> oqmduser/qmpy_live
```
![Gif](/static/create_container.gif?raw=true "Create Docker Container"){:height="100%" width="100%"}

The default entrypoint for this container is an `IPython` environment and you can start [Django](https://docs.djangoproject.com/en/1.8/) queries immediately.

To overwrite the default entrypoint, you can do 
```
docker run -it --rm --env-file <path_to_envlist> --entrypoint "/bin/bash" oqmduser/qmpy_live
```

Also, if you want to transfer files from your local computer to the qmpy container or vice versa, you can perform the file synchronization implemented in docker. 

You create a local path to store files you want to synchronize with docker container and then type:
```bash
docker run -it --rm --env-file <path_to_envlist> -v <some_local_path>:/workspace oqmduser/qmpy_live
``` 

_This docker image is designed for internal use only. Contact administrators to get the required environment variables to start a docker container._

## Contact
Mohan Liu: <mohan@u.northwestern.edu>
