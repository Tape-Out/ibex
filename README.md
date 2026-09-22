# ibex

lowRISC's Ibex, the RV32 core inside OpenTitan, taken as a black box.

![maturity](https://img.shields.io/badge/maturity-planned-lightgrey) ![license](https://img.shields.io/badge/license-MIT%20OR%20Apache--2.0%20OR%20MulanPSL--2.0-blue) ![upstream](https://img.shields.io/badge/upstream-Apache--2.0-lightgrey)

Part of the [Tape-Out](https://github.com/Tape-Out) IP library, wired up by
[`xirang`](https://github.com/Tape-Out/xirang). The core is a submodule at
`third_party/ibex`; nothing in it is modified. 194 SystemVerilog files elaborate, including
the `prim_*` library OpenTitan vendors into it.

## What this repository adds

35 knobs, all read out of the elaborated design rather than copied by hand. Five of them are
enumerations with the upstream's own names:

```yaml
rv32M:
  type: choice
  values: [RV32MNone, RV32MSlow, RV32MFast, RV32MSingleCycle]
  default: RV32MFast
```

That distinction matters. An enum parameter given an integer does not fail — slang leaves it
`<unset>` and the backend quietly uses the default, so the area and timing you measure belong
to a different core. The receipt compares the elaborated value against what was asked for and
says so by name.

Five parameters stay at their defaults and the manifest says which: the PMP reset tables are
arrays, and three are random constants. They are not scalars, so they cannot be knobs yet.

## Testing

No upstream test runs here yet. Ibex ships no prebuilt firmware — `examples/simple_system`
builds its software with a RISC-V toolchain. Until that is wired up, every matrix point still
has to elaborate, and the declaration is still checked against the elaborated design.

Saying "no upstream test" is the point: a green matrix that ran nothing would be worse than
a red one.

## Limits

The instruction and data ports speak Ibex's own `req`/`gnt`/`rvalid` protocol, not AXI; a
bridge belongs outside. The `rvfi` port group exists only under the `RVFI` macro and is not
declared. The CHERIoT revocation path is declared but nothing drives it.

## License

This repository: 任选其一 [MIT](LICENSE-MIT) · [Apache 2.0](LICENSE-APACHE) ·
[木兰宽松许可证 第2版](LICENSE-MULAN). `third_party/ibex` stays **Apache-2.0**.
