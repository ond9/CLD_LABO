# LAB 02: APP SCALING ON AMAZON WEB SERVICES

![img](img/title_img.PNG "logo")

Link to the full lab [here](https://cyberlearn.hes-so.ch/mod/assign/view.php?id=551613)  

## TASK 1: CREATE A DATABASE USING THE RELATIONAL DATABASE SERVICE (RDS)
#### DELIVERABLE 1:

The endpoint address of the database:
```
zundler-drupa.cgcjxeuocv6f.eu-central-1.rds.amazonaws.com
```
 Compare EC2 and RDS if it were running continuously for a month:  
+ RDS monthly bill : $15.33  
+ EC2 monthly bill : $10.98  

(Region Frankfurt, t2.micro)

In a two-tier architecture the web application and the database are kept separate and run on different hosts. Imagine that for the second tier instead of using RDS to store the data you would create a virtual machine in EC2 and install and run yourself a database on it. If you were the Head of IT of a medium-size business, how would you argue in favor of using a database as a service instead of running your own database on an EC2 instance? How would you argue against it?

Faut répondre à ça laurent ! ![laurent-part](http://thewordchef.com/wp-content/uploads/2012/01/red-arrow.png "logo")

## TASK 2: CONFIGURE THE DRUPAL MASTER INSTANCE TO USE THE RDS DATABASE
####  DELIVERABLE 2:
The content of the generated file /etc/drupal/7/sites/default/dbconfig.php:  
```php
?php
$databases['default']['default'] = array(
        'driver' => 'mysql',
        'database' => 'drupal7',
        'username' => 'drupal7',
        'password' => '1234',
        'host' => 'zundler-drupa.cgcjxeuocv6f.eu-central-1.rds.amazonaws.com',
        'port' => '',
        'prefix' => ''
);
?>
```

## TASK 3: CREATE A CUSTOM VIRTUAL MACHINE IMAGE
####  DELIVERABLE 3:
Screenshot of the AWS console showing the AMI parameters:  

![img](img/labo02_task3.PNG "AMI")

![img](img/labo02_task3bis.PNG "AMI details")

## TASK 4: CREATE A LOAD BALANCER
####  DELIVERABLE 4:

Nslookup :
```
PS C:\Users\st4ck\Documents\HEIG-VD\S6\CLD\CLD_LABO> nslookup Zundler-Drupal-1068779716.eu-central-1.elb.amazonaws.com
Serveur :   ns01.heig-vd.ch
Address:  10.192.22.5

Réponse ne faisant pas autorité :
Nom :    Zundler-Drupal-1068779716.eu-central-1.elb.amazonaws.com
Addresses:  52.29.19.58
          52.29.147.251
```

health check :
```
20 "-" "ELB-HealthChecker/1.0"
172.31.6.155 - - [20/Mar/2015:10:18:32 +0000] "GET /index.html HTTP/1.1" 200 118
20 "-" "ELB-HealthChecker/1.0"
172.31.6.155 - - [20/Mar/2015:10:18:42 +0000] "GET /index.html HTTP/1.1" 200 118
20 "-" "ELB-HealthChecker/1.0"
172.31.26.84 - - [20/Mar/2015:10:18:43 +0000] "GET /index.html HTTP/1.1" 200 118
20 "-" "ELB-HealthChecker/1.0"
172.31.26.84 - - [20/Mar/2015:10:18:52 +0000] "GET /index.html HTTP/1.1" 200 118
```

## TASK 5: LAUNCH A SECOND INSTANCE FROM THE CUSTOM IMAGE
####  DELIVERABLE 5:

Diagram of our setup on AWS :

![img](img/labo02_task4.PNG "infra")

corresponding Security-Group :

![img](img/labo02_task4_bis.PNG "Security-Group")

Note: Security-Group are permissive, we can filter more precisely but for this lab we just made simple filtering to the objective that everything works. Note that icmp is required if you want that the ELB can check status of the instances.

Total cost of the setup :  
+ $59.25

That include ELB, RDS(t2.micro) and 2 instance (t2.micro).

## TASK 6: TEST THE DISTRIBUTED APPLICATION
####  DELIVERABLE 6:

Document your observations. Include screenshots of JMeter and the AWS console monitoring output.



When you resolve the DNS name of the load balancer into IP addresses while the load balancer is under high load what do you see? Explain.

The Nslookup comamnd show that ip address switch between each command and this what ever the load is.

```
PS C:\Users\st4ck\Documents\HEIG-VD\S6\CLD\CLD_LABO> nslookup Zundler-Drupal-1068779716.eu-central-1.elb.amazonaws.com
Serveur :   ns01.heig-vd.ch
Address:  10.192.22.5

Réponse ne faisant pas autorité :
Nom :    Zundler-Drupal-1068779716.eu-central-1.elb.amazonaws.com
Addresses:  52.29.19.58
          52.29.147.251

PS C:\Users\st4ck\Documents\HEIG-VD\S6\CLD\CLD_LABO> nslookup Zundler-Drupal-1068779716.eu-central-1.elb.amazonaws.com
Serveur :   dnsman.heig-vd.ch
Address:  10.192.22.5

Réponse ne faisant pas autorité :
Nom :    Zundler-Drupal-1068779716.eu-central-1.elb.amazonaws.com
Addresses:  52.29.147.251
          52.29.19.58
```

Did this test really test the load balancing mechanism? What are the limitations of this simple test? What would be necessary to do realistic testing?
