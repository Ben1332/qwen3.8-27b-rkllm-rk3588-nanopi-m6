# Qwen 3.8 27B on NanoPi M6 / RK3588S

Running **Qwen 3.8 27B Dense** locally on a **FriendlyElec NanoPi M6** using the **Rockchip RK3588S NPU**.

This repository documents the working setup, conversion result, performance, and ongoing integration work.

## Current Status

Working:

- Qwen 3.8 27B Dense
- RKLLM W8A8
- RK3588S
- 3 NPU cores
- 32 GB RAM
- 2048 context
- Fully offline inference
- Integrated into the ASLIX local AI project
- Local speech recognition with Vosk
- Local speech output with Piper / Picard
- GUI integration
- GPS, camera/vision, memory, and local hardware features

The model has successfully initialized and produced normal interactive responses on the NanoPi M6.

Example runtime output:

```text
rkllm-runtime version: 1.3.0
rknpu driver version: 0.9.8
platform: RK3588
model_dtype: W8A8
npu_core_num: 3

rkllm init success
Ready: Qwen 3.8 27B (NPU)
```

## Hardware

Primary test platform:

- FriendlyElec NanoPi M6
- Rockchip RK3588S
- 32 GB LPDDR5
- NVMe storage
- 3-core Rockchip NPU

## Model Configuration

Current working configuration:

| Setting | Value |
|---|---|
| Model | Qwen 3.8 27B Dense |
| Runtime | RKLLM 1.3.0 |
| Quantization | W8A8 |
| Context | 2048 |
| NPU cores | 3 |
| Platform | RK3588 / RK3588S |
| Operation | Fully offline |

## Performance

Current sustained generation is approximately **1 token/sec**, depending on prompt length and output.

The goal of this project is not to claim desktop-class speed from an RK3588 device. The goal is to show that a model of this size can run locally on low-power edge hardware and be integrated into a complete offline AI system.

## ASLIX Integration

Qwen 3.8 27B is being integrated as one selectable model inside **ASLIX**, an offline AI system running locally on the NanoPi.

The ASLIX stack includes:

- local LLM inference
- Vosk speech recognition
- Piper text-to-speech
- Picard voice
- graphical interface
- GPS
- local memory
- camera and vision support
- sensor integration
- navigation and utility functions

A major part of the work has been getting the full stack to coexist with the 27B model under the NanoPi M6's memory limits.

## Memory Notes

The Qwen 3.8 27B RKLLM process can use roughly **27 GB of resident memory** on the 32 GB system.

Because of that, ASLIX uses model-specific memory management and carefully controls when optional services and vision workloads are loaded.

This remains active development work.

## Why This Repository Exists

There was very little practical documentation for getting a model of this size converted, loaded, and running reliably through RKLLM on RK3588-class hardware.

This repository exists to document the achievement, the hardware used, the working runtime configuration, and the ongoing engineering work around it.

## Model Distribution

The converted `.rkllm` model is **not being distributed at this time**.

The conversion, deployment, optimization, and recovery process required substantial testing and development. The project is still being actively developed.

This repository is currently focused on documenting the result and the platform rather than distributing the finished converted model.

## Files

- `QWEN38_27B_EDGE_REASONING_TEST.txt` — test/output record
- `QWEN38_27B_NANOPI_M6_ACHIEVEMENT_RECORD.md` — project achievement record
- `LICENSE` — repository license

## Project Goals

Ongoing work includes:

- improving long-session stability
- optimizing memory usage
- improving ASLIX/Qwen voice interaction
- reducing response latency where possible
- improving camera/vision integration
- improving hardware sensor integration
- testing additional RK3588-class hardware

## Project

**ASLIX**  
Offline AI • RK3588 • Qwen 3.8 • Edge AI

---

This project is a work in progress. Results, configuration, and implementation details may change as testing continues.
