# libbox-spm-bullios

`Libbox.xcframework` สำหรับ BullVPN iOS — build เองจาก [SagerNet/sing-box](https://github.com/SagerNet/sing-box)


## ใช้ยังไง

กด **Actions → Build Libbox → Run workflow** ใส่ 2 ค่า:

| input | ตัวอย่าง | คืออะไร |
|---|---|---|
| `sing_box_tag` | `v1.12.25` | tag ของ sing-box ที่จะเอามา build |
| `release_version` | `1.12.25` | ชื่อ release ของ repo นี้ |

workflow จะ build → สร้าง release → เขียน `Package.swift` ชี้ release นั้นให้อัตโนมัติ
ฝั่งแอปแค่กด *File → Packages → Update to Latest Package Versions*

## build tags

เอาเฉพาะที่ VLESS + REALITY ใช้จริง

- `with_utls` — REALITY client + fingerprint chrome อยู่ใต้ tag นี้ ถอดแล้วต่อไม่ได้
- `with_low_memory` — บีบ buffer เหลือ 16KB/8KB จำเป็นกับเพดาน memory ~50MB ของ Network Extension

ที่ตัดออกจาก default ของ upstream: `with_gvisor`, `with_quic`, `with_wireguard`,
`with_clash_api`, `with_conntrack`, `with_dhcp`, `with_tailscale`

**เงื่อนไขที่ต้องรักษาไว้ฝั่งแอป** — config ต้องปักที่ `"stack": "system"` และ
`includeAllNetworks` ต้องเป็น false ตลอด เพราะสองอย่างนี้คือสิ่งที่ต้องใช้ gvisor
ถ้าเผลอเปลี่ยนจะได้ error `gVisor is not included in this build` ทันที (ไม่พังเงียบ)

## ทำไมไม่ใช้ `go run ./cmd/internal/build_libbox`

คำสั่งนั้น hardcode build tags ไว้ในโค้ด (`sharedTags` ใน `cmd/internal/build_libbox/main.go`)
ไม่มี flag ให้ override เลยเรียก `gomobile bind` ตรงๆ ด้วย args ชุดเดียวกัน
เปลี่ยนแค่ `-tags` — ไม่ต้อง patch source ของ upstream

## เวอร์ชัน

pin ไว้ที่สาย **1.12** — 1.13 ถอด `LibboxNewService()` ทิ้งแล้วเปลี่ยนไปใช้
`CommandServer` (ลาก gRPC มาด้วย) ซึ่งต้องเขียน PacketTunnelProvider ใหม่ทั้งตัว
การขึ้น 1.13 ให้ถือเป็นงานแยก

## License

sing-box เป็น GPL-3.0-or-later — binary ที่ build จาก repo นี้จึงอยู่ใต้ GPL-3.0 เช่นกัน
