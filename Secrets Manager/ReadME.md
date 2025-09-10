# Secure Secrets with Secrets Manager

![Image](https://github.com/dev-boris67/AWS-Basics/blob/main/Project%20images/25.png?raw=true)

**Project Link:** [View Project](http://learn.nextwork.org/projects/aws-security-secretsmanager)

**Author:** Nchindo Boris  
**Email:** nchindoboris37@gmail.com

---

![Image](http://learn.nextwork.org/soothed_rose_serene_peach/uploads/aws-security-secretsmanager_r7s8t9u0)

---

## Introducing Today's Project!

In this project, I will;
- Identify how a web app insecurely stores credentials.
- See how GitHub's secret scanning feature can block insecure code from being pushed to a repository.
- Update the web app to use AWS Secrets Manager to store and retrieve credentials securely.
- Verify that secured web app code can be made public without exposing sensitive credentials.

### Tools and concepts

Services I used were; AWS Secrets Manager, AWS IAM, GitHub
Key concepts I learnt include; 
- Applying security best practices for managing credentials in code
- Understanding GitHub's secret scanning feature and why it's important
- Using AWS Secrets Manager to securely store and manage AWS credentials
- Retrieving secrets programmatically in Python using the AWS SDK (boto3)
- Cleaning up sensitive data from Git commit history by rebasing and handling merge conflicts

### Project reflection

This project took me approximately 1.5 hours to complete. The most challenging part was resolving the merge conflict during the Git rebase, especially making sure I kept the secure version of the code and removed all traces of the hardcoded credentials. It was most rewarding to successfully integrate AWS Secrets Manager into the application and confirm that the sensitive data was completely removed from the repositoryʼs history, leaving behind a clean, secure, and professional setup.

I did this project as it is an essential project for a cloud engineering career


---

## Hardcoding credentials

In this project, a sample web app is exposing my AWS credentials publicly. It is unsafe to harcode credentials because once someone else gets access to my credentials, they can use them to access my AWS account, delete resources, steal data, and cause damage.

I've set up the initial configuration with Access keys. These credentials are just examples because I am going to imagine that these are real AWS credentials that would give someone access to my AWS account!

![Image](http://learn.nextwork.org/soothed_rose_serene_peach/uploads/aws-security-secretsmanager_j2k3l4m5)

---

## Using my own AWS credentials

As an extension for this project, I also decided to run the web app locally by using my own AWS credentials; I set up my virtual environment and installed packages such as boto3, fast-api and uvicorn. I'll need these packages because my web 
app's main file (app.py) will be leveraging them to connect to my AWS environment. 

When I first ran the app, I ran into an error because the AWS credentials I was using weren't valid. Unfortunately I'm using placeholder credentials in my web app's config.py file, which means they're not real and I'll have to create my own credentials in my AWS account to ensure the functionality of my web app.

To resolve the 'InvalidAccessKeyId' error, I updated my web app's configuration file (config.py) so it would use the new credentials I created in my AWS account, which were the Access Key ID, Secret Access Key and the Region. I verified that the region I chose was also where my S3 buckets would be stored as it's best practice to keep resources in the same region.

![Image](http://learn.nextwork.org/soothed_rose_serene_peach/uploads/aws-security-secretsmanager_wghjteykut)

---

## Pushing Insecure Code to GitHub

Once I updated the web app code with credentials, I forked the repository because I want to showcase my version of the web app, and make changes to it without affecting the original repository.

A fork is different from a clone in the sense that When you fork a repository, you're making a copy of it in your GitHub account online. If your forked repository is public, anyone can see it!
But When you clone a repository, you're creating a local, offline copy. By default, no one can see it since it's stored on your computer.



To connect my local repository to the forked repository, I ran the command git remote set-url origin. This updates the remote origin to point to my fork instead of the original repository. Then I used git add and git commit to save the changes I made to config.py. Finally, git push uploads those committed changes to the remote origin, in this case, my forked repository on GitHub

GitHub blocked my push because its secret scanning feature detected that I was hardcoding AWS credentials into the code. This is a good security feature because it helps prevent sensitive information like access keys from being exposed in public repositories, which could lead to unauthorised access, data breaches, or unexpected charges.

![Image](http://learn.nextwork.org/soothed_rose_serene_peach/uploads/aws-security-secretsmanager_o2p3q4r5)

---

## Secrets Manager

Secrets Manager is a secure service provided by AWS for storing and managing sensitive information like API keys, database passwords, and other secrets. I'm using it to store the AWS credentials that would otherwise be hardcoded in config.py. Other common use cases include managing credentials for databases, third-party services, or internal APIs, and automatically rotating those secrets to enhance security.

Another feature in Secrets Manager is secret rotation, which means automatically updating and replacing your secrets on a regular schedule without manual intervention. It's useful in situations where you're managing high-risk credentials like database passwords, API keys, or service account credentials.

Secrets Manager provides sample code in various languages, like Python, Java, and Node.js. This is helpful because it shows developers exactly how to retrieve secrets from Secrets Manager in their applications. By using this sample code, developers can avoid hardcoding sensitive information and instead fetch it securely at runtime, reducing the risk of credential leaks and improving overall application security.

![Image](http://learn.nextwork.org/soothed_rose_serene_peach/uploads/aws-security-secretsmanager_h2i3j4k5)

---

## Updating the web app code

I updated the config.py file to retrieve AWS credentials securely from Secrets Manager instead of hardcoding them. The get_secret() function will connect to AWS Secrets Manager using the boto3 library, fetch the secret named "aws-access-key" from the specified region (like "us-east-1"), and return the credentials stored there. This function includes error handling using a try...except block to catch issues like missing 
permissions or incorrect secret names. By doing this, the application now retrieves sensitive information at runtime, improving security and following best practices for managing secrets.

I also added code to config.py to extract the individual credential values from the secret returned by the get_secret() function. This is important because our application expects specific variables, i.e. AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY, and AWS_REGION, to be defined so it can authenticate with AWS services. The new code calls get_secret(), parses the returned JSON using json.loads(), and assigns the appropriate values to each variable. This ensures the app gets the credentials securely at runtime instead of relying on hardcoded values.

![Image](http://learn.nextwork.org/soothed_rose_serene_peach/uploads/aws-security-secretsmanager_v0w1x2y3)

---

## Rebasing the repository

Git rebasing is the process of rewriting commit history by moving or editing commits to create a cleaner, more linear sequence. I used it to remove a specific commit that accidentally included sensitive AWS credentials. This was necessary because pushing secrets to a repository, even temporarily, poses a serious security risk. By using interactive rebase and the drop command, I was able to erase that commit from the history as if it never existed, helping ensure the credentials werenʼt exposed or retrievable later.

A merge conflict occurred during rebasing because the commit I removed contained hardcoded AWS credentials, and the following commit also modified the same file, config.py. Git wasnʼt sure how to reconcile the changes once the earlier commit was dropped. I resolved the merge conflict by manually editing the file: I removed the hardcoded credentials and kept the updated code that uses AWS Secrets Manager to securely retrieve the credentials. I also deleted all the merge conflict markers (<<<<<<<, =======, and >>>>>>>) to clean up the file. This ensured the file only contained the secure version of the code and allowed the rebase to continue successfully.

Once the merge conflict was resolved, I verified that my hardcoded credentials were removed by checking the commit history and the config.py file in my forked GitHub repository. I made sure that the file now only contains the secure version that uses AWS Secrets Manager, and that no previous commits include the sensitive credentials I had accidentally added. This confirms that the rebase and cleanup were successful, and that my repository is now safe from accidentally exposing secrets through Git history.

![Image](http://learn.nextwork.org/soothed_rose_serene_peach/uploads/aws-security-secretsmanager_t5u6v7w8)

---

---

