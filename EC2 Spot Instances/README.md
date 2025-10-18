# [AWS Launch & Provision EC2 Spot Instances](https://catalog.us-east-1.prod.workshops.aws/workshops/36a2c2bb-b92d-4428-8626-3a75df01efcc/en-US)
## Overview
To understand the fundamentals of Amazon EC2 Spot Instances:
* Recommended tools to launch Spot Instances
* Determine where to launch capacity
* How to test your workload for resiliency, and
* How to visualise the compute savings

### Objectives
* Specify instance configuration in a Launch Template and use that to configure Amazon EC2 Auto Scaling group and EC2 Fleet
* Launch EC2 Spot Instances using Auto Scaling groups and EC2 Fleet to deploy an application
* Evaluate the availability of Spot capacity using Spot Placement Score
* Configure AWS Fault Injection Simulator to simulate Spot Instance interruption
* Visualise the estimated cost savings via cost savings dashboard


# Provision Spot Instances
## Launch Template
1. Create a **launch-template-data.json** configuration file in CloudShell.
```
cat << EOF > launch-template-data.json
{
  "ImageId": "%ami-id%",
  "TagSpecifications": [
    {
      "ResourceType": "instance",
      "Tags": [
        {
          "Key": "Name",
          "Value": "mySpotWorkshop"
        }
      ]
    }
  ],
  "UserData": "%user-data%"
}
EOF
```
2. Replace **ImageId** with an AMI that runs Amazon Linux 2.
```
# Use Amazon Linux 2023 for X86 architecture AMI
export ami_id=$(aws ec2 describe-images --owners amazon --filters "Name=name,Values=al2023*" "Name=virtualization-type,Values=hvm" "Name=root-device-type,Values=ebs" "Name=architecture,Values=x86_64" --query "sort_by(Images, &CreationDate)[-1].ImageId" --output text)

sed -i.bak -e "s#%ami-id%#$ami_id#g" launch-template-data.json
```
3. Replace **UserData**
```
cat << EOF > user-data.sh
#cloud-config
repo_update: true
repo_upgrade: all

packages:
  - httpd
  - curl

runcmd:
  - [ sh, -c, "amazon-linux-extras install -y epel" ]
  - [ sh, -c, "yum -y install stress-ng" ]
  - [ sh, -c, "echo hello world. My instance-id is $(curl -s http://169.254.169.254/latest/meta-data/instance-id). My instance-type is $(curl -s http://169.254.169.254/latest/meta-data/instance-type). > /var/www/html/index.html" ]
  - [ sh, -c, "systemctl enable httpd" ]
  - [ sh, -c, "systemctl start httpd" ]


EOF

export user_data=$(base64 -w 0 ./user-data.sh)

sed -i.bak -e "s#%user-data%#$user_data#g" launch-template-data.json
```
4. Create the launch template using the newly updated config.
```
aws ec2 create-launch-template --launch-template-name TemplateForWebServer --launch-template-data file://launch-template-data.json
```
5. Perform an additional API call to retrieve the identifier of the launch template that was just created, and store it in an ev.
```
export LAUNCH_TEMPLATE_ID=$(aws ec2 describe-launch-templates --filters Name=launch-template-name,Values=TemplateForWebServer | jq -r '.LaunchTemplates[0].LaunchTemplateId')
```
<img width="1016" height="596" alt="image" src="https://github.com/user-attachments/assets/8951c608-9e9e-4a8a-9025-233302797e43" />



## Launch via EC2 Auto Scaling group
<img width="2983" height="1275" alt="image" src="https://github.com/user-attachments/assets/130471f7-e13f-42f6-a09c-4392dd867749" />

1. Create a config .json file using the launch template.
```
cat << EOF > ./asg-policy.json
{
   "LaunchTemplate":{
      "LaunchTemplateSpecification":{
         "LaunchTemplateId":"${LAUNCH_TEMPLATE_ID}",
         "Version":"1"
      },
      "Overrides":[{
         "InstanceRequirements": {
            "VCpuCount": {
               "Min": 2, 
               "Max": 2
            },
            "MemoryMiB": {
               "Min": 0
            },
            "CpuManufacturers": [
               "intel",
               "amd"
            ],
            "InstanceGenerations": [
               "current"
            ],
            "AcceleratorCount": {
               "Max": 0
            }
         }
      }]
   },
   "InstancesDistribution":{
      "OnDemandBaseCapacity":1,
      "OnDemandPercentageAboveBaseCapacity":25,
      "SpotAllocationStrategy":"price-capacity-optimized"
   }
}
EOF
```

