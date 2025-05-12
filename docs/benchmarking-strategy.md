# Benchmarking Strategy for Model Serving Systems

To ensure a fair and meaningful comparison between different LLM/model serving solutions, follow these best practices:

## 1. Use the Same Model
- Example: `mistralai/Mistral-7B-Instruct-v0.2` (or any other LLM you have access to)
- Ensure your Hugging Face token (or other credentials) has access to the model for all systems.

## 2. Deploy Each Serving System with the Same Model
- **TGI**: Set `model: mistralai/Mistral-7B-Instruct-v0.2` in your values file or config.
- **vLLM**: Use the same model path or Hugging Face repo.
- **Triton**: Convert/download the model to ONNX or TorchScript if needed, and mount it in the right directory.
- **Ray Serve, OpenLLM, TorchServe, FastChat, etc.**: Point to the same model repo or local path.

## 3. Use the Same Hardware
- Same GPU type, node size, and storage class for all deployments.

## 4. Use the Same Inference Parameters
- Set identical values for `max_new_tokens`, `temperature`, `batch size`, etc.

## 5. Use the Same Prompt Set and Load Pattern
- Use a script to send identical requests to each endpoint.
- Automate the load generation for consistency.

## 6. Monitor with the Same Stack
- Use Prometheus + Grafana for all systems.
- Scrape the same metrics at the same interval.

## 7. Compare the Same Metrics
- Latency (p50, p90, p99)
- Throughput (requests/sec, tokens/sec)
- Resource usage (CPU, GPU, memory)
- Feature set (streaming, batching, multi-model, etc.)

---

## Example: Deploying Mistral-7B on TGI and vLLM

- **TGI**: Set `model: mistralai/Mistral-7B-Instruct-v0.2` in your deployment values.
- **vLLM**: Example Docker run:
  ```bash
  docker run --gpus all -p 8000:8000 vllm/vllm-openai:latest \
    --model mistralai/Mistral-7B-Instruct-v0.2 --token <your-hf-token>
  ```
- **Triton**: Convert/download the model if needed, and mount it in the right directory.
- **Other systems**: Use the same model repo or local path.

---

## Tips
- Ensure your Hugging Face token or credentials are valid and have access to the model for all systems.
- Some systems may require model conversion (e.g., ONNX for Triton).
- Use the same batch size and inference parameters for all systems.
- Compare not just performance, but also features and ease of use.

---

## Recommended Systems to Benchmark
- **TGI (Text Generation Inference)**
- **vLLM**
- **Triton Inference Server**
- **Ray Serve**
- **OpenLLM (BentoML)**
- **TorchServe**
- **FastChat**

---

If you need example deployment YAMLs, Docker commands, or benchmarking scripts for any of these systems, reach out or check the project templates! 