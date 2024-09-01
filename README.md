# ollamma-cloud-run

Gemma 9B over Ollama on Google Cloud Run.

## Configure with cloud build

You will need to connect the GitHub repository to Google Cloud Build and setup a trigger to build and deploy the container image. You will also need to create a `Docker` repository in Artifiact Registry, like below:

![Cloud build example output](/images/artifact-repo-create-repo.jpg)

I have used `us-central1` as the region for the repository, build and Cloud Run deploy region. The `cloudbuild.yaml` file is already in the repository and it uses Docker caching to make the Docker build faster, anyhow the most time consuming part is the push to Google Artifact Registry as it is a `5.4 GB` image.

![Cloud build example output](/images/cloud-build-output.jpg)

## Deploy to Cloud Run

After the container is built and pushed to artifact registry, you can deploy it to Cloud Run.

You select the built Docker image from Artifact Registry. For the model to function properly on Google Cloud Run make sure to configure:

* 32 GB of memory
* 8 vCPUs
* 600 seconds of timeout
* 4 concurrent requests per instance
* Max instanced >= 4
* Environment variable of `OLLAMA_NUM_PARALLEL=4`
* CPU is always allocated (selected)
* Startup CPU boost (enabled)

I could get the model running without a GPU with above configuration. GPUs are supported on Cloud Run but it is being a [request form](https://g.co/cloudrun/GPU) for now. This offical [guide](https://cloud.google.com/run/docs/tutorials/gpu-gemma2-with-ollama) gives an example with GPU.

## Examples

After the container is deployed successfully on Cloud Run, you can run the below examples:

### Without streaming

The one below will take a long time to run and generate a bit of text.

```bash
curl https://<cloud-run-identifier>.a.run.app/api/generate -d '{
  "model": "gemma2:9b",
  "prompt": "Why is the sky blue?"
}'
```

### With streaming

It will respond faster but the output will be streamed.

```bash
curl https://<cloud-run-identifier>.a.run.app/api/generate -d '{
  "model": "llama3",
  "prompt": "Why is the sky blue?",
  "stream": false
}'
```

### Optimal example without streaming

It will respond fast and the output will not be streamed.

```bash
curl -i https://<cloud-run-identifier>.a.run.app/api/generate -d '{
  "model": "gemma2:9b",
  "prompt": "Why is the sky blue? Give the shortest answer possible",
  "stream": false
}'
```

In the test done, it took `7.73s` to generate the output (not bad withotu any GPUs):

![Response from Gemma with Ollama in 7.73 s](/images/ollama-gemma-output.jpg)
