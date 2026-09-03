# Qwen3.8-27B on FriendlyElec NanoPi M6 (RK3588S) — Technical Achievement Record

**Achievement date:** September 1, 2026 (Eastern Daylight Time)  
**Record prepared:** September 2, 2026  
**Operator / project:** Ben Reynolds / ASLIX  
**Platform:** FriendlyElec NanoPi M6, RK3588S, 32 GB LPDDR5, NVMe  
**Model:** Qwen3.8-27B  
**Target format:** RKLLM W8A8  
**Target NPU:** RK3588, 3 NPU cores  
**RKLLM Toolkit:** 1.3.0  
**RKLLM Runtime:** 1.3.0, side-loaded  
**RKNPU driver:** 0.9.8  
**Context limit:** 2048

## Achievement

On September 1, 2026, Ben Reynolds successfully converted Qwen3.8-27B to RKLLM W8A8 and demonstrated working NPU inference on a FriendlyElec NanoPi M6 (RK3588S).

**Priority statement:**  
> On September 1, 2026, Ben Reynolds successfully converted Qwen3.8-27B to RKLLM W8A8 and demonstrated working NPU inference on a FriendlyElec NanoPi M6 (RK3588S). At the time of this record, no earlier independently published NanoPi M6 / RK3588S RKLLM deployment of Qwen3.8-27B was identified.

This repository establishes a public, independently timestamped technical record.

## Exported Model Identity

**Filename**

`Qwen3.8-27B-RK3588-W8A8-20260830-133629.attempt-001.rkllm`

**Size**

`28,336,913,396 bytes`

**SHA-256**

`BC6997B87FC7238E7DF0952AF1615E07DD25DFE1DB0FBB8491CA44E6EA9F2AC3`

## Conversion Result

```text
load returned 0
BUILD + QUANTIZATION
Building model: 100% 1063/1063
Optimizing model: 100% 64/64
build returned 0
BUILD + QUANTIZATION COMPLETE
EXPORT ATTEMPT 1
Converting model: 100% 851/851
Setting max_context_limit to 2048
Exporting the model
1209/1209 (100%)
Model saved
export returned 0; size=28336913396 bytes
```

Quantization checkpoint:
```text
Qwen3.8-27B-RK3588-W8A8.qparams
2,768,416 bytes
```

## NanoPi M6 Runtime Configuration

```text
rkllm-runtime version: 1.3.0
rknpu driver version: 0.9.8
platform: RK3588
rkllm-toolkit version: 1.3.0
max_context_limit: 2048
npu_core_num: 3
target_platform: RK3588
model_dtype: W8A8
```

## Deployment Constraints

1. NanoPi's existing ASLIX runtime was RKLLM 1.2.1; RKLLM 1.3.0 was side-loaded to preserve stable runtime
2. 27B model requires nearly the entire 32 GB board; graphical desktop and ASLIX services were disabled to provide sufficient memory

With the NanoPi in headless mode, the model loaded and generated text on the RK3588 NPU.

## Inference Evidence

**Date/Time:** September 1, 2026  
**Runtime:** Custom RKLLM 1.3.0 runner with explicit chat template

**Input:**
```text
user: answer in english only . what is your name
```

**Output:**
```text
robot: If the user is asking about my name, I should clarify that I am Qwen,
a large language model developed by Tongyi Lab.
...
I'm Qwen, a large language model developed by Tongyi Lab.
How can I assist you today?
```

This confirms the converted 27B model performed actual generation on the NanoPi M6 NPU.

## Runtime Isolation

Existing ASLIX models remain on RKLLM 1.2.1. Qwen3.8-27B uses side-loaded RKLLM 1.3.0:

```bash
LD_LIBRARY_PATH=/mnt/nvme/rkllm-runtime-1.3.0
```

## Evidence Checklist

- Technical achievement record (this file)
- RKLLM export transcript
- NanoPi M6 model metadata
- Live model generation output
- Model SHA-256 hash
- Converter configuration
- Custom Qwen3.8 runner source
- System information (`uname -a`, `/proc/device-tree/model`)
- Version information (RKLLM runtime, RKNPU driver)

