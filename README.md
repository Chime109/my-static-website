gf # Deploying a Static Website to Amazon S3: A Beginner's Guide

So you've built a simple HTML/CSS website and want to put it on the internet? You've come to the right place. Amazon S3 (Simple Storage Service) can host static websites — HTML, CSS, JavaScript, images — with high durability and low cost. In this hands‑on guide, you'll go from zero to a live website in about 30 minutes.

## What You'll Need

- **An AWS account** – [Sign up](https://aws.amazon.com/) if you don't have one (free tier works perfectly).
- **AWS CLI installed and configured** – [Install guide](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html). After installing, run `aws configure` and enter your access keys (from the AWS Management Console: IAM > Users > your user > Security credentials).
- **Your website files** – At minimum an `index.html`. You can also have CSS, JS, images, etc. Place them all in a folder on your computer.

## Step 1: Create an S3 Bucket

A bucket is like a folder in the cloud. Bucket names must be **globally unique**.

1. Open the [S3 console](https://s3.console.aws.amazon.com/s3/).
2. Click **Create bucket**.
3. Give it a unique name (e.g., `my-awesome-site-2025`).
4. Choose a region close to your audience (e.g., `us-east-1`).
5. Under **Block Public Access settings**, **uncheck** "Block all public access". Acknowledge that the bucket will become public.
 > **Why?** Because we want the world to see your site. We'll control access with a bucket policy later.
6. Leave other settings as default and click **Create bucket**.

## Step 2: Enable Static Website Hosting

1. Click on your bucket name in the list.
2. Go to the **Properties** tab.
3. Scroll to **Static website hosting** and click **Edit**.
4. Select **Enable**.
5. **Index document**: enter `index.html` (or whatever your main page is named).
6. **Error document**: enter `error.html` (optional, but nice to have).
7. Click **Save changes**.

After saving, you'll see an **Endpoint URL** at the top of the static hosting section. It looks like:
`http://my-awesome-site-2025.s3-website-us-east-1.amazonaws.com`
Copy this – you'll use it to view your site.

## Step 3: Set a Bucket Policy to Allow Public Read Access

Now your bucket is ready to host, but nobody can see your files yet. We'll add a bucket policy that grants everyone read access.

1. Go to the **Permissions** tab of your bucket.
2. Scroll to **Bucket policy** and click **Edit**.
3. Paste the following policy, **but replace `your-bucket-name` with your actual bucket name**:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::your-bucket-name/*"
        }
    ]
}
```

4. Click **Save changes**.

Your bucket is now public – anyone can view your website files.

## Step 4: Upload Your Website Files

You can upload files through the AWS Console, but the AWS CLI is faster and more repeatable.

Open a terminal in the folder that contains your website files (e.g., `index.html`, `style.css`, `script.js`). Run:

```bash
aws s3 sync . s3://your-bucket-name/ --delete
```

- `sync` uploads only new or changed files.
- `--delete` removes files from the bucket that are no longer in your local folder (optional but keeps things tidy).

Wait for the upload to finish. You'll see each file listed as it's uploaded.

## Step 5: Test Your Live Website

Open your browser and go to the **Endpoint URL** you copied in Step 2. You should see your website!

If you see an error, check:
- The bucket policy – did you replace the bucket name?
- The index document name – is it exactly `index.html`?
- Files are uploaded – go to the bucket's **Objects** tab and verify.

## Conclusion & Next Steps

You've just deployed a static website on AWS S3! Here's what you accomplished:

- Created an S3 bucket.
- Enabled static website hosting.
- Made the bucket public with a policy.
- Uploaded files using the AWS CLI.

## Troubleshooting Quick Tips

| Problem | Likely Fix |
|--||
| **403 Access Denied** | Bucket policy is missing or incorrect. Double‑check the policy JSON and bucket name. |
| **404 Not Found** | The file isn't in the bucket, or the index/error document names don't match. |
| **CLI upload fails** | Verify AWS CLI is configured (`aws sts get-caller-identity`). Check bucket name spelling. |
| **Website still loading old version** | Use `--delete` with `aws s3 sync` to remove outdated files. CloudFront may need an invalidation. |

