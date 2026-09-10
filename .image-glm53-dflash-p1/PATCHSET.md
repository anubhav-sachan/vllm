# GLM-5.3-Flash DFlash2 p1

Base image:

- `vllm/vllm-openai@sha256:96b234afa2867031ad0a226b149a06483861e613e3a3083da53398d347b5ffbd`
- vLLM revision `2a02f6efe319c885e3ccbcecde402e0028f9ec1e`
- Native `linux/amd64` and `linux/arm64` base manifests

Old image behavior retained:

- #52816: DFlash2 model, selector, and speculator support (in base)
- #53046: structured-output validation across the reasoning boundary (in base)
- #53388 and #54163: preserve reusable target KV blocks for DFlash/DSpark
- #55423: GLM-5.3 target integration and hybrid KV grouping, replacing the
  older image's hand-written auxiliary-capture and drafter-group patches

Selective additions:

- #55682 auxiliary-state capture implementation on top of #55423 KV grouping
- #54276 reasoning parser compatibility, extended to both `thinking=false`
  and `enable_thinking=false`, including streaming extraction
- #55558 required-first nested tool-schema rendering and strict validation
- #55449 packed MLA widths for hybrid cache alignment
- #55450 Mamba-state retirement across null gaps
- #55800 window-bounded sliding-window allocation in admission and execution

Also inherited from the pinned base:

- #54373 DFlash2 draft RoPE correctness
- #54374 DFlash2 sliding-window scheduling correctness
- #55448 bounded renderer warmup for lower startup memory

The image overlays Python sources only. CUDA libraries and native extensions
remain platform-specific artifacts from each corresponding base manifest.
