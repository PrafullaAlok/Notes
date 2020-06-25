You have been given the AWSCodeCommitPowerUser permission by the root user :)
---
# Steps for SSH connection to AWS CodeCommit:
1. On your terminal, run `ssh keygen` command to generate the public and private RSA key pairs.
2. Follow the directions to save the file to the .ssh directory. You can skip the passphrase part by pressing enter.
3. Run the command `cat ~/.ssh/codecommit_rsa.pub` (to get your public key). Copy the public key (big one)
4. In the IAM console, in the navigation pane, choose **Users**, and from the list of users, choose your IAM user.
5. Choose the **Security Credentials** tab, and then and then choose **Upload SSH public key**
6. Paste your SSH public key (that you had got in Step 3) into the field, and then choose **Upload SSH public key**
Copy or save the information in SSH Key ID
7. On your local machine, use a text editor to create a `config` file in the `~/.ssh directory`, and then add the following lines to the file, where the value for User is the SSH key ID you copied earlier:
`Host git-codecommit.*.amazonaws.com
  	User SSH Key ID
  	IdentityFile ~/.ssh/codecommit_rsa` (your saved the private key .. step 2)
Save this file as `config`.
8. From the terminal, run the following command to change the permissions for the config file: `chmod 600 config`
9. Run this command to test your SSH configuration:
`ssh git-codecommit.us-east-2.amazonaws.com`
You should see a success message.
---

# Steps to connect to a CodeCommit repository:

1. Open the **CodeCommit** console
2. Choose the AWS Region where the repository was created. Repositories are specific to an AWS Region. You can create your repository if you haven’t created one yet.
3. Find the repository you want to connect to from the list and choose it. Choose **Clone URL**, and then choose the protocol you want to use when cloning or connecting to the repository. This copies the clone URL.
- Copy the HTTPS URL if you are using either Git credentials with your IAM user or the credential helper included with the AWS CLI.
- Copy the HTTPS (GRC) URL if you are using the git-remote-codecommit command on your local computer.
- Copy the SSH URL if you are using an SSH public/private key pair with your IAM user.
Copy the SSH URL.
4. In the terminal, run the `git clone` command with the SSH URL you copied to clone the repository.
`git clone ssh://git-codecommit.eu-west-2.amazonaws.com/v1/repos/idmbt-deploy`
