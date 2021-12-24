# ansible_tutorial
A basic tutorial to get to know Ansible.

1) Install Dependencies:
  - Mac:
    - Open terminal:
      - Hit command + spacebar keys to open Spotlight search, type "terminal" and hit return key
    - Install homebrew package manager:
      - Paste this into terminal and hit return key: 
      - /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
    - Update command line tools:
        - In the terminal, type "softwareupdate --all --install" and hit return key
        - In the terminal, type "xcode-select --install" and hit return key
    - Install Ansible and Docker:
        - In the terminal, type "brew install ansible" and hit return key
        - In the terminal, type "brew install docker" and hit return key
  - Linux:
      - Open terminal (Ctrl+Alt+T)
      - Install Ansible and Docker
        - In the terminal, type "sudo apt/yum install ansible" and hit Enter key 
        - In the terminal, type "sudo apt/yum install docker" and hit Enter key

2) Start Docker:
  - Mac:
    - In the terminal, type "launchctl start docker" and hit return key
  - Linux:
    - In the terminal, type "systemctl start docker" and hit Enter key

3) Start Docker Container:
  - In the terminal, paste, "sudo docker run -d -p 8080:22 -p 80:80 rastasheep/ubuntu-sshd:14.04" and hit Enter key

4) Open text editor
  - Either open a text editor (TextEdit in Mac or vim in Linux) or use an IDE like VS Code

5) Get this Git repository
  - If you haven't already, get this repository by first navigating to a folder in your terminal that you would like to store this project ("cd folder-name"), then typing in your terminal and then hitting Enter:
  - "https://github.com/jacobemery/ansible_tutorial"

6) Creating Apache role
  - A key part of Ansible is to be able to find module that do things you want it to do.
  - To build a website, we need HTML. A common 