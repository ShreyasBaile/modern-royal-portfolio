# Deployment Guide

## 1. Save the project locally

Create this folder structure on your computer:

```text
modern-royal-portfolio/
├── .github/
│   └── workflows/
│       └── deploy.yml
├── aws/
│   ├── iam-permissions-policy.json
│   ├── iam-trust-policy.json
│   └── s3-bucket-policy.json
├── 404.html
├── .gitignore
├── DEPLOYMENT-GUIDE.md
├── README.md
├── index.html
├── script.js
└── styles.css
```

## 2. Edit the placeholders

Replace the placeholder values in these files:

- `index.html`
- `aws/iam-trust-policy.json`
- `aws/iam-permissions-policy.json`
- `aws/s3-bucket-policy.json`

### Placeholder meanings

- `<AWS_ACCOUNT_ID>` = your 12-digit AWS account number
- `<GITHUB_ORG>` = your GitHub username or organization
- `<GITHUB_REPOSITORY>` = your GitHub repository name
- `<S3_BUCKET_NAME>` = your S3 bucket name

## 3. Create the GitHub repository

Create a new repository on GitHub.

Example repository name:

`modern-royal-portfolio`

## 4. Upload the files from local to GitHub

Open terminal in your project folder and run:

```bash
git init
git branch -M main
git add .
git commit -m "Initial portfolio setup"
git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPOSITORY.git
git push -u origin main
```

## 5. Create the S3 bucket

In AWS S3:

1. Create a bucket.
2. Choose the AWS Region you want.
3. Keep the bucket name exactly as the name you will use in your secrets and policy files.

## 6. Enable static website hosting

In your S3 bucket:

1. Open the bucket.
2. Go to **Properties**.
3. Under **Static website hosting**, click **Edit**.
4. Enable static website hosting.
5. Set:
   - Index document = `index.html`
   - Error document = `404.html`

## 7. Make the website public

### 7a. Turn off block public access for this bucket

In the bucket:

1. Open **Permissions**.
2. Under **Block public access (bucket settings)** click **Edit**.
3. Clear **Block all public access**.
4. Save changes.

### 7b. Add the bucket policy

In the bucket:

1. Stay in **Permissions**.
2. Open **Bucket policy**.
3. Paste the code from `aws/s3-bucket-policy.json` after replacing `<S3_BUCKET_NAME>`.
4. Save it.

## 8. Create the GitHub OIDC identity provider in IAM

In AWS IAM:

1. Open **Identity providers**.
2. Click **Add provider**.
3. Provider type = **OpenID Connect**.
4. Provider URL = `https://token.actions.githubusercontent.com`
5. Audience = `sts.amazonaws.com`
6. Save.

## 9. Create the IAM permissions policy

In IAM:

1. Open **Policies**.
2. Click **Create policy**.
3. Open the **JSON** tab.
4. Paste the contents of `aws/iam-permissions-policy.json` after replacing `<S3_BUCKET_NAME>`.
5. Name it:
   - `GitHubActionsS3PortfolioDeployPolicy`
6. Save.

## 10. Create the IAM role

In IAM:

1. Open **Roles**.
2. Click **Create role**.
3. Trusted entity type = **Web identity**.
4. Select the GitHub OIDC provider.
5. Use the trust policy from `aws/iam-trust-policy.json` with your placeholders replaced.
6. Go to the permissions page.
7. Select the policy you created:
   - `GitHubActionsS3PortfolioDeployPolicy`
8. Continue.
9. Name the role:
   - `github-actions-s3-portfolio-deploy`
10. Create the role.

## 11. Copy the role ARN

Open the IAM role and copy its ARN.

Example format:

`arn:aws:iam::123456789012:role/github-actions-s3-portfolio-deploy`

## 12. Add GitHub repository secrets

In GitHub:

1. Open your repository.
2. Go to **Settings** > **Secrets and variables** > **Actions**.
3. Add these repository secrets:

- `AWS_ROLE_ARN` = the full IAM role ARN
- `AWS_REGION` = your AWS region, for example `ap-south-1`
- `S3_BUCKET_NAME` = your bucket name

## 13. Check the workflow file path

Make sure this file exists in GitHub exactly here:

```text
.github/workflows/deploy.yml
```

If the path is wrong, GitHub Actions will not show the workflow.

## 14. Trigger the deployment

If all files are already in GitHub, push one more small change:

```bash
git add .
git commit -m "Trigger deployment"
git push origin main
```

Then check the **Actions** tab in GitHub.

## 15. Open the website

In S3:

1. Open the bucket.
2. Go to **Properties**.
3. Under **Static website hosting**, copy the website endpoint.
4. Open that URL in your browser.

## 16. If the site does not load

Check these things:

- the bucket name matches in IAM policy, bucket policy, and GitHub secret
- `AWS_ROLE_ARN` is the full role ARN
- the workflow file is in `.github/workflows/deploy.yml`
- the workflow is pushed to `main`
- the trust policy has the correct GitHub username and repository
- static website hosting is enabled
- block public access is turned off for the bucket
- bucket policy is added correctly

## 17. Clean up later

If you no longer want the project live, delete:

- the S3 bucket contents
- the S3 bucket
- the IAM role
- the IAM policy
- the IAM identity provider (only if you do not need it for any other repo)
