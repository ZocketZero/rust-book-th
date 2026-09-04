<!-- Old headings. Do not remove or links may break. -->

<a id="installing-binaries-from-cratesio-with-cargo-install"></a>

## การติดตั้งไฟล์ไบนารีด้วย `cargo install`

คำสั่ง `cargo install` ช่วยให้คุณสามารถติดตั้งและใช้งานไบนารี crate ในเครื่องของคุณได้ สิ่งนี้ไม่ได้มีไว้เพื่อทดแทนแพ็กเกจระบบ แต่มีไว้เพื่อเป็นวิธีที่สะดวกสำหรับนักพัฒนา Rust ในการติดตั้งเครื่องมือที่ผู้อื่นได้แบ่งปันไว้บน [crates.io](https://crates.io/)<!-- ignore --> โปรดทราบว่าคุณสามารถติดตั้งเฉพาะแพ็กเกจที่มีไบนารีเป้าหมาย (binary targets) เท่านั้น โดย *ไบนารีเป้าหมาย* คือโปรแกรมที่สามารถรันได้ซึ่งถูกสร้างขึ้นเมื่อ crate มีไฟล์ _src/main.rs_ หรือไฟล์อื่นที่ถูกระบุว่าเป็นไบนารี ซึ่งต่างจากไลบรารีเป้าหมาย (library target) ที่ไม่สามารถรันได้ด้วยตัวเอง แต่เหมาะสำหรับการนำไปรวมไว้ในโปรแกรมอื่น โดยทั่วไป crate จะมีข้อมูลในไฟล์ README เกี่ยวกับว่า crate นั้นเป็นไลบรารี มีไบนารีเป้าหมาย หรือมีทั้งสองอย่าง

ไฟล์ไบนารีทั้งหมดที่ถูกติดตั้งด้วย `cargo install` จะถูกจัดเก็บไว้ในโฟลเดอร์ _bin_ ของไดเรกทอรีรากการติดตั้ง (installation root) หากคุณติดตั้ง Rust โดยใช้ _rustup.rs_ และไม่ได้ตั้งค่าปรับแต่งใด ๆ ไดเรกทอรีนี้จะเป็น *$HOME/.cargo/bin* ตรวจสอบให้แน่ใจว่าไดเรกทอรีนี้อยู่ใน `$PATH` ของคุณเพื่อให้สามารถรันโปรแกรมที่คุณติดตั้งด้วย `cargo install` ได้

ตัวอย่างเช่น ในบทที่ 12 เราได้กล่าวถึงว่ามีการประยุกต์ใช้งานเครื่องมือ `grep` ในภาษา Rust ที่ชื่อว่า `ripgrep` สำหรับการค้นหาไฟล์ ในการติดตั้ง `ripgrep` เราสามารถรันคำสั่งต่อไปนี้:

<!-- manual-regeneration
cargo install something you don't have, copy relevant output below
-->

```console
$ cargo install ripgrep
    Updating crates.io index
  Downloaded ripgrep v14.1.1
  Downloaded 1 crate (213.6 KB) in 0.40s
  Installing ripgrep v14.1.1
--snip--
   Compiling grep v0.3.2
    Finished `release` profile [optimized + debuginfo] target(s) in 6.73s
  Installing ~/.cargo/bin/rg
    Installed package `ripgrep v14.1.1` (executable `rg`)
```

บรรทัดรองสุดท้ายของผลลัพธ์แสดงถึงตำแหน่งและชื่อของไบนารีที่ถูกติดตั้ง ซึ่งในกรณีของ `ripgrep` คือ `rg` ตราบใดที่ไดเรกทอรีการติดตั้งอยู่ใน `$PATH` ของคุณตามที่กล่าวไว้ก่อนหน้านี้ คุณก็สามารถรัน `rg --help` และเริ่มใช้เครื่องมือค้นหาไฟล์ที่รวดเร็วและเป็นสไตล์ Rust ยิ่งขึ้นได้แล้ว!
