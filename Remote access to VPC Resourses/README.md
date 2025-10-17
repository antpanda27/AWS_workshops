# [Setting up remote access to VPC](https://catalog.us-east-1.prod.workshops.aws/workshops/d903ff2b-f043-4126-a4f5-64a7cc2922ec/en-US)
### Learning Objectives
In this workshop, you will learn how to create, configure, and test three different types of remote connectivity options to access your VPC resources.
* Remote connectivity using AWS Verified Access.
* Remote connectivity using Amazon EC2 Instance Connect endpoints.
* Remote connectivity using AWS Client VPN.
### Outcome
* Utilize AWS Verified Access: Gain the knowledge to use AWS Verified Access to grant secure access to internal web applications.
* Enable SSH Connections: Learn how to utilize Amazon EC2 Instance Connect endpoints for secure SSH connections to servers.
* Master AWS Client VPN: Acquire the skills to configure and utilize AWS Client VPN, enabling network-level access for remote users.
* Apply Practical Knowledge: Obtain hands-on experience and real-world use cases for each service, ensuring you're prepared to apply your skills in practical scenarios.
### Diagram
<img width="3138" height="2082" alt="image" src="https://github.com/user-attachments/assets/20a05073-3f74-44cf-9d40-0cd0c7dfbf6a" />

### Getting Started
1. Create a **key pair** of *RSA* type and as a *.pem* format
2. Deploy AWS Verified Workshop prerequisites using IAC. [Instructions](https://catalog.us-east-1.prod.workshops.aws/workshops/d903ff2b-f043-4126-a4f5-64a7cc2922ec/en-US/0-introduction/2-aws-account)
   <img width="964" height="692" alt="image" src="https://github.com/user-attachments/assets/73caddb5-39af-4c87-99e7-ca4ccbad2737" />
<img width="829" height="234" alt="image" src="https://github.com/user-attachments/assets/624ef832-b108-49c0-998d-a4c1b286703d" />

# Lab 1
## Connect to an internal web app using AWS Verified Access
<img alt="AWS Verified Access Diagram" src="https://static.us-east-1.prod.workshops.aws/public/80e312c8-97ec-4ab2-8f25-b9645eddb6ad/static/AVA/AWS-Verified-Access-Product-Page.png">

**Walkthrough steps within first lab:**
1. Create Amazon Cognito users
2. Create AWS Verified Access trust provider using Amazon Cognito
3. Create AWS Verified Access Instance
4. Create AWS Verified Access Groups with basic policy
5. Create AWS Verified Access Endpoint
6. Update Amazon Route 53
7. Test the application
<img alt="Lab diagram" src="https://static.us-east-1.prod.workshops.aws/public/80e312c8-97ec-4ab2-8f25-b9645eddb6ad/static/AVA/AWSVerifiedAccessLabDiagram.png">
