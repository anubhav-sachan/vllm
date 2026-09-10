# syntax=docker/dockerfile:1.7

ARG BASE_IMAGE=vllm/vllm-openai@sha256:96b234afa2867031ad0a226b149a06483861e613e3a3083da53398d347b5ffbd
FROM ${BASE_IMAGE}

ARG PATCHSET_COMMIT

LABEL org.opencontainers.image.title="vLLM GLM-5.3-Flash DFlash2 p1"
LABEL org.opencontainers.image.description="Pinned multi-arch vLLM nightly with selective GLM-5.3-Flash fixes"
LABEL org.opencontainers.image.base.digest="sha256:96b234afa2867031ad0a226b149a06483861e613e3a3083da53398d347b5ffbd"
LABEL org.opencontainers.image.base.revision="2a02f6efe319c885e3ccbcecde402e0028f9ec1e"
LABEL org.opencontainers.image.revision="${PATCHSET_COMMIT}"
LABEL ai.cohere.glm53.patchset="52816,53046,53388,54163,54276+p1,54373,54374,55423+55682,55448,55449,55450,55558,55800"

# Overlay Python sources only. Platform-specific CUDA/native artifacts remain
# those from the corresponding amd64 or arm64 image in the pinned base index.
COPY --link vllm/ /usr/local/lib/python3.12/dist-packages/vllm/

RUN python3 - <<'PY'
import ast
import importlib.util
from pathlib import Path

spec = importlib.util.find_spec("vllm")
assert spec is not None and spec.submodule_search_locations
root = Path(next(iter(spec.submodule_search_locations)))
assert root == Path("/usr/local/lib/python3.12/dist-packages/vllm"), root

checks = {
    "model_executor/models/registry.py": '"DFlash2DraftModel"',
    "models/glm5next/nvidia/model.py": "SupportsEagle3",
    "v1/core/kv_cache_utils.py": "sliding_buckets",
    "config/speculative.py": "use_eagle_block_drop",
    "parser/glm47_moe.py": "ignored_controls",
    "tool_parsers/glm47_moe_tool_parser.py": "reorder_tool_schema_required_first",
    "v1/core/single_type_kv_cache_manager.py": "vLLM #55800",
}
for relative, marker in checks.items():
    path = root / relative
    source = path.read_text(encoding="utf-8")
    assert marker in source, (relative, marker)
    ast.parse(source, filename=str(path))

print("GLM-5.3-Flash p1 overlay validation passed")
PY
