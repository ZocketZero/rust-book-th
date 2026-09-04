## พื้นที่ทำงานของ Cargo (Cargo Workspaces)

ในบทที่ 12 เราได้สร้างแพ็กเกจที่ประกอบด้วยไบนารี crate และไลบรารี crate เมื่อโปรเจกต์ของคุณได้รับการพัฒนาอย่างต่อเนื่อง คุณอาจพบว่าไลบรารี crate มีขนาดใหญ่ขึ้นเรื่อย ๆ และคุณต้องการแบ่งแพ็กเกจของคุณออกเป็นหลาย ๆ ไลบรารี crate Cargo จึงมีฟีเจอร์ที่เรียกว่า *พื้นที่ทำงาน (workspaces)* ซึ่งสามารถช่วยจัดการหลาย ๆ แพ็กเกจที่เกี่ยวข้องกันและถูกพัฒนาไปพร้อมกันได้

### การสร้างพื้นที่ทำงาน (Workspace)

*พื้นที่ทำงาน (workspace)* คือชุดของแพ็กเกจที่ใช้ไฟล์ _Cargo.lock_ และไดเรกทอรีผลลัพธ์ (output directory) ร่วมกัน มาลองสร้างโปรเจกต์โดยใช้พื้นที่ทำงานกัน โดยเราจะใช้โค้ดแบบง่าย ๆ เพื่อให้สามารถมุ่งเน้นไปที่โครงสร้างของพื้นที่ทำงานได้ มีหลายวิธีในการจัดโครงสร้างพื้นที่ทำงาน ดังนั้นเราจะแสดงวิธีที่พบบ่อยวิธีหนึ่ง โดยเราจะมีพื้นที่ทำงานที่ประกอบด้วยหนึ่งไบนารีและสองไลบรารี ไบนารีซึ่งจะทำหน้าที่หลักจะพึ่งพาไลบรารีทั้งสองนั้น ไลบรารีหนึ่งจะให้บริการฟังก์ชัน `add_one` และอีกไลบรารีหนึ่งจะให้บริการฟังก์ชัน `add_two` ทั้งสาม crate นี้จะอยู่ภายใต้พื้นที่ทำงานเดียวกัน เราจะเริ่มจากการสร้างไดเรกทอรีใหม่สำหรับพื้นที่ทำงาน:

```console
$ mkdir add
$ cd add
```

ถัดไป ภายในไดเรกทอรี _add_ เราจะสร้างไฟล์ _Cargo.toml_ ที่จะกำหนดโครงร่างพื้นที่ทำงานทั้งหมด ไฟล์นี้จะไม่มีส่วน `[package]` แต่จะเริ่มต้นด้วยส่วน `[workspace]` ซึ่งจะอนุญาตให้เราเพิ่มสมาชิกเข้าไปในพื้นที่ทำงานได้ เรายังระบุให้ใช้อัลกอริทึม Resolver เวอร์ชันล่าสุดและดีที่สุดของ Cargo ในพื้นที่ทำงานของเราโดยตั้งค่า `resolver` เป็น `"3"`:

<span class="filename">ชื่อไฟล์: Cargo.toml</span>

```toml
{{#include ../listings/ch14-more-about-cargo/no-listing-01-workspace/add/Cargo.toml}}
```

ถัดไป เราจะสร้างไบนารี crate ชื่อ `adder` โดยการรัน `cargo new` ภายในไดเรกทอรี _add_:

<!-- manual-regeneration
cd listings/ch14-more-about-cargo/output-only-01-adder-crate/add
remove `members = ["adder"]` from Cargo.toml
rm -rf adder
cargo new adder
copy output below
-->

```console
$ cargo new adder
     Created binary (application) `adder` package
      Adding `adder` as member of workspace at `file:///projects/add`
