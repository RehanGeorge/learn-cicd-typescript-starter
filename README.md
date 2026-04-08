![Workflow Report](https://github.com/RehanGeorge/learn-cicd-typescript-starter/actions/workflows/ci.yml/badge.svg)

# learn-cicd-typescript-starter (Notely)

This repo contains the typescript starter code for the "Notely" application for the "Learn CICD" course on [Boot.dev](https://boot.dev).

## Local Development

Make sure you're on Node version 22+.

Create a `.env` file in the root of the project with the following contents:

```bash
PORT="8080"
```

Run the server:

```bash
npm install
npm run dev
```

_This starts the server in non-database mode._ It will serve a simple webpage at `http://localhost:8080`.

You do _not_ need to set up a database or any interactivity on the webpage yet. Instructions for that will come later in the course!

## Docker

We'll be using Docker to deploy Notely to Google Cloud Run. If you're not familiar with Docker, you should take our Learn Docker course first.

Make sure you have Docker installed by running `docker --version` in your terminal. You should be on at least version 20.10.21. The project starter includes a prebuilt Dockerfile just for this project.

### Assignment

**Build the Docker image locally:**

```bash
docker build -t DOCKERHUB_NAMESPACE/notely:latest .
```

Replace `DOCKERHUB_NAMESPACE` with your Docker Hub username.

**Run the Docker image locally:**

```bash
docker run -e PORT=8080 -p 8080:8080 DOCKERHUB_NAMESPACE/notely:latest
```

Open the app in your browser at http://localhost:8080. You should see the Notely app running locally!

Then run and submit the CLI tests.

### Troubleshooting

If you get an architecture or exec format error, that's probably because your machine is different than the intended Docker Container.

#### macOS

Set the `--platform` flag to `linux/arm64` in the Dockerfile or when running the docker build command:

```bash
docker build -t DOCKERHUB_NAMESPACE/notely:latest . --platform=linux/arm64
```

### Tips

- Use `http`, not `https`!
- Creating a user on the Notely site is not expected to work yet, we haven't set up the database!

Rehan's version of Boot.dev's Notely app.
