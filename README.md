# Model Serving Benchmark Deployment on GKE Autopilot

This repository contains the configuration to deploy and benchmark multiple inference/model serving solutions (TensorRT-LLM and Text Generation Inference) on GKE Autopilot with GPU support, persistent storage, Prometheus monitoring, and Grafana dashboards.

## Prerequisites

- GKE Autopilot cluster with GPU support
- `kubectl` and `helm` installed and configured
- A Hugging Face token (if using TGI)
- GCP service account with appropriate permissions (if using TensorRT)

## Configuration

The deployment can be configured using the following files:
- `values.example.yaml`: Template for configuration values

Key configuration options:
- TGI settings (model, batch size, resources)
- TensorRT settings (model, organization, deployment)
- Prometheus scraping configuration
- Grafana settings

## Deployment

1. **Set the right context:**

   ```bash
   kubectl config use-context CLUSTER_NAME
   ```

2. **Update Helm dependencies:**

   ```bash
   helm dependency update
   ```

3. **Create namespace and deploy:**

   ```bash
   helm install model-serving . -f values.prod.yaml -n tensorrt-llm-model-server --create-namespace
   ```

4. **Create required secrets:**

   For TGI:
   ```bash
   kubectl create secret generic huggingface-token --from-literal=token="<your-token>" -n tensorrt-llm-model-server
   ```

   For Grafana:
   ```bash
   kubectl create secret generic grafana-admin --from-literal=admin-user=admin --from-literal=admin-password=admin -n tensorrt-llm-model-server
   ```

5. **Upgrade deployment (if needed):**

   ```bash
   helm upgrade --install model-serving . -f values.prod.yaml -n tensorrt-llm-model-server
   ```

## Monitoring

### Accessing Grafana

1. **Port forward the Grafana service:**
   ```bash
   kubectl port-forward svc/model-serving-grafana 3000:3000 -n tensorrt-llm-model-server
   ```

2. **Access the dashboard:** http://localhost:3000

### Setting up Dashboards

1. **Configure Prometheus data source:**
   - Go to Connections → Data Sources → Add Data Source → Prometheus
   - Name: `TGI` or `TensorRT`
   - URL: `http://prometheus.model-serving.svc.cluster.local:9090`
   - Click "Save & Test"

2. **Import dashboards:**
   - Go to Dashboards → New → Import
   - Import either:
     - `dashboards/tgi.json` for TGI monitoring
     - `dashboards/tensorrt.json` for TensorRT monitoring

## Testing the Deployment

### TGI Testing

```bash
kubectl port-forward svc/tgi -n tensorrt-llm-model-server 8000:8000
```

```python
import requests, time, random
URL = "http://localhost:8000/generate"
PROMPTS = ["Hello!", "Tell me a joke.", "Explain quantum physics."]
for _ in range(20):
    prompt = random.choice(PROMPTS)
    requests.post(URL, json={"inputs": prompt, "parameters": {"max_new_tokens": 32}})
    time.sleep(2)
```

### TensorRT Testing

```bash
kubectl port-forward svc/tensorrt-server -n tensorrt-llm-model-server 8000:8000
```

```python
import requests, time, random
URL = "http://localhost:8000/v2/models/ensemble/generate"
response = requests.post(URL, json={"text_input": "Hello, I would like to know if my son is covered by my car insurance policy to drive my vehicle?", "max_tokens": 2048, "bad_words": "", "stop_words": ""})
print(response.json())
```

For a more detailed use case, check this [repository](https://github.com/elamribadrayour/tensorrt-llm-model-user)


## Architecture

- **Model Serving:**
  - TGI or TensorRT-LLM for model inference
  - Configurable resource limits and requests
  - GPU support
  
- **Monitoring:**
  - Prometheus for metrics collection (1s scrape interval)
  - Grafana for visualization
  - Custom dashboards for both TGI and TensorRT

- **Storage:**
  - Persistent storage for model cache
  - Grafana persistent storage (10Gi)

## Version Information

- Chart Version: 0.1.0
- Grafana Version: 11.6.5
- TGI Image: ghcr.io/huggingface/text-generation-inference:3.2.3
- TensorRT Image: nvcr.io/nvidia/tritonserver:25.03-trtllm-python-py3