```

การรัน `cargo new` ภายในพื้นที่ทำงานจะเพิ่มแพ็กเกจที่สร้างขึ้นใหม่เข้าไปในคีย์ `members` ของส่วน `[workspace]` ในไฟล์ _Cargo.toml_ ของพื้นที่ทำงานโดยให้อัตโนมัติ เช่นนี้:

```toml
{{#include ../listings/ch14-more-about-cargo/output-only-01-adder-crate/add/Cargo.toml}}
```

ณ จุดนี้ เราสามารถบิลด์พื้นที่ทำงานได้โดยการรัน `cargo build` ไฟล์ในไดเรกทอรี _add_ ของคุณควรจะดูเป็นดังนี้:

```text
├── Cargo.lock
├── Cargo.toml
├── adder
│   ├── Cargo.toml
│   └── src
│       └── main.rs
└── target
```

พื้นที่ทำงานมีไดเรกทอรี _target_ เพียงอันเดียวที่ระดับบนสุด ซึ่งชิ้นงานที่คอมไพล์แล้ว (compiled artifacts) จะถูกจัดวางไว้ในนั้น แพ็กเกจ `adder` จึงไม่มีไดเรกทอรี _target_ เป็นของตัวเอง แม้ว่าเราจะรัน `cargo build` จากภายในไดเรกทอรี _adder_ ชิ้นงานที่คอมไพล์แล้วก็ยังคงไปอยู่ที่ _add/target_ แทนที่จะเป็น _add/adder/target_ Cargo จัดโครงสร้างไดเรกทอรี _target_ ในพื้นที่ทำงานในลักษณะนี้เพราะ crate ในพื้นที่ทำงานถูกออกแบบมาให้พึ่งพากัน หากแต่ละ crate มีไดเรกทอรี _target_ ของตนเอง แต่ละ crate จะต้องคอมไพล์ crate อื่น ๆ ทั้งหมดในพื้นที่ทำงานซ้ำเพื่อวางชิ้นงานไว้ในไดเรกทอรี _target_ ของตนเอง การแชร์ไดเรกทอรี _target_ ร่วมกันจึงช่วยให้ crate ต่าง ๆ หลีกเลี่ยงการบิลด์ซ้ำโดยไม่จำเป็นได้

### การสร้างแพ็กเกจที่สองในพื้นที่ทำงาน

ถัดไป มาสร้างแพ็กเกจสมาชิกตัวที่สองในพื้นที่ทำงานโดยตั้งชื่อว่า `add_one` ให้สร้างไลบรารี crate ใหม่ชื่อ `add_one`:

<!-- manual-regeneration
cd listings/ch14-more-about-cargo/output-only-02-add-one/add
remove `"add_one"` from `members` list in Cargo.toml
rm -rf add_one
cargo new add_one --lib
copy output below
-->

```console
$ cargo new add_one --lib
     Created library `add_one` package
      Adding `add_one` as member of workspace at `file:///projects/add`
```

ไฟล์ _Cargo.toml_ ที่ระดับบนสุดจะรวมพาธ _add_one_ เข้าไปในรายการ `members` แล้ว:

<span class="filename">ชื่อไฟล์: Cargo.toml</span>

```toml
{{#include ../listings/ch14-more-about-cargo/no-listing-02-workspace-with-two-crates/add/Cargo.toml}}
```

ไดเรกทอรี _add_ ของคุณควรจะมีไดเรกทอรีและไฟล์เหล่านี้แล้ว:

```text
├── Cargo.lock
├── Cargo.toml
├── add_one
│   ├── Cargo.toml
│   └── src
│       └── lib.rs
├── adder
│   ├── Cargo.toml
│   └── src
│       └── main.rs
└── target
```

ในไฟล์ _add_one/src/lib.rs_ มาเพิ่มฟังก์ชัน `add_one` กัน:

<span class="filename">ชื่อไฟล์: add_one/src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch14-more-about-cargo/no-listing-02-workspace-with-two-crates/add/add_one/src/lib.rs}}
```

ตอนนี้เราสามารถกำหนดให้แพ็กเกจ `adder` ซึ่งมีไบนารีของเรา พึ่งพาแพ็กเกจ `add_one` ซึ่งมีไลบรารีของเราได้แล้ว อันดับแรก เราต้องเพิ่มพาธพึ่งพา (path dependency) บน `add_one` ลงใน _adder/Cargo.toml_

<span class="filename">ชื่อไฟล์: adder/Cargo.toml</span>

```toml
{{#include ../listings/ch14-more-about-cargo/no-listing-02-workspace-with-two-crates/add/adder/Cargo.toml:6:7}}
```

Cargo ไม่ได้ทัศนคติล่วงหน้าว่า crate ต่าง ๆ ในพื้นที่ทำงานจะพึ่งพากันเอง ดังนั้นเราจึงต้องระบุความสัมพันธ์ของการพึ่งพาให้ชัดเจน

ถัดไป มาใช้ฟังก์ชัน `add_one` (จาก crate `add_one`) ใน crate `adder` ให้เปิดไฟล์ _adder/src/main.rs_ แล้วเปลี่ยนฟังก์ชัน `main` เพื่อเรียกใช้ฟังก์ชัน `add_one` ดังในโค้ดตัวอย่างที่ 14-7

<Listing number="14-7" file-name="adder/src/main.rs" caption="การใช้งานไลบรารี crate `add_one` จาก crate `adder`">

```rust,ignore
{{#rustdoc_include ../listings/ch14-more-about-cargo/listing-14-07/add/adder/src/main.rs}}
```

</Listing>

มาบิลด์พื้นที่ทำงานโดยการรัน `cargo build` ในไดเรกทอรี _add_ ที่ระดับบนสุดกัน!

<!-- manual-regeneration
cd listings/ch14-more-about-cargo/listing-14-07/add
cargo build
copy output below; the output updating script doesn't handle subdirectories in paths properly
-->

```console
$ cargo build
   Compiling add_one v0.1.0 (file:///projects/add/add_one)
   Compiling adder v0.1.0 (file:///projects/add/adder)
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.22s
```

ในการรันไบนารี crate จากไดเรกทอรี _add_ เราสามารถระบุแพ็กเกจในพื้นที่ทำงานที่เราต้องการรันได้โดยใช้อาร์กิวเมนต์ `-p` และตามด้วยชื่อแพ็กเกจร่วมกับ `cargo run`:

<!-- manual-regeneration
cd listings/ch14-more-about-cargo/listing-14-07/add
cargo run -p adder
copy output below; the output updating script doesn't handle subdirectories in paths properly
-->

```console
$ cargo run -p adder
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.00s
      Running `target/debug/adder`
Hello, world! 10 plus one is 11!
```

การทำงานนี้จะรันโค้ดใน _adder/src/main.rs_ ซึ่งพึ่งพา crate `add_one`

<!-- Old headings. Do not remove or links may break. -->

<a id="depending-on-an-external-package-in-a-workspace"></a>

### การพึ่งพาแพ็กเกจภายนอก

สังเกตว่าพื้นที่ทำงานมีไฟล์ _Cargo.lock_ เพียงไฟล์เดียวที่ระดับบนสุด แทนที่จะมีไฟล์ _Cargo.lock_ ในแต่ละไดเรกทอรีของ crate สิ่งนี้ช่วยให้มั่นใจได้ว่าทุก crate จะใช้เวอร์ชันเดียวกันของทรัพยากรภายนอกทั้งหมด หากเราเพิ่มแพ็กเกจ `rand` ลงในไฟล์ _adder/Cargo.toml_ และ _add_one/Cargo.toml_ Cargo จะแก้ไขค่าทรัพยากรภายนอกทั้งสองนั้นให้เป็น `rand` เวอร์ชันเดียว และบันทึกค่านั้นไว้ในไฟล์ _Cargo.lock_ เพียงไฟล์เดียว การทำให้ทุก crate ในพื้นที่ทำงานใช้ทรัพยากรภายนอกเดียวกันหมายความว่า crate จะสามารถทำงานร่วมกันได้อย่างสมบูรณ์เสมอ ให้เราเพิ่ม crate `rand` ลงในส่วน `[dependencies]` ในไฟล์ _add_one/Cargo.toml_ เพื่อให้เราสามารถใช้ crate `rand` ใน crate `add_one` ได้:

<!-- When updating the version of `rand` used, also update the version of
`rand` used in these files so they all match:

* ch01-01-installation.md
* ch02-00-guessing-game-tutorial.md
* ch07-04-bringing-paths-into-scope-with-the-use-keyword.md
-->

<span class="filename">ชื่อไฟล์: add_one/Cargo.toml</span>

```toml
{{#include ../listings/ch14-more-about-cargo/no-listing-03-workspace-with-external-dependency/add/add_one/Cargo.toml:6:7}}
```

ตอนนี้เราสามารถเพิ่ม `use rand;` ลงในไฟล์ _add_one/src/lib.rs_ ได้แล้ว และการบิลด์พื้นที่ทำงานทั้งหมดโดยการรัน `cargo build` ในไดเรกทอรี _add_ จะดึงและคอมไพล์ crate `rand` เข้ามา เราจะได้คำเตือนหนึ่งคำเตือนเพราะเรายังไม่ได้อ้างอิงถึง `rand` ที่เรานำเข้ามาใน scope:

<!-- manual-regeneration
cd listings/ch14-more-about-cargo/no-listing-03-workspace-with-external-dependency/add
cargo build
copy output below; the output updating script doesn't handle subdirectories in paths properly
-->

```console
$ cargo build
    Updating crates.io index
  Downloaded rand v0.10.1
   --snip--
   Compiling rand v0.10.1
   Compiling add_one v0.1.0 (file:///projects/add/add_one)
warning: unused import: `rand`
  --> add_one/src/lib.rs:1:5
   |
1 | use rand;
   |     ^^^^
   |
   = note: `#[warn(unused_imports)]` (part of `#[warn(unused)]`) on by default

warning: `add_one` (lib) generated 1 warning (run `cargo fix --lib -p add_one` to apply 1 suggestion)
   Compiling adder v0.1.0 (file:///projects/add/adder)
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.95s
```

ตอนนี้ไฟล์ _Cargo.lock_ ที่ระดับบนสุดจะบรรจุข้อมูลเกี่ยวกับการพึ่งพาของ `add_one` บน `rand` อย่างไรก็ตาม แม้ว่า `rand` จะถูกใช้ที่ใดที่หนึ่งในพื้นที่ทำงาน แต่เราก็ไม่สามารถใช้มันใน crate อื่น ๆ ในพื้นที่ทำงานได้ เว้นแต่เราจะเพิ่ม `rand` ลงในไฟล์ _Cargo.toml_ ของ crate เหล่านั้นด้วย ตัวอย่างเช่น หากเราเพิ่ม `use rand;` ลงในไฟล์ _adder/src/main.rs_ สำหรับแพ็กเกจ `adder` เราจะได้ข้อผิดพลาด:

<!-- manual-regeneration
cd listings/ch14-more-about-cargo/output-only-03-use-rand/add
cargo build
copy output below; the output updating script doesn't handle subdirectories in paths properly
-->

```console
$ cargo build
  --snip--
   Compiling adder v0.1.0 (file:///projects/add/adder)
error[E0432]: unresolved import `rand`
  --> adder/src/main.rs:2:5
   |
2 | use rand;
   |     ^^^^ no external crate `rand`
```

เพื่อแก้ไขปัญหานี้ ให้แก้ไขไฟล์ _Cargo.toml_ สำหรับแพ็กเกจ `adder` และระบุว่า `rand` เป็นทรัพยากรพึ่งพาสำหรับมันด้วย การบิลด์แพ็กเกจ `adder` จะเพิ่ม `rand` ลงในรายการทรัพยากรพึ่งพาสำหรับ `adder` ใน _Cargo.lock_ แต่จะไม่มีการดาวน์โหลดสำเนาของ `rand` เพิ่มเติม Cargo จะช่วยให้มั่นใจว่าทุก crate ในทุกแพ็กเกจของพื้นที่ทำงานที่ใช้แพ็กเกจ `rand` จะใช้เวอร์ชันเดียวกันตราบใดที่พวกมันระบุเวอร์ชันของ `rand` ที่เข้ากันได้ ซึ่งช่วยประหยัดพื้นที่และมั่นใจได้ว่า crate ในพื้นที่ทำงานจะเข้ากันได้ซึ่งกันและกัน

หาก crate ในพื้นที่ทำงานระบุเวอร์ชันของทรัพยากรพึ่งพาเดียวกันที่ไม่เข้ากันได้ Cargo จะแก้ปัญหาของแต่ละ crate แต่จะยังคงพยายามแก้ปัญหาให้ใช้เวอร์ชันน้อยที่สุดเท่าที่จะเป็นไปได้

### การเพิ่มการทดสอบลงในพื้นที่ทำงาน

สำหรับการปรับปรุงเพิ่มเติม มาเพิ่มการทดสอบฟังก์ชัน `add_one::add_one` ภายใน crate `add_one`:

<span class="filename">ชื่อไฟล์: add_one/src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch14-more-about-cargo/no-listing-04-workspace-with-tests/add/add_one/src/lib.rs}}
```

ตอนนี้ ให้รัน `cargo test` ในไดเรกทอรี _add_ ที่ระดับบนสุด การรัน `cargo test` ในพื้นที่ทำงานที่จัดโครงสร้างเหมือนอันนี้ จะรันการทดสอบสำหรับทุก crate ในพื้นที่ทำงาน:

<!-- manual-regeneration
cd listings/ch14-more-about-cargo/no-listing-04-workspace-with-tests/add
cargo test
copy output below; the output updating script doesn't handle subdirectories in
paths properly
-->

```console
$ cargo test
   Compiling add_one v0.1.0 (file:///projects/add/add_one)
   Compiling adder v0.1.0 (file:///projects/add/adder)
    Finished `test` profile [unoptimized + debuginfo] target(s) in 0.20s
      Running unittests src/lib.rs (target/debug/deps/add_one-93c49ee75dc46543)

running 1 test
test tests::it_works ... ok

test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s

      Running unittests src/main.rs (target/debug/deps/adder-3a47283c568d2b6a)

running 0 tests

test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s

   Doc-tests add_one

running 0 tests

test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s
```

ส่วนแรกของผลลัพธ์แสดงว่าการทดสอบ `it_works` ใน crate `add_one` ผ่านการทดสอบ ส่วนถัดไปแสดงว่าไม่พบการทดสอบใด ๆ ใน crate `adder` และส่วนสุดท้ายแสดงว่าไม่พบการทดสอบเอกสารประกอบใด ๆ ใน crate `add_one`

เรายังสามารถรันการทดสอบสำหรับ crate เฉพาะเจาะจงในพื้นที่ทำงานจากไดเรกทอรีระดับบนสุดได้โดยใช้แฟล็ก `-p` และระบุชื่อของ crate ที่เราต้องการทดสอบ:

<!-- manual-regeneration
cd listings/ch14-more-about-cargo/no-listing-04-workspace-with-tests/add
cargo test -p add_one
copy output below; the output updating script doesn't handle subdirectories in paths properly
-->

```console
$ cargo test -p add_one
    Finished `test` profile [unoptimized + debuginfo] target(s) in 0.00s
      Running unittests src/lib.rs (target/debug/deps/add_one-93c49ee75dc46543)

running 1 test
test tests::it_works ... ok

test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s

   Doc-tests add_one

running 0 tests

test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s
```

ผลลัพธ์นี้แสดงว่า `cargo test` รันเฉพาะการทดสอบสำหรับ crate `add_one` เท่านั้น และไม่ได้รันการทดสอบของ crate `adder`

หากคุณเผยแพร่ crate ในพื้นที่ทำงานไปยัง [crates.io](https://crates.io/)<!-- ignore --> แต่ละ crate ในพื้นที่ทำงานจะต้องถูกเผยแพร่แยกกัน เช่นเดียวกับ `cargo test` เราสามารถเผยแพร่ crate เฉพาะเจาะจงในพื้นที่ทำงานของเราได้โดยใช้แฟล็ก `-p` และระบุชื่อของ crate ที่เราต้องการเผยแพร่

สำหรับการฝึกฝนเพิ่มเติม ลองเพิ่ม crate `add_two` ลงในพื้นที่ทำงานนี้ในลักษณะที่คล้ายกับ crate `add_one` ดูสิ!

เมื่อโปรเจกต์ของคุณเติบโตขึ้น ให้พิจารณาใช้พื้นที่ทำงาน: มันช่วยให้คุณทำงานกับส่วนประกอบที่เล็กลงและเข้าใจได้ง่ายกว่าโค้ดก้อนใหญ่เพียงก้อนเดียว ยิ่งไปกว่านั้น การเก็บ crate ไว้ในพื้นที่ทำงานยังช่วยให้การประสานงานระหว่าง crate ต่าง ๆ ง่ายขึ้นหากพวกมันมักถูกเปลี่ยนแปลงไปพร้อม ๆ กัน
