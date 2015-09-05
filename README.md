# Deploying-Docker-Containers-on-Mesos-0.23.0-on-Ubuntu-14.04


This process should be done on each slave on the mesos cluster. Because my network  was under a proxy server, I had to use additional commands. 

**1. Import Docker release tool key**

sudo  apt-key adv --keyserver-options http-proxy="http://username:password@proxyIPaddress:port" --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 36A1D7869245C8950F966E92D8576A8BA88D21E9


Note: Change the username, password, proxyIaddress and port with the correct values.

**2. Add Docker repositoty**

echo "deb https://get.docker.io/ubuntu docker main"  |  \
sudo tee /etc/apt/sources.list.d/docker.list

**3. Install Docker lxc-docker package**

sudo apt-get update 
sudo apt-get install lxc-docker

**4. Edit Docker file**

Because I was working in a network under a proxy server, I had to edit the /etc/default/docker. I opened this file and uncommented the line the starts with **export http_proxy** and put username, password, proxy IP address and port number. 

**sudo vi /etc/default/docker**

The line should look like this:

export http_proxy="http://username:passowrd@proxyIPaddress:port/"

**5. Add Docker registry to the /etc/hosts**

vi /etc/hosts

Insert the docker CDN registry line after your local IP address line:

**54.224.119.89          cdn-registry-1.docker.io**

Then save and quit the file.

**6. Restart Docker service**

sudo service docker restart

**7. Test docker**

sudo docker run hello-world

It should pull the hello-world image and display the information.


**Enable Docker container on Mesos slave**

echo "docker,mesos" | sudo tee /etc/mesos-slave/containerizers

**Increase the executor timeout**

In my setup, I increased the executer timeout to 10 minutes because sometimes the network is not very fast

echo "10mins" | sudo tee /etc/mesos-slave/executor_registration_timeout

**Restart the Mesos slave process**

service mesos-slave restart

Note: After restarting the mesos slave process, it was not connecting to the master. when I run **netstat -ntv** I could not see the mesos slave port 5051. If you run into this issue, just reboot the whole system.

**sudo reboot**

After rebooting the system, everything should be working just fine.


I hope this tutorial will help anyone working on a project similar to mine. Thanks
