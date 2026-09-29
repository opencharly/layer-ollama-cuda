# ollama-cuda

NVIDIA CUDA inference backend for the Ollama LLM server, as a charly *add-on*
layer.

The `ollama-cuda` candy adds the CUDA `ggml` backend so a composing GPU box runs
inference on an NVIDIA card instead of falling back to CPU. It is an **add-on to
the `ollama` candy, never a replacement**: it contributes only the backend
libraries that drop into Ollama's own runner directory
(`/usr/lib/ollama/cuda_v*/libggml-cuda.so` plus the CUDA runtime it links).
Keeping the backend in its own candy is what makes GPU support a per-box
composition choice — an image that wants CUDA composes this; an image that does
not pays none of its ~1 GiB.

## What it provides

| Property | Value |
|---|---|
| Layer / candy | `ollama-cuda` |
| Requires | `pod-ollama` (the base `ollama` candy) |
| Backend | `/usr/lib/ollama/cuda_v*/libggml-cuda.so` |
| Package | `ollama-cuda` (Arch) |
| Service / port | none (contributes a backend only) |

## Two install paths, one assertion

- On Arch (CachyOS included) the split `ollama-cuda` package supplies the
  backend; it depends on the `ollama` package, so the two compose rather than
  conflict.
- On a distro with no Ollama package, the base candy installs the upstream
  tarball, which **already bundles** the CUDA backend — so this candy adds no
  package there and simply asserts the backend is present.

Either way the check below fails when the CUDA backend is missing, which is the
property worth having. The check is deliberately version-agnostic (a glob), so
it survives the next CUDA-major bump.

## How to use it

Compose the add-on alongside the base Ollama candy in a GPU box:

```yaml
my-ollama-gpu:
  candy:
    base: cachyos
    candy:
      - '@github.com/opencharly/pod-ollama:v2026.243.0411'
      - '@github.com/opencharly/layer-ollama-cuda:v2026.243.1754'
```

## Layout

- `charly.yml` — the `ollama-cuda:` candy entity: the `require:` on
  `pod-ollama`, the `distro:` package arm, and the `plan:` `check:` assertion.
- `CHANGELOG/` — per-CalVer release history.
- `README.md` — this user overview.

## Related

- Closest family skill: `/charly-ollama:ollama` — the nearest owning procedure; this
  repo carries no `skill:` entity of its own.
- Base server: `/charly-ollama:ollama` — the CPU-first Ollama server this adds to.
- AMD alternative: `/charly-ollama:ollama` composition of
  `opencharly/layer-ollama-rocm`.
- GPU runtime: `/charly-distros:cuda`.
- [`opencharly/opencharly`](https://github.com/opencharly/opencharly) — the umbrella.
