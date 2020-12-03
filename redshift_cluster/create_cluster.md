## Create Redshift Cluster through IaC (Infrastructure as Code)
###### Version 1.0 - November 2020

#### 1.0 Getting Started - What is IaC?

<p>Infrastructure as code (IaC) is the process of managing and provisioning computer data centers through machine-readable definition files, rather than physical hardware configuration or interactive configuration tools. The IT infrastructure managed by this process comprises both physical equipment, such as bare-metal servers, as well as virtual machines, and associated configuration resources. The definitions may be in a version control system. It can use either scripts or declarative definitions, rather than manual processes, but the term is more often used to promote declarative approaches. (Wikipedia: https://en.wikipedia.org/wiki/Infrastructure_as_code).</p>

<p>Now that we have the technical definition out of the way, in short: IaC allows people to spin up, pause, and delete machines to process a targeted set of operations without having to use the command line interface (CLI). This allows data engineers to save time, increase consistency, and even bid out use of elastic infrastructure... utimately saving money.</p>

#### 2.0 Files needed to Launch

###### 2.1 `dwh.cfg`

<p> The "dwh.cfg` file holds configurations information for setting up the cluster. Largely made up of credentials and settings, this file holds information that is used across systems and files to authenticate and direct the flow of information. More details can be found in the "Details of Code within Files" section.</p>

###### 2.2 `create_cluster.py`

<p> The 'create_cluster.py' file is the main file used to create the redshift cluster. This file contains the main portions of IaC that will establish a connection to redshift services, and create server instances. More details can be found in the "Details of Code within Files" section.</p>

###### 2.3 `delete_cluster.py`

<p> The 'delete_cluster.py` file is used to delete redshift instances after they have been used to perform a business action. It is important to shutdown servers and clusters that are not in use, particularly because users are charge for the amount of time and information being processed. More details can be found in the "Details of Code within Files" section.</p>

#### 3.0 Details of Code within Files

###### 3.1 `dwh.cfg` - Configuration File

###### 3.2 `create_cluster.py` - Launch Cluster File

###### -- Libraries Import --

<p>This section will import external libraries that are referenced within the code to execute commands from existing code base.</p>

import pandas as pd
import boto3
import json
import configparser
from pprint import pprint
from time import time

###### -- Load all config parameters from the config file "dwh.cfg" --

<p>This section will setup variables for use within the IaC commands using the "configparser" library. </p>

config = configparser.ConfigParser()
config.read_file(open('dwh.cfg'))

KEY                    = config.get('AWS','KEY')
SECRET                 = config.get('AWS','SECRET')

DWH_CLUSTER_TYPE       = config.get("DWH","DWH_CLUSTER_TYPE")
DWH_NUM_NODES          = config.get("DWH","DWH_NUM_NODES")
DWH_NODE_TYPE          = config.get("DWH","DWH_NODE_TYPE")

DWH_IAM_ROLE_NAME      = config.get("DWH", "DWH_IAM_ROLE_NAME")
DWH_CLUSTER_IDENTIFIER = config.get("DWH","DWH_CLUSTER_IDENTIFIER")

DB_NAME=config.get("CLUSTER","DB_NAME")
DB_USER=config.get("CLUSTER","DB_USER")
DB_PASSWORD=config.get("CLUSTER","DB_PASSWORD")
DB_PORT=config.get("CLUSTER","DB_PORT")

DWH_ENDPOINT = config.get("CLUSTER","HOST")
DWH_ROLE_ARN = config.get("IAM_ROLE","DWH_ROLE_ARN")

SONG_DATA = config.get("S3","SONG_DATA")
LOG_DATA = config.get("S3","LOG_DATA")
LOG_JSONPATH = config.get("S3","LOG_JSONPATH")


# Create the needed AWS Clients

ec2 = boto3.resource('ec2',
                 region_name="us-west-2",
                 aws_access_key_id=KEY,
                 aws_secret_access_key=SECRET
                 )

s3 = boto3.resource('s3',
                 region_name="us-west-2",
                 aws_access_key_id=KEY,
                 aws_secret_access_key=SECRET
                 )

iam = boto3.client('iam',
                     region_name="us-west-2",
                     aws_access_key_id=KEY,
                     aws_secret_access_key=SECRET
                  )

redshift = boto3.client('redshift',
                 region_name="us-west-2",
                 aws_access_key_id=KEY,
                 aws_secret_access_key=SECRET
                 )

    
# Create the IAM Role to have Redshift Access the S3 Bucket (ReadOnly)

def create_role(DWH_IAM_ROLE_NAME):
    try:
        print("1.1 Creating a new IAM Role") 
        dwhRole = iam.create_role(
            Path='/',
            RoleName=DWH_IAM_ROLE_NAME,
            Description = "Allows Redshift clusters to call AWS services on your behalf.",
            AssumeRolePolicyDocument=json.dumps(
                {'Statement': [{'Action': 'sts:AssumeRole',
                   'Effect': 'Allow',
                   'Principal': {'Service': 'redshift.amazonaws.com'}}],
                 'Version': '2012-10-17'})
            )    
    except Exception as e:
        print(e)

    
# Attach Policy

def attach_policy(DWH_IAM_ROLE_NAME):
    print("1.2 Attaching Policy")
    iam.attach_role_policy(RoleName=DWH_IAM_ROLE_NAME,
                   PolicyArn="arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess"
                  )['ResponseMetadata']['HTTPStatusCode']


# Get & Print the IAM Role ARN

def get_role(DWH_IAM_ROLE_NAME):
    print("1.3 Get the IAM role ARN")
    roleArn = iam.get_role(RoleName=DWH_IAM_ROLE_NAME)['Role']['Arn']
    print(roleArn)
    return roleArn


# Create the Redshift Cluster

def create_cluster(DWH_CLUSTER_TYPE, DWH_NODE_TYPE, DWH_NUM_NODES, DB_NAME, DWH_CLUSTER_IDENTIFIER, DB_USER, DB_PASSWORD, roleArn):

    try:
        response = redshift.create_cluster(        
            #HW
            ClusterType=DWH_CLUSTER_TYPE,
            NodeType=DWH_NODE_TYPE,
            NumberOfNodes=int(DWH_NUM_NODES),

            #Identifiers & Credentials
            DBName=DB_NAME,
            ClusterIdentifier=DWH_CLUSTER_IDENTIFIER,
            MasterUsername=DB_USER,
            MasterUserPassword=DB_PASSWORD,
        
            #Roles (for s3 access)
            IamRoles=[roleArn]  
            )

    except Exception as e:
        print(e)


# Run "connection.py" functions to create role, attach policy, set roleArn, and create the cluster.        
        
def main():
    
    create_role(DWH_IAM_ROLE_NAME)
    attach_policy(DWH_IAM_ROLE_NAME)
    roleArn = get_role(DWH_IAM_ROLE_NAME)
    create_cluster(DWH_CLUSTER_TYPE, DWH_NODE_TYPE, DWH_NUM_NODES, DB_NAME, DWH_CLUSTER_IDENTIFIER, DB_USER, DB_PASSWORD, roleArn)

    
if __name__ == "__main__":
    main()

#### 4.0 Executing the Files
