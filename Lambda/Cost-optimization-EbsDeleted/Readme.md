### AWS Cloud Cost Optimization

## Identifying Stale EBS Snapshots
In this example, we'll create a Lambda function that identifies EBS snapshots that are no longer associated with any active EC2 instance and deletes them to save on storage costs.

## Description:
The Lambda function fetches all EBS snapshots owned by the same account ('self') and also retrieves a list of active EC2 instances (running and stopped). For each snapshot, it checks if the associated volume (if exists) is not associated with any active instance. If it finds a stale snapshot, it deletes it, effectively optimizing storage costs.

### Deployment Enviroment 

- [EC2](https://aws.amazon.com/pm/ec2)
- [lambda](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html)
- [IAM](https://docs.aws.amazon.com/rolesanywhere/latest/userguide/introduction.html)
- [Snapshots](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSSnapshots.html)

<<<<<<< HEAD

### Steps:
- Create EC2 Instance 
  - Attach Snapshots to EC2 Instance it will backup our ec2 instance and store the data on it.
- Create IAM Role 
  - Attach the role of policy/permission , the Lambda have some privillege that its communicate with other resources.
  - List of Policy:
  ```bash
  {
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "ec2:DescribeInstances",
                "ec2:DeleteSnapshot",
                "ec2:DescribeVolumes",
                "ec2:DescribeSnapshots"
            ],
            "Resource": "*"
        }
    ]
   }
  ```
- Create a Lambda Funtion
  - Create a new lambda function from scratch as Cost-Optimaization-SnapShots 
  - Using runtime as python 
  - Go with existing iam role we created.
  - Execution time increase to 10s.
  - Python code 

  ```bash
  import boto3

  def lambda_handler(event, context):
    ec2 = boto3.client('ec2')

    # Get all EBS snapshots
    response = ec2.describe_snapshots(OwnerIds=['self'])

    # Get all active EC2 instance IDs
    instances_response = ec2.describe_instances(Filters=[{'Name': 'instance-state-name', 'Values': ['running']}])
    active_instance_ids = set()

    for reservation in instances_response['Reservations']:
        for instance in reservation['Instances']:
            active_instance_ids.add(instance['InstanceId'])

    # Iterate through each snapshot and delete if it's not attached to any volume or the volume is not attached to a running instance
    for snapshot in response['Snapshots']:
        snapshot_id = snapshot['SnapshotId']
        volume_id = snapshot.get('VolumeId')

        if not volume_id:
            # Delete the snapshot if it's not attached to any volume
            ec2.delete_snapshot(SnapshotId=snapshot_id)
            print(f"Deleted EBS snapshot {snapshot_id} as it was not attached to any volume.")
        else:
            # Check if the volume still exists
            try:
                volume_response = ec2.describe_volumes(VolumeIds=[volume_id])
                if not volume_response['Volumes'][0]['Attachments']:
                    ec2.delete_snapshot(SnapshotId=snapshot_id)
                    print(f"Deleted EBS snapshot {snapshot_id} as it was taken from a volume not attached to any running instance.")
            except ec2.exceptions.ClientError as e:
                if e.response['Error']['Code'] == 'InvalidVolume.NotFound':
                    # The volume associated with the snapshot is not found (it might have been deleted)
                    ec2.delete_snapshot(SnapshotId=snapshot_id)
                    print(f"Deleted EBS snapshot {snapshot_id} as its associated volume was not found.")
  ```

- It will be trigger when only the Shapshots will not attach to any Ec2 Instance.

=======
>>>>>>> 196dced22f8d72924e7c4f7da4b1e91701f25c47

