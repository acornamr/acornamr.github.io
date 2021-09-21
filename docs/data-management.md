# Data Management

Raw clinical data and lab data are managed in ACORN with REDCap and with each site lab data management software. 
The dashboard is used when all clinical and lab data has been collected. It is the tool to combine these data into an .acorn file and to save this file on servers. *(an .acorn file contains data frames that can be read with R.)*
We use Amazon Web Services (AWS) to manage the ACORN data and to create backups on a secure platform.


# Data on AWS

For ACORN, we manage several independent buckets containing all the data elements required to operate the dashboard and securely save the data created (`.acorn` files).

<img src="./images/acorn2-buckets.png" alt="Data Buckets" width = "80%"/>

The buckets are of four types:

- site buckets `acornamr-cyXXX` (one for each site). Each site bucket contains :
    - several `.acorn` files with data specific to the site.
    - two data dictionaries files for laboratory data in WHONET and tabular formats.
- one demo bucket `acornamr-demo` containing `.acorn` files that can be showcase for demonstration purposes.
- one bucket for data used across sites `shared-acornamr`. It contains:
    - a file with ACORN project lab codes.
    - encrypted files with credentials to access each site bucket.
- one backup bucket `acornamr-backup`. *Using the AWS DataSync service, we have created daily backup tasks: at 10am UTC, a backup of all new objects in every site buckets is completed in this backup bucket located in “US East (N. Virginia) us-east-1” region.*


# Add a New Site

Adding a site require several steps that we will detail.

- create a bucket for the site
- add one or several users in AWS with appropriate access to the site data/bucket
- include the bucket in the backup plan

Several files/tools/infos are used in the process and should be gathered beforehand:

- MS Excel file `ACORN2_Cred.xlsx`
- Credentials for access to AWS acornamr console
- REDCap API keys for F01-F05 forms and HAI.
- R script for `create_encrypted_credentials.R`

## Create a Site Bucket

In **AWS S3**, create a bucket `acornamr-cyXXX` with :

- `cy` the [two-letters ISO country code ](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2#Officially_assigned_code_elements)
- `XXX` the number of the site, starting at `001` and adding a unit with every new site added to the project.

and the following settings:

- region set to `EU (Paris) eu-west-3`
- all public access blocked
- enable bucket versioning
- server-side encryption with Amazon S3 key (SSE-S3)

## Create a Policy

In **AWS Identity and Access Management (IAM)**, create a policy, using JSON, name it “cyXXX-LRW-policy”:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "statement1",
            "Effect": "Allow",
            "Action": [
                "s3:GetBucketLocation",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::acornamr-cyXXX"
            ]
        },
        {
            "Sid": "statement2",
            "Effect": "Allow",
            "Action": [
                "s3:GetObject"
            ],
            "Resource": [
                "arn:aws:s3:::acornamr-cyXXX/*"
            ]
        },
        {
            "Sid": "statement3",
            "Effect": "Allow",
            "Action": [
                "s3:PutObject"
            ],
            "Resource": [
                "arn:aws:s3:::acornamr-cyXXX/*"
            ]
        }
    ]
}
```

## Create a User

In **AWS IAM**, add user

- User name: “admin-cyXXX”
- Access type: Programmatic access
- Attach existing policies directly, search for “cyXXX-LRW-policy”.
- Copy the access key and secret key in a safe place

### Create a Backup Task

In **AWS DataSync**, add a backup task to create a daily backup task:

- Create a new location
- Location type: Amazon S3; Region: Europe (Paris)
- Select the bucket "acornamr-cyXXX"
- Storage Class Standard
- Folder "" (blank)
- IAM role: daily backup role
- (NEXT)
- Location type: Amazon S3; Region: US East (N. Virginia)
- S3 bucket is acornamr-backup
- Folder prefix "cyXXX"
- IAM role: daily-backup-role
- execute this task at 10:00
- (NEXT)
- name it backup-cyXXX
- target is acornamr-backup


## Create Credentials with R

Add a line to `ACORN2_Cred.xlsx` with the following information:

- site name
- user name and password, possibly using https://xkpasswd.net/s/
- access and secret keys obtained when creating the user
- Two REDCap API keys for the site

Share this updated `ACORN2_Cred.xlsx` file with Olivier who will:

- generate credentials with the script https://github.com/acornamr/acorn-dashboard/blob/master/misc/create_encrypted_credentials.R
- upload these new credentials to the `shared-acornamr` bucket via FTP.
- test that these new credentials are working properly.


# Access AWS via FTP

Several FTP clients are available to access AWS, we recommend [Cyberduck](https://en.wikipedia.org/wiki/Cyberduck) that is multi-platform and free.

To access AWS buckets, you will need to:

1. connect to the AWS console using acorn project credential
2. create a KEY and SECRET pair in the console
3. use this KEY and SECRET pair to set up a connection in your FTP client


The following actions should be performed with a FTP client:

- update the lab codes by replacing `ACORN2_lab_codes.xlsx` in /shared-acornamr bucket
- add/remove a user by adding/removing `encrypted_cred_xxYYYY_zzz.rd` in /shared-acornamr bucket
- update a site data dictionary by replacing `ACORN2_lab_data_dictionary_xxYYY_TABULAR.xlsx` and/or `ACORN2_lab_data_dictionary_xxYYY_WHONET.xlsx` in the site bucket.
- download `.acorn` or `.acorn_non_anonymised` files from a site bucket.