2. Retrieve default VPC and its subnets.
```
   export VPC_ID=$(aws ec2 describe-vpcs --filters Name=isDefault,Values=true | jq -r '.Vpcs[0].VpcId')
export SUBNETS=$(aws ec2 describe-subnets --filters Name=vpc-id,Values="${VPC_ID}")
export SUBNET_1=$((echo $SUBNETS) | jq -r '.Subnets[0].SubnetId')
export SUBNET_2=$((echo $SUBNETS) | jq -r '.Subnets[1].SubnetId')
```
3. Create Auto Scaling group across 2 AZs, and desired-capacity of 10 vCPU units.
```
aws autoscaling create-auto-scaling-group --auto-scaling-group-name EC2SpotWorkshopASG --min-size 2 --max-size 20 --desired-capacity 10 --desired-capacity-type vcpu --vpc-zone-identifier "${SUBNET_1},${SUBNET_2}" --capacity-rebalance --mixed-instances-policy file://asg-policy.json
```

<img width="1035" height="655" alt="image" src="https://github.com/user-attachments/assets/550610ce-65a3-4c16-af90-69efb20ebcb2" />



### Challenges
<details>
<summary>1. How many Spot Instance pools does the Auto Scaling group consider when applying Spot diversification?</summary>
  
  _My Answer:_ N/A
  _AWS Explanation:_
Create JSON config file with attributes specified in our ASG:
  
```cat << EOF > ./instance-requirements.json
{
    "ArchitectureTypes": [
            "x86_64"
    ],
    "VirtualizationTypes": [
            "hvm"
    ],
    "InstanceRequirements": {
        "VCpuCount": {
            "Min": 2,
            "Max": 2
        },
        "MemoryMiB": {
            "Min": 0
        },
        "CpuManufacturers": [
            "intel","amd"
        ],
        "InstanceGenerations": [
            "current"
        ],
        "AcceleratorCount": {
            "Max": 0
        }
    }
}
EOF
```

Preview matching instance types: `aws ec2 get-instance-types-from-instance-requirements --cli-input-json file://instance-requirements.json --output text`

<details>
<summary>Terminal</summary>

  <img width="902" height="743" alt="image" src="https://github.com/user-attachments/assets/77fe0baa-82b5-4789-b3ce-f0a001a44cc6" />
  
</details>

Output count of matching instance types: `aws ec2 get-instance-types-from-instance-requirements --cli-input-json file://instance-requirements.json --output text | wc -l`
<details>
<summary>Terminal</summary>

  <img width="873" height="29" alt="image" src="https://github.com/user-attachments/assets/c2c38f12-1436-4537-be00-84fb6d101a51" />
  
</details>

</details>
<details>
<summary>2. How many Spot vs On-Demand Instances have been requested by the Auto Scaling group?</summary>
  
_My Answer:_ With a Desired Capacity of 10 instances (where each instance has 2 vCPUs) and an OnDemandBaseCapacity of 1, the distribution is as follows:
  * Base Capacity: 1 On-Demand instance is guaranteed.
  * Remaining Capacity: 9 instances are left. The 25% On-Demand share for the remaining capacity is $9 \times 0.25 = 2.25.
  * Rounding: The system rounds up to 3 On-Demand instances to meet this minimum requirement.
  This results in a total of 4 On-Demand instances and 6 Spot Instances.

</details>
<details>
<summary>3. How can you confirm which instances have been created within the Auto Scaling group?</summary>

_My Answer:_ We can find instances with the specific measurements from our launch template, such as key-value name of `mySpotWorkshop` **Wrong**

