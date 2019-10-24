# Command-line bootcamp

Learn the unix command-line by using it in your browser. This repo was originally forked from <https://github.com/blahah/command_line_bootcamp>

**NOTE** These are not the original setup instructions that came from the original repo. These have been modified to include some fixes and describe how to set this command line bootcamp up to run on AWS.

Launch AWS instance with base Ubuntu image.

```bash
sudo apt-get -y update
sudo apt-get -y upgrade
sudo apt-get dist-upgrade
# say yes when it asks to keep local version of tools that you've already updated/upgraded
```

In this example we're going to run the frontend and backend on the same ec2 instance. In order to have both the frontend and backend running on the same aws instance, we're going to build each in a separate screen session.

## Build and deploy the frontend

You'll need node.js

```bash
# install and set up nvm and node
screen
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.29.0/install.sh | bash
source ~/.bashrc

# some dependencies don't work with node 1+, so stick with 0.12
nvm install 0.12
nvm use 0.12
```

Once you've got node installed, grab this repo and let node install the dependencies:

```bash
git clone https://github.com/griffithlab/command_line_bootcamp.git
cd command_line_bootcamp
npm install
npm run deploy
```

Use nano to replace `#eeeeec` with `#333333` in `node_modules/adventure-time/node_modules/term.js/src/term.js` in order to make cursor color different from background of the terminal.

Then start the frontend server and disconnect from the screen session.

```bash
npm start
ctrl a+d
```

## Setup and run the docker server backend

```bash
# keep it running
screen

# need some basics
apt-get install -y build-essential g++
sudo systemctl enable docker
sudo systemctl start docker
sudo usermod -a -G docker ubuntu

# install and set up nvm and node
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.29.0/install.sh | bash
source ~/.bashrc

# use node version 4.1
nvm install 4.1
nvm use 4.1

# major dependencies
npm install --global docker-browser-server

# setup docker
docker pull ubuntu
cd ~/command_line_bootcamp
docker build -t "command_line_bootcamp" .
docker-browser-server command_line_bootcamp -p 8080
```

### cleaning up long-running containers

Sometimes people leave their browser windows open for a really long time. If you don't want to fund the docker container running for that time, you can clean up long-running containers:

```bash
docker stop $(docker ps | grep hours | cut -d ' ' -f1 | paste - - - - )
```
