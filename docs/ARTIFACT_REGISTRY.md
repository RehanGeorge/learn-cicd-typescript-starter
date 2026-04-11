# Google Artifact Registry

We'll be using Google Artifact Registry to store our Docker images. It's similar to Docker Hub, but it's private and it's hosted on GCP.

Whenever we create a new version of Notely, we'll build it into a new Docker image version and push that to Artifact Registry.

## Assignment

### 1. Enable the Cloud Build API
Search for and enable the Cloud Build API.

### 2. Set Up Artifact Registry
Within Artifact Registry in the GCP console, enable the Artifact Registry API.

### 3. Create Repository
Click Create Repository with the following settings:
- **Name:** `notely-ar-repo`
- **Format:** Docker
- **Mode:** Standard
- **Location type:** Region
- **Region for deployment:** us-central1
- **Encryption:** Leave "Google-managed encryption key" selected

> **Note:** The image hosting region from earlier, and service deployment region we are targeting now, may not necessarily be the same region. Cloud providers provide flexibility with availability zones, so that engineers can pick and choose the most optimal regions for your system.

### 4. Build and Push the Docker Image

```bash
gcloud builds submit --tag REGION-docker.pkg.dev/PROJECT_ID/REPOSITORY/IMAGE:TAG .
```

You can copy/paste your actual value for the tag from your Artifact Registry repo page in the GCP console.

### 5. Test
Run and submit the CLI tests from the root of your repo.

### 6. Troubleshooting
If the gcloud command responds with an error about permissions, simply check that the image was uploaded to the new registry. We will take care of permissions in a future step.
