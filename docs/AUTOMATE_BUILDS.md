# Automate Builds

Now that we've built the Docker image locally, let's build it automatically on every push to the main branch.

## Assignment

Use the setup-gcloud action to authenticate with GCP.

I recommend using the simple service account key JSON setup.

### Creating a Service Account

1. Go to the IAM & Admin Service Accounts section of the GCP console.
2. Create a service account and name it "Cloud Run Deployer" with these permissions:
   - Cloud Build Editor
   - Cloud Build Service Account
   - Cloud Run Admin
   - Service Account User
   - Viewer
3. Create a JSON key for that service account and download it to your computer.

### Add the Key As a Secret in GitHub Actions

1. Go to your GitHub Repo > Repository Settings > Secrets and variables > Actions > New repository secret (not "environment secret" - use a repository secret)
2. Name: `GCP_CREDENTIALS`
3. Secret: Paste the entire JSON key from the file you downloaded from GCP
4. Save the secret

### Update Your GitHub Action Workflow

1. After the step that builds the app with `npm run build`, add the setup-gcloud steps to setup the gcloud CLI and authenticate with GCP.
2. Finally, add a step to build the Docker image and push it to Google Artifact Registry.

```bash
gcloud builds submit --tag REGION-docker.pkg.dev/PROJECT_ID/REPOSITORY/IMAGE:TAG .
```

You can copy/paste your actual value for the tag from your Artifact Registry repo page in the GCP console.

### Verify Your Setup

1. Commit and push your changes to GitHub. You should see the GitHub Action run and successfully build and push the Docker image to Google Artifact Registry.
2. Paste the URL of your GitHub repo into the box and run the GitHub checks.

## Google Cloud Run

Cloud Run is a serverless container hosting service. It's a great fit for Notely because it has a generous free tier, scales automatically, and automagically configures pesky infrastructure like:

- Load balancing
- DNS
- HTTPS

In a nutshell, we give Cloud Run a Docker image, and it runs it for us.

### Cloud Run Services

Cloud run has 2 types of applications:

- **Services**: A Cloud Run application that listens and responds to web requests
- **Jobs**: A task that runs to completion

Notely makes sense as a service because it's a web application that needs to respond to web requests. It needs to serve the frontend, and it needs to respond to API requests.

### Assignment

For now, let's create a test service. Once we're comfortable with the process, we'll deploy Notely.

1. Navigate to the Cloud Run section of the GCP console.
2. Click "Create Service"
3. Configure the service:
   - **Container image URL**: `bootdotdev/getting-started`
   - **Service name**: `test`
   - **Region**: `us-central1`
   - **Authentication**: Choose "Allow public access". This will allow anyone to access the webpage the service serves without having to log in to GCP
   - **Service scaling**: Set maximum number of instances to 4
   - **Ingress control**: "All" checked (allow direct access to the service)
4. Go to "Container(s), Volumes, Networking, Security". Under "Container(s)" change the "Container port" from 8080 to 80.
5. Create the service
6. Wait for the service to deploy
7. Click the service's URL to see the webpage it serves
8. Run and submit the CLI tests using the URL of your service:
   ```bash
   bootdev config base_url https://test-vo4kpyh36a-uc.a.run.app/
   ```

### Clean Up

Delete the "Getting Started" test service you just created.
