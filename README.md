# DDEV_WSL_config
walk through of the configuration of ddev in WSL

Patheon/WSL project setup workflow

Note the paths in prompt prefixes, even if they are not usual in linux (for having them just append this line to ~/.bashrc: PS1='\w\$ '):

00. Install Docker-Desktop, WSL2, then launch WSL:<br>
    PS C:\> choco install -y docker-desktop<br>

01. Install WSL2 then launch it:<br>
    PS C:\> wsl --install<br>
    (reboot)<br>
    PS C:\> wsl --set-default-version 2<br>
    PS C:\> wsl<br>

02. Ensure xdg-utils is installed:<br>
    \\WSL$ sudo apt-get update && sudo apt-get install -y xdg-utils<br>

03. Install ddev:<br>
    \\WSL$ Set-ExecutionPolicy Bypass -Scope Process -Force;
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072;
iex ((New-Object System.Net.WebClient).DownloadString('https://raw.githubusercontent.com/drud/ddev/master/scripts/install_ddev_docker_desktop_wsl2.ps1'))

    Change the current directory to the linux user home (~$):<br>
    \\WSL$ cd ~<br>

04. Register in Pantheon to get an account with the Umami demo site;<br>
    Make backups of site's db and files;<br>

05. Ensure same user name and email in pantheon's and wsl's git:<br>
    ~$ git config --global user.name "Anita Pantheon"<br>
    ~$ git config --global user.email anita@xpto.io<br>

06. Generate the .ssh folder with the respective key files (empty passphrase):<br>
    ~$ ssh-keygen<br>

07. Start the SSH agent:<br>
    ~$ eval `ssh-agent`<br>

08. Add the newly created key to the ssh-agent:<br>
    ~$ ssh-add ~/.ssh/id_rsa<br>

09. Copy the public key in the file .ssh/id_rsa.pub to the Pantheon site;<br>

10. Log in to Pantheon Dashboard, go to the SSH Keys tab of User Profile's Personal Settings;<br>

11. Click Add New Key;<br>

12. Paste the public key into the box, click Save;<br>

13. Get Pantheon machine token (ignore Terminus configuration);<br>

14. Add the machine token to the global ddev configuration:<br>
    ~$ code ~/.ddev/global_config.yaml<br>

15. In this file locate and set the field:<br>
    web_environment:<br>
        - TERMINUS_MACHINE_TOKEN=somepantheontoken<br>

16. Clone project directory and preliminary setup:<br>
    ~$ git clone ssh://<projectUrl> -b master myProjectName<br>

17. Make the project directory the current one:<br>
    ~$ cd <projDir><br>

18. Initialize ddev into <projDir>/.ddev/config.yaml:<br>
    projDir$ ddev config            (specify 'web' and 'drupal 9')<br>

19. Check .ddev/config.yaml about project type, docroot, etc;<br>

20. Start docker containers and ssh agent:<br>
    projDir$ ddev start<br>

21. Install drush:<br>
    projDir$ ddev composer require drush/drush<br>

22. Pantheon configuration:<br>
    projDir$ cp .ddev/providers/pantheon.yaml.example .ddev/providers/pantheon.yaml<br>
    
23. Pantheon dashboard>sites>proj, get <proj_uuid>.<environment> from the URL:<br>
    dashboard.pantheon.io/sites/437e6ad3-8303-45ca-8487-8a441710cd9c#dev/code<br>
    => project: 437e6ad3-8303-45ca-8487-8a441710cd9c.dev<br>

24. Open pantheon.yaml ...<br>
    projDir$ code .ddev/providers/pantheon.yaml.example<br>

25. Locate and set the following parameter (using specific data, not <placeholders>):<br>
    environment_variables:<br>
        project: <proj_uuid>.<environment><br>

26. Update ddev to connect to the specific Pantheon project:<br>
    projDir$ ddev restart<br>

27. Ensure ssh credentials:<br>
    projDir$ ddev auth ssh<br>

28. Download db&files from the Pantheon project:<br>
    projDir$ ddev pull pantheon<br>

29. Check proj alive:<br>
    projDir$ ddev launch        (or visit the URLs given by ddev describe)<br>
    
30. If neccessary, refresh the cache:<br>
    projDir$ ddev drush cr<br>

31. Upload after changes:<br>
    projDir$ ddev push pantheon<br>
