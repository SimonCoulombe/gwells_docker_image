# GWELLS Docker Image (Rstudio, Python, geospatial libraries)

## Description 

This is a python/R/geospatial docker image that builds upon rocker/geospatial:4.1.2.   

A second repo, [gwells_geocode_and_archive_data]((https://github.com/SimonCoulombe/gwells_geocode_and_archive_data)) will contain scripts that will download gwells.csv, reverse-geocode the lat-long, create data quality variables and save the results to a repo of ever-increasing size.   The python scripts come from [Simon Norris code](https://github.com/bcgov/GWELLS_LocationQA).

A third repo, `gwells_shiny` will read these csv and display them.  

## Credits  

Lots of code for the Dockerfile comes from [this image by Thomas Schaffer](https://github.com/tschaffter/rstudio), and there is more to learn there..

## Notes   


Here is more info than required:  


Here is the lines of codes I use to build the image and upload it to dockerhub (my username is morglum):

    docker build -t monrstudio .
    docker tag monrstudio  morglum/monrstudio  
    docker push morglum/monrstudio



Here is a line of code to start the server with "yourpasswordhere".  The rstudio server will be available at 127.0.0.1:8787

    docker run   -d --rm -p 8787:8787 -e PASSWORD=yourpasswordhere  --name monrstudio -v ~/git/montest:/tmp/workingdir -w /tmp/workingdir monrstudio  


The -d  (detached)  starts the server but allows us to keep working in the terminal.   
The -v (volume) allows us to work on code that is available locally.
The -w (working dir) sets the current work directory to /tmp/workingdir


This is how to SSH into the docker after starting it :  
     
     docker exec -it monrstudio bash


Once in Rstudio,  one can use `reticulate` to set the conda environment to gwells_locationqa using this code:

    library(reticulate)  
    use_condaenv(condaenv = "gwells_locationqa", required= TRUE)  


We do not use reticulate in this project, because reticulate converts functions to R. The geocoding and qa scripts are closer to shell commands.  They  do not take a table in memory as input and do not return a table in memory as output.  Instead, they read a CSV file and write to a different CSV file.

The easiest way I found to run the python scripts from the `gwells_locationqa` environment is to create a shell script that activates the environment and calls the .py file, like so:

### geocode.sh:

    #!/bin/bash  
    #set -euxo pipefail  
    eval "$(conda shell.bash hook)"  
    conda activate gwells_locationqa  
    cp   /GWELLS_LocationQA/gwells_locationqa.py  /tmp/workingdir/gwells_locationqa.py  
    python gwells_locationqa.py geocode  not_an_api_key  



