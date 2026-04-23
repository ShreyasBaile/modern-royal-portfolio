# Modern Royal Portfolio

A modern single-page portfolio site with a royal blue and black design.

## Files

- `index.html` - main website
- `styles.css` - styling
- `script.js` - mobile menu and scroll animation
- `404.html` - error page for S3 static hosting
- `.github/workflows/deploy.yml` - GitHub Actions deployment workflow
- `aws/iam-trust-policy.json` - IAM trust policy for GitHub OIDC
- `aws/iam-permissions-policy.json` - IAM permissions policy for S3 access
- `aws/s3-bucket-policy.json` - bucket policy for public website access
- `DEPLOYMENT-GUIDE.md` - step-by-step deploy guide

## Before deploy

Replace placeholder values in:

- `index.html`
- `aws/iam-trust-policy.json`
- `aws/iam-permissions-policy.json`
- `aws/s3-bucket-policy.json`

## GitHub secrets needed

- `AWS_ROLE_ARN`
- `AWS_REGION`
- `S3_BUCKET_NAME`
