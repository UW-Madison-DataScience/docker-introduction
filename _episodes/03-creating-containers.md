---
title: "Creating containers"
teaching: 10
exercises: 0
questions:
- "How do I get Docker to perform computation?"
objectives:
- "Demonstrate how to create an instance of a container from an image."
- "Explain how to list (container) images on your laptop."
- "Explain how to list running and completed containers."
keypoints:
- "Containers are usually created using command line invocations."
- "The `docker run` command creates containers from images."
- "The `docker image` command lists images that are (now) on your computer."
- "The `docker container` command lists containers that have been created."
---

### Creating a "hello world" container

> ## Reminder of terminology: images and containers
> - Recall that a container "image" is the template from which particular instances of containers will be created.
{: .callout}

One of the simplest Docker container images just allows you to create containers that print a welcome message.

To create and run containers from named Docker images you use the `docker run` command. Open a shell window if you do not already have one open and try the following `docker run` invocation. Note that it does not matter what your current working directory is.
~~~
$ docker run hello-world
~~~
{: .language-bash}
~~~
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
1b930d010525: Pull complete 
Digest: sha256:2557e3c07ed1e38f26e389462d03ed943586f744621577a99efb77324b0fe535
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
~~~
{: .output}

Now try to run the above command again, and observe the difference in the output.

The message from "Hello from Docker!" is what's produced by instances of the hello-world container. It should be identical every time you make an instance of the hello-world container and run it.

 The first time you saw some initial output from the Docker command itself:
~~~
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
1b930d010525: Pull complete 
Digest: sha256:2557e3c07ed1e38f26e389462d03ed943586f744621577a99efb77324b0fe535
Status: Downloaded newer image for hello-world:latest
~~~
{: .output}

What this says is that your laptop didn't have its own local copy of the hello-world container's image.

Docker then connected to the Docker Hub to find and download the (container) image with that name.

The message notes information about the Docker Hub website, but we'll return to that in the next episode.

The "digest" is a secure fingerprint (a "hash") of the particular version of the container image that you now have... (Although of course the usefulness of that hash is minimal if you don't know what to compare it to!)

### Where did that "hello-world" container image get stored?

The `docker image` command is used to list and modify Docker images.
You can find out what container images you have local copies of using the following command ("ls" is short for "list"):
~~~
$ docker image ls
~~~
{: .language-bash}
OR
~~~
$ docker images
~~~
{: .language-bash}
~~~
REPOSITORY             TAG                 IMAGE ID            CREATED             SIZE
hello-world            latest              fce289e99eb9        4 weeks ago         1.84kB
~~~
{: .output}

The `docker image ls` is orthoglonal to what we know from the unix shell however `docker images` can be used as a shortcut for this command.
The number of repositories listed above may vary depending on if you have used docker before.

### Removing images

If you need to reclaim space, you will need to remove image files.
The images and their corresponding containers can start to take up a lot of disk space of you don't clean them up occasionally.
On macOS and Windows, when you uninstall the overall Docker software, it should have the effect of removing all of your image files, although we have not explicitly tested this.

If you want to explicitly remove a container image, you will need to find out details such as the "image ID" or name of the repository. For example say my laptop contained the following image.
~~~
$ docker image ls
~~~
{: .language-bash}
~~~
REPOSITORY             TAG                 IMAGE ID            CREATED             SIZE
ubuntu                 trusty              dc4491992653        12 months ago       222MB
hello-world            latest              fce289e99eb9        4 months ago        1.84kB
~~~
{: .output}

We can try to remove remove the `hello-world` image with a `docker image rm` command that includes the repository name, like so but...:
~~~
$ docker image rm hello-world
~~~
{: .language-bash}
~~~
Error response from daemon: conflict: unable to remove repository reference "hello-world" (must force) - container e7d3b76b00f4 is using its referenced image fce289e99eb9
~~~
{: .output}
I get an error because their are containers created that depend on this image.

### What containers are running?
As indicated by the error above there is still an existing container from this image.
We can list the running containers by typing.
~~~
$ docker container ls
~~~
{: .language-bash}
OR
~~~
$ docker ps
~~~
{: .language-bash}
~~~
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
~~~
{: .output}

The first version of the command is ortholognal to what we know from the unix shell but we might want to use the shorter version. For refernece, "ps" stands for “Process Status”.
We should also notice that this command didn't return any containers because our containers all exited and stopped running after they ran.


### What containers have run recently?
There is also a way to list running containers, and those that have completed recently, which is to add the `--all`/`-a` flag to the `docker container ls`/`docker ps` command as shown below.
~~~
$ docker container ls --all
~~~
{: .language-bash}
OR
~~~
$ docker ps -a
~~~
{: .language-bash}
~~~
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS               NAMES
9c698655416a        hello-world         "/hello"            2 minutes ago       Exited (0) 2 minutes ago                       zen_dubinsky
6dd822cf6ca9        hello-world         "/hello"            3 minutes ago       Exited (0) 3 minutes ago                       eager_engelbart
~~~
{: .output}

We will talk more about how you might use these exited containers and how to restart a container later in this lesson.


### How do I remove an exited container.
To delete an exited container you can run the following command, inserting the `CONTAINER ID` for the container you wish to remove.
It will repeat the `CONTAINER ID` back to you, if successful.
~~~
$ docker container rm 9c698655416a
~~~
{: .language-bash}
~~~
9c698655416a
~~~
{: .output}

If you want to remove all exited containers at once you can use the `docker containers prune` command. 
**Be careful** with this command.
If you have containers you may want to reconnect to, you should not use this command.
It will ask you if to confirm you want to remove these containers, see output below.
If successfull it will print the full `CONTAINER ID` back to you.
~~~
$ docker container prune
~~~
{: .language-bash}
~~~
WARNING! This will remove all stopped containers.
Are you sure you want to continue? [y/N] y
Deleted Containers:
9c698655416a848278d16bb1352b97e72b7ea85884bff8f106877afe0210acfc
6dd822cf6ca92f3040eaecbd26ad2af63595f30bb7e7a20eacf4554f6ccc9b2b
~~~
{: .output}

### Removing images, for real this time

~~~
$ docker image rm hello-world
~~~
{: .language-bash}
~~~
Untagged: hello-world:latest
Untagged: hello-world@sha256:5f179596a7335398b805f036f7e8561b6f0e32cd30a32f5e19d17a3cda6cc33d
Deleted: sha256:fce289e99eb9bca977dae136fbe2a82b6b7d4c372474c9235adc1741675f587e
Deleted: sha256:af0b15c8625bb1938f1d7b17081031f649fd14e6b233688eea3c5483994a66a3
~~~
{: .output}

The reason that there is a few lines output, is that a given image may be created by merging multiple underlying layers.
Any layers that are used by multiple Docker images will only be stored once.
Now the result of `docker images` should no longer include the `hello-world` image.



### Conclusion

You have now successfully acquired a Docker image file to your computer, and have created a Docker container from it. While this already effects a reproducible computational environment, the image contents are not under your control, so we look at this topic, after a quick discussion about the Docker Hub.

> ## The Docker official documentation is helpful!
> There is lots of great documentation at <https://docs.docker.com/>, for example, detailed reference material and tutorials covering the use of the commands mentioned above.
{: .callout}

{% include links.md %}

{% comment %}
<!--  LocalWords:  keypoints amd64 fce289e99eb9 zen_dubinsky links.md
 -->
<!--  LocalWords:  eager_engelbart endcomment
 -->
{% endcomment %}
