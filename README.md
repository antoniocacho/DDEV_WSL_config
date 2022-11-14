# DDEV_WSL_config
walk through of the configuration of ddev in WSL

Patheon/WSL project setup workflow

Note the paths in prompt prefixes, even if they are not usual in linux (for having them just append to ~/.bashrc this line: PS1='\w\$ '):

00. Install Docker-Desktop, WSL2, then launch WSL:
    PS C:\> choco install -y docker-desktop

01. Install WSL2 then launch it:
    PS C:\> wsl --install
    (reboot)
    PS C:\> wsl --set-default-version 2
    PS C:\> wsl

02. Ensure xdg-utils is installed:
    \\WSL$ sudo apt-get update && sudo apt-get install -y xdg-utils

03. Install ddev:
    \\WSL$ Set-ExecutionPolicy Bypass -Scope Process -Force;
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072;
iex ((New-Object System.Net.WebClient).DownloadString('https://raw.githubusercontent.com/drud/ddev/master/scripts/install_ddev_docker_desktop_wsl2.ps1'))

    Change the current directory to the linux user home (~$):
    \\WSL$ cd ~

04. Register in Pantheon to get an account with the Umami demo site;
    Make backups of site's db and files;

05. Ensure same user name and email in pantheon's and wsl's git:
    ~$ git config --global user.name "Anita Pantheon"
    ~$ git config --global user.email anita@xpto.io

06. Generate the .ssh folder with the respective key files (empty passphrase):
    ~$ ssh-keygen

07. Start the SSH agent:
    ~$ eval `ssh-agent`

08. Add the newly created key to the ssh-agent:
    ~$ ssh-add ~/.ssh/id_rsa

09. Copy the public key in the file .ssh/id_rsa.pub to the Pantheon site;

10. Log in to Pantheon Dashboard, go to the SSH Keys tab of User Profile's Personal Settings;

11. Click Add New Key;

12. Paste the public key into the box, click Save;

13. Get Pantheon machine token (ignore Terminus configuration);

14. Add the machine token to the global ddev configuration:
    ~$ code ~/.ddev/global_config.yaml

15. In this file locate and set the field:
    web_environment:
        - TERMINUS_MACHINE_TOKEN=somepantheontoken

16. Clone project directory and preliminary setup:
    ~$ git clone ssh://<projectUrl> -b master myProjectName

17. Make the project directory the current one:
    ~$ cd <projDir>

18. Initialize ddev into <projDir>/.ddev/config.yaml:
    projDir$ ddev config            (specify 'web' and 'drupal 9')

19. Check .ddev/config.yaml about project type, docroot, etc;

20. Start docker containers and ssh agent:
    projDir$ ddev start

21. Install drush:
    projDir$ ddev composer require drush/drush

22. Pantheon configuration:
    projDir$ cp .ddev/providers/pantheon.yaml.example .ddev/providers/pantheon.yaml
    
23. Pantheon dashboard>sites>proj, get <proj_uuid>.<environment> from the URL:
    dashboard.pantheon.io/sites/437e6ad3-8303-45ca-8487-8a441710cd9c#dev/code
    => project: 437e6ad3-8303-45ca-8487-8a441710cd9c.dev

24. Open pantheon.yaml ...
    projDir$ code .ddev/providers/pantheon.yaml.example

25. Locate and set the following parameter (using specific data, not <placeholders>):
    environment_variables:
        project: <proj_uuid>.<environment>

26. Update ddev to connect to the specific Pantheon project:
    projDir$ ddev restart

27. Ensure ssh credentials:
    projDir$ ddev auth ssh

28. Download db&files from the Pantheon project:
    projDir$ ddev pull pantheon

29. Check proj alive:
    projDir$ ddev launch        (or visit the URLs given by ddev describe)
    
30. If neccessary, refresh the cache:
    projDir$ ddev drush cr

31. Upload after changes:
    projDir$ ddev push pantheon
