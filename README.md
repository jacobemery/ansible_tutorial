# Ansible Tutorial
Created by Jacob Emery (jacob.emery@ibm.com), IBM 2021 \
Feel free to reach out! Always happy to help.

## Table of Contents
* [Goal](#Goal)
* [Difficulty Level](#Difficulty-Level)
* [Supported Operating Systems](#Supported-Operating-Systems)
* [Summary](#Summary)
* [Setup](#Setup)
* [Creating Ansible Content](#Creating-Ansible-Content)
* [Running Ansible](#Running-Ansible)
* [Clean-up](#Clean-up)
* [Additional Optional Steps](#Additional-Optional-Steps)

## Goal
First-time introduction to Ansible - walk-through creating some basic Ansible content and then using Ansible to deploy a basic simulated website. 

## Difficulty Level
Easy/Moderate - This was created to be as simple as possible while still giving you some room to fail and learn from mistakes. A basic understanding of the command line and text editor is necessary to use Ansible.

## Supported Operating Systems
Ansible runs in the command line of a Mac or Linux workstation (your laptop / desktop computer or a virtual machine).

## Summary
* First, we'll install Ansible.
* Normally, we would then configure a remote server to host your website. This tutorial will use a local Docker container to simulate a remote server.
* Next, walk through the creation of some very minimal Ansible content.
* Then, Run Ansible and debug any problems.
* Visit your new website.
* Optionally, try some more advanced Ansible configurations.
* Finally, clean-up.

## Setup

### 1) Install Ansible:
* <u>For Mac users</u>:
  * Open the terminal:
    * Hit command + spacebar to open Spotlight search, type "terminal" and hit return key.
  * Install homebrew package manager:
    * Copy/paste this into terminal and hit return key: 
      ~~~
      /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
      ~~~
  * Update command line tools:
      * In the terminal, copy/paste the following and hit Enter:
        ~~~
        softwareupdate --all --install
        ~~~
      * Then, in the terminal, copy/paste the following and hit Enter:
        ~~~
        xcode-select --install
        ~~~
  * Install Ansible:
      * In the terminal, copy/paste the following and hit Enter: 
        ~~~
        brew install ansible
        ~~~
* <u>For Linux users</u>:
    * Open the terminal (Ctrl+Alt+T).
    * Install Ansible:
      * In the terminal, copy/paste one of the following (depending on your distribution) hit Enter, type in your sudo password, hit Enter again:
        ~~~
        sudo apt install -y ansible
        ~~~
        or
        ~~~
        sudo yum install -y ansible
        ~~~

### 2) Create a server to configure:
* <u>Important Note</u>: Usually you would be configuring a remote server for your website (see [Advanced](####<u>Advanced</u> ) below), but assuming you don't have one ready, we'll be setting up a Docker container locally that will act as a "remote server" for us to configure.
#### <u>Recommended</u>
Start Docker container:
  * For Mac users:
    * In the terminal, copy/paste the following and hit the Enter. Type in your password and hit Enter again:
      ~~~
      brew install docker
      ~~~
    * Once that installs, in the terminal, copy/paste the following, hit Enter, type in your password and hit Enter again:
      ~~~
      sudo launchctl start docker
      ~~~
    * In the terminal, copy/paste:
      ~~~
      sudo docker run -d -p 8080:22 -p 80:80 rastasheep/ubuntu-sshd:14.04
      ~~~
  * For Linux users:
    * In the terminal, copy/paste one of the following (depending on your distribution), hit Enter, type in your password and hit Enter again:
      ~~~
      sudo apt install -y docker
      ~~~
      or 
      ~~~ 
      sudo yum install -y docker
      ~~~
    * Once that installs, in the terminal, copy/paste the following, hit the Enter key, type in your password, and hit Enter again: 
      ~~~
      sudo systemctl start docker
      ~~~
    * In the terminal, copy/paste the following, hit the Enter key, type in your password, and hit Enter again:
      ~~~
      sudo docker run -d -p 8080:22 -p 80:80 rastasheep/ubuntu-sshd:14.04
      ~~~
#### <u>Advanced</u> 
Provision a remote server:
  * Provision an Ubuntu server with the minimum amount of resources in a public cloud of your choosing.
  * Change the IP address in the [inventory](inventory) file to the IP address of your remote server.
  * Delete the line containing "ansible_port" in the [inventory](inventory) file.
  * Enable port 22 for SSH connection on the remote server.
  * Change the SSH username, password, and sudo password in the [inventory](inventory) file.

### 3) Open text editor
* Either open a text editor ("TextEdit" application in Mac or vim/nano in Linux) or use an Integrated Development Environment (IDE) like Visual Studio Code (VS Code). I would recommend using VS Code because the folders, text editor, and terminal (the three things you need to use Ansible) are all integrated together in one window. You can also install helpful extensions like "YAML" from Red Hat which helps you catch syntax errors in YAML files (which the majority of Ansible content is written in).

### 4) Clone Git repository
* If you haven't already, get this repository by first navigating to a folder in your terminal that you would like to store this project (`ls`, then `cd folder-name` in the terminal), then copying the line below into your terminal and hitting Enter:
  ~~~
  git clone https://github.com/jacobemery/ansible_tutorial
  ~~~

## Creating Ansible Content

### 5) Ansible 101
* <u>Note</u>: Click the links below just to get a basic understanding of these concepts, they will make a lot more sense once you start using them.
* [Ansible](https://www.ansible.com) (["What is Ansible?" video](https://www.youtube.com/watch?v=fHO1X93e4WA)) is free and open-source software from Red Hat that automates computer tasks. It's usually used for the configuration of servers, but it is very flexible and can be adapted to do many things.
* It uses Secure Shell (SSH) to connect to the servers it aims to configure. Only the local workstation needs to have Ansible installed (something that differentiates it from other DevOps tools like Chef). 
* To work properly, Ansible must have a few essential components: playbook(s), an inventory, and a configuration file.
  * [Playbooks](site.yaml) tell Ansible what to do.
  * The [inventory file](inventory) tells Ansible where to run.
  * The [configuration file](ansible.cfg) contains Ansible settings.
* Other Ansible components to know:
  * 'Tasks' are the basic units of Ansible work, which include a 'module' and some other information like a name, tags, conditionals, and others.
  * 'Modules' are official or community-created actions available in Ansible, e.g. shell commands with separated-out and standardized parameters so they are easier to read, understand, and use.
  * 'Roles' are sequences of tasks which fulfill a specifc function (like configuring the firewall). Check out the [roles](roles) folder with [install_packages](roles/install_packages/tasks/main.yaml) inside it. Then look for the reference to it in the [main playbook](site.yaml). See it?
  * 'Playbooks' are sequences of roles and tasks which accomplish a broader goal (e.g. configuring and deploying a web server).

### 6) Creating a role
* What we'll do now is create an Ansible role that starts and enables Apache to serve a basic web page.
* Apache is an http web server that will allow you to connect to your deployed website in a browser by the end of the tutorial.
* I've already written a main playbook called [site.yaml](site.yaml) and a role that installs the software for apache2 (http) on the server for you.
* To create a role, create folders that follow this required structure: `roles/custom-role-name/tasks/main.yaml`:
  ~~~
  roles
    └─ apache
        └─ tasks
            └─ main.yaml
  ~~~
* Open the main.yaml file that you just created and put three dashes at the top of the file `---`, which signals the start of the file. Hit enter twice.
* A key part of using Ansible effectively is being able to find modules that accomplish actions you want Ansible to do for you. We'll simulate that search now.
* Open up a web browser and Google "ansible module service" to find the Ansible module that manages services like httpd.
* You should find a website at docs.ansible.com with a page titled "ansible.builtin.service - Manage Services"
* Read this page, which describes how to use the module. Particularly useful are the examples near the bottom.
* We want to use this module to start and enable the service named "apache2".
* Read the parameters and pull from the examples to create two tasks within the file you created above that start and enable apache2.
* Use the [install_packages](roles/install_packages/tasks/main.yaml) role as a reference if you need another example.
* See the [answers file](answers/step6) to check your work. Give it a good try first though!

### 7) Editing a playbook
* Now that you've created a role and the tasks within it, you need to tell Ansible to call that role in a playbook.
* The goal is for well-written playbooks to be usable over and over again without causing any problems (known as idempotency).
* Open the [main playbook](site.yaml) and add another line below `- install_packages` in the same list format that matches the name of your new role folder that you just created in step 6.
* Great! Now Ansible will call the role you created, which runs a task that starts Apache. (We'll double check your work at the end of this step).
* One more thing needs to be done in this playbook here before we move on. And that is that you need to copy your (html) file to the web server. This is the file whose contents will show up in the web-browser when you type in the URL.
* As was mentioned earlier, playbooks run a combination of roles and tasks. We've run some roles, now we want the playbook to run a task afterwards.
* First, at the same indentation level as the "roles:" line, add a new line "post_tasks:" after your new role call's line.
* We'll need an Ansible module that copies [website/index.html](website/index.html) to `/var/www/html/index.html` on the server.
* Can you find the correct Ansible module and use it to complete this task?
* See the [answers file](answers/step7) to check your work.

### 8) Editing the HTML file
* Once this playbook runs, it will tell Ansible to install apache, start it, then copy over the HTML file to the web server... But before we do that, first we need to customize the HTML file with what you want your pseudo website to say!
* Open the [index.html](website/index.html) file in the [website](website) foler.
* Replace "Website-Title-Here" with what you want the website to be called. This will show up on the tab.
* Replace "Webiste-Content-Here" with what you want the website's homepage to say.
* Alternatively, copy or type out any HTML you'd like to show up on the website here. 
* Go wild! Be creative!

## Running Ansible

### 9) ansible-playbook
* Ok! We're ready to run Ansible.
* First, make sure to save all the files you've changed if you haven't already.
* In your terminal, navigate to the [home folder](ansible_tutorial) of your cloned repository, and run the following command:
  ~~~
  ansible-playbook site.yaml
  ~~~
* Watch Ansible as it runs. 
* If all goes smoothly, you should be able to open a web browser and type in http://localhost:8080 as the URL and see your website!
* Likely though, you will encounter some errors...

### 10) Troubleshooting
* When you encounter errors while running Ansible, here are some steps you can take.
* Read the error message and find out the answers to: 
  * What type of error is it? 
  * What file is the error occurring in? 
  * Where in that file is the error occurring? 
  * For example, if it's a syntax error, open the file it is referencing, go to the task it is complaining about, see if the error message gives you any clues about the problem, and then try to correct the error. 
  * A lot of the time it's an indentation issue with YAML. I highly recommend using an IDE like VS Code where you can install extensions to catch these errors early.
* Google the error message. Try including "ansible" in the search and if it's not giving you what you need, try it without "ansible". A lot of the time it's a Linux error that is broader than just Ansible. Click on and read some of the results. 
  * First see if the problem they are describing matches the problem you are having. 
  * If it seems right, or is close, read the information there. If it doesn't get you the right answer, maybe it gets you closer.
  * If you think it could be the answer, incorporate it into your playbook and re-run it. 
  * If I'm testing just one or a few tasks, sometimes I'll add a "test" [tag](#Additional-Optional-Steps) to them in order to be able to selectively run just those.
* Search the documentation for your specific error at docs.ansible.com if needed.
* Try to correct the error and re-run the Ansible playbook (step 9), maybe this time with more verbosity by adding v options to the command like this: 
  ~~~
  ansible-playbook site.yaml -v
  ~~~
* The more v's you add, the more information Ansible will give you. This can help you get to the root of the problem. This playbook should be idempotent, meaning you can run it as many times as you want and it won't create problems.

## Clean-Up

### 11) Kill container
* If you would like to do some additional steps and learn more about Ansible, skip this step until after doing the optional steps below.
* To tear down your web server, in your terminal run the command: 
  ~~~
  docker kill rastasheep/ubuntu-sshd:14.04
  ~~~

## Additional Optional Steps
### 12) Tags
* What if you decide you don't like what you put on your website? Or you spot a typo? What can you do? Do you have to run the playbook from the beginning? Nope! Thankfully, you can use tags.
* Each task can include one or more 'tags' which help the user selectively run individual tasks or roles within a playbook.
* Try adding another line to the copy 'HTML to web server' task in [site.yaml](site.yaml) just below "name" and just above "copy" at the same indentation:
  ~~~
  tags: update_website
  ~~~
* Now you can update the website content in [index.html](website/index.html) by changing that file's contents, and running the playbooks with tags, like this:
  ~~~
  ansible-playbook site.yaml --tags update_website
  ~~~
* If you want to use multiple tags, enclose them in single or double quotes, and separate with commas. For example: 
  ~~~
  ansible-playbook site.yaml --tags 'start_apache,update_website'
  ~~~
### 13) Loops
* What if you wanted to install more packages on your web server? Maybe you're new to Linux and don't know how to quit vim so you want to use nano instead...
* In [the install_packages playbook](roles/install_packages/tasks/main.yaml), you could use a loop to install any number of packages instead of just one.
* Replace `apache2` with `"{{ item }}"` (include the double quotes).
* Then, at the bottom of that task, at the same indentation as the `name` parameter, add the following (with the single quotes):
  ~~~ 
  loop: '{{ packages }}'
  ~~~
* This is slightly confusing with the quotes, so make sure to look at the [answer](answers/step13-1) to check your work.
* Next, you'll have to define the `packages` variable that you just used. You can do this in a number of ways.
* One way of doing it is by specifying it at the playbook level.
* Open [site.yaml](site.yaml). At the same indentation level, below `gather_facts: no`, add the following:
  ~~~
  vars:
    packages: ['apache2', 'nano']
  ~~~
* See the [answer](answers/step13-2) to check your work.
* Try it out by running the [main playbook](site.yaml) with tags. (Find the correct tag by opening the [install_packages role](roles/install_packages/tasks/main.yaml).
### 14) Debug messages
* Let's say that at the end of the playbook, you'd like Ansible to print out a URL for the user to easily click on to bring them to their new website.
* To print information to the terminal, Ansible uses the `debug` module.
* Do a quick Google search to find it.
* Open the [main playbook](site.yaml) and create a new task after copying the html to the web server that prints a message which includes `localhost:80`.
* You could include a tag or two to selectively run just that task too.
* See the [answers file](answers/step14) to check your work.
### 15) Ad-hoc commands
* There are some things that Ansible modules have not yet been created to do, or that the existing Ansible modules are not flexible enough to accomodate.
* For these tasks, use the Ansible module `command` or `shell` modules to run any shell command. 
* For example:
  ~~~
  - name: Check VM DNS resolution
  tags: dns
  command: dig +short hostname.base_domain  | tail -n1
  ~~~
* That's all I've got! I hope this tutorial was helpful and that you've come away with some basic understanding of Ansible and how it works.
* Remember to return to step 11 to clean up.