# Ansible Tutorial
By Jacob Emery 2021

## Table of Contents
* [Goal](#Goal)
* [Difficulty Level](#Difficulty-Level)
* [Supported Operating Systems](#Supported-Operating-Systems)
* [Summary](#Summary)
* [Setup](#Setup)
* [Creating Ansible Content](#Creating-Ansible-Content)
* [Running Ansible](#Running-Ansible)
* [Troubleshooting](#Troubleshooting)
* [Clean-up](#Clean-up)
* [Additional Optional Steps](#Additional-Optional-Steps)

## Goal
To walk-through creating some basic Ansible content and then using Ansible to deploy a basic simulated website. 

## Difficulty Level
Easy/Moderate - This was created to be as simple as possible while still giving you some room to fail and learn from mistakes. A basic understanding of the command line and text editor is necessary to use Ansible.

## Supported Operating Systems
Ansible runs on a controller, which can be your local workstation (your laptop or desktop computer) or a virtual machine. 
The Ansible controller runs on Mac and Linux machines.

## Summary
* First, install some dependencies like Ansible itself and Docker
* Next, start a Docker image to use as a psuedo remote server to host your website. (advanced users: see note below)
* Then, walk through the creation of some very minimal Ansible content
* Run Ansible and debug any problems
* Visit your pseudo website
* Finally, clean-up (and/or do some additional optional steps)

Note for advanced users:
* Ansible is used to configure servers. Usually you would be configuring a remote server for your website. For the purposes of this tutorial and ease-of-use, we will be using a Docker container as the "web server" we will be configuring. This Docker container will be running on your local machine. If you would like to use a remote server, in a public cloud for example, feel free to provision an Ubuntu server with the minimum amount of resources in a public cloud of your choosing, change the IP address in the "inventory" file to the IP address of your server, delete the "ansible_port" line in the "inventory" file, and enable port 22 for SSH connection on the remote server. 

## Setup

### 1) Install dependencies:
* For Mac users:
  * Open terminal:
    * Hit command + spacebar keys to open Spotlight search, type "terminal" and hit return key
  * Install homebrew package manager:
    * Paste this into terminal and hit return key: 
    * /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
  * Update command line tools:
      * In the terminal, type "softwareupdate --all --install" and hit return key
      * In the terminal, type "xcode-select --install" and hit return key
  * Install Ansible and Docker:
      * In the terminal, type "brew install ansible" and hit return key
      * In the terminal, type "brew install docker" and hit return key
* For Linux users:
    * Open terminal (Ctrl+Alt+T)
    * Install Ansible and Docker
      * In the terminal, type "sudo apt/yum install ansible" and hit Enter key 
      * In the terminal, type "sudo apt/yum install docker" and hit Enter key

### 2) Start Docker:
* Mac:
  * In the terminal, type "launchctl start docker" and hit return key
* Linux:
  * In the terminal, type "systemctl start docker" and hit Enter key

### 3) Start container
* In the terminal, paste, "sudo docker run -d -p 8080:22 -p 80:80 rastasheep/ubuntu-sshd:14.04" and hit Enter key, type in your password, hit Enter

### 4) Open text editor
* Either open a text editor ("TextEdit" application in Mac or vim in Linux) or use an Integrated Development Environment (IDE) like Visual Studio Code (VS Code). I would recommend using VS Code because the folders, text editor, and terminal (the three things you need to use Ansible) are all integrated together in one window. You can also install a very helpful extension called "YAML" from Red Hat which helps you catch syntax errors with YAML files (which Ansible uses for a lot of things).

### 5) Clone Git repository
* If you haven't already, get this repository by first navigating to a folder in your terminal that you would like to store this project ("ls", then "cd folder-name"), then copying the line below into your terminal and hitting Enter:
* "git clone https://github.com/jacobemery/ansible_tutorial"

## Creating Ansible Content

### 6) Understanding ansible components
* Ansible uses Secure Shell (SSH) to connect to servers in order to configure them. 
* All Ansible content must have a few essential components: playbooks, an inventory, and a configuration file.
* [Playbooks](site.yaml) tells Ansible what to do.
* The [inventory file](inventory) tells Ansible where to run.
* The [configuration file](ansible.cfg) file configures Ansible.
* Other basic Ansible terms to know are:
  * 'Tasks' are the basic units of Ansible work. 
  * 'Roles' are sequences of tasks which fulfill a specifc function (like configuring the firewall). See the [roles](roles) folder with [install_packages](roles/install_packages) and the reference to it in the [playbook](site.yaml)?
  * Playbooks are sequences of roles and individual tasks which are run to complete a broader goal (like configuring and deploying a web server)
  * Playbooks are written in Yet Another Markup Language (YAML), which tells Ansible what to run with Python in the background.
* Click the links / go to the files referenced here and take a look. Just get a basic sense of these concepts, it will make more sense as you use it.

### 6) Creating a role
* In order to view content in a web browser, you need to use Hyper-Text Transfer Protocol (http)
* A key part of Ansible is to be able to find modules that accomplish things you want it to do.
* Modules are official or community-created tools used to carry out specific functions (like installing packages)
* I've already written a main [playbook](site.yaml) and a role which installs the software for apache2 (http) on the web server.
* What you need to do now is start and enable apache2. To do that we're going to create a role.
* To create a role, first create a folder within the 'roles' folder and name it something related to apache or http.
* Within that folder, create another folder named 'tasks'. And within that folder, create a file called 'main.yaml'
* Kind of confusing, but this is the structure that must be used for Ansible to be able to find the tasks within: roles/role-name/tasks/main.yaml
* Open the main.yaml file you just created and put three dashes at the top of the file like this "---", which signals the start of the file to run.
* Hit enter twice and now open up a web browser and Google "ansible module service" to find the Ansible module which manages services like httpd.
* You should find a website at docs.ansible.com with a page titled "ansible.builtin.service - Manage Services"
* Read this page, which describes how to use the module. Particularly useful are the examples near the bottom.
* We want to use this module to start and enable the service named "apache2".
* Read the parameters and pull from the examples to create two tasks within the file you created above which starts and enables apache2.
* Use the install_packages role as a reference if you need another example.
* See the [answers file](answers/step6) to check your work.
* Give it a good try first though!

### 7) Editing a playbook
* Now that you've created a role and the tasks within it, you need to tell Ansible to call that role in a playbook.
* The goal is for well-written roles to be usable over and over again (idempotent), and in any playbook, without causing any problems.
* Open the [main playbook](site.yaml) and add another line below '- install_packages' which matches the name of your new role folder that you just created in step 6. Match the format of "- install_packages" above it.
* Great! Now Ansible will call the role you created, which runs a task that starts Apache. (We'll double check ypur work at the end of this step).
* One more thing needs to be done in this playbook here before we move on. And that is that you need to copy your Hyper-Text Markup Language (html) file to the web server. This is the file whose contents will show up in the web-browser when you type in the URL.
* As was mentioned earlier, playbooks run a combination of roles and tasks. We've run some roles, now we want the playbook to run a task afterwards.
* First, at the same indentation as the 'roles:' line, add a new line 'post_tasks:' after your new role call's line.
* We'll need an Ansible module which copies [website/index.html](website/index.html) to '/var/www/html/index.html' on the "remote" web server.
* Can you find the correct Ansible module and use it to complete this task?
* See the [answers file](answers/step7) to check your work.

* Important notes about tasks: 
  * The 'name' has no effect on the outcome of the task, but it's extremely helpful for you, and for other users of your playbooks, when trying to figure out what this task is doing later on. Use full sentences to give the user the fullest possible picture of the task.
  * You may have noticed that in the answers for [step 7](answers/step7), instead of calling the module "ansible.builtin.copy", it's just "copy". This is because it is a builtin feature of Ansible. There are official modules, like copy, and then there are community-created modules in Ansible Galaxy which extend Ansible's usefulness. Galaxy collections must be installed separately, for example "ansible-galaxy collection install community.general". When referencing Galaxy modules, the full module name must be used in the task, like 'community.crypto.openssh_keypair' for example, unlike official modules. You can create your own modules (written in Python) and contribute them too!

### 8) Editing the HTML file
* So now that we've told Ansible to install apache, start it, then copy over the HTML file to the web server... now we need to customize the HTML file with what you want your pseudo website to say!
* Open the [index.html](website/index.html) file in the [website](website) foler.
* Replace "Website-Title-Here" with what you want the website to be called. This will show up on the tab.
* Replace "Webiste-Content-Here" with what you want the website's homepage to say.
* Alternatively, copy or type out any HTML you'd like to show up on the website here. 
* Go wild!

## Running Ansible

### 9) ansible-playbook
* We're ready to run Ansible!
* Make sure to save the files you've changed first.
* In your terminal, navigate to the [main folder](ansible_tutorial) of your cloned repository, and run the following command:
* "ansible-playbook site.yaml"
* Watch Ansible as it runs. 
* If all goes smoothly, you should be able to open a web browser and type in "localhost:8080" as the URL and see your website!
* Likely though, you will encounter some errors...

### 10) Troubleshooting
* When you encounter errors while running Ansible, here are some steps you can take:
* Read the error message and find out the answers to these questions: What type of error is it? What file is the error occurring in? Where in that file is the error occurring? 
* If for example it's a syntax error, open the file it is referencing, go to the task it is complaining about, see if the error message gives you any clues about the problem, and then try to correct the error. A lot of the time it's an indentation issue with YAML. I highly recommend using an IDE like VS Code where you can install extensions that catch these errors early.
* Google the error message. Include "ansible" in the search and only copy the parts of the message which describe the error generally. For example, if the error message includes an IP address, don't include the IP address and just copy the error name. Click on and read some of the results. First see if the problem they are describing matches the problem you are having. Then, read the information there. If it doesn't get you the right answer, maybe it gets you closer.
* Try to correct the error and run the Ansible playbook again (step 9), this time with more verbosity by adding v options to the command like this: "ansible-playbook site.yaml -vv". The more v's, the more information Ansible will give you. Which can help you get to the root of the problem more easily. This playbook should be idempotent, meaning you can run it as many times as you want and it won't create problems.

## Clean-Up

### 11) Kill container
* If you would like to do some additional steps and learn more about Ansible, skip this until after doing the optional steps below.
* To tear down your web server, in your terminal run the command "docker kill rastasheep/ubuntu-sshd:14.04"

## Additional Optional Steps
### 12) Tags
* What if you decide you don't like what you put on your website, or you spot a typo? What can you do? Do you have to run the playbook from the beginning? Nope, you can use tags.
* Each task can include one or more 'tags' which help the user selectively run individual tasks or roles within a playbook.
* Try adding a line to the copy HTML to web server task in [site.yaml](site.yaml) at the same indentation as the 'name' parameter like this:
* "tags: update_website"
* Now you can update the website content in [index.html] by changing that file's contents, and running the playbooks with tags, like this:
* "ansible-playbook site.yaml --tags update_website"
* If you want to use multiple tags, enclose them in quotes, (for example: --tags 'start_apache,update_website')
### 13) Loops
* What if you wanted to install more packages on your web server? Maybe you're new to Linux and the vim text editor is hard to use and you want to nano instead.
* In the install_packages playbook, you could use a loop to install any number of packages instead of just one.
* In the install_packages [main.yaml](roles/install_packages/tasks/main.yaml) file, replace "apache2" with "{{ item }}" (include the double quotes).
* Then, at the bottom of that task, at the same indentation as the 'name' parameter, add "loop: '{{ packages }}'" (include the single quotes but not the double quotes).
* This is slightly confusing with the quotes, so make sure to look at the [answers file](answers/step13-1) to check your work.
* Next, you'll have to specify the "packages" variable. You can do this in a number of ways. 
* One way of doing it is by specifying it at the playbook level.
* Open [site.yaml](site.yaml), below "gather_facts: no" at the same indentation level, add the line "vars:". Hit Enter and then hit the spacebar twice and add the line "packages: ['apache2', 'nano']" (without the double quotes).
* See the [answers file](answers/step13-2) to check your work.
* Try it out by running the main playbook with tags. (Find the correct tag by opening install_packages' [main.yaml](roles/install_packages/tasks/main.yaml) file).
### 14) Debug messages
* Let's say that at the end of the playbook, you'd like Ansible to print out a URL for the user to easily click on to bring them to their new website.
* To print information to the terminal, Ansible uses the debug module.
* Google search the debug module.
* Open the [main playbook](site.yaml) and create a new task after copying the html to the web server that prints a message which includes "localhost:80".
* You could include a tag or two to selectively run that task too.
* See the [answers file](answers/step14) to check your work.
### 15) Ad-hoc commands
* There are some things that Ansible modules have not yet been created to do, or that the existing Ansible modules are not flexible enough to accomodate.
* For these tasks, use the Ansible module "command" to run any shell command.
* Return to step 11 to clean up.