# Building and deploying an RStudio container for dashDB

## Overview ##

This project generates a Docker image that is based on the [rocker/rstudio](https://github.com/rocker-org/rocker/tree/master/rstudio) image but that includes additional dashDB-specific components, such as IBM Data Server Driver packages and the ibmdbR and ibmdbRXt packages for R. The components provided by the rocker/rstudio image are described in the [documentation](https://github.com/rocker-org/rocker/wiki) for that image.

## Getting started ##

Follow these steps to get your own Docker container instance:

1. Install a Git client in your path.

2. Issue this command to clone the repository into a new directory:
   
  `git clone https://github.com/ibmdbanalytics/dashdb_analytic_tools.git`
 
 This creates a new directory **dashdb_analytic_tools**.
3. Download the dashDB driver package **ibm_data_server_driver_package_linuxx64_v10.5.tar.gz** into the same dashdb_analytic_tools directory from either of the following sources:
  * From the dashDB web console:
    1. Log in with your dashDB credentials.
    2. Click **Connect > Download Tools**.
  * From the [IBM support web site] (http://www-01.ibm.com/support/docview.wss?uid=swg21385217):
    1. Select the package **IBM Data Server Driver Package (DS Driver)**.
    2. Log in with your IBM ID (sign-up is free).
    3. Select the offering **IBM Data Server Driver Package (Linux AMD64 and Intel EM64T)** for the Linux platform.
  * Make sure the downloaded file is really named **ibm_data_server_driver_package_linuxx64_v10.5.tar.gz**. Rename it if this is not the case before you continue with the build process.
4. As a user with root authority, build the image:
  * If you want to build the image and run the container in your own Linux environment, issue these commands:
    - `docker build -t <image name> <path to your dashdb_analytic_tools directory>`
    - `docker run -d -p 8787:8787 <image name>`
  * If you want to use Bluemix to host your RStudio container, issue these commands:
    - `cf ic build -t registry.ng.bluemix.net/<private namespace>/<image name>  <path to your dashdb_analytic_tools directory>`
    - `cf ic run -p 8787 registry.ng.bluemix.net/<private namespace>/<image name>`
    - `cf ic ip`
  * If you already built the image locally you can push it to Bluemix like this:
    - `cf login`
    - `cf ic login`
    - `docker tag <image name> registry.ng.bluemix.net/<private namespace>/<image name>`
    - `docker push registry.ng.bluemix.net/<private namespace>/<image name>`

    The last of these commands returns the IP address of the RStudio container in Bluemix. Click [here] (https://www.ng.bluemix.net/docs/containers/container_cli_reference_cfic.html) for more information about Bluemix containers.
5. To launch the RStudio web UI:
  * On a Linux system, point your browser to `<ip_addr>:8787`, where `<ip_addr>` represents the IP address of the Linux system that hosts the RStudio container.  
  * On a Windows or Mac system, use the [Docker Toolbox] (https://www.docker.com/products/docker-toolbox) to create a Docker environment on you computer in which you can run Docker commands and containers.
6. Log in to RStudio. The default user and password are both **rstudio**. For security, change the password immediately after you log in for the first time.

## Running a sample R script ##

The docker file you build in step #4 creates a "samples" directory in your home directory, with a sample script linreg_indb.R. All of the sample data used by this script is preloaded for dashDB databases*. Follow these steps to run linreg_indb.R directly in this directory:

1.	Enter the running RStudio container:

  `docker exec -it <container-id> bash`
2. Use a text editor to modify the user, password, and host variables in the script as appropriate for the database that you are connecting to. 
3. Execute the script from the command line:

  `Rscript linreg_indb.R`



*Note: If you use a different system that does not have the preloaded sample data, you need to upload it before running the linreg_indb.R script, using the following steps:

1. Download the following three CSV files: 
   * [SHOWCASE_SYSTYPES.csv] (https://github.com/ibmdbanalytics/dashdb_analytic_tools/blob/master/SHOWCASE_SYSTYPES.csv)
   * [SHOWCASE_SYSUSAGE.csv] (https://github.com/ibmdbanalytics/dashdb_analytic_tools/blob/master/SHOWCASE_SYSUSAGE.csv)
   * [SHOWCASE_SYSTEMS.csv] (https://github.com/ibmdbanalytics/dashdb_analytic_tools/blob/master/SHOWCASE_SYSTEMS.csv)
2. Load the contents of these CSV files into the following tables in your database:
   * SAMPLES.SHOWCASE_SYSTYPES 
   * SAMPLES.SHOWCASE_SYSUSAGE 
   * SAMPLES.SHOWCASE_SYSTEMS

If you choose different table names, modify the script to reflect these different names. 

## Status ##

This is work in progress. For any request please contact torsten@de.ibm.com or mwurst@de.ibm.com.

## License ##

The docker file above and the content of the samples directory are provided under the GPL v2 or later. 

## Base Docker containers ##

| Docker Container Source on GitHub             | Docker Hub Build Status and URL
| :---------------------------------------      | :-----------------------------------------
| r-base (base package to build from)           | [good](https://registry.hub.docker.com/u/rocker/r-base/)
| rstudio (base plus RStudio Server)            | [good](https://registry.hub.docker.com/u/rocker/rstudio/)
