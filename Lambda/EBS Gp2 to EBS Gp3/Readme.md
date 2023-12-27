### Project Description

As a Cloud Engineering team we take care of the AWS environment and make sure it is in compliance with the organizational policies. We use AWS cloud watch in combination with AWS Lambda to govern the resources according to the policies. For example, we Trigger a Lambda function when an Amazon Elastic Block Store (EBS) volume is created. We use Amazon CloudWatch Events. CloudWatch Events that allows us to monitor and respond to EBS volumes that are of type GP2 and convert them to type GP3.

### Devloping Envrionment 

- [EC2](https://aws.amazon.com/pm/ec2)
- [lambda](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html)
- [IAM](https://docs.aws.amazon.com/rolesanywhere/latest/userguide/introduction.html)
- [Snapshots](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSSnapshots.html)


### Steps:

- Create a new lambda function from scratch as EBS_VOLUME_CHECKS
  - Using runtime as python
  - Go with default IAM role and create the function, where you can see the test page 

- Now go to the cloud watch
  - under events go to rulescreate a new rule
  - service as ec2 , select the name of the event as per our project (ebs volume notification) 
  - specify events as creation of volumeany volume ARN
  - select target as lambda function that we wrote above(ebs_volume_checks)
Note:-cloudwatch rules now moved to AWS eventbridge


-  Now go to IAM roles to edit the existing lamda role that we created by default, edit the role by going with inline permissions 
   - (Inline policies are useful when there is a direct one-to-one relationship between the policy and the user or group.
- Now, edit the lambda function, add print(event)..i.e it is importing event in json from cloudwatch logs. So to verify the event we are using print the event.-->test is from the cloudwatch logs by creating a new volume.
- Now I need the ID of my volume from this event, to convert into gp3(by importing python module boto3 package/functionality into our function) as per our project. So, we have to extract the volume id out of entire Json data

)
### Lambda Trigger Code
```bash
import boto3

def get_volume_id_from_arn(volume_arn):
    # Split the ARN using the colon(':') separator
    arn_parts = volume_arn.split(':')
    # The volume ID is the last part of the ARN after the 'volume/'  prefix
    volume_id = arn_parts[-1].split('/')[-1]
    return volume_id
    
    
def lambda_handler(event, context):
    
    volume_arn = event['resources'][0]
    volume_id = get_volume_id_from_arn(volume_arn)
    
    ec2_client = boto3.client('ec2')
    
    response = ec2_client.modify_volume(
        VolumeId=volume_id,
        VolumeType='gp3',
    )

```