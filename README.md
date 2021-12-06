this is a python / R / geospatial docker image that has the required environment to run the scripts at  https://github.com/bcgov/GWELLS_LocationQA

The gwells_qa_data github will run daly and save the results


This is my first non-hello-world docker, so here is more info than required:  

Here is a line of code to start the server with "yourpasswordhere".  The rstudio server will be available at 127.0.0.1:8787.  
The -v (volume) allows us to work on code that is available locally.
The -w (working dir) sets the current work directory to /tmp/workingdir


docker run   --rm -p 8787:8787 -e PASSWORD=yourpasswordhere  --name monrstudio -v ~/git/montest:/tmp/workingdir -w /tmp/workingdir monrstudio 

This is how to SSH into the docker after starting it :
docker exec -it monrstudio bash

Once in Rstudio,  one can use `reticulate` to set the conda environment to gwells_locationqa using this code:

library(reticulate)
use_condaenv(condaenv = "gwells_locationqa", required= TRUE)


if one needs to run a python script from the conda environment, the easiest way is to create a script that activates the environment and calls the .py file, like so:

----- geocode.sh -------

#!/bin/bash
#set -euxo pipefail

eval "$(conda shell.bash hook)"

conda activate gwells_locationqa

cp   /GWELLS_LocationQA/gwells_locationqa.py  /tmp/workingdir/gwells_locationqa.py
python gwells_locationqa.py geocode  not_an_api_key


------end geocode.sh ---


Lots of code for the Dockerfile comes from this image, and there is more to learn there..
https://github.com/tschaffter/rstudio
