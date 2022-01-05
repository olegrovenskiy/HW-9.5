# HW-9.4

## Домашнее задание к занятию "09.04 Jenkins"

## Подготовка к выполнению

### 1. Создать 2 VM: для jenkins-master и jenkins-agent.

done

	vagrant@vagrant:~/mnt-homeworks/09-ci-04-jenkins/infrastructure$ git status
	On branch MNT-7
	Your branch is up to date with 'origin/MNT-7'.
	
	Changes not staged for commit:
	  (use "git add <file>..." to update what will be committed)
	  (use "git restore <file>..." to discard changes in working directory)
	        modified:   inventory/cicd/hosts.yml

	no changes added to commit (use "git add" and/or "git commit -a")

2. Установить jenkins при помощи playbook'a.

	vagrant@vagrant:~/mnt-homeworks/09-ci-04-jenkins/infrastructure$ ansible-playbook site.yml -i ./inventory/cicd/hosts.yml
	
	PLAY [Preapre all hosts] *********************************************************************************************************************
	
	TASK [Gathering Facts] ***********************************************************************************************************************
	ok: [jenkins-master-01]
	ok: [jenkins-agent-01]
	
	TASK [Create group] **************************************************************************************************************************
	changed: [jenkins-agent-01]
	changed: [jenkins-master-01]
	
	TASK [Create user] ***************************************************************************************************************************
	changed: [jenkins-agent-01]
	changed: [jenkins-master-01]
	
	TASK [Install JDK] ***************************************************************************************************************************
	changed: [jenkins-master-01]
	changed: [jenkins-agent-01]
	
	PLAY [Get Jenkins master installed] **********************************************************************************************************
	
	TASK [Gathering Facts] ***********************************************************************************************************************
	ok: [jenkins-master-01]
	
	TASK [Get repo Jenkins] **********************************************************************************************************************
	changed: [jenkins-master-01]
	
	TASK [Add Jenkins key] ***********************************************************************************************************************
	changed: [jenkins-master-01]

	TASK [Install epel-release] ******************************************************************************************************************
	changed: [jenkins-master-01]
	
	TASK [Install Jenkins and requirements] ******************************************************************************************************
	changed: [jenkins-master-01]
	
	TASK [Ensure jenkins agents are present in known_hosts file] *********************************************************************************
	# 62.84.125.60:22 SSH-2.0-OpenSSH_7.4
	# 62.84.125.60:22 SSH-2.0-OpenSSH_7.4
	# 62.84.125.60:22 SSH-2.0-OpenSSH_7.4
	# 62.84.125.60:22 SSH-2.0-OpenSSH_7.4
	# 62.84.125.60:22 SSH-2.0-OpenSSH_7.4
	changed: [jenkins-master-01] => (item=jenkins-agent-01)
	[WARNING]: Module remote_tmp /home/jenkins/.ansible/tmp did not exist and was created with a mode of 0700, this may cause issues when running
	as another user. To avoid this, create the remote_tmp dir with the correct permissions manually
	
	TASK [Start Jenkins] *************************************************************************************************************************
	changed: [jenkins-master-01]
	
	PLAY [Prepare jenkins agent] *****************************************************************************************************************
	
	TASK [Gathering Facts] ***********************************************************************************************************************
	ok: [jenkins-agent-01]
	
	TASK [Add master publickey into authorized_key] **********************************************************************************************
	changed: [jenkins-agent-01]
	
	TASK [Create agent_dir] **********************************************************************************************************************
	changed: [jenkins-agent-01]
	
	TASK [Add docker repo] ***********************************************************************************************************************
	changed: [jenkins-agent-01]
	
	TASK [Install some required] *****************************************************************************************************************
	changed: [jenkins-agent-01]
	
	TASK [Update pip] ****************************************************************************************************************************
	changed: [jenkins-agent-01]
	
	TASK [Install Ansible] ***********************************************************************************************************************
	changed: [jenkins-agent-01]

	TASK [Reinstall Selinux] *********************************************************************************************************************
	changed: [jenkins-agent-01]
	
	TASK [Add local to PATH] *********************************************************************************************************************
	changed: [jenkins-agent-01]
	
	TASK [Create docker group] *******************************************************************************************************************
	ok: [jenkins-agent-01]
	
	TASK [Add jenkinsuser to dockergroup] ********************************************************************************************************
	changed: [jenkins-agent-01]
	
	TASK [Restart docker] ************************************************************************************************************************
	changed: [jenkins-agent-01]
	
	TASK [Install agent.jar] *********************************************************************************************************************
	changed: [jenkins-agent-01]
	
	PLAY RECAP ***********************************************************************************************************************************
	jenkins-agent-01           : ok=17   changed=14   unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
	jenkins-master-01          : ok=11   changed=9    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
	
	vagrant@vagrant:~/mnt-homeworks/09-ci-04-jenkins/infrastructure$
	vagrant@vagrant:~/mnt-homeworks/09-ci-04-jenkins/infrastructure$

