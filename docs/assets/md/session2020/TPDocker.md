# TP Docker

## Table of contents

- A. General information
- B. Introduction to Docker
    + Client / Server
    + Skeleton of a Docker command
    + Download a pre-defined image available on the DockerHub
    + Use a pulled image
    + Bind a local folder into a container
- C. Creating and interacting with a Docker container
    + Pulling a Docker image
    + Interacting with a Docker container
- D.	Automating the process
- Bonus exercises
    + Annex: create a mini SAM/BAM file.	
    + Annex: load a saved image

**Version:** 1.1 (30/08/2020)
**Contacts:**
+ Thomas Denecker   (thomas.denecker@u-psud.fr)
+ Celine Hernandez (celine.hernandez@i2bc.paris-saclay.fr)  + Claire Toffano-Nioche (claire.toffano-nioche@universite-paris-saclay.fr)

## A.	General information

**Websites**
Docker : https://www.docker.com/ 
DockerHub : https://hub.docker.com/ 
Genomic Paris Centre hub : https://hub.docker.com/u/genomicpariscentre/ 

**Resources**
+ Docker
     - Understanding Docker :
 https://docs.docker.com/get-started/overview/ 
     - Get started with Docker :
 https://docs.docker.com/get-started/ 
     - More on Dockerfiles :
 https://docs.docker.com/develop/develop-images/dockerfile_best-practices/ 
 
**Samtools**
Official website : http://www.htslib.org/ 
Documentation (v0.1.19) : http://www.htslib.org/doc/0.1.19/samtools.html 
Documentation (v1.7) : http://www.htslib.org/doc/1.7/samtools.html 

## B.	Introduction to Docker

### 1.	Client / Server


Docker is a command line-based software allowing to manipulate images and create application containers.

As presented in the course, Docker consists of two elements:
-	a server, to manage images and containers
-	a client, used to interact with the server

Typing this command will give the Client and Server versions available on your computer.

```
docker version
```


### 2.	Skeleton of a Docker command


The general usage of the Docker command line is as follows:

```
#Usage: 
docker [OPTIONS] COMMAND [arg...]
docker [ --help | -v | --version ]
```

If you consider the first line, a Docker command is made of four parts. 
•	The ‘docker’ keyword
•	[OPTIONS] : optional parameters to run the client
•	COMMAND : the name of a command to be run by the client
•	[arg…] : parameters for the chosen command

Usage, options and a full list of available commands can be accessed by typing :
```
docker
```

Among the most interesting for this tutorial are the commands pull, run, rm/rmi and build. All commands have a dedicated documentation page.

Try to access the basic usage of one Docker command : 
```
docker pull
```

<ins>Q: How many arguments are absolutely required by the command ‘docker pull’ ?</ins>

Try to access the extended help page of a Docker command :
```
docker pull -help
```
 
This lists also the options specific to the command.



### 3.	Download a pre-defined image available on the DockerHub


**Goal: On the Docker hub, how do we find and download the ‘Samtools’ image made by the GenomicParisCentre user ?**

