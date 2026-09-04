## คำอธิบายโค้ด (Comments)

โปรแกรมเมอร์ทุกคนต่างมุ่งมั่นที่จะทำให้โค้ดของตนเข้าใจง่าย แต่บางครั้งการเขียนอธิบายเพิ่มเติมก็มีความจำเป็น ในกรณีเหล่านี้ โปรแกรมเมอร์จะใส่ *คำอธิบายโค้ด (comments)* ลงในซอร์สโค้ดของตน ซึ่งตัวคอมไพเลอร์จะเพิกเฉย แต่ผู้ที่เข้ามาอ่านซอร์สโค้ดในภายหลังจะพบว่ามันมีประโยชน์

นี่คือตัวอย่างคำอธิบายโค้ดแบบง่ายๆ:

```rust
// hello, world
```

ในภาษา Rust รูปแบบการเขียนอธิบายโค้ดตามหลักปฏิบัติจะเริ่มต้นด้วยเครื่องหมายทับสองตัว (double slashes) และคำอธิบายจะดำเนินต่อไปจนกระทั่งสิ้นสุดบรรทัดนั้น สำหรับคำอธิบายที่มีความยาวมากกว่าหนึ่งบรรทัด คุณจำเป็นต้องใส่เครื่องหมาย `//` ไว้ที่หน้าบรรทัดทุกๆ แถว ดังนี้:

```rust
// So we're doing something complicated here, long enough that we need
// multiple lines of comments to do it! Whew! Hopefully, this comment will
// explain what's going on.
```

คำอธิบายโค้ดสามารถเขียนระบุไว้ที่ด้านท้ายบรรทัดของแถวโค้ดได้เช่นกัน:

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-24-comments-end-of-line/src/main.rs}}
```

แต่โดยส่วนใหญ่แล้ว คุณจะพบเห็นการเรียกใช้งานในรูปแบบนี้มากกว่า นั่นคือการเขียนระบุคำอธิบายโค้ดแยกต่างหากไว้ที่บรรทัดด้านบนของโค้ดที่ต้องการอธิบาย:

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-25-comments-above-line/src/main.rs}}
```

นอกจากนี้ Rust ยังมีคำอธิบายโค้ดอีกประเภทหนึ่งคือ คำอธิบายสำหรับทำคู่มือเอกสาร (documentation comments) ซึ่งเราจะได้ร่วมพูดคุยกันในบทที่ 14 หัวข้อ [“การเผยแพร่เครตไปยัง Crates.io”][publishing]<!-- ignore -->

[publishing]: ch14-02-publishing-to-crates-io.html