### 3. Запустить и проверить работоспособность.

https://c2n.me/4ewAUGx

### 4. Сделать первоначальную настройку.

https://c2n.me/4ewB5iy

https://c2n.me/4ewBaJ7

https://c2n.me/4ewBci0

https://c2n.me/4ewFtG4

------------


## Основная часть - Molecule Test


### 1. Сделать Freestyle Job, который будет запускать molecule test из любого вашего репозитория с ролью.

	pip3 install "molecule==3.4.0"
	pip3 install molecule-docker
	cd ./roles/kibana-role
	molecule test


### 2. Сделать Declarative Pipeline Job, который будет запускать molecule test из любого вашего репозитория с ролью.

	 pipeline {
	    agent  {
	     label 'docker'
	           }
	    stages {
	        stage("Run Molecule"){
	            steps {
	              git branch: 'Branch-9.5', url: 'https://github.com/olegrovenskiy/HW-9.5.git'
	              echo 'Hello'
	              sh '''pip3 install "molecule==3.4.0"
	              pip3 install molecule-docker
	              cd ./roles/kibana-role
	              molecule test'''
	            }
	        }
	    }
	}

3. Перенести Declarative Pipeline в репозиторий в файл Jenkinsfile.

	https://github.com/olegrovenskiy/HW-9.5
	
	vagrant@vagrant:~/kibana-role$ ls -l
	total 32
	drwxrwxr-x 2 vagrant vagrant 4096 Jan  5 13:28 delete
	drwxrwxr-x 3 vagrant vagrant 4096 Jan  5 13:28 inventory
	-rw-rw-r-- 1 vagrant vagrant  433 Jan  5 16:00 Jenkinsfile
	-rw-rw-r-- 1 vagrant vagrant    8 Jan  5 13:28 README.md
	drwxrwxr-x 4 vagrant vagrant 4096 Jan  5 13:28 roles
	-rw-rw-r-- 1 vagrant vagrant 2956 Jan  5 13:28 site.old_8.3
	-rw-rw-r-- 1 vagrant vagrant 2956 Jan  5 13:28 site.yml
	drwxrwxr-x 2 vagrant vagrant 4096 Jan  5 13:28 templates
	vagrant@vagrant:~/kibana-role$

### 4. Создать Multibranch Pipeline на запуск Jenkinsfile из репозитория.

	Scan Multibranch Pipeline Log
	Started
	[Wed Jan 05 16:13:33 UTC 2022] Starting branch indexing...
	 > git --version # timeout=10
	 > git --version # 'git version 1.8.3.1'
	 > git ls-remote https://github.com/olegrovenskiy/HW-9.5.git # timeout=10
	Creating git repository in /var/lib/jenkins/caches/git-6db89d87817033f20a1a96ebf34d8b99
	 > git init /var/lib/jenkins/caches/git-6db89d87817033f20a1a96ebf34d8b99 # timeout=10
	Setting origin to https://github.com/olegrovenskiy/HW-9.5.git
	 > git config remote.origin.url https://github.com/olegrovenskiy/HW-9.5.git # timeout=10
	Fetching & pruning origin...
	Listing remote references...
	 > git config --get remote.origin.url # timeout=10
	 > git --version # timeout=10
	 > git --version # 'git version 1.8.3.1'
	 > git ls-remote -h https://github.com/olegrovenskiy/HW-9.5.git # timeout=10
	Fetching upstream changes from origin
	 > git config --get remote.origin.url # timeout=10
	 > git fetch --tags --progress --prune origin +refs/heads/*:refs/remotes/origin/* # timeout=10
	Checking branches...
	  Checking branch Branch-9.5
	      ‘Jenkinsfile’ found
	    Met criteria
	Scheduled build for branch: Branch-9.5
	Processed 1 branches
	[Wed Jan 05 16:13:38 UTC 2022] Finished branch indexing. Indexing took 5.3 sec
	Finished: SUCCESS



