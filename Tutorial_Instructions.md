# Tutorial Instructions
Here you will find step-by-step instructions to walk you through this Ansible tutorial.

## Table of Contents
* [Setup](#Setup)
* [Creating Ansible Content](#Creating-Ansible-Content)
* [Run Ansible](#Run-Ansible)
* [Clean-up](#Clean-up)
* [Additional Optional Steps](#Additional-Optional-Steps)

## Setup

### 1) Install Ansible:
* <u>For Mac users</u>:
  * Open the terminal of your local workstation:
    * Hit command + spacebar to open Spotlight search, type "terminal" and hit return key.
  * Install homebrew package manager:
    * Copy/paste this into terminal and hit Enter, type in your password, hit Enter again, may have to hit Enter one more time.
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
  * Install Python3 (Ansible needs it)
      * In the terminal, copy/paste the following and hit Enter:
        ~~~
        brew install python3
        ~~~
  * Install Ansible:
      * In the terminal, copy/paste the following and hit Enter: 
        ~~~
        brew install ansible
        ~~~
* <u>For Linux users</u>:
    * Open the terminal (Ctrl+Alt+T) of your local workstation.
    * Install Python3 (Ansible needs it): 
      * In the terminal, copy/paste one of the following (depending on your distribution) hit Enter, type in your sudo password, hit Enter again:
        ~~~
        sudo apt install -y python3
        ~~~
        or 
        ~~~
        sudo yum install -y python3
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
* <u>Important Note</u>: Usually you would be configuring a remote server for your website (see [Advanced](#<u>Advanced</u>---provision-a-remote-server:) below), but assuming you don't have one ready, we'll be setting up a Docker container locally that will act as a "remote server" for us to configure ([Recommended](#<u>Recommended</u>---start-Docker-container:)).
* #### <u>Recommended</u> - start Docker container:
  * For Mac users:
    * In the terminal, copy/paste the following and hit Enter. If needed, type in your password and hit Enter again:
      ~~~
      brew install docker
      ~~~
    * Open Docker:
      * Hit command + spacebar to open Spotlight search, type "docker" and hit return key.
      ~~~
    * Once Docker starts up, in the terminal, copy/paste the following, hit Enter, type in your password and hit Enter again:
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
    * Once that installs, in the terminal, copy/paste the following, hit Enter, type in your password, and hit Enter again: 
      ~~~
      sudo systemctl start docker
      ~~~
    * In the terminal, copy/paste the following, hit the Enter key, type in your password, and hit Enter again:
      ~~~
      sudo docker run -d -p 8080:22 -p 80:80 rastasheep/ubuntu-sshd:14.04
      ~~~
* #### <u>Advanced</u> - provision a remote server:
    * Do this step instead of the above steps if you would like to provision a remote server instead of using a local Docker container.
    * Provision an Ubuntu server with the minimum amount of resources in a public cloud of your choosing.
    * Change the IP address in the [inventory](inventory) file to the IP address of your remote server.
    * Delete the line containing "ansible_port" in the [inventory](inventory) file.
    * Enable port 22 for SSH connection on the remote server.
    * Change the SSH username, password, and sudo password in the [inventory](inventory) file.

### 3) Open text editor
* Either open a text editor ("TextEdit" application in Mac or vim/nano in Linux) or use an Integrated Development Environment (IDE) like Visual Studio Code (VS Code). 
* I would recommend using something like [VS Code](https://code.visualstudio.com/download) because the folders, text editor, and terminal (the three things you need to use Ansible) are all integrated together in one window.
* With VS Code, you can also install helpful extensions like ["YAML" from Red Hat](https://marketplace.visualstudio.com/items?itemName=redhat.vscode-yaml) which helps you catch syntax errors in YAML files (which the majority of Ansible content is written in).

### 4) Clone Git repository
* If you haven't already, get this repository by first navigating to a folder in your terminal that you would like to store this project (`ls`, then `cd folder-name` in the terminal).
* Then copy the line below into your terminal and hit Enter:
  ~~~
  git clone https://github.com/jacobemery/ansible_tutorial
  ~~~

## Creating Ansible Content

### 5) Ansible 101
* <u>Note</u>: Click the links below just to get a basic understanding of these concepts, they will make a lot more sense once you start using them.
* [Ansible](https://www.ansible.com) (["What is Ansible?" video](https://www.youtube.com/watch?v=fHO1X93e4WA)) is free, open-source software from Red Hat that automates tasks on computers. It's usually used for the configuration of servers, but it is very flexible and can be adapted to do many things.
* It uses Secure Shell (SSH) to connect to the servers it aims to configure. Only the local workstation needs to have Ansible installed (something that differentiates it from other DevOps tools like Chef). 
* To work properly, Ansible must have a few essential components: playbook(s), an inventory, and a configuration file.
  * [Playbooks](site.yaml) tell Ansible what to do.
  * The [inventory file](inventory) tells Ansible where to run.
  * The [configuration file](ansible.cfg) contains Ansible settings.
* Other Ansible components to know:
  * 'Tasks' are the basic units of Ansible work, which include a 'module' and some other information like a name, tags, conditionals, and others.
  * 'Modules' are official or community-created actions available in Ansible, e.g. shell commands with separated-out and standardized parameters so they are easier to read, understand, and use.
  * 'Roles' are sequences of tasks which fulfill a specifc function (like configuring the firewall). Check out the [roles](roles) folder with [install_packages](roles/install_packages/tasks/main.yaml) inside it. Then look for the reference to it in the [site.yaml playbook](site.yaml). See it?
  * 'Playbooks' are sequences of roles and tasks which accomplish a broader goal (e.g. configuring and deploying a web server).

### 6) Customize your soon-to-be website
* The end product of this tutorial is a very basic website. So first, let's customize it!
* Open the [index.html](website/index.html) file in the [website](website) foler.
* Replace "`Website-Title-Here`" with what you want the website to be called. This will show up on the web browser tab.
* Replace "`Webiste-Content-Here`" with what you want the website's homepage to say.
* Alternatively, copy/paste or type out any HyperText Markup Language (HTML) content you'd like to show up on the website here. 
* Go wild! Be creative!

### 7) Create a task
* Now that we have the website's HTML content ready, we'll need to tell Ansible to copy it over to the web server so that it shows up when you visit the URL in a web browser.
  * <u>Remember</u>: 'Tasks' are the basic units of Ansible work, which include a 'module' and some other information like a name, tags, conditionals, and others.
* A key part of using Ansible effectively is being able to find the correct module that accomplishes the action you need Ansible to do for you. Let's simulate that search now!
  * Open up a web browser and Google "ansible module copy" to find the Ansible module that copies files from the local workstation to Ansible-managed remote servers.
  * You should find a website at https://docs.ansible.com with a page titled "ansible.builtin.copy - Copy files to remote locations"
  * Read this page, which describes how to use the module. 
  * The examples near the bottom are very helpful. That first one looks close to what we want to use, select and copy it.
* Now we need to incorporate it into the playbook.
  * Open up the [site.yaml playbook](site.yaml).
  * Create a new line under "`- install_packages`". Line-up the text cursor with "`roles:`" above and type "`post_tasks:`" and hit Enter. 
    * <u>Remember</u>: 'playbooks' are sequences of roles and tasks which accomplish a broader goal.
  * Hit the spacebar twice so that the text cursor lines up with the dash in the "`- install_packages`" line above.
  * On that new line, paste the example copy module task from the Ansible website. 
    * The "`name`" line from the example task should be in the correct place, but the other lines may not be.
    * Go back to the Ansible website with the copy module and indent the lines to match what you see in the example. You can use either tab or two spaces to indent. 
    * <u>Note on YAML syntax</u>: indentation has to be perfect with YAML files. If something is not indented properly, it will cause an error. I highly recommend using an IDE like VS Code where you can install extensions to catch these errors early (the "YAML" extension from Red Hat is great).
* Now that we have a properly formatted task in our playbook, we now need to customize it to meet our specific needs.
  * Replace "Copy file with owner and permissions" in the "`name:`" line from the example with something like: "Copy index.html to web server."
  * <u>Note on task names</u>: The "`name:`" parameter has no effect on the outcome of the task. It is simply your description of the task being run. It is, however extremely important in terms of documenting your playbooks. Both you and other users of your playbooks in the future will either thank or curse you based on the quality of your descriptions. I recommend using full sentences to give the user the best possible picture of the task. This is what shows up on the terminal when you run the playbook (which you will see later).
  * Replace the example file path in the source (`src:`) line with the location of [the HTML file](website/index.html) we want copied over: "`website/index.html`"
  * Next, replace the destination (`dest:`) parameter with the file path where we need [the HTML file](website/index.html) to be place on the remote server so that it is accessible via the internet: "`/var/www/html/index.html`"
  * Because we do not need to set the permissions/ownership of this file, delete the rest of the lines from the example (`owner`, `group`, and `mode` parameters).
* Great! You created your first Ansible task. 
* Check your work [here](answers/step7). Make sure to double-check your indentation.

### 8) Create a role
* You've customized your website's contents and told Ansible to copy the HTML file over to the web server. We're almost ready to run our playbook.
* Before we can do that and visit our website though, we need one more very essential part: HTTP
  * You may have seen Hyper-Text Transfer Protocol at the beginning of a URL in a web-browser (like this: `http://`). That's because it is the manner by which much of the internet exchanges information.
* What we'll do now is create an Ansible role that starts and enables HTTP to serve a basic web page.
    * <u>Remember</u>: 'Roles' are sequences of tasks which fulfill a specifc function.
  * Apache is HTTP server software that will allow you to connect to your deployed website in a browser by the end of this tutorial. Just what we need!
  * I've already written a role that installs the software for apache2 on the server for you. Check it out [here](roles/install_packages/tasks/main.yaml) as a role example.
* The next step is create a role which starts and enables the Apache software.
  * The first step to making a new role, is to create a series folders within each other that follow this required structure: `roles/role_name/tasks/main.yaml`. Replace `role_name` with something related to apache or http.
    ~~~
    roles
      └─ role_name
          └─ tasks
              └─ main.yaml
    ~~~
    * <u>In terminal</u>:
      * Navigate to the folder you'd like to create a folder in (`ls`, then `cd folder-name`)
      * Create the new folder with `mkdir new-folder-name`, and so on, until you make the last folder: "tasks". 
      * Type `cd tasks` and then `touch main.yaml` and hit Enter to create the file.
    * <u>In VS Code</u>: do this by right clicking on the folder you'd like to make a folder in and click "New Folder". Type in the name, hit Enter, and so on until "tasks", then click "New File" to create "main.yaml".
  * Now we'll start creating the role's tasks.
    * First, open the main.yaml file that you just created and put three dashes at the top of the file, like this: "`---`", which signals the start of Ansible content to be run. Hit Enter twice to move to a new line.
    * Remember how I said that finding the correct Ansible module is a core skill for using Ansible effectively? 
    * See if you can find an Ansible module on your own that manages software services like http.
    * Find it?
    * We want to use this module to start and enable the service named "apache2". Read the parameters and pull from the examples to create two tasks which tell Ansible to do just that. 
    * Use the [install_packages](roles/install_packages/tasks/main.yaml) role as a reference if you need an example.
  * Check your work [here](answers/step8). Give it a good try first though!
* Ok so we've created the roles tasks, but we're not quite done yet. We also need to tell Ansible that we'd like it to run this role in the playbook.
  * Open the [site.yaml](site.yaml) playbook and, in the same format and indentation as "`- install_packages`", add a line that calls your new role's custom name that you gave it in the folder structure from above.
* Great! Now Ansible will call the role you created that starts and enables Apache.

## Run Ansible

### 9) Run Ansible playbook
* Ok! We're ready to run the Ansible playbook we've been working on.
* First, make sure to save all the files you've changed if you haven't already.
* In your terminal, navigate to the [top-level folder](ansible_tutorial) of your cloned repository, and run the following command:
  ~~~
  ansible-playbook site.yaml
  ~~~
* Watch Ansible as it runs. 
* If all goes smoothly, you should be able to open a web browser and type in the following as the URL to see your new website!
  ~~~
  http://127.0.0.1:80
  ~~~
* Likely though, you will encounter some errors...

### 10) Troubleshooting
* When you encounter errors while running Ansible, here are some steps you can take:
* Read the error message and find out the answers to: 
  * What type of error is it? 
  * What file is the error occurring in? 
  * Where in that file is the error occurring? 
  * For example, if it's a syntax error, open the file it is referencing, go to the task it is complaining about, see if the error message gives you any clues about the problem, and then try to correct the error. 
  * A lot of the time it's an indentation issue with YAML. I highly recommend using an IDE like VS Code where you can install extensions to catch these errors early.
* Google the error message. Try including "ansible" in the search and if it's not giving you what you need, try it without "ansible". A lot of the time it's a Linux error that is broader than just Ansible. Click on and read some of the results. 
  * First see if the problem they are describing matches the problem you are having. 
  * If it seems right, or is close, read the information there. If it doesn't get you the right answer, maybe it gets you closer.
  * If you think it could be the answer, incorporate it into your playbook and re-run it the ansible-playbook command in step 9. 
  * If I'm testing just one or a few tasks, sometimes I'll add a "test" [tag](#Additional-Optional-Steps) to them in order to be able to selectively run just those.
* Search the documentation for your specific error at https://docs.ansible.com if needed.
* Try to correct the error and re-run the Ansible playbook (step 9), but this time with more verbosity by adding the v option to the command like this: 
  ~~~
  ansible-playbook site.yaml -v
  ~~~
  * The more v's you add, the more information Ansible will give you. This can help you get to the root of the problem. This playbook should be idempotent, meaning you can run it as many times as you want and it won't create problems.

## Clean-Up

### 11) Kill container
* If you would like to do some additional steps to learn more about Ansible, skip this step until after doing the optional steps below.
* To tear down your web server, in your terminal run the command: 
  ~~~
  sudo docker kill rastasheep/ubuntu-sshd:14.04
  ~~~

## Additional Optional Steps
### 12) Tags
* What if you decide you don't like what you put on your website? Or you spot a typo? What can you do? Do you have to run the playbook from the beginning? Nope! Thankfully, you can use tags.
* Each task can include one or more 'tags' which help the user selectively run individual tasks or roles within a playbook.
* Try adding the line below to the 'Copy index.html to web server.' task in [site.yaml](site.yaml) between "`name:`" and "`ansible.builtin.copy:`", at the same indentation:
  ~~~
  tags: update_website
  ~~~
* Let's try it out! 
  * First, update the website content in [index.html](website/index.html).
  * Next, run the playbook again, with a tag this time:
    ~~~
    ansible-playbook site.yaml --tags update_website
    ~~~
  * Watch Ansible as it runs and updates the website automatically.
  * Refresh the web page at:
    ~~~
    http://127.0.0.1:80
    ~~~
* If you want to use multiple tags, enclose them in single or double quotes, and separate with commas. For example: 
  ~~~
  ansible-playbook site.yaml --tags 'start_apache,update_website'
  ~~~
### 13) Loops
* Check out the [install_packages role](roles/install_packages/tasks/main.yaml), see the `"{{ item }}"` and `loop: "{{ packages }}"` lines?
* This is the syntax for Ansible's simple looping function. Check out this [helpful documentation page](https://docs.ansible.com/ansible/latest/user_guide/playbooks_loops.html) for more information on loops.
* So, what if you wanted to, for example, install more packages on your web server besides just Apache? Maybe you're new to Linux and don't know how to quit vim yet so you want to use nano instead...
* Since there's already a looping mechanism in place, all we have to do is add package names to the "`packages`" list in the [playbook](site.yaml).
  * Open [site.yaml](site.yaml). Add nano to the packages list like so:
    ~~~
    vars:
      packages: ['apache2', 'nano']
    ~~~
  * Try it out by running the [main playbook](site.yaml) with tags. (Find the correct tag by opening the [install_packages role](roles/install_packages/tasks/main.yaml).
* What else could benefit from looping? Maybe you want to start and enable more software services? Play around with loops a bit if you'd like.
### 14) Debug messages
* To print information to the terminal, Ansible uses the `debug` module.
* Let's say that at the end of the playbook, you'd like Ansible to print out a URL for the user to easily click on to bring them to their new website.
  * Do a quick Google search to find the debug module.
  * Open the [main playbook](site.yaml) and create a new task after copying the html to the web server that prints a message which includes `localhost:80`.
  * You could include a tag or two to selectively run just that task too.
* Check your work [here](answers/step14).
### 15) Ad-hoc commands
* There are some things that Ansible modules have not yet been created to do, or that the existing Ansible modules are not flexible enough to accomodate.
* For these tasks, use the Ansible modules `command` or `shell` to run any shell command. 
* For example:
  ~~~
  - name: Check server DNS resolution.
    tags: dns
    command: dig +short hostname.base_domain  | tail -n1
  ~~~
* That's all I've got! I hope this tutorial was helpful and that you've come away with some basic understanding of Ansible and how it works.
* Remember to return to step 11 to clean up.
