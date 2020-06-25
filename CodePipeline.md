# Steps to create a pipeline

## Create a new user for Dev

1. Navigate to the **IAM** Console, and then choose **Users** from the navigation pane.
2. Select **Add User**. Give it a name and tick on **Programmatic access**. Choose **Next:Permissions**
3. In the **Set permissions** menu, choose **Attach existing policies directly**. Search for and select the following policies:
AmazonS3FullAccess, AmazonDynamoDBFullAccess, AWSAppSyncAdministrator, IAMReadOnlyAccess, AmazonCognitoPowerUser, AWSCodePipelineFullAccess.
4. Choose **Next: tags**, and then **Next: Review**. Review your policies and select **Create user**   
5. Copy and download the Access Key ID and Secret Access Key. This is the only time you can download it so do not close the screen before downloading! Then select **CLose**
6. Select your Username then **Add inline policy**
In the Visual Editor section, click **Choose a service** and search for and select **IAM**. In the **Actions** menu, drop down **Write** and tick on the **CreateRole** box. In the **Actions** menu, drop down **Permissions Management** and tick on the **PutRolePolicy** box.
3. In the **Resources** menu, select **All resources**
4. Select **Review policy**. Give it a name **iam_create_role_policy** and select **Create policy.

## Store secrets in parameter store through CLI

aws ssm put-parameter --name DEV_ACCESS_KEY-ID --value `VALUE` --type SecureString

aws ssm put-parameter --name DEV_SECRET_ACCESS_KEY --value `VALUE` --type SecureString


## Set up

1. Source stage uses AWS CodeCommit.
- In the console, navigate to **AWS CodeCommit** and choose **Create repository**. Enter a name and description and choose create. Read Readme.md for steps for SSH connection to CodeCommit and how to connect to CodeCommit repository.
2. Terraform stores the state files in S3 and a record of the deployment in DynamoDB. So, we create a DynamoDB table and an S3 bucket.
### Create an S3 Bucket
- In the console, navigate to **S3** and choose **Create Bucket**.
- Enter a **unique name** and **Region** where your resources are and choose **Next**.
- Enable **Versioning** and **Default encryption**, and then choose **Next**.
- Select **Block all public access**, choose **Next**, and then choose **Create bucket**.
### Create a DynamoDB Table
- Navigate to **Amazon DynamoDB**, and then choose **Create table**.
- Give your table a name *terraform-state-lock-dynamo*.
- Enter *LockID* as your **Primary key**, keep the box checked for **Use default settings**, and then choose **Create**.


## Create pipeline

1. Navigate to the **AWS CodePipeline** console, and then choose **Create pipeline**.
Enter a **Pipeline name**, select **New service role**, and then choose **Next**
2. Select **AWS CodeCommit** as the **Source provider**, select the name of the repository you created, and then choose **master** as your **Branch name**.
3. Choose **Amazon CloudWatch Events (recommended)** as your detection option, and then choose **Next**.
4. ### Build stage uses AWS CodeBuild which uses a buildspec file, which is a collection of build commands, variables and related settings, in a YAML file, that CodeBuild uses to run a build.
For **Build provider**, choose **AWS CodeBuild** and change your region as needed, and then choose **Create project**.
5. A new screen will open in your browser with the **AWS CodeBuild** console.
    1. Enter a Project name and description, and then scroll to the Environment section. For **Environment image**, choose **Managed image**, and then configure the following:
      Operating system: Ubuntu
      Runtimes(s): Standard
      Image: aws/codebuild/standard:1.0
      Image version: Always use the latest image for this runtime version
    2. Select the checkbox under Privileged, select New service role, and take note of this Role name because you will be modifying it later.
    3. In the **Buildspec** section, choose **Use a buildspec** file. You don’t need to provide a name because buildspec.yaml in your ZIP package is the default value CodeBuild will look for.
    4. In the **Logs** section, choose the checkbox next to **CloudWatch logs – optional**, and then choose **Continue to CodePipeline**.

6.  Back in the **CodePipeline** console, choose **Environment variables** then enter the following values:
Name: AWS_REGION
Value: `your Region`
Type: Plaintext
Select **Add Environment variables** then enter the following values:
Name: DEV_ACCESS_KEY
Value: `Value of AWS_ACCESS_KEY_ID`
Type: Parameter
Select **Add Environment variables** then enter the following values:
Name: DEV_SECRET_ACCESS_KEY_
Value: `Value of AWS_SECRET_ACCESS_KEY_ID`
Type: Parameter

7. Choose **Next**, choose **Skip deploy stage**, and then choose **Skip**.
8. Check the **Review** screen, and then choose **Create pipeline**. You will be taken to the Pipeline Status view for the pipeline you just created. This interface allows you to monitor the status of CodePipeline in near real time.


## Add ssm permission to the CodeBuild service role
1. Navigate to the **IAM** Console, and then choose **Roles** from the navigation pane.
Search for the CodeBuild service role, select it, and then choose **Add inline policy**.
2. In the Visual Editor section, click **Choose a service** and search for and select **Systems Manager**. In the **Actions** menu, drop down **Read** and tick on the **GetParameters** box.
3. In the **Resources** menu, select **All resources**
4. Select **Review policy**. Give it a name ssm_permissions and select **Create policy.
