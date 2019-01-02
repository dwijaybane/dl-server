# dl-server

### steps to connect server from local machine

0. Note the dl-server ip address

1. Ensure Public Keys are available

- `cd` into `~/.ssh` directory
- if you don't have an `.ssh` directory in your home folder, create it (`mkdir ~/.ssh`)
- if you don't have an `id_rsa.pub` file in your `~/.ssh` folder, create it (`ssh-keygen` and hit Enter 3 times)

2. Add dl-server info to `config` file
- if you don't have a config file, create one. This example creates file using gedit editor.
`$ gedit config`
- add these contents to your config file (replace IP address here with your dl-server IP address)
```
Host dl-server
     HostName 10.7.1.150
     IdentityFile ~/.ssh/id_rsa
     # StrictHostKeyChecking no  
     User <user-name>
     # LocalForward 5000 localhost:5000
     # LocalForward 8888 localhost:8888
```

3. `ssh` into dl-server from local computer
```
$ ssh dl-server   
```

### Connect to dl-server

#### User Creation steps
```
$ ssh bostonai@10.7.1.150
$ sudo adduser user<#number>

$ sudo usermod -aG docker user<#number>
```

1. General setup
```
$ ssh dl-server
$ mkdir ~/docker_mount
$ cd ~/docker_mount
$ docker volume create docker_store
$ docker run hello-world
$ docker load --input /LAB_Setup/dl_lab_v3.tar
```

2. Once we have docker running lets setup our data for examples
```
$ echo "LAB=~/docker_mount" >> ~/.bashrc
$ source ~/.bashrc 
```

3. Make sure docker_store exist (If not create if ref:docker-setup.sh)
`$ docker volume inspect docker_store`

4. Copy example you want to run to your docker_mount folder
`$ cp -R /LAB_Setup/dl-lab-files/<example> ~/docker_mount`

5. Now we can download and run our dl-lab image in container
```
$ nvidia-docker run --rm --init -it --name container1 -p 50##:5000 -p 88##:8888 -v $LAB:/LAB -w="/LAB" -v docker_store:/docker_store streaminterrupt/dl-lab:v3
```
6. To Run NVIDIA Digits [In Container]
`$ digits-devserver   #access on local browser localhost:50##`

Now open new tab of terminal
```
$ ssh dl-server
$ docker exec -it container1 bash
$ jupyter-notebook --ip=0.0.0.0 --port=8888 --allow-root  
```
access on local browser by ctrl + clicking link it generates

#### Note Everytime you make change in docker make sure to save image
Do it in new tab while your container is still active and required changes are done
`$ docker commit container1 streaminterrupt/dl-lab:v3` 

