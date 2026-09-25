# 01 - Prepare Your Environment to Utilize Terraform (Steps for Mac OS)

## Install Terraform on your device
1. utilize the terminal and run 'brew tap hashicorp/tap" and then 'brew install hashicorp/tap/terraform'
2. OR you can go to https://developer.hashicorp.com/terraform/install and install the proper package manager
3. Make sure your Terraform binary file is sitting in the directory path that you can easily reference.

## Ensure Visual Studio Code IDE is downloaded
1. Access the download link for your machine here: https://code.visualstudio.com/download?_exp_download=d53503e735

## Set UP AWS Credentials for Terraform
1. Login to your AWS Management Console
2. IAM -> Users -> Create User. Name it what you'd like. Select Attach Policies directly and add "AmazonVPCFullAccess" (specific to this learning path, add whichever policies are relevant) -> Next -> Create User.
3. Select the newly created user. Click Security Credentials tab -> Access Keys - Create Access Key, click 'Command Line Interface CLI' -> Create Access key.
4. Open terminal and run the following replacing the values with your Access Key credentials. Also updating region.
export AWS_ACCESS_KEYID='remove quotes and add your key name'
export AWS_SECRET_ACCESS_KEY='remove quotes and add your key pass'
export AWS_DEFAULT_REGION=us-east-1

## Set up GitHub Credentials for Terraform
1. Login to your Github and access Developer Settings. Select Personal access tokens -> Fine-grained tokens -> name your token, set expiry, and give description. Reposity access = public.
2. Permissions select Repository Permissons and add Administration (read and write) and Contents (read and write). Generate token. 
3. Utilize this token and run the following
export GITHUB_TOKEN='remove quotes and paste token'