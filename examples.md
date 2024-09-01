## Examples

Below are some examples:

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

### Optimal example

It will respond fast and the output will not be streamed.

```bash
curl -i https://<cloud-run-identifier>.a.run.app/api/generate -d '{
  "model": "gemma2:9b",
  "prompt": "Why is the sky blue? Give the shortest answer possible",
  "stream": false
}'
```

In the test done, it took `7.73s` to generate the output:

![Response from Gemma with Ollama in 7.73 s](/images/ollama-gemma-output.jpg)
