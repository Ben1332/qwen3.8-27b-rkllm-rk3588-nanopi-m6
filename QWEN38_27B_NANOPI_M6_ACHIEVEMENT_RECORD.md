# Qwen3.8-27B on FriendlyElec NanoPi M6 (RK3588S) — Technical Achievement Record

**Achievement date:** September 1, 2026 (local time, Eastern Daylight Time)  
**Public record prepared:** September 2, 2026  
**Operator / project:** Ben Reynolds / ASLIX  
**Platform:** FriendlyElec NanoPi M6, RK3588S, 32 GB LPDDR5, NVMe  
**Model:** Qwen3.8-27B  
**Target format:** RKLLM W8A8  
**Target NPU:** RK3588, 3 NPU cores  
**RKLLM Toolkit used for export:** 1.3.0  
**RKLLM Runtime used for successful inference:** 1.3.0, side-loaded  
**RKNPU driver:** 0.9.8  
**Context limit in exported model:** 2048

## Achievement

On September 1, 2026, Ben Reynolds successfully converted Qwen3.8-27B from its original model weights into an RKLLM W8A8 model targeted at RK3588 and demonstrated working NPU inference on a FriendlyElec NanoPi M6 (RK3588S).

This repository is intended to establish a public, independently timestamped technical record of that work.

A careful priority statement is:

> On September 1, 2026, Ben Reynolds successfully converted Qwen3.8-27B to RKLLM W8A8 and demonstrated working NPU inference on a FriendlyElec NanoPi M6 (RK3588S). At the time this record was prepared, no earlier independently published single-board NanoPi M6 / RK3588S RKLLM deployment of Qwen3.8-27B had been identified in the searches performed for this record.

This is deliberately **not** an unconditional "world first" claim. If an earlier public record is found, this statement should be updated to preserve accuracy.

## Exported Model Identity

**Filename**

`Qwen3.8-27B-RK3588-W8A8-20260830-133629.attempt-001.rkllm`

**Exact size**

`28,336,913,396 bytes`

**SHA-256**

`BC6997B87FC7238E7DF0952AF1615E07DD25DFE1DB0FBB8491CA44E6EA9F2AC3`

These values identify the exact artifact used for the successful NanoPi M6 test.

## Conversion Result

The successful converter run reported:

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

The quantization parameter checkpoint was also preserved:

```text
Qwen3.8-27B-RK3588-W8A8.qparams
2,768,416 bytes
```

## NanoPi M6 Runtime Proof

The NanoPi M6 recognized the exported model as:

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

The 27B model initially exposed two deployment constraints:

1. the NanoPi's existing ASLIX runtime was RKLLM 1.2.1, so RKLLM 1.3.0 had to be side-loaded rather than replacing the stable runtime;
2. the 27B load required nearly the entire 32 GB board, so the graphical desktop and ASLIX services had to be temporarily stopped to provide sufficient memory.

With the NanoPi in low-memory/headless mode, the model loaded and generated text on the RK3588 NPU.

## Inference Evidence

A custom RKLLM 1.3.0 runner was built so Qwen3.8 could be given an explicit chat template.

Observed generation included:

```text
user: answer in english only . what is your name
robot: If the user is asking about my name, I should clarify that I am Qwen,
a large language model developed by Tongyi Lab.
...
I'm Qwen, a large language model developed by Tongyi Lab.
How can I assist you today?
```

This established that the converted 27B model was not merely parsed by RKLLM; it was performing actual generation on the NanoPi M6.

## Runtime Isolation

The existing ASLIX models remain on their previously working RKLLM runtime. Qwen3.8-27B is run using a side-loaded RKLLM 1.3.0 library so the new model does not replace or modify the stable runtime used by the rest of the system.

Example isolated launch environment:

```bash
LD_LIBRARY_PATH=/mnt/nvme/rkllm-runtime-1.3.0
```

The custom Qwen3.8 runner was stored separately from the normal `rkllm` executable.

## Reproducibility / Evidence Checklist

A strong public record should include as many of these as possible:

- this README;
- screenshot or terminal transcript showing successful RKLLM export;
- screenshot or terminal transcript showing the NanoPi M6 model metadata;
- screenshot or transcript of live Qwen3.8-27B generation;
- exact SHA-256 above;
- converter configuration or script;
- custom Qwen3.8 runner source;
- `uname -a`;
- `/proc/device-tree/model`;
- RKLLM runtime version;
- RKNPU driver version;
- date/time of the demonstration.

