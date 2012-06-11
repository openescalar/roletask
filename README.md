roletask
========

Large collection of free and public role tasks that can be incorporated into openescalar

### Welcome to Open Escalar Role Tasks.
This is a large collection of scripts, puppet and chef recipies which can be use to configure server in the different clouds that Open Escalar manages.
The files here you can see them as templates for your tasks which at the end will take information from Open Escalars metadata service in order to customize and bring to live each of them once they are executed in thier respective targes (servers). 

### Scripting
Remember that for each script file you'll have to specify the path for your interpreter if is not located under your common environment PATH and specify the exit value. Let's do an example:

```
#!/bin/bash
echo "executing my task"
exit 0
```

### Puppet 
In case of puppet make sure you're writing down all the classes you'll need in case you're not using Open Escalar public modules. One best practice while writing puppets is to use require to execute classes in an specific order if you wish to setup everyting on a single task. All the facter facts will be available by default. Keep in mind that order of the workflow which will execute all the role tasks can be defined in Open Escalar, but if you decided to execute your classes in an order here's an example:

```
#Configure my resolv.conf
class mydns {
  file {
    "/etc/resolv.conf":
       owner => "root",
       group => "root",
       content => "nameserver 127.0.0.1\nnameserver 192.168.1.2";
}
class mylampstack {
  require mydns
  package {
    "httpd":
      ensure => "latest";
    "mysql-server":
      ensure => "latest";
    "php":
      ensure => "installed";
  }
  service {
    "httpd":
       ensure => "running";
    "mysqld":
       ensure => "running";
  }
}

class myapp {
  require mylampstack
  exec {
    "git clone git://github.com/myuser/myrepo.git":
      cwd     => "/var/www/html/",
      creates => "/var/www/html/myrepo/myfile",
      path    => ["/usr/bin", "/usr/sbin"],
      onlyif => "test -f /var/www/html/myrepo/myfile";
   }
}

include myapp
```

### Chef
I'll finish this one up later :D
