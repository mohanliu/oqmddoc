## Table of Content
- [OQMD API Python Wrapper](#oqmd-api-python-wrapper)
- [QMPY Docker](#qmpy-docker)

# OQMD API Python Wrapper
A Python interface, `qmpy_rester`, is designed for OQMD API.
### Installation
Install via pip:
```bash
pip install qmpy_rester
```
Install via source:
```bash
git clone https://github.com/mohanliu/qmpy_rester.git
cd qmpy_rester
python setup.py install
```
### Usage
Make requests through OQMD API by `get_oqmd_phases()` and Optimade API by `get_optimade_structures()`. No API key required. Just launch a Python environment and start API request.

![Gif](static/rester.gif?raw=true "qmpy rester demo"){:height="100%" width="100%"}

### Attribute List

|OQMD API|Optimade API|Description|Usage|
|-------|--------|---------|--------|
|`composition`|`chemical_formula`|composition of the materials|Al2O3|
|`element_set`|`elements`|set of elements the compound must have (OR: `-`, AND: `,`)|(Fe-Mn),O|
|`ntypes`|`nelements`| number of element types|2, <3|
|`generic`|`formula_prototype`|chemical formula abstract|AB, AB2|
|`icsd`|-|whether exists in ICSD|False, True, F, T|
|`spacegroup`|`_oqmd_spacegroup`|structure space group|Fm-3m|
|`prototype`|`_oqmd_prototype`|structure prototype|Cu, CsCl|
|`natoms`|`_oqmd_natoms`|number of atoms in a unit cell|2, >5|
|`volume`|`_oqmd_volume`|volume of the unit cell|>10|
|`stability`|`_oqmd_stability`|hull distance| <-0.1|
|`delta_e`|`_oqmd_delta_e`|formation energy| <-0.5|
|`band_gap`|`_oqmd_band_gap`|band gap|0, >2|
|`fields`|`fields`|return subset of fields|'name,id,delta_e', '!sites'|
|`filter`|`filter`|customized filter|element_set=O AND ( stability<-0.1 OR delta_e<-0.5 )|
|`limit`|`limit`|number of data per page|default: 100|
|`offset`|`offset`|offset of data|default: 0|

### Example
The following is an example to make request through Optimade API.
```python
import qmpy_rester as qr
import json
import time
import os

PAGE_LIMIT = 10

if not os.path.exists('query_files'):
    os.mkdir('query_files')

def download_by_batch(batch_num):
    t1 = time.time()
    with qr.QMPYRester() as q:
        kwargs = {'limit':PAGE_LIMIT, 
                  'offset': batch_num*PAGE_LIMIT,
                  'elements': 'He',
                  'fields':'_oqmd_entry_id,_oqmd_unit_cell,_oqmd_sites',
                  }
        data = q.get_optimade_structures(verbose=False, **kwargs)
    t2 = time.time()

    if batch_num == 0:
        print('Size of query dataset is %d.'%data['meta']['data_available'])
    
    with open('query_files/query_'+str(batch_num)+'.json', 'w') as json_file:
        json.dump(data['data'], json_file, indent=2)
    
    print('Loading Batch %d time %.3f seconds'%(batch_num, t2-t1))

    if data['links']['next']:
        return True
    else:
        return False

if __name__ == "__main__":
    batch_num = 0
    while download_by_batch(batch_num):
        batch_num = batch_num + 1
```

# QMPY Docker
A Docker image, `qmpy_live`, is built in order to use [qmpy](https://github.com/wolverton-research-group/qmpy) in a Docker container.
### Step 1. Install Docker. 
Docker is avaiable on different systems including Cloud, Linux, MacOS and Windows. The complete installation document can be found [here](https://docs.docker.com/install/){:target="_blank"}. For MacOS and Windows users, a Docker Desktop is required.


### Step 2. Collect qmpy docker image. 

The pre-built image is available at [Docker Hub](https://hub.docker.com/r/oqmduser/qmpy_live){:target="_blank"}. You can simply pull the image by
```bash
docker pull oqmduser/qmpy_live
```
![Gif](static/pull_image.gif?raw=true "Pull Docker Image"){:height="100%" width="100%"}

Alternatively, you can also build your own docker image. The required Dockerfile and dependencies can be found from this [Github Repository](https://github.com/wolverton-research-group/qmpy_docker){:target="_blank"}. In order to build a new docker image, you can type
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
![Gif](static/create_container.gif?raw=true "Create Docker Container"){:height="100%" width="100%"}

The default entrypoint for this container is an `IPython` environment and you can start [Django](https://docs.djangoproject.com/en/1.8/){:target="_blank"} queries immediately.

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
