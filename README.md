C:\Users\nidal\dockerapp

export COMPOSE_CONVERT_WINDOWS_PATHS=1

 worked for me with COMPOSE_CONVERT_WINDOWS_PATHS=1, but I had to shut down all containers docker-compose stop, not just the one who uses it.

 /c/Users/prash/Documents/www/meanstackapp/

 C:\Users\prash\Documents\www

  have the same behavior. After enabling the shared drives, and running the command listed in the window

docker run -v c:/Users:/data alpine ls /data
There are no results. If I then run

docker run -v c:/:/data alpine ls -R /data
I get the following results

/data:
Users

/data/Users:

npm install -g --unsafe-perm puppeteer

----------------
When you use a named volume, like "data:/cjworkbench:rw" you must declare it at the docker-compose file (at the end as a good pratice). Here's an example:

volumes:
data:
driver: local

If you just want to use the path "data" you have to specify all address of it. Here's an exemple:
./data:cjworkbench:rw/

volumes:
    volume-name:
    driver: local  # is already local by default

---------
On Windows, the paths must be specified using Windows-style semantics. You should not use a leading slash in front of the path.

docker run -v c:\Users\[path]:c:\[containerPath]


-------------------

3
down vote
Windows 10 Anniversary Update and Windows Server 2016 RTM.

Add a volume:

docker run -d -v my-named-volume:C:\MyNamedVolume testimage:latest
Mount a host directory:

docker run -d -v C:\Temp\123:C:\My\Shared\Dir testimage:latest
------------
f /var/www/html/test is needed, you could try and add

RUN mkdir /var/www/html/test
-
Turned out I had to move my files in the C/Users/Public folder

docker run --name=php5.6_container --rm -v “/c/Users/sites:/var/www/html” -p 80:80 -p 8080:8080 php5.6
-------------


32
down vote
accepted
+50
See the note for Windows and Mac at https://docs.docker.com/engine/userguide/dockervolumes/#mount-a-host-directory-as-a-data-volume. Particularly:

If you are using Docker Machine on Mac or Windows, your Docker daemon has only limited access to your OS X or Windows filesystem. Docker Machine tries to auto-share your /Users (OS X) or C:\Users (Windows) directory.

Basically, you will need to move your site files to somewhere such as c:\Users\sites and then mount using something like suggested in documentation:

docker run --name=php5.6_container --rm -v "/c/Users/sites:/var/www/html" -p 80:80 -p 8080:8080 php5.6

------------
step 1: C:/Program Files/Oracle/VirtualBox/VBoxManage sharedfolder add default -name $shared_folder -hostpath d:/data

To show shared folders VBoxManage showvminfo default | less

step 2(a): mount -t vboxsf -o uid=1000,gid=50 $shared_folder /data

step 2(b): Adding an automount to boot2docker

While logged into the machine, edit/create (as root) /mnt/sda1/var/lib/boot2docker/bootlocal.sh by adding below block to the file, sda1 may be different for you...


mkdir -p /data
mount -t vboxsf -o uid=1000,gid=50 $shared_folder /data

then, docker-machine restart default

step 3: Adding volume when running the container(gogs as example) docker run --name=gogs -rm --net=host -d -v /fuple:/data gogs/gogs
---------------
version: '2.1'

services:
  myngfirebase:
    image: myngfirebase
    build: .
    environment:
      NODE_ENV: development
    ports:
      - 4200:4200
      - 9229:9229
    volumes:
      - .:/usr/src/app
    ## set your startup file here
    command: node --inspect app.js