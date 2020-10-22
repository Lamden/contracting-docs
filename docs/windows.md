# Lamden Setup on Windows

Performing development on Windows can sometimes be tricky.  Nix based operating systems have a build in C++ compilers and Windows does not. Some Lamden developers have exprienced issues because of this. Additionally, many of the tools and programs come preinstalled on Linux like Python and Git!

Luckily Microsoft has a solution! Windows Subsystem for Linux.  Which is basically a Linux virtual machine that you access through terminal.  There is no GUI, but some really nice tools Visual Studio Code can reach inside WLS and it is like you are developing natively on Windows.

## Prequisites
- Must be running Windows 10 

## Installation Guide
1. [Install and configure WSL (Windows Subsystem for Linux) 2](https://docs.microsoft.com/en-us/windows/wsl/install-win10)
 1. Choose Unbuntu as the Linux distribution 20.04
1. Install [Visual Studio Code](https://code.visualstudio.com/) (VS Code)
1. Install the VS Code [WSL Extenstion](https://code.visualstudio.com/docs/remote/wsl)
1. Install the VS Code [Python Extenstion](https://code.visualstudio.com/docs/languages/python)
1. Download and install the [Linux kernel update package](https://docs.microsoft.com/windows/wsl/wsl2-kernel)
1. Install [Docker](https://hub.docker.com/editions/community/docker-ce-desktop-windows/) 
1. Follow the [Docker WLS 2 Backend guide](https://docs.docker.com/docker-for-windows/wsl/#:~:text=%20Develop%20with%20Docker%20and%20WSL%202%20%F0%9F%94%97,and%20start%20working%20natively%20from%20your...%20More%20),*Note: The prerequisites were completed in the above steps
1. Open a terminal: `Start > Ubuntu`
1. Run `docker run -d -p 27017:27017/tcp --name mongodb mongo:latest` to create and start a Docker containter for Mongo DB (in the future you can delete this containter and use this command to reset the database or change the name `--name` argument to create multiple containers for different projects)
1. Create a mongo db startup script
 1. Run `echo "docker start mongodb" > ~/startmongo.sh`
 1. Run `chmod 755 ~/startmongo.sh` to grant execute permissions
1.  Run `pip3 install contracting` to install the [Lamden Contracting Python Library](https://github.com/Lamden/contracting)

## Usage
1. Open a terminal: `Start > Ubuntu`
1. Run `~/startmongo.sh` to start mongo in Docker from Ubuntu if the 'mongodb' Docker containter is not running
1. Run `cd ~` to change directory to your home directory
1. Clone the repo using `git clone https://github.com/JeffWScott/my_token_lamden_tutorial.git` 
1. Run `code my_token_lamden_tutorial` to open the tutorial folder in VS Code
1 Under the `server` folder select `contracting_server.py` and in the top right corner hit the play button
 1. The results should be something like below in the termimal window:
 
 ```
 onlyluck@OnlyLuck:~/my_token_lamden_tutorial$ /usr/bin/python3 /home/onlyluck/my_token_lamden_tutorial/server/contracting_server.py
[2020-09-24 00:23:50 -0400] [1990] [INFO] Goin' Fast @ http://0.0.0.0:3737
[2020-09-24 00:23:50 -0400] [1990] [INFO] Starting worker [1990]
 ````
 
 1. Open [http://localhost:3737/contracts](http://localhost:3737/contracts) and if everything is working you should see:
 
 ```
 {"contracts":["submission","my_token"]}
 ```

#### With everything running you should be all set to go through the tutorials on [Smart Contracting](https://blog.lamden.io/smart-contracting-with-python-2af233620dca)
