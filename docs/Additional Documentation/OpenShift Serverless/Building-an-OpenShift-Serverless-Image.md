# Building an OpenShift Serverless Image

## Introduction

To use OpenShift Serverless, you must create a container image of your serverless function or application. This image will be deployed to OpenShift and scaled dynamically based on demand. While there are multiple ways to create and push an image, this guide demonstrates how to do it using Knative Functions (`kn func`) and push the image to Azure Container Registry (ACR) using Podman as the container engine.

Follow this step-by-step guide to get your function packaged into an image and ready for deployment on OpenShift Serverless.

## Prerequisites

Ensure you have the following installed and configured on your local machine:

- Knative Functions CLI (`kn func`)
- Podman or Docker
- OpenShift CLI (`oc`)
- Access to Azure Container Registry (ACR) and an Azure service principal with `acrpush` permissions.

---

## Step-by-Step Guide to Building an OpenShift Serverless Image

### 1. Initialize a Podman Machine (if not already initialized)
Before building and pushing your function image, make sure Podman is running with enough resources:

```bash
# Initialize a Podman machine with specific resources (only needed if not already initialized)
podman machine init --memory=8192 --cpus=2 --disk-size=20 

# Start the Podman machine
podman machine start 

# Set the Docker host environment variable for Podman (replace with actual path from output)
export DOCKER_HOST='unix:///var/folders/j_/tgnt8z2143v7h75y9scfgc_w0000gq/T/podman/podman-machine-default-api.sock' 
```

### 2. Create a New Knative Function Project

Knative Functions (`kn func`) supports multiple runtimes such as Node.js, Python, Go, and others. In this example, we will use the **Node.js** runtime to create a simple function:

```bash
kn func create hello-world -l node
cd hello-world
```

### 3. Implement a Simple "Hello, World!" Application (Optional)
You can modify the function to handle HTTP requests by implementing a simple "Hello, World!" application:

- Create an `index.js` file with the following content:

```javascript
const express = require('express');
const app = express();
const port = process.env.PORT || 8080;

app.get('/healthz', (req, res) => res.status(200).send('OK'));
app.get('/ready', (req, res) => res.status(200).send('Ready'));

app.get('/', (req, res) => {
  res.send('Hello, World!');
});

app.listen(port, () => {
  console.log(`Server is running on port ${port}`);
});
```

- Create a `package.json` file with the necessary dependencies and scripts:

```json
{
  "name": "hello-world-function",
  "version": "1.0.0",
  "description": "A simple Hello World serverless function for OpenShift.",
  "main": "index.js",
  "scripts": {
    "start": "node index.js"
  },
  "dependencies": {
    "express": "^4.17.1"
  },
  "author": "Your Name",
  "license": "MIT"
}
```

- Install the required Node.js packages:

```bash
npm install
```

### 4. Build the Function Image
Now that the function is implemented, use Podman to build the image and push it to your Azure Container Registry (ACR).

- Log in to ACR:
=== "Login to ACR"
    ```bash
    podman login <acr>.azurecr.io --username <app_id> --password <password>
    ```
=== "Example"
    ```bash
    podman login demo.azurecr.io --username 390fug94-93j5-3er6-9263-dsf892hakjfe --password T8j8Q~qWrIavwhpsHRaRzs2aJdJJavpuLeJltdBJ
    ```

- Build the function image:
=== "Build a function image"
    ```bash
    kn func build --image <acr>.azurecr.io/<repository>:<tag>
    ```
=== "Example"
    ```bash
    kn func build --image demo.azurecr.io/hello-world:latest
    ```

- Push the image to ACR:
=== "Push image to ACR"
    ```bash
    podman push <acr>.azurecr.io/<repository>:<tag>
    ```
=== "Example"
    ```bash
    podman push demo.azurecr.io/hello-world:latest
    ```

Replace the placeholders `<acr>`, `<app_id>`, `<tag>`, and `<password>` with your actual Azure Container Registry (ACR) name, application ID, and password.

### 5. Verify the Image in ACR
Once the image is pushed, you can verify that it is available in your ACR:

- Log in to the Azure portal and navigate to your **Container Registry**.
- Under **Repositories**, you should see the newly pushed image (`hello-world:latest`).



## Conclusion

You have now successfully built and pushed a serverless function image to Azure Container Registry using Knative Functions and Podman. This image is ready to be deployed in OpenShift Serverless, where it can scale up and down based on demand.

While this guide used Knative Functions and Podman, you can also use other container engines (like Docker) or registries (such as Docker Hub) depending on your environment and preferences. 

The next step is to deploy this image as a Knative service in OpenShift Serverless, where it will be managed and scaled automatically according to the traffic it receives.

