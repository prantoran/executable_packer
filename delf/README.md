# delf

Demystify ELF

https://fasterthanli.me/series/making-our-own-executable-packer/part-1


## Create library

```bash
cargo new --lib delf
cd delf
cargo add nom@5
```

output:

```bash
    Updating crates.io index
      Adding nom v5 to dependencies
             Features as of v5.0.0:
             + alloc
             + lexical
             + lexical-core
             + std
             - lazy_static
             - regex
             - regexp
             - regexp_macros
    Updating crates.io index
     Locking 10 packages to latest compatible versions
      Adding arrayvec v0.5.2 (latest: v0.7.6)
      Adding bitflags v1.3.2 (latest: v2.6.0)
      Adding lexical-core v0.7.6 (latest: v1.0.2)
      Adding nom v5.1.3 (latest: v7.1.3)
```


`nom` is used for parsing.

Only focusing on little endian 64 bit ELF.

## Another crate to test delf crate

```bash
cargo new --bin elk
cd elk/
cargo add --path ../delf
```
output:
```bash
      Adding delf (local) to dependencies
    Updating crates.io index
     Locking 11 packages to latest compatible versions
      Adding arrayvec v0.5.2 (latest: v0.7.6)
      Adding bitflags v1.3.2 (latest: v2.6.0)
      Adding lexical-core v0.7.6 (latest: v1.0.2)
      Adding nom v5.1.3 (latest: v7.1.3)
```

```bash
# in elk/
cargo build -q
# input is a supported ELF file!
```