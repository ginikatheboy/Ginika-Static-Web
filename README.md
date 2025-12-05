# 🌐 Ginika — Static Website Deployment (AWS S3 + GitHub Actions)

A simple static website deployed automatically to **AWS S3** using **CI/CD with GitHub Actions**.

🔗 **Live Demo:** http://ginika-website.s3-website-us-east-1.amazonaws.com/  
📦 **Repository:** https://github.com/ginikatheboy/Ginika-Static-Web  

---

## 📌 Project Summary

This repository contains a **single-page static website** and a **CI/CD pipeline** that deploys updates to an S3 bucket every time you push to `main`.

This project demonstrates:

- 🚀 Hosting a static site on **Amazon S3**
- 🔁 Automatic deployments using **GitHub Actions**
- 🔐 Secure CI/CD using IAM and GitHub Secrets
- 🧹 Clean, minimal IAM policy & proper infra hygiene

---

## ✨ Features

- Static website (`index.html`)
- Automatic S3 deployment using GitHub Actions
- Ready-to-use workflow (`deploy.yml`)
- Clear instructions for IAM setup and troubleshooting

---

## 📁 Repository Structure

.
├── .github/
│ └── workflows/
│ └── deploy.yml # CI/CD pipeline
├── index.html # Static website
└── README.md # Documentation


---

## ⚡ Quick Start Requirements

Before using this repo, ensure you have:

- An **S3 bucket** (website hosting optional)
- (Recommended) A **CloudFront distribution**
- An **IAM user** for GitHub Actions with minimal S3 permissions
- GitHub repo secrets:

AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
S3_BUCKET


---

## 🔒 Minimal IAM Policy

Replace `ginika-website` with your bucket name:

```json
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
```

---
##🔧 GitHub Actions — How It Works

The deployment workflow triggers on pushes to main:

Checkout repository

Configure AWS credentials

Run aws s3 sync to upload files

(Optional) Invalidate CloudFront cache

This workflow ignores files like .git and .github to keep deployments clean.

##🛠️ Setup Guide (Step-by-Step)
- Create an S3 Bucket

Name must be globally unique

For public website hosting: enable static hosting & public object reads


Then visit your S3 website endpoint to confirm.

- Add Repo Secrets

GitHub → Repository → Settings → Secrets → Actions:

AWS_ACCESS_KEY_ID

AWS_SECRET_ACCESS_KEY

S3_BUCKET

- Push to Deploy

Commit changes and push to main:
git add .
git commit -m "Update site"
git push

Check:
GitHub → Actions → Deploy static site to S3

📝 Example deploy.yml

Full workflow included in:
.github/workflows/deploy.yml


It uses:

aws-actions/configure-aws-credentials

aws-actions/aws-cli@v2

Clean s3 sync with file exclusions

✔️ Validation / Testing Checklist

Site loads via S3 website endpoint or CloudFront

GitHub Actions workflow succeeds

Objects appear in bucket

If updates don’t show: invalidate CloudFront or wait for TTL

🩺 Troubleshooting Guide

❌ SignatureDoesNotMatch

Region mismatch

Wrong secret keys

❌ Files like .git uploaded

Add more --exclude rules

❌ AccessDenied

IAM policy missing s3:PutObject or s3:DeleteObject

🔐 Security Notes

Follow least privilege (policy provided above)

Store AWS keys ONLY in GitHub Secrets

Never commit credentials to the repo

👤 Author

Okeke Jehohanan (Ginika)
Cloud / DevOps Engineer — learning practical cloud security & automation

🔗 Repo: https://github.com/ginikatheboy/Ginika-Static-Web
