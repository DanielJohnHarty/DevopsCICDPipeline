# *Setting up continuous integration and continuous deployment* pipeline for a python project using GitLab, Vagrant and Ansible

### Steps to follow:


1. Create your python project. Include at least 1 set of tests
   - I have used an existing Python project https://github.com/DanielJohnHarty/website_monitoring_app 


2. Use Vagrant and Ansible to configure 2 environments in your infrastructure
   - One to host a Gitlab instance on. It will be a private server.
     - This is an alternative to using a solution like Github, where code, whether private and secure or not,; resides in a different network.
      - We'll need to adjust the local machine's /etc/hosts file to direct local machine 'gitlab.local' browser traffic to the hosted Gitlab instance on the VM

   - One for your runner, which will perform post git push pipeline/jobs automatically


       


# Use Vagrant and Ansible to configure 2 environments in your infrastructure

1. Create a folder called **gitlab-deploy** and add a Vagrantfile file inside it. Here we instruct Vagrant to declare our gitlab_server and our gitlab_runner virtual machines. We define the OS and some networking parameters.

    After the vms are declared in Vagrant, we instruct Vagrant to provision the VMs using the playbooks stored in the playbooks directory.

2. Create a folder called **playbooks** and add a run.yml file inside it. Here we broadly define the two infrastructural entities:
  - Gitlab runner
    - To perform the jobs in the pipeline
  - Gitlab server
    - To host the gitlab server

The role parameter of both these entities describes where exactly to find the Anisble instruction for building the VM.

3. Add subdirectory in **playbooks** for install, healthchecks and runner, each with a main.yml file. These yml files include instructions for Ansible to execute to build the VMs. Configure them to meet the needs of your project.

***Make sure that that the roles in the run.yml are correctly synced with the the ansible file's location in the directory structure.***

## ***From this point you, if you are working on a windows PC, ensure that:***
  1. Your workstations virtualization is **enabled** in its start up BIOS
  2. The **HyperV feature is disabled**. Check this in the 'Turn Windows Features On and Off' application.

# At this point, you're ready to boot build use vagrant to load and provision your VMs.

Commands to help do this are:

- **vagrant up** -> [Creates & configs VM using Ansible](https://www.vagrantup.com/docs/cli/up.html)
- **vagrant global-status** -> Lists VMs and their status
- **vagrant rsync** -> [Forces resync of folders](https://www.vagrantup.com/docs/cli/rsync.html)
- **vagrant reload** -> [Halt then UP](https://www.vagrantup.com/docs/cli/reload.html)
- **vagrant provision** -> [Runs any configured provisioners against the running Vagrant managed machine, useful for incremental changes](https://www.vagrantup.com/docs/cli/provision.html)
- **vagrant halt** -> This command shuts down the running machine Vagrant is managing.

- **Vagrant rsync -> vagrant provision is applies changes to running VM**

# Accessing your Gitlab instance

Gitlab is usually accessed via a browser but your Gitlab instance is not on the internet, it's on a VM on your machine.

This means you can access it in 2 ways:
  1. Browsing to the IP of your gitlab server (specified in this example as 10.20.20.2)
  2. Update your /etc/hosts file so that browsing to a particular url will redirect you to the IP of the Gitlab server.
    
      - On Windows that means:
        - **Update your /etc/hosts file by opening a cmd as administrator and running:**
          *notepad "C:\Windows\System32\drivers\etc\hosts"*

            - Once inside you need to add an 'ip gitlab.local' configuration. This means that brosing to gitlab.local on your machine will forward you to the IP. The ip should match that set in the Vagrantfile as the server.vm.network (in this case "10.20.20.2")
      - On Linux the process is much the same but the filepath to update is:
        - /etc/hosts



***
## **At this point, you should have a gitlab server and a gitlab runner each running on different VMs. You should have a project ready to connect to this pipeline and your /etc/hosts file updated to redirect from gitlab.local to the hosted gitlab instance.**
***

### In the next step I'll move an existing python project to the Gitlab server running on our gitlab_server. The steps for this are analagous with any git repository move of this kind:

  1. Create a new blank repository in the target git host
    - In this case, by navigating to http://gitlab.local
  2. Take your existing


  - Create a gitlab project and add our python project to it
  - Register the gitlab runner VM as the gitlab runner of our project using the repo token on our gitlab project


### Setting up your Gitlab instance

1. Goto http://gitlab.local
2. Register with a username and password
3. Create a new blank project
4. Push you project to it
  - This Was only possible for me by cloning the blank project to a new directory locally and then copy pasting the project files to the cloned blank directory. It should be possible to take a project and add the new gitlab project as a remote but I couldn't get this to work.

## Creating a Continuous Integration/ Continuous Deployment pipeline

In order to do this we need to create a runner token in our gitlab_server then register our gitlab_runner to the gitlab_server. To do that:

1. Go to your Gitlab project>settings>CI/CD>Runners
2. Get a runner token from the Runners menu, ![get your runner token](imgs\GitlabRunnerToken.png)


3. Now we use this token to register our runner to our gitlan server.
      - Get your gitlab_runner instance id with the command **vagrant global-status**
      - Once you have that, ssh into it with the command **vagrant ssh <id>**
      - The command **gitlab-runner register** will start a process to register this as a runner of your Gitlab Server
      - ***Follow follow the details in the image below to finish registering your runner***
      ![](imgs\RegisteringRunner.png)

## At this point you have a Gitlab server with your project on. The proejct has a runner associated to it which is runing on the gitlab_runner VM.

next steps:

  - Set up the tasks which are performed on push
    - run test file
    - run code standardisation tool like black