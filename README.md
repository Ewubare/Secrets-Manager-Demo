---

![Image](http://learn.nextwork.org/restful_olive_calm_riroriro/uploads/aws-security-secretsmanager_r7s8t9u0)

---

### Tools and concepts

Services I used were AWS Secrets Manager for secure credential storage, AWS IAM for access key management and permissions, Amazon S3 for bucket creation and listing, GitHub for version control and secret scanning protection, and AWS KMS for encryption of stored secrets. Key concepts I learnt include the fundamental security risks of hardcoded credentials in source code, how GitHub's automated secret scanning prevents credential exposure, the proper implementation of secrets management using AWS SDK integration, Git rebasing techniques for removing sensitive data from commit history, and the separation of application logic from credential management.


### Project reflection

This project took me about an hour to complete. The most challenging part was understanding and executing the Git rebasing process to remove sensitive data from commit history, particularly resolving the merge conflicts that arose when trying to eliminate the hardcoded credentials while preserving the Secrets Manager implementation. It was most rewarding to learn more about how powerful and versatile Git really is.

I engaged with this project because it provided an excellent opportunity to demonstrate practical cybersecurity skills and walk through real-world security challenges that developers face daily.

---

## Hardcoding credentials

In this project, a sample web app is exposing AWS credentials directly in the source code by hardcoding access keys and secret keys as plain text variables. It is unsafe to hardcode credentials because this practice creates multiple critical security vulnerabilities that can lead to complete compromise of cloud resources..


I've set up the initial configuration with placeholder AWS credentials including an AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY hardcoded directly in the config.py file. These credentials are just examples because this is a controlled educational environment designed to demonstrate security vulnerabilities, not a production system with real AWS resources at risk.


![Image](http://learn.nextwork.org/restful_olive_calm_riroriro/uploads/aws-security-secretsmanager_j2k3l4m5)

---

## Using my own AWS credentials

As an extension for this project, I also decided to run the web application locally to see the complete functionality before securing the credentials. To set up my virtual environment, I installed:

Boto3 the AWS SDK for Python that provides the interface for authenticating with AWS services and performing operations like listing S3 buckets.

FastAPI a web framework for building APIs with Python. It handles the HTTP routing, request processing, and response formatting for our web application endpoints.

Uvicorn an ASGI server implementation that actually runs the FastAPI application. It listens for incoming HTTP requests and serves our application on localhost:8000.
 


When I first ran the app, I ran into an error because the application was configured with placeholder AWS credentials that don't correspond to any real AWS account, resulting in an authentication failure when attempting to access AWS services. 

To resolve the 'InvalidAccessKeyId' error, I updated the config.py file by replacing the placeholder AWS credentials with real access keys generated from my IAM user account. The file now contains my actual AWS Access Key ID, Secret Access Key, and the correct AWS region where my S3 buckets are located.

![Image](http://learn.nextwork.org/restful_olive_calm_riroriro/uploads/aws-security-secretsmanager_wghjteykut)

---

## Pushing Insecure Code to GitHub

Once I updated the web app code with credentials, I forked the repository because I needed to create a public copy of the code in my own GitHub account to demonstrate what happens when credentials are exposed in a public repository. A fork is different from a clone because forking creates a publicly visible copy on GitHub's servers that others can access, while cloning only creates a private local copy on my computer.

To connect my local repository to the forked repository, I used git remote set-url origin to redirect the existing remote connection from the original NextWork repository to my personal forked repository on GitHub. Then I used git add . and git commit to stage all modified files in my local working directory and create a snapshot of those changes with a descriptive message about updating the config.py file with hardcoded credentials. Finally, git push -u origin main uploads my local commits to the remote repository on GitHub's servers, attempting to make my code changes publicly visible.

GitHub blocked my push because its automated secret scanning detected real AWS credentials in both my config.py file and the downloaded CSV file, triggering push protection that prevents sensitive information from being committed to public repositories. This is a good security feature because it acts as a critical safety net that prevents accidental credential exposure, which could lead to unauthorized access to AWS accounts which could then lead to unexpected charges, data breaches, and potential compromise of entire cloud infrastructures.


![Image](http://learn.nextwork.org/restful_olive_calm_riroriro/uploads/aws-security-secretsmanager_o2p3q4r5)

---

## Secrets Manager

Secrets Manager is a fully managed AWS service that provides secure storage, retrieval, and lifecycle management of sensitive information such as passwords, API keys, and database credentials through encrypted storage and programmatic access controls. I'm using it to store my AWS Access Key ID and Secret Access Key in an encrypted format that my application can retrieve at runtime without having these credentials visible in source code. Other common use cases include storing database connection strings for applications, API tokens for third-party services, SSL certificates and private keys, OAuth tokens and refresh tokens, and service account credentials for microservices architectures.


Another feature in Secrets Manager is automatic secret rotation, which means the service can periodically replace existing credentials with new ones on a scheduled basis without requiring manual intervention or application downtime. It's useful in situations where you need to minimize the window of vulnerability if credentials become compromised, meet compliance requirements that mandate regular password changes, or manage high-privilege accounts that pose significant security risks if exposed.


Secrets Manager provides sample code in various languages, like Python, Java, JavaScript, and C#, that shows developers exactly how to retrieve secrets using the appropriate SDK for their programming language. This is helpful because it provides copy-paste code snippets that follow AWS best practices for error handling, authentication, and secret parsing.


![Image](http://learn.nextwork.org/restful_olive_calm_riroriro/uploads/aws-security-secretsmanager_h2i3j4k5)

---

## Updating the web app code

I updated the config.py file to retrieve credentials dynamically from AWS Secrets Manager instead of storing them as hardcoded variables. The get_secret() function will connect to the Secrets Manager service using boto3, authenticate using the application's IAM permissions, retrieve the encrypted secret named "aws-access-key", and return the decrypted JSON content containing the AWS credentials.


I also added code to config.py to extract the individual credential values from the JSON object returned by Secrets Manager and assign them to the specific variable names that our application expects. This is important because the get_secret() function only retrieves the raw JSON data from Secrets Manager, but our app.py file still needs the credentials available as individual variables (AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY, and AWS_REGION) to maintain compatibility with the existing application logic.

![Image](http://learn.nextwork.org/restful_olive_calm_riroriro/uploads/aws-security-secretsmanager_v0w1x2y3)

---

## Rebasing the repository

Git rebasing is an advanced version control operation that allows you to rewrite commit history by changing, reordering, or removing commits from a repository's timeline. I used it to completely eliminate the commit that contained hardcoded AWS credentials from the project's Git history. This was necessary because simply updating the config.py file with Secrets Manager code wasn't sufficient as the real credentials remained accessible in the repository's commit history, where anyone could retrieve them by examining previous versions of the code.

A merge conflict occurred during rebasing because Git couldn't automatically determine how to reconcile the removal of the commit containing hardcoded credentials with the subsequent commit that had already modified the same config.py file with Secrets Manager code. I resolved the merge conflict by manually editing the config.py file to remove the conflict markers and keeping only the Secrets Manager implementation while discarding the hardcoded credential sections.


Once the merge conflict was resolved, I verified that the hardcoded credentials were completely removed from the repository by checking both the current file contents and the commit history on GitHub. I navigated to my forked repository and examined the config.py file to confirm it only contained the Secrets Manager implementation code, then reviewed the commit history to ensure no commits showed the hardcoded AWS credentials that had previously caused GitHub's secret scanning to block the push.

![Image](http://learn.nextwork.org/restful_olive_calm_riroriro/uploads/aws-security-secretsmanager_t5u6v7w8)

---
