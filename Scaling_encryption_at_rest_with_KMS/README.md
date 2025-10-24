<img width="1665" height="1024" alt="image" src="https://github.com/user-attachments/assets/8818c0ff-6c44-463e-942e-1c05a0310afb" /># Setting Up
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
  
  1. In the KMS Console, create a key with the following settings:
     * Key type: Symmetric
     * Key usage: Encrypt and decrypt
     * Key alias: cloudtrail-s3-encryption-key
     * Key administrators: KMS Admin Role

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
  
  <img width="1131" height="457" alt="image" src="https://github.com/user-attachments/assets/2deb1617-03e5-47cd-a79a-69b0ef226e19" />
  <img width="1287" height="414" alt="image" src="https://github.com/user-attachments/assets/36ef9608-128e-4ade-8899-78ad62629995" />
  <img width="1192" height="578" alt="image" src="https://github.com/user-attachments/assets/21e08691-25c8-4431-b0cf-9ea5ed857913" />
  <img width="857" height="507" alt="image" src="https://github.com/user-attachments/assets/6b80e696-218f-4bf5-b13e-86d0d60e9eb3" />
  <img width="729" height="627" alt="image" src="https://github.com/user-attachments/assets/d9536187-c896-4907-ac61-71a1f5ea34d5" />

### Provide Cross Account KMS Access
  <img width="1288" height="636" alt="image" src="https://github.com/user-attachments/assets/d01788cf-7081-47f6-9414-964f0e45a56c" />
  <img width="864" height="588" alt="image" src="https://github.com/user-attachments/assets/36aa5850-f990-4203-96db-118b0f958ed2" />

</details>


# RDS Track

This module continues your journey to address the privacy and compliance challenges of the existing configuration. Based on the recommendation of Amazon Web Services architects, you will:

* Create and assign permissions to a KMS key
* Encrypt existing RDS resources
* Troubleshoot AWS KMS permission challenges, and learn how to fix them
* Enforce encryption on RDS resources
* Share an encrypted RDS instance with another AWS Account

## Module 1: Manage and Assign KMS Key to RDS Instance
<details>

  ### Configure a KMS CMK and Check RDS Compliance
  Create a CMK with the following:
  * Key type: Symmetric
  * Key usage: Encrypt and decrypt
  * Key alias: cloudtrail-s3-encryption-key
  * Key administrators: KMS Admin Role
    
  <img width="1643" height="935" alt="image" src="https://github.com/user-attachments/assets/ef31633f-d7a6-46c1-ac24-1044d9013edf" />

  **Review current resources for compliance**
  Switch to ProjectRole, to check the AWS Config Rules, and in RDS_Storage_Encrypted, check the noncompliant resource.

  <img width="1489" height="605" alt="image" src="https://github.com/user-attachments/assets/32605b16-1a61-4619-bcd6-56ec446bac37" />
  <img width="1132" height="237" alt="image" src="https://github.com/user-attachments/assets/508b4b7d-c517-4368-acc2-119354a3e1b9" />
  
</details>