Do **not** upload the 28.3 GB model artifact directly to a normal GitHub Release asset: GitHub release assets have a per-file size limit below the size of this model. The hash is sufficient to identify the exact artifact, while the model itself can be hosted elsewhere if redistribution is permitted.

## Hard Reasoning Test for Edge-AI Suitability

The following prompt is intended to test whether this 27B model can act as a high-level reasoning/controller model for small offline edge systems rather than merely answer trivia.

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
1. listen continuously for speech,
2. detect whether a person is present,
3. answer questions,
4. occasionally analyze a camera image,
5. preserve battery and avoid overheating,
6. never exceed available RAM,
7. remain usable when a subsystem crashes,
8. preserve conversational state even when models are unloaded.

Design the runtime architecture.

Your answer must make concrete engineering decisions and explain:
- which tasks run continuously and which run only on demand;
- which model handles which class of task;
- how RAM is managed when the 27B model needs almost the entire machine;
- how models are unloaded and switched safely;
- how conversation state survives model unloading;
- how services recover after crashes;
- how thermal and battery limits change routing decisions;
- exact criteria for choosing the 27B model instead of a smaller model;
- how the system should behave if the 27B model cannot be loaded.

Then provide:
A. a component diagram in text,
B. a state machine,
C. model-routing pseudocode,
D. failure-recovery pseudocode,
E. three concrete example requests and show which model/subsystems would handle each one.

Do not give generic advice. Treat the 32 GB RAM limit, offline operation, hardware contention, and crash recovery as real engineering constraints. If two requirements conflict, explicitly identify the conflict and choose a policy.
```

### What a strong answer should notice

A strong answer should independently reason that:

- the 27B model should not remain loaded continuously if it consumes most of RAM;
- lightweight speech/presence/sensor processes should stay resident;
- conversational state should live outside the LLM process;
- a smaller model should handle routine requests;
- the 27B should be reserved for difficult reasoning, planning, coding, or ambiguous tasks;
- the current LLM must be unloaded before a large model is loaded;
- model switching needs watchdogs, timeouts, rollback, and state restoration;
- camera/vision workloads and NPU workloads must be scheduled rather than blindly run together;
- thermal, RAM, and battery telemetry should be routing inputs.

## Recommended GitHub Publication Procedure

1. Create a new public repository, for example:
   `qwen3.8-27b-rkllm-rk3588-nanopi-m6`

2. Commit this README together with the small proof artifacts and source files.

3. Push the commit to GitHub.

4. Create a Git tag such as:
   `qwen38-27b-nanopi-m6-proof-2026-09-01`

5. Prefer a **signed tag** if your Git signing key is configured.

6. Create a GitHub Release from that tag with a title such as:
   `Qwen3.8-27B RKLLM W8A8 running on NanoPi M6 / RK3588S`

7. Attach only small evidence artifacts and source files. Put the 28.3 GB model hash in the release notes rather than trying to attach that model as a single normal GitHub release asset.

8. If your GitHub account/repository supports **immutable releases**, enable that before publication and publish the release only after the evidence bundle is complete.

## Suggested Release Notes

```text
On September 1, 2026, I successfully converted Qwen3.8-27B to RKLLM W8A8
and demonstrated live NPU inference on a FriendlyElec NanoPi M6 (RK3588S,
32 GB LPDDR5).

Export:
- RKLLM Toolkit 1.3.0
- target RK3588
- W8A8
- 3 NPU cores
- context 2048
- output size 28,336,913,396 bytes
- SHA256 BC6997B87FC7238E7DF0952AF1615E07DD25DFE1DB0FBB8491CA44E6EA9F2AC3

Runtime proof:
- FriendlyElec NanoPi M6 / RK3588S
- RKLLM Runtime 1.3.0, side-loaded
- RKNPU driver 0.9.8
- successful Qwen3.8-27B text generation on the NPU

This release is a public technical record of the conversion and deployment.
It does not make an unconditional world-first claim. At publication time,
I had not identified an earlier independently published NanoPi M6 / RK3588S
RKLLM deployment of Qwen3.8-27B.
```

## Notes on Priority

A GitHub commit/release provides a useful public chronological record. A signed tag or verified signature strengthens authorship evidence. An immutable GitHub Release, where available, further strengthens the record because release assets and the associated release attestation are protected against later modification.

For stronger archival evidence beyond GitHub, a later step can deposit the repository snapshot with an archival service that issues a persistent identifier/DOI.

---

**Record prepared from contemporaneous conversion and deployment logs.**
