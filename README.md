DeployBundle
============

Task runner for Symfony project

[![Latest Stable Version](https://poser.pugx.org/sokil/deploy-bundle/v/stable.png)](https://packagist.org/packages/sokil/deploy-bundle)
[![Total Downloads](http://img.shields.io/packagist/dt/sokil/deploy-bundle.svg)](https://packagist.org/packages/sokil/deploy-bundle)
[![Build Status](https://travis-ci.org/sokil/DeployBundle.png?branch=master&1)](https://travis-ci.org/sokil/DeployBundle)
[![Coverage Status](https://coveralls.io/repos/github/sokil/DeployBundle/badge.svg?branch=master)](https://coveralls.io/github/sokil/DeployBundle?branch=master)

# Tasks

* [Git](#git)
* Writing own tasks

## Git

If repository is private, password will be asked on pull:

```
Permission denied (publickey).
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
```

For example web server started under www-data user. To prevent asking password, 
add ssh keys to `/home/$USER/.ssh` directory, using ssh key generation tool. 

1) Generate keys: 

```
$ sudo -u www-data ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/home/www-data/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/www-data/.ssh/id_rsa.
Your public key has been saved in /home/www-data/.ssh/id_rsa.pub.
The key fingerprint is:
...
```

2) Add public cey to your repository to fetch changes without password prompt.
3) Test your connection:
```
$ sudo -H -u www-data git pull origin master
```

## Writting own tasks

First, create task class which extends `Sokil\DeployBundle\Task\AbstractTask`. Then add Symfony's service definition:

```yaml
acme.deploy.my_task:
    class: Acme\Deploy\Task\MyTask
    abstract: true
    public: false
    tags:
      - {name: "deploy.task", alias: "myTask"}
```

This service must containb tag with name `deploy.task` and alias, which will be used as CLI command's option name and configuration section name.

Then, you must add it to bundle's configuration in `app/config.yaml` to `deploy` section in proper place of order:

```yaml
deploy:
  git: {}
  grunt: {}
  myTask: {}
```

Now, you can call your task from console:

```
$ ./app/console deploy --myTask
```
