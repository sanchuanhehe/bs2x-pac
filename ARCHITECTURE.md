# bs2x-pac — 架构

`bs2x-pac` 是用 **svd2rust** 从 [`bs2x-svd`](https://github.com/hispark-rs/bs2x-svd)
（嵌套子模块 `bs2x-svd/`）生成的 HiSilicon **BS21/BS2X** 寄存器访问层，角色与 `ws63-pac` 对等。

## 生成关系

```
bs2x-svd/BS2X.svd  ──(svd2rust 0.37.1 --target riscv)──▶  bs2x-pac/src/lib.rs
        │                                                    （+ device.x，rt 特性）
        └─ 复用 ws63-svd 的同版本 IP 寄存器块 + BS21 地址/实例/中断
```

`BS2X.svd` 的 `<registers>` 复用自 `WS63.svd`（BS21 与 WS63 同版本化 IP，已对照 `fbb_bs2x` 核对），
但 `bs2x-pac` 本身**不依赖 `ws63-pac`** —— 生成的 crate 自包含（`generic` 模块、外设单例、`Peripherals`、
`interrupt::ExternalInterrupt` 均由 svd2rust 原生产出）。

## 不要手改 lib.rs

`src/lib.rs` 由 `bs2x-svd/regen.sh` 可复现生成。改寄存器/地址/中断：编辑 `bs2x-svd/BS2X.svd` 后重跑 `regen.sh`。

## 与生态的关系

- 上游真值：[`bs2x-svd`](https://github.com/hispark-rs/bs2x-svd)（CMSIS-SVD）。
- 下游消费：[`hisi-riscv-hal`](https://github.com/hispark-rs/hisi-riscv-hal) 的 `chip-bs21` 特性
  （`#[no_mangle] DEVICE_PERIPHERALS` 单例、`device.x` 中断向量表均来自本 crate）。
- 同族对照：[`ws63-pac`](https://github.com/hispark-rs/ws63-pac)（WS63 的 per-chip PAC）。
- 文档：[`bs2x-guide`](https://github.com/hispark-rs/bs2x-guide)。

详见伞仓 <https://github.com/hispark-rs/hisi-riscv-rs>。