## Module 2: Remediate Unencrypted RDS Instance
<details>

  ### Create an Encrypted Snapshot
  Aurora and RDS Dashboard > Databases > Select the RDS instance > Actions > Take Snapshot > Give a Snapshot name > Take snapshot
  <img width="972" height="533" alt="image" src="https://github.com/user-attachments/assets/c088dce1-dbae-4ad6-b0b6-955586c41272" />
  Copy snapshot > Name snapshot copy > Enable Encryption > Select CMK created in previous step > Copy snapshot
  <img width="1662" height="1019" alt="image" src="https://github.com/user-attachments/assets/75da7078-c451-440a-98c9-c78f0918d1f2" />

  ### Grant KMS access to ProjectRole
  Using Admin Role:
  <img width="1651" height="1027" alt="image" src="https://github.com/user-attachments/assets/243da319-85a0-488c-b694-86ddea3833de" />

  Return to ProjectRole, and retry copying snapshot = success
  <img width="1230" height="590" alt="image" src="https://github.com/user-attachments/assets/20513935-a04d-48b8-ad9f-015b1c747585" />

  ### Restore Encrypted Snapshot to the RDS Instance
  Select encrypted snapshot > Actions > Restore snapshot > Input a name for DB Instance > Select single DB instance > Select db.t3.micro
  Scroll down to Encryption. Note - enabled by default = restore encrypted snapshot to unencrypted instance.
  <img width="1661" height="1835" alt="image" src="https://github.com/user-attachments/assets/1823e09e-4d7a-4c36-9114-4b3d292d8230" />
  <img width="2251" height="321" alt="image" src="https://github.com/user-attachments/assets/0064364d-1681-4672-9363-80470d3c6ec8" />

  **Delete unencrypted resources**
  _IRL: Ensure clients have failed-over to the new encrypted database, and are functioning correctly before deleting these resources_
  Select unencrypted snapshot > Actions > Delete snapshot > Delete
  Databases > selected unencrypted DB instance > Actions > Delete > Uncheck "Create final snapshot" > Check acknowledgement > Delete
  <img width="1327" height="928" alt="image" src="https://github.com/user-attachments/assets/de1cce43-4e34-4fe6-9197-99902e44551f" />

  **Check Config rule compliance**
  AWS Config > RDS_Storage_Encrypted > Actions > Re-evaluate
  Should be compliant
  <img width="2189" height="251" alt="image" src="https://github.com/user-attachments/assets/c13a15bc-2a8f-43a9-9351-b3b7cbf1535f" />

  
</details>

## Module 3: Creating an Encrypted RDS Instance
<details>
  
### Attempt to create new RDS DB instance w/o encryption
Use these settings to create a new RDS:
* Standard Create
* MySQL
* Dev/Test
* Auto-generate password
* VPC: workshop-vpc, Subnet group: DBSubnetGroup
* Additional configuration > Uncheck: Enable encryption
* Uncheck: Enable Enhanced monitoring
Results in:

<img width="1649" height="259" alt="image" src="https://github.com/user-attachments/assets/58b23354-dc6f-4ffe-8937-fe1ef45e3887" />

**Review IAM Access for ProjectRole**
Looking at the "custom" rule:

<img width="518" height="257" alt="image" src="https://github.com/user-attachments/assets/c266fe5d-c276-41a2-bda5-7f2fe444319d" />

This enforces ProjectRole to only be able to create encrypted RDS instances. Therefore, enable encryption when creating the DB when as ProjectRole.
<img width="2253" height="170" alt="image" src="https://github.com/user-attachments/assets/451de265-fb55-46d8-8b14-a87b6d334307" />
</details>

## Module 4: Sharing Encrypted RDS Snapshot
<details>

  <img width="1140" height="657" alt="image" src="https://github.com/user-attachments/assets/fa53b24a-e6d5-4110-ad24-326a58cdb6ab" />

  Error (due to permissions):
  
  <img width="737" height="635" alt="image" src="https://github.com/user-attachments/assets/fa9eae0b-6c6e-4c22-a9b4-120c838b3194" />

  Adjusting RMS Key as Admin Role:
  
  <img width="542" height="444" alt="image" src="https://github.com/user-attachments/assets/5bfb5243-7904-4096-bfeb-6410c293f718" />
  
  <img width="712" height="577" alt="image" src="https://github.com/user-attachments/assets/60956938-45ce-4b19-9572-e61ad4fc0f02" />

</details>

# EBS Track

This module continues your journey to address the privacy and compliance challenges of the existing configuration. Based on the recommendation of Amazon Web Services architects, you will:
* Create and assign permissions to a KMS key
* Encrypt existing EBS volumes
* Troubleshoot AWS KMS permission challenges, and learn how to fix them
* Enforce encryption on EBS volumes
* Share an encrypted EBS volume with another AWS Account

## Module 1: Manage & Assign KMS Key to an EBS Volume
<details>
  
  Using an Admin role create a KMS CMK Key with:
  
  * Key type: Symmetric
  * Key material origin: KMS
  * Key alias: workshop-key-ebs
  * Admin permissions: Admin role
  * Key usage permissions: Participant role
    
</details>

