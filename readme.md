# Quick start with this code

1. Ensure that Virtualbox is installed on your system
2. Ensure that Vagrant is installed on your system
2. If you're on a Windows workstation:
    - Ensure that virtualisation is enabled in the BIOS
    - Ensure that HyperV is disabled in the 'Turn Windows Features On and Off' menu

3. git clone https://github.com/DanielJohnHarty/DevopsCICDPipeline.git
4. cd DevopsCICDPipeline/gitlab-deploy
5. Vagrant up --provision

### This process may take some time. In the meantime, you can update your /etc/hosts file to map 10.20.20.2 to gitlab.local and 10.20.20.3 to gitlab.runner.local

## When youre VMs are up and running:

6. Navigate to http://gitlab.local and register with the new gitlab instance.
7. Create a new Gitlab project.
8. Within the Gitlab project, create a runner token for http://gitlab.local and note it down
9. Access your Gitlab runner instance using the command:
    - vagrant ssh <gitlab_server ID>
    - If you don't know the ID of your gitlab_server VM, you can find it out with the command:
        - vagrant global-status
10. Register your noted runner token for http://gitlab.local using the command:
    - gitlab-runner register
    - Follow the image below answering the posed registration questions:
    ![](imgs\RegisteringRunner.png)

