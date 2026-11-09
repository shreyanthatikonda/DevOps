# DevOps: Dockerized Web App

This is a CI/CD project for dockerizing a web application. Nginx serves a simple HTML page from a Docker container, and Jenkins builds the image and publishes it to Docker Hub as `shreyanthatikonda/webapp:latest`.

## Project files

- `index.html`: the web page with a **Dockerized Web App** heading.
- `Dockerfile`: uses `nginx:alpine` and copies the page into Nginx's default web root.
- `Jenkinsfile`: checks out the source, builds the Docker image, and pushes it to Docker Hub.
- `README.md`: project overview and setup instructions.

## Run locally

Install Docker and start its engine, then run these commands from this folder:

```sh
docker build -t shreyanthatikonda/webapp:latest .
docker run --rm -p 8080:80 shreyanthatikonda/webapp:latest
```

Open <http://localhost:8080> to see the web page. Press Ctrl+C to stop the container.

## Configure Jenkins

1. Use a Linux Jenkins agent with Git, the Docker CLI, and access to a running Docker daemon. The pipeline uses `agent any`, so all eligible agents must meet these requirements; otherwise, set an appropriate agent label in the Jenkinsfile.
2. Install or enable the Jenkins Pipeline, Git, and Credentials Binding plugins.
3. Ensure the Docker Hub repository `shreyanthatikonda/webapp` exists.
4. Add a Jenkins **Username with password** credential with ID `dockerhub-credentials`. Set the username to `shreyanthatikonda` and the password to a Docker Hub access token with permission to push to that repository. Keep the token in Jenkins credentials.
5. Create a Jenkins Pipeline job using **Pipeline script from SCM**, select Git, and use `https://github.com/shreyanthatikonda/DevOps.git`. Set the branch specifier to `*/main` and the script path to `Jenkinsfile`. If the GitHub repository is private, configure a separate SCM credential for checkout.
6. Click **Build Now** to run the pipeline. To run it after future GitHub pushes, configure a GitHub webhook and the corresponding Jenkins trigger, or enable SCM polling.

## CI/CD flow

```text
GitHub main -> Jenkins checkout -> Docker build -> Docker Hub push
```

Jenkins builds `shreyanthatikonda/webapp:latest` with the current `nginx:alpine` base image, signs in using the stored credential, and pushes the image. Docker login uses a temporary configuration directory that is removed when the push stage exits. Concurrent builds of this job are disabled to avoid competing updates to the image tag.

This setup delivers the image to Docker Hub. Deployment to a running server is a separate step. Committing the Jenkinsfile does not create a Jenkins job or run the pipeline by itself.
