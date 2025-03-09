<h1>Hosting-the-Employee-Directory-Application-on-AWS</h1>

<h2>Description</h2>
we are going to host our employee directory application using a service called Amazon EC2, AWS provides you with something called a default VPC which is a private network for your AWS resources.
Every EC2 instance you launch using AWS must live inside of a network. we will be using the default network provided by AWS and we will accept most of the default values for launching an EC2 instance.
<br />


<h2>Program walk-through:</h2>

<img src="https://res.cloudinary.com/dk3bkl3ji/image/upload/v1741549974/Screenshot_2025-03-09_154033_nzocii.png"/>
</b>

1. I'm in the EC2 management console and I will navigate to the service Amazon EC2, Amazon EC2 is a compute service that allows you to host virtual machines, we'll launch a new EC2 instance.

<img src="https://res.cloudinary.com/dk3bkl3ji/image/upload/v1741550333/Screenshot_2025-03-08_185231_nf8p9f.png"/>
<img src="https://res.cloudinary.com/dk3bkl3ji/image/upload/v1741550264/Screenshot_2025-03-08_190529_ifxu2j.png"/>
</b>

2. Now we have to select the configurations, We can give this instance a name, I'll call it employee-web-app and then we will select the free tier eligible options using a Linux AMI or Amazon Machine Image.
And then scrolling down to the next section

<img src="https://res.cloudinary.com/dk3bkl3ji/image/upload/v1741552593/Screenshot_2025-03-08_190559_gindhj.png"/>
</b>

3. we will select the free tier eligible t2.micro for the instance type, Next, we normally need to select a key pair, This would be used to SSH into the instance but we won't need to do this,so I'm going to select proceed without a key pair
<img src="https://res.cloudinary.com/dk3bkl3ji/image/upload/v1741552812/Screenshot_2025-03-08_190623_xbjw42.png"/>
</b>

4. Continue scrolling to the network settings, we will select the network, we will click Edit, Then we can select the VPC and subnet for this instance, and as we discussed earlier, we will use the default VPC
and leave the subnet at No preference.The default VPC has configurations in place that make this experimentation process a lot easier when you're first getting started with AWS.
<img src="https://res.cloudinary.com/dk3bkl3ji/image/upload/v1741553116/Screenshot_2025-03-08_190647_rvmic9.png"/>
</b>

5. Continuing on with network settings, we will create a new security group which is an instance level firewall that will allow HTTP and HTTPS traffic into reach the instance, So we will add inbound rules for both of those.
<img src="https://res.cloudinary.com/dk3bkl3ji/image/upload/v1741553761/Screenshot_2025-03-08_190709_frbfeu.png"/>
<img src="https://res.cloudinary.com/dk3bkl3ji/image/upload/v1741554072/Screenshot_2025-03-08_190723_aecxzd.png"/>
</b>

6. Now we will scroll down and expand advanced details, We will accept most of the defaults here except the IAM instance profile and user data which we will provide a value for.
For the IAM instance profile we will use the IAM role that will be used by the application, though this won't come into play until later when we have our S3 bucket created.
But for now, let's click the dropdown and select that IAM role.
<img src="https://res.cloudinary.com/dk3bkl3ji/image/upload/v1741555022/Screenshot_2025-03-09_171646_atd99m.png"/>
</b>

7. Let's go ahead and paste in our user data script, This script will download the source code for the app, start the web server, and kick off the application code so it's ready to start handling requests
while you could have launched the instance, then connected to it via SSH, configured and started your application manually.
We have decided to use a script to automate this process on launch. Alright, now we can click Launch instance. It can take a few minutes for the instance to boot upso let's wait for that.
Amazon Linux 2023 user data script: 
 
 
#!/bin/bash -ex
wget https://aws-tc-largeobjects.s3-us-west-2.amazonaws.com/DEV-AWS-MO-GCNv2/FlaskApp.zip
unzip FlaskApp.zip
cd FlaskApp/
yum -y install python3-pip
pip install -r requirements.txt
yum -y install stress
export PHOTOS_BUCKET=${SUB_PHOTOS_BUCKET}
export AWS_DEFAULT_REGION=<INSERT REGION HERE>
export DYNAMO_MODE=on
FLASK_APP=application.py /usr/local/bin/flask run --host=0.0.0.0 --port=80 
 
When using the user data scripts, remember to replace the <INSERT REGION HERE> with whatever AWS region you are operating in, and ensure you remove both brackets as well.

<img src="https://res.cloudinary.com/dk3bkl3ji/image/upload/v1741555238/Screenshot_2025-03-08_190828_tsqaqn.png"/>
<img src="https://res.cloudinary.com/dk3bkl3ji/image/upload/v1741555250/Screenshot_2025-03-08_190909_zewxis.png"/>
<img src="https://res.cloudinary.com/dk3bkl3ji/image/upload/v1741555332/Screenshot_2025-03-09_172154_nucw9o.png"/>

</b>



