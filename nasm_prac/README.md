# NASM: Netwide Assembler

https://fasterthanli.me/series/making-our-own-executable-packer/part-1

[Searchable Linux Syscall Table](https://filippo.io/linux-syscall-table/)


## Compile

```bash
nasm -f elf64 hello.asm
ld hello.o -o hello
file hello
```


```bash
gzip -9 hello
```
- hello (8.7 kb) -> hello.gz (357 b)

## Hexdump

```bash
xxd < hello | head
```
```bash
00000000: 7f45 4c46 0201 0100 0000 0000 0000 0000  .ELF............
00000010: 0200 3e00 0100 0000 0010 4000 0000 0000  ..>.......@.....
00000020: 4000 0000 0000 0000 0821 0000 0000 0000  @........!......
00000030: 0000 0000 4000 3800 0300 4000 0600 0500  ....@.8...@.....
00000040: 0100 0000 0400 0000 0000 0000 0000 0000  ................
00000050: 0000 4000 0000 0000 0000 4000 0000 0000  ..@.......@.....
00000060: e800 0000 0000 0000 e800 0000 0000 0000  ................
00000070: 0010 0000 0000 0000 0100 0000 0500 0000  ................
00000080: 0010 0000 0000 0000 0010 4000 0000 0000  ..........@.....
00000090: 0010 4000 0000 0000 2500 0000 0000 0000  ..@.....%.......
```

```bash
xxd < hello | tail -32 | head
```
```bash
00002090: 0920 4000 0000 0000 0000 0000 0000 0000  . @.............
000020a0: 2200 0000 1000 0200 1020 4000 0000 0000  "........ @.....
000020b0: 0000 0000 0000 0000 0068 656c 6c6f 2e61  .........hello.a
000020c0: 736d 006d 7367 005f 5f62 7373 5f73 7461  sm.msg.__bss_sta
000020d0: 7274 005f 6564 6174 6100 5f65 6e64 0000  rt._edata._end..
000020e0: 2e73 796d 7461 6200 2e73 7472 7461 6200  .symtab..strtab.
000020f0: 2e73 6873 7472 7461 6200 2e74 6578 7400  .shstrtab..text.
00002100: 2e64 6174 6100 0000 0000 0000 0000 0000  .data...........
00002110: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00002120: 0000 0000 0000 0000 0000 0000 0000 0000  ................
```


### Section names

```bash
# -s = seek, -l = length
xxd -s 62 -l 2 ./hello
# 0000003e: 0500
```
- Little endian file, so value: 0x0005
- Fifth section header in the table contains section names


### seaction header table
```bash
# -g = group size, -e = little-endian
xxd -s 40 -l 8 -g 8 -e ./hello
# 00000028: 0000000000002108      

echo $((0x2108))
# 8456
```
- Start of section headers table: `0x2108`

sh_offset = 0x18 # Offset of the section in the file image. 

e_shentsize = Every section header has length 0x40

```bash
xxd -s $((0x2108 + 0x40*5 + 0x18)) -l 8 -g 8 -e ./hello
# 00002260: 00000000000020df                    . ......
```
- The data of the section that contains section names should be at `0x20df`


```bash
xxd -s $((0x20df)) ./hello | head -4
# 000020df: 002e 7379 6d74 6162 002e 7374 7274 6162  ..symtab..strtab
# 000020ef: 002e 7368 7374 7274 6162 002e 7465 7874  ..shstrtab..text
# 000020ff: 002e 6461 7461 0000 0000 0000 0000 0000  ..data..........
# 0000210f: 0000 0000 0000 0000 0000 0000 0000 0000  ................
```