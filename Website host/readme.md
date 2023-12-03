# AWS Environment

### Deploying a Website in Aws using Ec2 and S3


- The applications which we are accessing through internet are called as web applications

- Web applications are used for Customer 2 Business Communications

		Ex: Google, Gmail, Facebook, Linkedin, Naukri, IRCTC etc.....

- Web application nothing but collection of web pages

- Web application will be deployed in webserver

- Users will send request to webserver to access web application

### Deployment Enviroment 

- [EC2](https://aws.amazon.com/pm/ec2)
- [Security Groups](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-security-groups.html)
- [S3](https://aws.amazon.com/pm/serv-s3)
- [Putty / MobaXterm](https://mobaxterm.mobatek.net/)

### Steps to host a website in EC2 instance

- Login into AWS Cloud Account :
<img src="Photos/Screenshot (134).png" alt="Aws Console">

- Launch EC2 Instance ( AMI : Amazon Linux )
<img src="Photos/Screenshot (126).png" alt="EC2 Launch">




- Configure Security Groups
	(SSH - 22 for admin access & HTTP - 80 for users to access our website)
	<img src="Photos/Screenshot (135).png" alt="PORT Open ssh & http">

- Connect to EC2 instance using Putty / MobaXterm :
```bash
$ ec2-user
```
<img src="Photos/Screenshot (128).png" alt="Connect With EC2">


- Install HTTPD Webserver in EC2 instance using following commands:
  

```bash
$ sudo yum update -y
$ sudo yum install httpd -y
```


- Setup Website in EC2 instance
```bash
$ cd /var/www/html
$ sudo vim index.html
```

- Apache serer will be Intialized : 
```bash
$ sudo service httpd start
$ sudo service httpd status
$ sudo service httpd stop
```

- Create S3 Bucket and upload the following files of the website.
<img src="Photos/Screenshot (129).png" alt="upload the css and data file">


- Bucket Policy make will be publicly access , you will be create a policy for it in the policy generator.
<img src="Photos/Screenshot (130).png" alt="Generate policy">


- The object link will be created will be attach to the html code in that the server.

- Access Website from Browser using EC2 Instance Public IP.

```bash
http://13.234.114.41/
```
<img src="Photos/Screenshot (131).png" alt="Website Deploy">


<hr>
