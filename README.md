Ginika — Static Website

Static site deployed to AWS S3 with CI/CD (GitHub Actions)

Live demo: (http://ginika-website.s3-website-us-east-1.amazonaws.com/)
Repo: https://github.com/ginikatheboy/Ginika-Static-Web

⸻

Project summary

This repository hosts a simple static website and an automated pipeline that deploys the site to an AWS S3 bucket. The site is automatically synced on every push to main using GitHub Actions.

What this demonstrates
 • Hosting a static site on S3 with static website hosting
 • Secure automated deployment using GitHub Actions and IAM credentials
 • Basic infra hygiene: minimal IAM permissions, secrets management, and CI debug helpers

⸻

Features
 • Single-page static website (index.html)
 • GitHub Actions workflow to s3 sync on push
 • Example .github/workflows/deploy.yml included and ready to use
 • Clear documentation for secrets / IAM setup and troubleshooting

⸻

Repo contents

.
├── .github/
│   └── workflows/
│       └── deploy.yml        # GitHub Actions workflow to sync to S3
├── index.html                # Simple static site
├── README.md                 # (this file)

⸻

Quick start — (what you need)
 1. An S3 bucket configured for website hosting (or simply as an origin for CloudFront).
 2. A CloudFront distribution configured to use the S3 bucket as origin (recommended).
 3. An IAM user with programmatic access and an access key + secret key. This user needs only the S3 permissions listed below.
 4. GitHub repo secrets created:
 • AWS_ACCESS_KEY_ID
 • AWS_SECRET_ACCESS_KEY
 • S3_BUCKET (the bucket name, no s3://)

⸻

IAM policy (minimal)

Attach a policy like this to the IAM user that your GitHub Action uses. Replace your-bucket-name with your bucket name.

{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ListBucket",
      "Effect": "Allow",
      "Action": ["s3:ListBucket"],
      "Resource": ["arn:aws:s3:::ginika-website"]
    },
    {
      "Sid": "ObjectsCRUD",
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject",
        "s3:DeleteObject",
        "s3:PutObjectAcl"
      ],
      "Resource": ["arn:aws:s3:::ginika-website/*"]
    }
  ]
}

⸻

GitHub Actions — How the workflow works

The workflow (.github/workflows/deploy.yml) runs on pushes to main. High level steps:
 1. Checkout the repo
 2. Configure AWS credentials (from GitHub Secrets)
 3. Run aws s3 sync to upload site files to the configured S3 bucket
 4. (Optional) run a CloudFront invalidation

You can use the example workflow included in the repo. It is built to be robust and excludes internal repo files like .git and .github.

⸻

How to configure and use (step-by-step)

1. Create S3 bucket
 • Create an S3 bucket (name must be globally unique).
 • If using CloudFront, the bucket can be private and serve via CloudFront OAI/OAC. For a simple demo you can enable public reads on objects (not recommended long term).

2. Upload the site manually (first time)
 • You can test manually to confirm everything works:

aws s3 sync . s3://your-bucket --exclude ".git/*" --exclude ".github/*"

 • Confirm index.html is reachable (via S3 website endpoint or CloudFront URL after distribution is ready).

3. Create CloudFront distribution
 • Origin: your S3 bucket (or the S3 website endpoint)
 • Viewer Protocol Policy: Redirect HTTP to HTTPS / or HTTPS only
4. Add GitHub repository secrets
 • Go to your GitHub repo > Settings > Secrets and set:
 • AWS_ACCESS_KEY_ID
 • AWS_SECRET_ACCESS_KEY
 • S3_BUCKET (bucket name)

5. Push to main and watch the workflow
 • Commit changes (e.g., update index.html) and push to main.
 • Navigate to Actions → Deploy static site to S3 and watch the run.
 • If successful, the site files sync to S3 and (optionally) CloudFront is invalidated.

⸻

Example deploy.yml (what this repo uses)

The repository contains an example workflow. It uses aws-actions/aws-cli@v2 for reliable aws execution and runs s3 sync excluding .git and .github.

⸻

Validation / Testing checklist
 • If using direct S3 website: visit http://<bucket-website-endpoint>/.
 • Confirm GitHub Action ran and s3 sync completed with no errors.
 • If content does not update immediately, invalidate CloudFront or wait for distribution TTL.

⸻

Troubleshooting (common problems)
 • SignatureDoesNotMatch → check AWS_REGION and keys. Ensure Secrets are correct and the region matches the bucket.
 • Files uploaded include .git → adjust the --exclude arguments in the workflow.
 • Permission denied / AccessDenied → verify IAM policy includes s3:PutObject and s3:DeleteObject for the bucket objects.

⸻

Security notes
 • Use least-privilege IAM credentials for CI/CD. The policy above is minimal for S3 sync.
 • Prefer bucket policies + CloudFront OAI/OAC to allow CloudFront-only access and block public object ACLs.
 • Store secrets in GitHub Secrets — never hard-code credentials in repo.

⸻

Author

Okeke Jehohanan (Ginika)
Cloud / DevOps Engineer — learning practical cloud security and automation.
Repo: https://github.com/ginikatheboy/Ginika-Static-Web

⸻

License

This project is released under the MIT License — see LICENSE (add if you want to include one).

⸻

Appendix — Useful commands

# Sync local files to S3 (manual)
aws s3 sync . s3://your-bucket --delete --exclude ".git/*" --exclude ".github/*"

# Check caller identity (debug credentials)
aws sts get-caller-identity
