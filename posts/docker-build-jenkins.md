<h2 id="a-few-things-you-should-know">
    <strong>1. Build network in Docker</strong>
</h2>
<blockquote>
   docker network create jenkins
</blockquote>

<h2 id="a-few-things-you-should-know">
    <strong>2. Run Docker: dind image</strong>
</h2>
<blockquote>
   docker run --name jenkins-docker --rm --detach  --privileged --network jenkins --network-alias docker  --env DOCKER_TLS_CERTDIR=/certs  --volume jenkins-docker-certs:/certs/client  --volume jenkins-data:/var/jenkins_home  --publish 2376:2376  docker:dind
</blockquote>

<h2 id="a-few-things-you-should-know">
    <strong>3. Create a Dockerfile to Customize the Official Jenkins Docker Image</strong>
</h2>
<pre>
FROM jenkins/jenkins:2.479.1-jdk17
USER root
RUN apt-get update && apt-get install -y lsb-release
RUN curl -fsSLo /usr/share/keyrings/docker-archive-keyring.asc \
  https://download.docker.com/linux/debian/gpg
RUN echo "deb [arch=$(dpkg --print-architecture) \
  signed-by=/usr/share/keyrings/docker-archive-keyring.asc] \
  https://download.docker.com/linux/debian \
  $(lsb_release -cs) stable" > /etc/apt/sources.list.d/docker.list
RUN apt-get update && apt-get install -y docker-ce-cli
USER jenkins
RUN jenkins-plugin-cli --plugins "blueocean docker-workflow"
</pre>

<h2 id="a-few-things-you-should-know">
    <strong>4. To build a new Docker image from the Dockerfile</strong>
</h2>
<blockquote>
    docker build -t myjenkins-blueocean:2.479.1-1 .
</blockquote>

<h2 id="a-few-things-you-should-know">
    <strong>5. Run the image as a container in Docker</strong>
</h2>
<blockquote>
    docker run --name jenkins-blueocean --restart=on-failure --detach  --network jenkins --env DOCKER_HOST=tcp://docker:2376  --env DOCKER_CERT_PATH=/certs/client --env DOCKER_TLS_VERIFY=1  --volume jenkins-data:/var/jenkins_home --volume jenkins-docker-certs:/certs/client:ro  --publish 8080:8080 --publish 50000:50000 myjenkins-blueocean:2.479.1-1
</blockquote>

<h2 id="a-few-things-you-should-know">
    <strong>6. Use a browser to connect to Jenkins</strong>
</h2>
<blockquote>
    connect to http://localhost:8080

</blockquote>

<h2 id="a-few-things-you-should-know">
    <strong>7. Get the initial Jenkins login password</strong>
</h2>
<blockquote>docker exec -it jenkins cat /var/jenkins_home/secrets/initialAdminPassword
sudo grub2-mkconfig -o /boot/grub2/grub.cfg
</blockquote>

<h2 id="a-few-things-you-should-know">
    <strong>8. Deploy agent</strong>
</h2>

<h3 id="a-few-things-you-should-know">
    <strong>Create Dockerfile</strong>
</h3>

<pre>
FROM jenkins/inbound-agent
USER root
RUN apt-get update && \
    apt-get install -y docker.io
</pre>

<h3 id="a-few-things-you-should-know">
    <strong>Build image</strong>
</h3>

<blockquote>
	docker build -t jenkins/inbound-agent .
</blockquote>

<h3 id="a-few-things-you-should-know">
    <strong>Mount the Docker socket file when starting the Jenkins Agent container, allowing the Jenkins Agent container to communicate with the Docker Daemon on the host machine</strong>
</h3>

<blockquote>
	docker run --name <agent name> --volume /var/run/docker.sock:/var/run/docker.sock --init jenkins/inbound-agent -url http://<jenkins-server>:<port> -workDir=/<path_to_workDir> <secret> <agent name>

</blockquote>


