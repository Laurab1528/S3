# Guide to Deploy a Static Website on Amazon S3 with GitHub Actions

This guide provides the necessary steps to create an Amazon S3 bucket, configure a GitHub repository, and use GitHub Actions to deploy a static website to the S3 bucket.

## 1. Create an S3 Bucket for Static Website Hosting

1. **Log in to the AWS Console**:
   - Access the [AWS Management Console](https://aws.amazon.com/console/) and log in with your credentials.

2. **Navigate to Amazon S3**:
   - Search for and select **S3** in the AWS console.

3. **Create a New Bucket**:
   - Click on **Create bucket**.
   - **Bucket name**: `epam-course1528`
   - **Region**: `us-east-2`
   - Click on **Create bucket**.
     ![image](https://github.com/user-attachments/assets/d3cca523-53f7-494c-899a-d77a0202b942)

     ![image](https://github.com/user-attachments/assets/ec3d61e0-1984-4b83-b240-90d0daaa4139)


4. **Configure the Bucket for Static Website Hosting**:
   - Select your bucket from the list.
   - Go to the **Properties** tab.
   - In the **Static website hosting** section, click on **Edit**.
   - Enable the **Enable** option.
   - **Index document**: Enter `index.html`.
   - Click on **Save changes**.

5. **Configure Bucket Permissions**:
   - Go to the **Permissions** tab.
   - In **Bucket Policy**, add the following JSON policy to allow public access:
     ```json
     {
       "Version": "2012-10-17",
       "Statement": [
         {
           "Effect": "Allow",
           "Action": [
             "s3:PutObject",
             "s3:DeleteObject",
             "s3:GetObject"
           ],
           "Resource": "arn:aws:s3:::epam-course1528/*"
         }
       ]
     }
     ```

## 2. Create a GitHub Repository


3. **Add Files to the Repository**:
   - Clone the repository locally using the command:
     ```bash
     git clone https://github.com/Laurab1528/S3.git
     ```
   - Add your `index.html` file to the repository.
   - Commit and push the changes:
     ```bash
     
     git add index.html
     git commit -m "Add initial static website files"
     git push origin main
     ```

## 3. Configure GitHub Actions to Deploy to S3

1. **Set Up Secrets in GitHub**:
   - In your GitHub repository, go to **Settings** > **Secrets and variables** > **Actions**.
   - Click on **New repository secret** to add the following secrets:
     - **Secret Name:** `AWS_ACCESS_KEY_ID`
       - **Secret Value:** Your AWS `Access Key ID`.
     - **Secret Name:** `AWS_SECRET_ACCESS_KEY`
       - **Secret Value:** Your AWS `Secret Access Key`.

2. **Create the Workflow File**:
   - In your repository, create the `.github/workflows` directory if it does not exist.
   - Inside this directory, create a file named `deploy.yml` with the following content:
     ```yaml
     name: Deploy to S3

     on:
       push:
         branches:
           - main

     jobs:
       deploy:
         runs-on: ubuntu-latest

         steps:
           - name: Checkout code
             uses: actions/checkout@v3

           - name: Setup AWS CLI
             uses: aws-actions/configure-aws-credentials@v2
             with:
               aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
               aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
               aws-region: us-east-2

           - name: Sync files to S3
             run: |
               aws s3 sync . s3://epam-course1528 --exclude ".git/*" --exclude ".github/*" --delete

     final url:http://epam-course1528.s3-website.us-east-2.amazonaws.com
     ```

