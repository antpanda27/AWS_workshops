# Setting Up
Create `AWS-KMS-Workshop` stack from `curl 'https://static.us-east-1.prod.workshops.aws/public/249e5f66-a361-4b1d-8792-92f37d92fb25/static/EnvironmentTemplate.yaml' --output template.yaml`

Create a central stack in a *different account* using `curl 'https://static.us-east-1.prod.workshops.aws/public/249e5f66-a361-4b1d-8792-92f37d92fb25/static/central_template.yaml' --output template.yaml`

# S3 Track
This module continues you on your journey to address the privacy and compliance challenges of the existing configuration. Based on the recommendation of Amazon Web Services architects, you will:

1. Create and assign permissions to a KMS key
2. Configure CloudTrail to encrypt data with your new KMS key
3. Troubleshoot AWS KMS permission challenges, and learn how to fix them
4. Configure and enforce S3 encryption through bucket and resource configuration policies
5. Encrypt your existing, clear text, CloudTrail objects through Amazon S3 batch manager

## Module 1: Manage and Assign KMS Key to S3 Bucket
<details>
  <summary>Expand</summary>
  
  1. In the KMS Console, create a key with the following settings:
     * Key type: Symmetric
     * Key usage: Encrypt and decrypt
     * Key alias: cloudtrail-s3-encryption-key
     *

  2. Configure CloudTrail to encrypt log data
      <img width="1263" height="375" alt="image" src="https://github.com/user-attachments/assets/f613dd63-283b-481b-98fc-6f17d25194de" />

      <img width="1238" height="1146" alt="image" src="https://github.com/user-attachments/assets/1f308600-b574-4799-9a89-5a24c8fb9c86" />


      Edit KMS key policy, and retry
      <img width="1266" height="652" alt="image" src="https://github.com/user-attachments/assets/b2d4765a-5088-4be8-a4cd-ac0fc865491e" />


  3. Set S3 Bucket encryption
    <img width="849" height="655" alt="image" src="https://github.com/user-attachments/assets/c7c773ca-7bd2-482d-a894-089927dd56bd" />
    <img width="1257" height="1005" alt="image" src="https://github.com/user-attachments/assets/1d4ccc64-003f-41b8-bd68-5901fe3cfe14" />
    <img width="1253" height="714" alt="image" src="https://github.com/user-attachments/assets/107a3aa8-4aa7-4271-8653-2c4a12cebb9d" />

  4. Enforce S3 Bucket encryption
    <img width="1261" height="712" alt="image" src="https://github.com/user-attachments/assets/45e6756a-0561-4b3d-9c61-bed4296b5770" />

</details>

## Module 2: Remediating unencrypted objects in an S3 bucket
<details>
  <summary>Expand</summary>
  
  <img width="959" height="766" alt="image" src="https://github.com/user-attachments/assets/db27c3b6-3e1c-49da-a123-fc452eaa4e17" />
  <img width="644" height="92" alt="image" src="https://github.com/user-attachments/assets/c6c00462-db90-4335-b80a-72cc3c347cdc" />
  <img width="982" height="440" alt="image" src="https://github.com/user-attachments/assets/e19be622-0d83-4e77-b515-a7487aac9503" />
  <img width="1257" height="1172" alt="image" src="https://github.com/user-attachments/assets/d8226f86-7a0c-4540-9f33-8c835f5f9986" />
  <img width="1243" height="1714" alt="image" src="https://github.com/user-attachments/assets/991b1adc-725d-4a8d-a95f-4e35be915a5b" />
  <img width="1238" height="1789" alt="image" src="https://github.com/user-attachments/assets/b1bdfecf-cd74-43ae-8ab3-4eb95b4b40d0" />
  <img width="1254" height="430" alt="image" src="https://github.com/user-attachments/assets/3b349c66-a491-43eb-8bc6-87534c18d0d3" />
  <img width="1250" height="668" alt="image" src="https://github.com/user-attachments/assets/9832a272-1675-42f8-b838-cc992ce7d490" />

  ### Remediate the S3 manager access issue
  Retrieve the batch role's ARN in the IAM, then edit the previous KMS policy to include access for this role.
  <img width="1270" height="289" alt="image" src="https://github.com/user-attachments/assets/0269ed1c-8d6c-4e07-b1c7-7b7ecdc4ae30" />
  <img width="1261" height="926" alt="image" src="https://github.com/user-attachments/assets/0c7f5173-ef63-47f1-be07-2226985d33ff" />
  Copy S3 Batch Job and rerun.

  <img width="1244" height="416" alt="image" src="https://github.com/user-attachments/assets/de00ed70-e9e8-4650-9e31-7d6eb8260dff" />
  <img width="1252" height="875" alt="image" src="https://github.com/user-attachments/assets/3c7906fe-4968-445f-91f7-40f3b8a3eb07" />

</details>

## Module 3
### Sharing an Encrypted S3 Bucket

<details>
  <summary>Expand</summary>
  <img width="1131" height="457" alt="image" src="https://github.com/user-attachments/assets/2deb1617-03e5-47cd-a79a-69b0ef226e19" />
  <img width="1287" height="414" alt="image" src="https://github.com/user-attachments/assets/36ef9608-128e-4ade-8899-78ad62629995" />
  <img width="1192" height="578" alt="image" src="https://github.com/user-attachments/assets/21e08691-25c8-4431-b0cf-9ea5ed857913" />
  <img width="857" height="507" alt="image" src="https://github.com/user-attachments/assets/6b80e696-218f-4bf5-b13e-86d0d60e9eb3" />
  <img width="729" height="627" alt="image" src="https://github.com/user-attachments/assets/d9536187-c896-4907-ac61-71a1f5ea34d5" />

### Provide Cross Account KMS Access
  <img width="1288" height="636" alt="image" src="https://github.com/user-attachments/assets/d01788cf-7081-47f6-9414-964f0e45a56c" />
  <img width="864" height="588" alt="image" src="https://github.com/user-attachments/assets/36aa5850-f990-4203-96db-118b0f958ed2" />



  
</details>
