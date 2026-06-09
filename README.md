# bs2x-pac

Peripheral Access Crate for the HiSilicon **BS21 / BS2X** RISC-V SoC
(RV32IMFC_Zicsr, BLE 5.4 + SLE/NearLink, **no Wi-Fi**), **generated via svd2rust**
from [`bs2x-svd`](https://github.com/sanchuanhehe/bs2x-svd) (vendored as a nested
submodule under `bs2x-svd/`).

Independent of `ws63-pac`: BS21 shares WS63's HimiDeer riscv31 core + versioned IP
(UART v151 / TIMER v150 / GPIO v150), so the SVD reuses those register-block
definitions, but the generated crate stands on its own (own addresses, instance
set, and interrupt map).

## Peripherals

Shared versioned-IP (reused from WS63's SVD): `GLB_CTL_M`, `GPIO0..4` + `ULP_GPIO`,
`UART0..2`, `I2C0..1`, `SPI0..2`, `PWM`, `DMA` + `SDMA`, `TIMER`, `WDT`, `TCXO`,
`RTC`, `TRNG`.

BS2X-specific (derived from the fbb_bs2x SDK HAL headers — no WS63 analogue):
`GADC` (13-bit ADC), `KEYSCAN`, `PDM`, `QDEC`.

All at the BS21 base addresses (`0x52xx_xxxx` M_CTL / `0x57xx_xxxx` GLB), plus the
BS21 `interrupt::ExternalInterrupt` map (`LOCAL_INTERRUPT0 = 26`; mie 26-31 + LOCI
≥32). USB / NFC are not yet register-modeled (no HAL register-block headers in the
SDK tree); their IRQs ride the GLB_CTL_M catch-all.

```rust
let p = bs2x_pac::Peripherals::take().unwrap();
let gpio0 = p.gpio0;   // GPIO0 @ 0x5701_0000
```

## Features

- `critical-section` (default) — backs `Peripherals::take()`.
- `rt` — emit `device.x` (riscv-rt interrupt vector table) for the active firmware.

## Regenerating

Do **not** hand-edit `src/lib.rs`. Edit `bs2x-svd/BS2X.svd` and rerun the
reproducible pipeline:

```bash
bash bs2x-svd/regen.sh
```

See [`bs2x-svd/README.md`](bs2x-svd/README.md) for the pipeline. Consumed by
[`hisi-riscv-hal`](https://github.com/sanchuanhehe/hisi-riscv-hal) under its
`chip-bs21` feature.