In a web browser, navigate to the DockerHub website : [docker website](https://hub.docker.com/) https://hub.docker.com/
	 

Search for user : GenomicParisCentre
As shown during the presentation, this user made many images available to the scientific community. We will have a look at the available images.

<ins>Q: How many times was the STAR image downloaded ?</ins>
NB: ‘pull’ is a synonym for ‘download’.

Among the list of images, find Samtools. Click on DETAILS to access the page description for this image. 
Note that we could also have searched for all samtools images available on the DockerHub and only then, tried to find the one made by user genomicpariscentre.

<ins>Q: On the Samtools image description page, can you find the ‘pull’ command of Samtools?</ins>

Now, copy the pull command and execute it inside a terminal. 
By default, Docker will pull the most recent and stable version of the image (tag: latest).
```
docker pull genomicpariscentre/samtools
```

<ins>Q: How many times do you see ‘Pull complete’ displayed ? Why ?</ins>

You can find more information on layers [here](https://docs.docker.com/storage/storagedriver/#images-and-layers ): 
https://docs.docker.com/storage/storagedriver/#images-and-layers 

Now, to be sure that the image was correctly pulled, let’s see the list of all available downloaded images inside our workspace.
```
docker images
```

<ins>Q: What is the size of the genomicpariscentre/samtools image ?</ins>

<ins>Optional exercise : inspect the image detailed description.</ins>

Run this command:
```
docker inspect genomicpariscentre/samtools
```
What is displayed on the terminal is a description of the image in JSON format. 


### 4.	Use a pulled image


**Goal: Now that we have a locally installed image containing Samtools, how do we use it?**

Among the Docker commands, we will now use the ‘run’ command.

<ins>Q: What are the options and parameters of the ‘run’ command ?</ins>

As displayed on the terminal, the description of the command is ‘Run a command in a new container’. 

<ins>Q: What is the difference between an image and a container ?</ins>

See the [Docker documentation](https://docs.docker.com/get-started/overview/#docker-objects) :
https://docs.docker.com/get-started/overview/#docker-objects 

Samtools has been installed as a global program in the image we pulled. Consequently, Samtools is directly accessible when interacting with the image.

Now, to run the samtools application, execute the following command:
```
docker run genomicpariscentre/samtools samtools
#Program: samtools (Tools for alignments in the SAM format)
#Version: 1.4.1 (using htslib 1.5)
```

<ins>Q: What was displayed on the terminal ? Is it a message from Docker or from Samtools ?</ins>

Congratulations! 
You just successfully downloaded and used your first Docker image !


 

### 5.	Bind a local folder into a container

**Goal: map a local folder and run samtools on a provided BAM file.**

Running samtools without parameters was interesting as a demonstration of Docker’s powerful functionalities. But if we want to really run samtools, we also need to provide parameters and, most importantly, input files. Because of encapsulation, this functionality is not available by default. We need to explicitly map a local folder to a corresponding one inside the container (usually /home/), binding the two separated worlds together.

To achieve this mapping, we use the –v option of the run command. Before trying it, you can check the command options once again.
```
docker run -help
```

To bind our current folder to the /home/ folder located inside a container, we first need the absolute path of the current folder, obtained through the unix pwd command.
```
pwd
```
This path will be used in further commands through ${PWD}.

To perform the folder mapping between the current folder and /home inside Docker, the syntax is simple. The –v option takes one parameter made out of the two paths we want to map concatenated together, with the character : as separator. 
Instead of running the samtools command, we will now just list the content of the /home/ folder inside the container.
First without the mapping:
```
docker run genomicpariscentre/samtools ls /home/
```
Then with the mapping:
```
docker run -v ${PWD}:/home/ genomicpariscentre/samtools ls /home/
```

<ins>Q: Is the file list the same as what is in your current folder?</ins>

Finally, we can run samtools on a SAM/BAM file, here on a file named “file.bam” located in the current folder. Change the name of the BAM file to one of the provided files or create your own (see last page of this document).
```
docker run –v ${PWD}:/home/ genomicpariscentre/samtools samtools view /home/file.bam | less
```
 

## C. Using containers

Our goal is to be able to create a container from a publicly available image, and to install and run **samtools** inside this container.

### 1. Pulling a Docker image

Pulling an image means downloading a publicly available image (browse https://hub.docker.com/ in order to look for all uploaded images). For this practical session, we are only interested in the official ubuntu release, named ```ubuntu```. 

```bash
docker search ubuntu
```

A table with four columns is now displayed. In the first column are the names of the available images. We will use these names to specify which image we are interested in. In the second column are descriptions for each image. The third column is the number of stars given by users to this particular image. The fourth and fifth columns indicate if a given image is an official release, and if it has been automatically created using a Dockerfile.

Now that we have found the image we want, we need to download it.

*Note that the website (https://hub.docker.com/_/ubuntu/) gives you more details concerning the available versions of Ubuntu downloadable as images, as well as which one is the default version (tagged ‘latest’). For this exercise, we specify the version we want (18.04).*
 
 ```bash
docker pull ubuntu:18.04
```

**Q: What is the size of the downloaded image? Compare that to the 2GB required for a full Ubuntu 18.04 Desktop installation.**
https://help.ubuntu.com/18.04/installation-guide/amd64/apds03.html 

Browsing the pulled images:

 ```bash
docker images
```

#### RECAP: basic commands for Docker images

+ Requesting the available versions of an image in the Docker registry:

```bash
docker search <keyword>
```

+ Pulling an image:

 ```bash
docker pull <name>:<version>
```

+ List all images:

 ```bash
docker images
```

+ Removing an image from locally pulled ones:

 ```bash
docker rmi <name>:<version>
```

### 2.	Running a Docker container

Let’s start a terminal (bash) session based on the image we just downloaded. 

```bash
docker run -ti ubuntu:18.04 --name test bash
```

*Check out https://docs.docker.com/engine/reference/commandline/run/ for the meaning of both options -t -i.*

Normally, the first part of the terminal line has changed to root@ followed by a series of letters and numbers. This series is the unique identifier for your container named *test*, which has been randomly generated at the creation of the container. You are now connected to lightweight version of Ubuntu 18.04!

Next step is to run the command that will install **samtools** using **apt** (don't copy "$" as it shows that you are inside the shell in the Docker container you have just created).

```bash
$ apt update
$ apt -y install samtools=1.7-1
$ apt clean 
```

Let's run **samtools** and exit the container:

```bash
$ samtools 
$ exit
```

Note that as we exited the container, it has now stopped. If we type again the first command of this exercise, we will get a brand new container, without **samtools** installed. We can check the status of the latest container created:

```bash
docker ps -l
```

This will display a table with seven columns. In the column titled STATUS, you can check that we “Exited” the latest container we created a certain time ago.

If we want to detach the container (that is, keeping it running in the background), do Ctrl-P Ctrl-Q while inside the container shell. To go back inside a container and open a shell:

```bash
docker exec -ti test bash
```

If you want to save the environment you have installed inside a given container, you can perfom a "commit" of the container *test* as an image named *test/image:1.0*:

```bash
docker commit test test/image:1.0
```

*Note: huge data files **shouldn't** be loaded inside a container, otherwise the image might become too heavy. Only save useful packages inside an image!*
 

#### RECAP: basic commands for Docker containers

+ Starting a container on a given image running a single command:

```bash
docker run -ti <image>:<version> <command>
```

+ List all containers and their status

```bash
docker ps -l
```

+ Open a shell inside a running container

```bash
docker exec -ti <container name> bash
```

+ Save a container as an image

```bash
docker commit <container> <image>:<tag>
```

### Session code only:

All code run inside this practical session:

```bash
docker pull ubuntu:18.04
docker run -ti ubuntu:18.04 bash
$ apt update
$ apt -y install samtools=1.7-1
$ apt clean 
$ samtools 
$ exit
```

## D. Creating images from configuration file

Now we want to automate the process of installing packages inside a generic image and saving the resulting environment/container as a custom image. Let's do it on our Ubuntu image with the installation of **samtools**.

All the steps that we manually did in the preceding exercise can be collected into a “recipe”/text file called a Dockerfile. A Dockerfile can then be used to build again the image on-demand.

The Dockerfile here should read:

```
FROM ubuntu:18.04
RUN apt update ; apt -y install samtools=1.7-1 ; apt clean
```

+ **FROM** specifies the base image that should be used as a starting point to create our new image.
+ **RUN** specifies any command that should be run for our purpose. In our case, we just needed one command line.

*You can find more information on what can be included in a Dockerfile at this URL: https://docs.docker.com/develop/develop-images/dockerfile_best-practices/. Note that, for instance, it is usually a good practice to give also the name of a MAINTAINER, responsible for the writing and maintenance of the Dockerfile.*

Now that we saw what is a Dockerfile, we will use it to build an image containing **Samtools version 1.3.1**, and most importantly, save it as an image.

```bash
docker build -t student/samtools:1.7 .
```

Docker will now execute one by one each instruction written inside the Dockerfile. We can now check that we have locally a new image called student/samtools with the tag 1.7:

```bash
docker images
```

And we can run a simple command to check if Samtools is working and what is the installed version in the image:

```bash
docker run student/samtools:1.7 samtools
```


### RECAP: basic commands for Dockerfile

+ Structure of Dockerfile

```
FROM <baseimage>
RUN <command1> ; <command2> ; <...>
```

+ Building a new image from Dockerfile

```bash
docker build -t <image>:<tag> .
```

## Bonus exercises

### Annex : create a mini SAM/BAM file.

Open a text editor (simple, like Notepad or BBEdit).
In the blank page, paste the text below:

```
@HD	VN:1.0	SO:coordinate
@SQ	SN:ERCC-00002	LN:1037
@SQ	SN:chr12	LN:133275309
@PG	ID:TopHat	VN:2.1.1	CL:/opt/tophat-2.1.1/bin/tophat
NB552053:112:H7LFHBGXG:4:13404:24475:13506	385	ERCC-00002	243	1	35M	chr12	124911843	0	AAGCTTGTTCCGTTAGCTGGCATAAGATTCCATGC	AAAAAEEEEEEEEEEEEEEEEEEEEEEEEEEEEEE	AS:i:0	XN:i:0	XM:i:0	XO:i:0	XG:i:0	NM:i:0	MD:Z:35	YT:Z:UU	NH:i:3	CC:Z:=	CP:i:243	HI:i:0
NB552053:112:H7LFHBGXG:4:13404:24475:13506	129	ERCC-00002	243	1	35M	chr12	124913211	0	AAGCTTGTTCCGTTAGCTGGCATAAGATTCCATGC	AAAAAEEEEEEEEEEEEEEEEEEEEEEEEEEEEEE	AS:i:0	XN:i:0	XM:i:0	XO:i:0	XG:i:0	NM:i:0	MD:Z:35	YT:Z:UU	NH:i:3	CC:Z:=	CP:i:243	HI:i:1
```


Save the file and name it **file.sam**. Consecutively, you can use a Docker image of Samtools to convert it into a BAM file.

### Annex : load a saved image

If you have problems with your Internet connection during the practical session, you can ask for a downloaded version of the Samtools image used here.

In order to load the image, use this command.
```
$ docker load < savedsamtoolsimage.tar
#0f5ff0cf6a1c: Loading layer [==================================================>]    126MB/126MB
#f1c896f31e49: Loading layer [==================================================>]  15.87kB/15.87kB
#51db18d04d72: Loading layer [==================================================>]  14.85kB/14.85kB
#f51f76255b02: Loading layer [==================================================>]  5.632kB/5.632kB
#174a611570d4: Loading layer [==================================================>]  3.072kB/3.072kB
#bfb4c941db16: Loading layer [==================================================>]  58.08MB/58.08MB
```
 
You can then check that it has been correctly loaded using ‘docker images’.
```
$ docker images
#REPOSITORY                    TAG         IMAGE ID        CREATED         SIZE
#ubuntu                        18.04       6526a1858e5d    10 days ago     64.2MB
#genomicpariscentre/samtools   latest      2ec1cf58981b    2 years ago     179MB

```

 
For your information, this is how you save an image to a transportable archive. 
First you need to identify the image that you want to export.
```
$ docker images
#REPOSITORY                    TAG         IMAGE ID        CREATED         SIZE
#ubuntu                        18.04       6526a1858e5d    10 days ago     64.2MB
#genomicpariscentre/samtools   latest      2ec1cf58981b    2 years ago     179MB

```
 
Then, you need to save the image to an archive. More information on the ‘docker save’ command is available [here](https://docs.docker.com/engine/reference/commandline/save/): 
https://docs.docker.com/engine/reference/commandline/save/
```
$ docker save genomicpariscentre/samtools > ./savedsamtoolsimage.tar
```
 
You can check the image has been saved.
```
$ ls -alh
#drwxr-xr-x   4 chernand  staff   128B Aug 30 16:42 .
#drwxr-xr-x  20 chernand  staff   640B Aug 30 16:39 ..
#-rw-r--r--   1 chernand  staff   176M Aug 30 16:42 #savedsamtoolsimage.tar
```
 
 ## Conclusion

This is the end of the practical session. We hope you enjoyed it. Don’t hesitate to ask any questions and feel free to contact us any time after the session (email addresses on the front page).