## Module 2: Remediate Un-encrypted EBS Volume
<details>

  ### Create a new Un-Encrypted Volume
  EC2 > Elastic Block Store > Volumes > Create Volumes
  Leave everything default - ensure *Encrypt this volume* is unchecked
  Tag: Key: `Name`, Value: `WS_Unencrypted_Volume`

  ### Create a new Encrypted Volume
  EC2 > Elastic Block Store > Volumes > Create Volumes
  Check *Encrypt this volume*
  KMS Key: select `workshop-key-ebs`
  Tag: Key: `Name`, Value: `WS_New_Encrypted_Volume`

  <img width="958" height="192" alt="image" src="https://github.com/user-attachments/assets/4906fe54-fed7-4c65-8e1b-aba2aa22564f" />

  ### Enable default encryption for EBS Volumes
  EC2 Dashboard > Account attributes: Settings: Data protection and security >
  <img width="1683" height="970" alt="image" src="https://github.com/user-attachments/assets/1e1095ea-ccea-42a0-ab0a-308f6aeaf7df" />
  Select *Enable* -> Use the suggested `alias/aws/ebs` or symmetric CMK created earlier. -> Update

  ### Encrypting a new EBS Volume while launching EC2 Instance
  Launch Instance with:
  * AMI: Free Tier
  * Architecture: 64-bit (x86)
  * t2.micro (unavailable in ap-southeast-2 free tier)
  * VPC: workshop-vpc-ebs
  * Storage: Advanced > Encrypted: Encrypted > KMS Key: workshop-key-ebs
  <img width="1096" height="689" alt="image" src="https://github.com/user-attachments/assets/39b5b26e-f4f7-45e1-b23e-8fe3afd77a49" />


  ### Encrypting existing EBS volume
  Elastic Block Store > Volumes > WS_Unencrypted_Volume > Actions > Create Snapshot
  Description: Un-encrypted snapshot of un-encrypted Volume
  Add tag: Key: `Name`, Value: `WS_Unencrypted_Snapshot`

  Copy EBS Snapshot:
  
  <img width="970" height="994" alt="image" src="https://github.com/user-attachments/assets/86af8670-05c8-473a-8357-e9682cb64288" />

  Select copy > Create Volume from snapshot > Add key: Name, Value: Encrypted_Volume > Create Volume
  
  <img width="1665" height="2728" alt="image" src="https://github.com/user-attachments/assets/6f6e3eb6-d6a9-481e-bb84-b2b9032519a2" />

</details>

## Module 3: Sharing EBS Volume
<details>
  
  ** **It is not possible to share an encrypted EBS Volume. Instead, create and share an encrypted EBS snapshot.**

  Snapshots > Select snapshot > Actions > Snapshot settings > Modify permissions > Add central account ID
  * Ensure snapshot is made **private**
  * 
  <img width="738" height="730" alt="image" src="https://github.com/user-attachments/assets/7a969ed2-2cad-4368-8e85-65441fcf46d1" />

  Modify KMS permissions:

  <img width="585" height="472" alt="image" src="https://github.com/user-attachments/assets/9c730622-25f6-4f1c-8f81-d134da0cb8a1" />

  Check results in Central Account:
  
  <img width="680" height="544" alt="image" src="https://github.com/user-attachments/assets/2b00bfbb-e939-4efe-9832-b37c407819ea" />


</details>


# Querying KMS CloudTrail logs with Athena

CloudTrail > Event history > Create Athena table > Storage location: `workshop-kms-s3-cloudtrail-...`

<img width="877" height="107" alt="image" src="https://github.com/user-attachments/assets/b71d56cc-ea10-4e27-8814-6093aa8a4738" />

Set up query result location (button in popup) 

<img width="1646" height="947" alt="image" src="https://github.com/user-attachments/assets/2cfae712-8c51-4ee4-ac9a-eb78223fbaec" />

Generate query:
<img width="2554" height="1108" alt="image" src="https://github.com/user-attachments/assets/67cab9a5-cd6c-47a6-b455-81cd9c41c1e0" />

<img width="2156" height="995" alt="image" src="https://github.com/user-attachments/assets/e9f15258-8ea9-4b31-876e-9ee580d5dd15" />

<img width="2180" height="779" alt="image" src="https://github.com/user-attachments/assets/2ab2ea4a-5c01-4fdd-a5ee-1aa2f2f83f86" />