_AWS Explanation:_
Check instances by using describe-auto-scaling-groups: `aws autoscaling describe-auto-scaling-groups --auto-scaling-group-names EC2SpotWorkshopASG`
Filter list of isntaces using `aws:autoscaling:groupName = EC2SpotWorkshopASG` in the [EC2 Dashboard home](https://console.aws.amazon.com/ec2/home?#Home:)

</details>
<details>
<summary>4. How can you check which instances have been launched using the Spot purchasing model and which ones using the On-Demand?</summary>

  _My Answer:_ N/A
  _AWS Explanation:_
  To describe one or more instances you use describe-instances. To retrieve all the Spot Instances that have been launched with the Auto Scaling group, you apply two filters: instance-lifecycle set to spot to retrieve only Spot Instances and the custom tag `aws:autoscaling:groupName` that must be set to EC2SpotWorkshopASG. Review [Tagging lifecycle](https://docs.aws.amazon.com/autoscaling/ec2/userguide/autoscaling-tagging.html#tag-lifecycle) to learn more.
<details>
<summary>Terminal</summary>

  <img width="1221" height="144" alt="image" src="https://github.com/user-attachments/assets/fbd463cd-7bcc-4998-a651-87a2c5034bd2" />

</details>

</details>
<details>
<summary>5. How can you select specific instance types manually instead of ABIS in your Auto Scaling group?</summary>

  _My Answer:_ Modify the `"InstanceRequirements"` in the configuration file when using the launch template. Specify the Instance Types instead.
  _AWS' Explanation:_ Use a .json file, similar to below:
  ```cat << EOF > ./asg-policy.json
{
   "LaunchTemplate":{
      "LaunchTemplateSpecification":{
         "LaunchTemplateId":"${LAUNCH_TEMPLATE_ID}",
         "Version":"1"
      },
      "Overrides":[
         {
            "InstanceType":"m5.large"
         },
         {
            "InstanceType":"c5.large"
         },
         {
            "InstanceType":"r5.large"
         },
         {
            "InstanceType":"m4.large"
         },
         {
            "InstanceType":"c4.large"
         },
         {
            "InstanceType":"r4.large"
         }
      ]
   },
   "InstancesDistribution":{
      "OnDemandBaseCapacity":1,
      "OnDemandPercentageAboveBaseCapacity":25,
      "SpotAllocationStrategy":"price-capacity-optimized"
   }
}
EOF
```

</details>
<details>
<summary>6. How can you select capacity-optimized Spot allocation strategy in your Auto Scaling group?</summary>

  _My Answer:_ Change the `"SpotAllocationStrategy":"price-capacity-optimized"` to `capacity-optimized` instead.
  _AWS' Explanation:_ 
</details>

## Launching via EC2 Fleet
1. Create config file with [ABIS](https://docs.aws.amazon.com/autoscaling/ec2/userguide/create-asg-instance-type-requirements.html)

  ```
cat << EOF > ./ec2-fleet-config.json
{
   "SpotOptions":{
      "SingleInstanceType": true,
      "SingleAvailabilityZone": true,
      "AllocationStrategy": "price-capacity-optimized",
      "InstanceInterruptionBehavior": "terminate"
   },
   "OnDemandOptions":{
      "AllocationStrategy": "lowest-price",
      "SingleInstanceType": true,
      "SingleAvailabilityZone": true
   },
   "LaunchTemplateConfigs":[
      {
         "LaunchTemplateSpecification":{
            "LaunchTemplateId":"${LAUNCH_TEMPLATE_ID}",
            "Version":"1"
         },
         "Overrides":[{
            "InstanceRequirements": {
               "VCpuCount": {
                  "Min": 2, 
                  "Max": 4
               },
               "MemoryMiB": {
                  "Min": 0
               },
               "CpuManufacturers": [
                  "intel"
               ]
            }
         }]
      }
   ],
   "TargetCapacitySpecification":{
      "TotalTargetCapacity": 4,
      "OnDemandTargetCapacity": 0,
      "DefaultTargetCapacityType": "spot"
   },
   "Type":"instant"
}
EOF
```


2. Create the EC2 Fleet, and export its identifier to an ev to later monitor the status of the fleet.

```
export FLEET_ID=$(aws ec2 create-fleet --cli-input-json file://ec2-fleet-config.json | jq -r '.FleetId')
```

View the configuration parameters: `aws ec2 describe-fleets --fleet-ids "${FLEET_ID}"`

<details>
<summary>Error Message:</summary>
  
  ```
~ $ aws ec2 describe-fleets --fleet-ids "${FLEET_ID}"
{
    "Fleets": [
        {
            "ActivityStatus": "fulfilled",
            "CreateTime": "2025-10-17T11:43:02+00:00",
            "FleetId": "fleet-4f1d05bd-9e2c-ee8f-8612-2908d09d0944",
            "FleetState": "active",
            "ClientToken": "2d62c3ef-7e20-4ebd-b045-513d71fe6451",
            "FulfilledCapacity": 0.0,
            "FulfilledOnDemandCapacity": 0.0,
            "LaunchTemplateConfigs": [
                {
                    "LaunchTemplateSpecification": {
                        "LaunchTemplateId": "lt-0c4a446887e6b8efb",
                        "Version": "1"
                    },
                    "Overrides": [
                        {
                            "InstanceRequirements": {
                                "VCpuCount": {
                                    "Min": 2,
                                    "Max": 4
                                },
                                "MemoryMiB": {
                                    "Min": 0
                                },
                                "CpuManufacturers": [
                                    "intel"
                                ]
                            }
                        }
                    ]
                }
            ],
            "TargetCapacitySpecification": {
                "TotalTargetCapacity": 4,
                "OnDemandTargetCapacity": 0,
                "SpotTargetCapacity": 0,
                "DefaultTargetCapacityType": "spot"
            },
            "TerminateInstancesWithExpiration": false,
            "Type": "instant",
            "ValidFrom": "1970-01-01T00:00:00+00:00",
            "ValidUntil": "1970-01-01T00:00:00+00:00",
            "ReplaceUnhealthyInstances": false,
            "SpotOptions": {
                "AllocationStrategy": "price-capacity-optimized",
                "InstanceInterruptionBehavior": "terminate",
                "SingleInstanceType": true,
                "SingleAvailabilityZone": true,
                "MinTargetCapacity": 4
            },
            "OnDemandOptions": {
                "AllocationStrategy": "lowest-price",
                "SingleInstanceType": true,
                "SingleAvailabilityZone": true,
                "MinTargetCapacity": 0
            },
            "Errors": [
                {
                    "LaunchTemplateAndOverrides": {
                        "LaunchTemplateSpecification": {
                            "LaunchTemplateId": "lt-0c4a446887e6b8efb",
                            "Version": "1"
                        },
                        "Overrides": {
                            "InstanceRequirements": {
                                "VCpuCount": {
                                    "Min": 2,
                                    "Max": 4
                                },
                                "MemoryMiB": {
                                    "Min": 0
                                },
                                "CpuManufacturers": [
                                    "intel"
                                ]
                            }
                        }
                    },
                    "Lifecycle": "spot",
                    "ErrorCode": "InvalidParameterCombination",
                    "ErrorMessage": "The specified instance type is not eligible for Free Tier. For a list of Free Tier instance types, run 'describe-instance-types' with the filter 'free-tier-eligible=true'."
                },
```
</details>
## Optimally running Spot Capacity
Use CLI to calculate Spot Placement Score:
```
cat << EOF > ./sps-input.json
{
    "InstanceRequirementsWithMetadata": {
        "ArchitectureTypes": [
            "x86_64"
        ],
        "InstanceRequirements": {
            "VCpuCount": {
                "Min": 4,
                "Max": 8
            },
            "MemoryMiB": {
                "Min": 16384
            }
        }
    },
    "TargetCapacity": 100,
    "TargetCapacityUnitType": "vcpu",
    "SingleAvailabilityZone": false
}
EOF
```
Request Spot placement score: `aws ec2 get-spot-placement-scores --cli-input-json file://./sps-input.json`
Returns:
<img width="612" height="652" alt="image" src="https://github.com/user-attachments/assets/29dbc53c-4fe4-4f1b-8802-0b2e4a3bf4b6" />

# Simulating Spot interruptions
<img width="898" height="457" alt="image" src="https://github.com/user-attachments/assets/0eb14ff8-0eed-4220-9de1-ac4d8b97b178" />
<img width="986" height="620" alt="image" src="https://github.com/user-attachments/assets/cd6605c3-5d60-4e00-b494-cdbf066933b0" />

# Spot Saving
<img width="1001" height="422" alt="image" src="https://github.com/user-attachments/assets/8a650a94-cb26-4be6-90b0-be31c375c8e6" />

# Clean Up
<img width="760" height="383" alt="image" src="https://github.com/user-attachments/assets/6afccf02-b669-4622-9698-9635d697dbdb" />


