# Lab 2: Docker

## Exploring Docker container registries

Every docker image is build upon a base image which is defined in the Dockerfile.

Now, let's try to find the base image from the following Dockerfile:

```Dockerfile
FROM registry.access.redhat.com/ubi8/nodejs-10

# Change working directory
WORKDIR /opt/app-root/src/

# Install npm production packages
COPY package*.json /opt/app-root/src/
RUN cd /opt/app-root/src; npm ci

COPY . /opt/app-root/src/

ENV NODE_ENV production
ENV PORT 3000

EXPOSE 3000

CMD ["npm", "start"]
```

Have a look at the Dockerfile of the base image!

> Hint: There are two well known public container registries:

- [Official redhat registry](https://catalog.redhat.com/software/containers/search)
- [Docker Hub](https://hub.docker.com/)

Be aware, that everybody can upload images to Docker Hub, so use them with caution! All the images from the redhat registry are certified and should be used whenever possible.

Now, explore the container registry. Do you find an image that you might use for one of your own applications?

## Build an application on OpenShift from a Dockerfile

After exploring the registries, let's acutally build an application from a Dockerfile.

For this purpose the `oc` command `oc new-build` is utilized.

The application that is being build is a simple http server, based on this git repository: https://github.com/sclorg/httpd-container

First, open the repository and locate the `Dockerfile` in the `2.4` folder. Second, open the file and have a look at the individual layers.

Now open a terminal, log into your OpenShift cluster, select your project and execute the following command:

```
oc new-build https://github.com/sclorg/httpd-container --context-dir 2.4/ --strategy docker
```

The command created a `BuildConfig` called `httpd-container` and starts a new build `httpd-container-1`.

Have a look at the progress of the build by executing:

```
oc build-logs httpd-container-1
```

For newer versions of the OpenShift CLI you need to run the following command:

```
oc logs build/httpd-container-1
```

Now you can see how each layer of the Dockerfile is build. At the end the resulting image is pushed to the internal image registry and could now be started up as a container.

> Hint: To start a new build use the command `oc start-build httpd-container` or navigate to the `Build` section in the UI, click on the BuildConfig and select `Start Build`
