# *Setting up continuous integration and continuous deployment* pipeline for a python project using GitLab, Vagrant and Ansible

### Steps to follow:


1. Create your python project. Include at least 1 set of tests
   - I have used an existing Python project https://github.com/DanielJohnHarty/website_monitoring_app 


2. Use Vagrant and Ansible to configure 2 environments in your infrastructure
   - One for your python project development
     - This is to facilitate new developers joing the team

   - One for your runner, which will perform post git push pipeline/jobs automatically
     - This environment will host Gitlab and registered to gitlab as a runner host

       - We'll need to adjust the local machine's /etc/hosts file to direct local machine 'gitlab.local' browser traffic to the hosted Gitlab instance on the VM


# Use Vagrant and Ansible to configure 2 environments in your infrastructure

1. Create a folder called **gitlab-deploy** and add a Vagrantfile file inside it. Here we instruct Vagrant to declare our gitlab_server and our gitlab_runner virtual machines. We define the OS and some networking parameters.

After the vms are declared in Vagrant, we instruct Vagrant to provision the VMs using the playbooks stored in the playbooks directory.

2. Create a folder called **playbooks** and add a run.yml file inside it. Here we broadly define the two infrastructural entities:
  - Gitlab runner
    - To perform the jobs in the pipeline
  - Gitlab server
    - To host the gitlab server

The role parameter of both these entities describes where exactly to find the Anisble instruction for building the VM.

3. Add subdirectory in **playbooks** for install, healthchecks and runner, each with a main.yml file. These yml files include instructions for Ansible to execute commands to build the VMs. Configure them to meet the needs of your project.

***Make sure that that the roles in the run.yml are correctly synced with the the ansible file's location in the directory structure.***

4. At this point, you're ready to boot build use vagrant to load and provision your VMs.

Commands to help do this are:

- **vagrant up** -> [Creates & configs VM using Ansible](https://www.vagrantup.com/docs/cli/up.html)
- **vagrant global-status** -> Lists VMs and their status
- **vagrant rsync** -> [Forces resync of folders](https://www.vagrantup.com/docs/cli/rsync.html)
- **vagrant reload** -> [Halt then UP](https://www.vagrantup.com/docs/cli/reload.html)
- **vagrant provision** -> [Runs any configured provisioners against the running Vagrant managed machine, useful for incremental changes](https://www.vagrantup.com/docs/cli/provision.html)
- **vagrant halt** -> This command shuts down the running machine Vagrant is managing.

***Vagrant rsync -> vagrant provision is applies changes to running VM***

5. **Update your /etc/hosts file by opening a cmd as administrator and running:**
   *notepad "C:\Windows\System32\drivers\etc\hosts"*

    - Once inside you need to add an 'ip gitlab.local' configuration. This means that brosing to gitlab.local on your machine will forward you to the IP. The ip should match that set in the Vagrantfile as the server.vm.network (in this case "10.20.20.2")

### At this point, you should have a gitlab server and a gitlab runner each running on different VMs. You should have a project ready to connect to this pipeline and your /etc/hosts file updated to redirect from gitlab.local to the hosted gitlab instance.

So whats left:

  - Create a gitlab project and add our python project to it
  - Register the gitlab runner VM as the gitlab runner of our project using the repo token on our gitlab project