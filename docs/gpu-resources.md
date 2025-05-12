# GPU Resources requirements

GPU Types and their characteristics:

- nvidia-l4: 24GB VRAM, Ada Lovelace architecture, supports FlashAttention (current config)
- nvidia-tesla-t4: 16GB VRAM, Turing architecture, no FlashAttention
- nvidia-tesla-a100: 40GB VRAM, Ampere architecture, supports FlashAttention
- nvidia-a100-80gb: 80GB VRAM, Ampere architecture, supports FlashAttention
- nvidia-h100-80gb: 80GB VRAM, Hopper architecture, supports FlashAttention
- nvidia-h100-mega-80gb: 80GB VRAM, Hopper architecture, supports FlashAttention
- nvidia-h200-141gb: 141GB VRAM, Hopper architecture, supports FlashAttention
- nvidia-b200: 180GB VRAM, Blackwell architecture, supports FlashAttention

Note: CPU and RAM requirements are minimal as the model runs on GPU:

- CPU: 1-2 cores for request handling and system operations
- RAM: 4-8GB for system operations and request buffering