**Note:** The 28.3 GB model artifact is not attached to GitHub Release assets due to size limits. The SHA-256 hash provides artifact verification.

## Hard Reasoning Test for Edge-AI Suitability

The following prompt tests whether this 27B model can act as a reasoning/controller model for offline edge systems:

```text
You are the AI controller inside a 32 GB RK3588S edge device with no internet.

Available hardware and software:
- camera
- microphone
- GPS
- IMU
- local speech recognition
- local text-to-speech
- a 27B reasoning model running on the NPU
- smaller fallback language models
- persistent local storage

The device must:
1. listen continuously for speech
2. detect whether a person is present
3. answer questions
4. occasionally analyze a camera image
5. preserve battery and avoid overheating
6. never exceed available RAM
7. remain usable when a subsystem crashes
8. preserve conversational state even when models are unloaded

Design the runtime architecture. Your answer must make concrete engineering decisions and explain:
- which tasks run continuously and which run only on demand
- which model handles which class of task
- how RAM is managed when the 27B model needs almost the entire machine
- how models are unloaded and switched safely
- how conversation state survives model unloading
- how services recover after crashes
- how thermal and battery limits change routing decisions
- exact criteria for choosing the 27B model instead of a smaller model
- how the system should behave if the 27B model cannot be loaded

Then provide:
A. a component diagram in text
B. a state machine
C. model-routing pseudocode
D. failure-recovery pseudocode
E. three concrete example requests and show which model/subsystems would handle each one

Do not give generic advice. Treat the 32 GB RAM limit, offline operation, hardware contention, and crash recovery as real engineering constraints. If two requirements conflict, explicitly identify the trade-off.
```

### Expected Indicators of Strong Response

- 27B model should not remain loaded continuously if it consumes most RAM
- Lightweight speech/presence/sensor processes stay resident
- Conversational state lives outside the LLM process
- Smaller model handles routine requests
- 27B reserved for difficult reasoning, planning, coding, or ambiguous tasks
- Current LLM must be unloaded before a large model is loaded
- Model switching includes watchdogs, timeouts, rollback, and state restoration
- Camera/vision and NPU workloads are scheduled rather than run blindly together
- Thermal, RAM, and battery telemetry inform routing decisions

## GitHub Publication Procedure

1. Create public repository: `qwen3.8-27b-rkllm-rk3588-nanopi-m6`
2. Commit this file with small proof artifacts and source
3. Push to GitHub
4. Create signed Git tag: `qwen38-27b-nanopi-m6-proof-2026-09-01`
5. Create GitHub Release with title: `Qwen3.8-27B RKLLM W8A8 running on NanoPi M6 / RK3588S`
6. Attach only small evidence artifacts and source files
7. Include model hash in release notes (not 28.3 GB artifact)
8. Enable immutable releases if available

## Release Notes Template

```text
On September 1, 2026, I successfully converted Qwen3.8-27B to RKLLM W8A8
and demonstrated live NPU inference on a FriendlyElec NanoPi M6 (RK3588S,
32 GB LPDDR5).

Export:
- RKLLM Toolkit 1.3.0
- Target: RK3588
- Format: W8A8
- NPU cores: 3
- Context: 2048
- Size: 28,336,913,396 bytes
- SHA256: BC6997B87FC7238E7DF0952AF1615E07DD25DFE1DB0FBB8491CA44E6EA9F2AC3

Runtime proof:
- FriendlyElec NanoPi M6 / RK3588S
- RKLLM Runtime 1.3.0, side-loaded
- RKNPU driver 0.9.8
- Successful Qwen3.8-27B text generation on NPU

This release documents the conversion and deployment. No earlier independently
published NanoPi M6 / RK3588S RKLLM deployment of Qwen3.8-27B was identified
at publication time.
```

---

**Record prepared from contemporaneous conversion and deployment logs.**
