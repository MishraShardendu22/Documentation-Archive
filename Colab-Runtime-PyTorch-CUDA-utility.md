# Colab Accelerator & PyTorch Backend Documentation

## 1. Initial Problem

Training log showed:

```
0.06 it/s
ETA ~149 hours (~6 days)
'pin_memory' argument is set as true but no accelerator is found
```

Despite selecting GPU in Colab, training was running on **CPU**.

---

## 2. Root Cause

Colab runtime selection only allocates hardware.

It does **not** guarantee your framework is using it.

For GPU training to actually work, all three must be true:

1. Runtime → GPU
2. CUDA-enabled PyTorch installed
3. `torch.cuda.is_available()` returns True

If PyTorch is CPU-only, it will ignore the GPU even if Colab provides one.

---

## 3. Why It Was Slow

Before fix:

```
0.06 it/s
~6 days ETA
```

After installing CUDA PyTorch:

```
5.66 it/s
~1.5 hours ETA
```

Speed improvement ≈ 94×.

Conclusion:

* Major speed gain = CUDA (GPU actually being used)
* Dataset reduction (1M → 100k) reduced total steps but not per-step speed

---

## 4. How to Verify GPU Usage

### Enable GPU

Colab:

```
Runtime → Change runtime type → GPU
```

### Check in Python

```python
import torch
print(torch.cuda.is_available())
print(torch.cuda.get_device_name(0) if torch.cuda.is_available() else "No GPU")
```

If `False` → training is on CPU.

---

## 5. Installing CUDA PyTorch in Colab

Use `!` because it is a shell command:

```python
!pip uninstall torch -y
!pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu121
```

After reinstalling, restart runtime and verify again.

---

## 6. Why GPU Runtime Didn’t Work Earlier

Colab does not automatically reinstall PyTorch when you change runtime.

If:

* You previously installed CPU-only torch
* Then switched runtime to GPU

PyTorch remains CPU-only until manually replaced.

GPU hardware was present, but framework couldn’t use it.

---

## 7. How HuggingFace Trainer Uses GPU

If CUDA is available:

* `Trainer` automatically moves model and tensors to GPU
* No manual `.to("cuda")` required

If CUDA is not available:

* It silently runs on CPU

---

## 8. TPU Environment

TPU does **not** use CUDA.

It requires **torch-xla**.

### Install for TPU:

```python
!pip install torch~=2.1.0 torch-xla~=2.1.0 -f https://storage.googleapis.com/libtpu-releases/index.html
```

### Verify TPU:

```python
import torch_xla.core.xla_model as xm
device = xm.xla_device()
print(device)
```

Output example:

```
xla:0
```

Notes:

* `torch.cuda.is_available()` will be False on TPU (normal)
* Standard CUDA PyTorch will not use TPU
* HuggingFace Trainer works automatically if torch-xla is installed

---

## 9. Accelerator Backend Summary

| Runtime | Required Backend     | Check Method                        |
| ------- | -------------------- | ----------------------------------- |
| CPU     | Default PyTorch      | `torch.cuda.is_available() → False` |
| GPU     | CUDA-enabled PyTorch | `torch.cuda.is_available() → True`  |
| TPU     | torch-xla            | `xm.xla_device()`                   |

---

## 10. Final Outcome

Original:

* CPU training
* ~6 days

After fix:

* GPU training active
* ~1–1.5 hours total

Primary improvement source: CUDA-enabled PyTorch successfully detecting GPU.
