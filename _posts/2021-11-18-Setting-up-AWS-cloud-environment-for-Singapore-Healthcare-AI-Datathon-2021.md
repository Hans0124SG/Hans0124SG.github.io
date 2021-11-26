---
layout: post
title: Setting up AWS cloud environment for Singapore Healthcare AI Datathon 2021
date: '2021-11-18 12:00:00 +0800'
categories: Datathon
published: true

---

<style>body {text-align: justify}</style>

Last year when I was preparing Huawei Cloud for our annual Singapore Healthcare AI Datathon event, I did not think of writing a summary, which could have greatly helped the setup this year. 

This year, I am setting up AWS as one of the cloud environments, so I decide to write down the process.

### Elastic Cloud Compute (EC2)

We provide one EC2 for every team to carry out their computing tasks. 

First of all, AWS has a default vCPU limit for every account, which in our case is not enough. A limit increase needs to be requested. Here is the [user guide](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-on-demand-instances.html) for this.

To pick the right instance type, there are a few considerations.

1. Storage: Participants will need to store their medical imaging datasets, which can go up to a few hundred GBs.
2. GPU: Some teams need to train deep learning models, so GPU is necessary.
3. Environment: Common tools and packages should be pre-installed for the teams to save their set-up time.

In this year's datathon, we choose AWS's `g4dn.12xlarge` instance that has 4* NVIDIA T4 GPUs, 48 vCPUs and 196GB system memory. `g4dn.12xlarge` also comes with 900 GB NVMe SSD storage that can be mounted. Take note that this NVMe SSD storage will be deleted when the instance is stopped, so don't keep any data (except for temporary ones) inside unless the instance will be kept running until the end of datathon. For teams who are not using GPU, we choose `m5.8xlarge` instances.

<u><i>Launching the first instance (example of GPU instance)</i></u>

1. Search for "**Deep Learning AMI (Ubuntu 18.04) Version 52.0**" AMI, which has all the mainstream deep learning environment set up
2. Choose the instance type `g4dn.12xlarge`
3. Configure the storage needed, increase the root volume to 1000 GB if budget allows (default is 110 GB which is almost full as the environment already takes up around 100 GB)
4. Configure one security group with port 22 open in the inbound rules (either use the default security group or create a new one)

<u><i>Setting up the environment</i></u>

1. `sudo apt update`
2. Install Jupyter Lab:`source activate python3 && pip install jupyterlab`
3. Install R and R Kernel for Jupyter: `conda install -c conda-forge r-irkernel`
4. Install other packages that are required
5. Test to make sure Jupyter Lab, Python kernels and R kernel are working properly with port forwarding, and GPU can be properly seen by your deep learning library.
6. Download the `mimic-cxr-jpg` dataset directly to the machine

<u><i>After S3 is set up</i></u>

1. Create an IAM role with `AmazonS3ReadOnlyAccess` and `AmazonSSMManagedInstanceCore` and associate to the EC2 in the console
2. Test to make sure the EC2 can see and download file from S3

<u><i>After RDS is set up</i></u>

1. Create Python and R example notebooks to show how to connect to the RDS

<u><i>Backup the instance</i></u>

1. Create an image from the EC2
   1. the size of the EBS volume can be increased when you launch new instance from it

<u><i>Launching more instances</i></u>

1. Use Python AWS SDK to create instances and private keys in batch
2. Associate the IAM role to all the EC2 in the console
3. Take note of the IP address and the private keys for each EC2 for distribution

<br>

### Relational Database Service (RDS)

We build our EHR databases on RDS for every team to query from. This year, we provide `MIMIC-IV` with ED and CXR, as well as `eICU`.

<u><i>Preparing a backup of the databases</i></u>

1. Login to our own machine that has the EHR databases installed
2. `pg_dump` each database into a single .sql file
   1. The `eicu` database in our server does not have views, a separate dump from `eicu-views.pgsql` needs to be imported

<u><i>Create an EC2</i></u>

1. Make sure the EC2 has enough space (e.g. 512GB) in the root volume
2. Install `postgresql & postgresql-client` on the EC2 to enable `psql` command ([example](https://computingforgeeks.com/install-postgresql-12-on-ubuntu/) for PostgreSQL 12)
3. Make sure port 22 are open in the inbound rules of the security group to allow SSH access
4. `scp` the .sql backup files from your own machine to the EC2

<u><i>Create an RDS</i></u>

1. Launch an RDS instance (start with a low-end instance to save cost, upgrade to a better instance a few days before datathon starts)
   1. Set the maintenance window to low-usage hours (like 4AM)
2. Create a separate security group for RDS that has only one inbound rule for the security group (5432 port)
3. Connect to the RDS via local `pgadmin4` by SSH tunneling using the EC2
4. Create the empty databases in the RDS (e.g. mimiciv, eicu)

<u><i>Importing the database backup from EC2 (based on official <aref src="https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/PostgreSQL.Procedural.Importing.EC2.html"> guide </aref> from AWS)</i></u>

1. Import the database with `psql` from the EC2

<u><i>Roles management</i></u>

1. Set up the respective `team` and `teammaster` roles, grant necessary priviledges
2. Create individual team roles with different passwords, in order for them to create tables in their own schemas
3. Let team login via pgadmin4 using their own username and password

<br>

### Simple Storage Service (S3)

We put our medical imaging datasets in S3 for teams to download if they need. ([reference](https://adamtheautomator.com/upload-file-to-s3/))

1. Create an S3 bucket
2. Create an IAM role with only permission policy as `AmazonS3FullAccess`, download the access key
3. Install AWS CLI on your local machine
4. Configure AWS CLI credentials using `access key ID` and `secret access key` from the IAM role created with `AmazonS3FullAccess` (default region name: ap-southeast-1, default output format: json)
5. Copy the data from our local machine to S3 with `aws s3 cp local_file s3://bucket_name`
6. Create an VPC endpoint gateway for S3 for EC2 to directly talk to it