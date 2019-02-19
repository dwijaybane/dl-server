# Docker Instructions for LAB

### steps to connect to server from local machine

0. On a server you will be given a username, password and ip to access the machine, Note that down.

1. Ensure Public Keys are available (On Local Machine)

- `cd` into `~/.ssh` directory
- if you don't have an `.ssh` directory in your home folder, create it (`mkdir ~/.ssh`)
- if you don't have an `id_rsa.pub` file in your `~/.ssh` folder, create it (`ssh-keygen` and hit Enter 3 times)

2. Add dl-server info to `config` file (Optional to make things easier)
- if you don't have a config file, create one. This example creates file using gedit editor.
`$ gedit config`
- add these contents to your config file (replace IP address here with your server Public IP address and <Username> with the given username)
```
Host dl-server
     HostName <184.###.###.###>
     IdentityFile ~/.ssh/id_rsa
     # StrictHostKeyChecking no  
     User <Username>
     LocalForward 5000 localhost:5000
     LocalForward 8888 localhost:8888
```

3. `ssh` into server from local computer
```
$ ssh dl-server   
```

### Connect to dl-server

#### User Creation steps (Admin)
```
$ ssh admin-username@10.###.###.###
$ sudo adduser user<#number>
$ sudo usermod -aG docker user<#number>
```

- General setup (One time)
```
$ ssh dl-server
$ mkdir ~/docker_mount
$ cd ~/docker_mount
$ docker volume create docker_store
$ docker run hello-world
$ docker load --input /LAB_Setup/dl_lab_v3.tar
```

- Once we have docker running lets setup our data for examples
```
$ echo "LAB=~/docker_mount" >> ~/.bashrc
$ source ~/.bashrc 
```

- Make sure docker_store exist (If not create if ref:docker-setup.sh)
`$ docker volume inspect docker_store`

#### LAB Usage Steps

1. Copy example you want to run to your docker_mount folder
`$ cp -R /LAB_Setup/dl-lab-files/<example> ~/docker_mount`

2. Now we can download and run our dl-lab image in container
```
$ nvidia-docker run --rm --init -it --name container1 -p 50##:5000 -p 88##:8888 -v $LAB:/LAB -w="/LAB" -v docker_store:/docker_store streaminterrupt/dl-lab:v3
```
3. To Run NVIDIA Digits [In Container]
`$ digits-devserver   #access on local browser localhost:50##`

Now open new tab of terminal
```
$ ssh dl-server
$ docker exec -it container1 bash
$ jupyter-notebook --ip=0.0.0.0 --port=8888 --allow-root  
```

7. To know your containers and images storage usage
```
$ docker system df
```

8. Make sure to terminate your docker instance otherwise it will take space on your disk. To check and if container is still running and terminate it.
```
$ docker container ls
$ docker container kill <container-name>
```

### Useful Links:
- [For pushing docker images and saving it offline](https://ropenscilabs.github.io/r-docker-tutorial/04-Dockerhub.html)

- [ssh port forwarding](https://stackoverflow.com/questions/37987839/how-can-i-run-tensorboard-on-a-remote-server)

- [Setup ssh for paperspace](https://github.com/reshamas/fastai_deeplearn_part1/blob/master/tools/paperspace.md)

