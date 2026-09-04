<!-- Old headings. Do not remove or links may break. -->

<a id="streams"></a>

## Streams: Futures ในรูปแบบลำดับ

ลองนึกถึงตอนที่เราใช้ตัวรับ (receiver) สำหรับช่องทาง async channel ของเราก่อนหน้านี้ในบทนี้ ในส่วน [“Message Passing”][17-02-messages]<!-- ignore --> เมธอด async `recv` จะสร้างลำดับของข้อมูลออกมาตามช่วงเวลา สิ่งนี้เป็นตัวอย่างของรูปแบบที่ทั่วไปยิ่งกว่าที่เรียกว่า _stream_ แนวคิดหลายอย่างสามารถแทนด้วย stream ได้อย่างเป็นธรรมชาติ เช่น ข้อมูลที่ทยอยเข้ามาในคิว (queue), ชิ้นส่วนของข้อมูลที่ถูกดึงทีละนิดจากระบบไฟล์เมื่อชุดข้อมูลทั้งหมดมีขนาดใหญ่เกินกว่าหน่วยความจำของคอมพิวเตอร์ หรือข้อมูลที่ทยอยมาถึงผ่านเครือข่ายตามเวลา เนื่องจาก stream คือ future รูปแบบหนึ่ง เราจึงสามารถใช้ stream ร่วมกับ future รูปแบบอื่นๆ และรวมเข้าด้วยกันในวิธีที่น่าสนใจได้ ตัวอย่างเช่น เราสามารถจัดกลุ่มเหตุการณ์ (batch events) เพื่อหลีกเลี่ยงการส่งคำขอทางเครือข่ายถี่เกินไป ตั้งเวลาหมดเวลา (timeout) ให้กับลำดับการทำงานที่ใช้เวลานาน หรือหน่วงการทำงานของเหตุการณ์บนส่วนประสานงานผู้ใช้ (user interface events) เพื่อหลีกเลี่ยงการทำงานที่ไม่จำเป็น

เราเคยเห็นลำดับของข้อมูลมาแล้วในบทที่ 13 ตอนที่เราดูเกี่ยวกับ Iterator trait ในส่วน [“The Iterator Trait and the `next` Method”][iterator-trait]<!-- ignore --> แต่มีความแตกต่างสองประการระหว่าง iterator กับตัวรับ async channel ประการแรกคือเรื่องของเวลา: iterator เป็นแบบซิงโครนัส (synchronous) ในขณะที่ตัวรับ channel เป็นแบบอะซิงโครนัส (asynchronous) ประการที่สองคือ API เมื่อทำงานกับ `Iterator` โดยตรง เราจะเรียกเมธอด `next` ที่เป็นซิงโครนัส แต่สำหรับ `trpl::Receiver` stream โดยเฉพาะ เราจะเรียกเมธอด `recv` ที่เป็นอะซิงโครนัสแทน นอกเหนือจากนี้แล้ว API เหล่านี้ให้ความรู้สึกคล้ายกันมาก และความคล้ายคลึงกันนั้นก็ไม่ใช่เรื่องบังเอิญ stream เปรียบเสมือนรูปแบบอะซิงโครนัสของการวนซ้ำ (iteration) อย่างไรก็ตาม ในขณะที่ `trpl::Receiver` จะรอรับข้อความโดยเฉพาะ API ของ stream ทั่วไปนั้นกว้างกว่ามาก: มันจะมอบคุณค่าถัดไปในลักษณะเดียวกับที่ `Iterator` ทำ แต่ทำในแบบอะซิงโครนัส

ความคล้ายคลึงกันระหว่าง iterator กับ stream ใน Rust หมายความว่าเราสามารถสร้าง stream จาก iterator ใดๆ ก็ได้ เช่นเดียวกับ iterator เราสามารถทำงานกับ stream ได้โดยการเรียกเมธอด `next` แล้วใช้ `await` รับผลลัพธ์ ดังแสดงในโค้ดตัวอย่างที่ 17-21 ซึ่งจะยังไม่สามารถคอมไพล์ได้ในตอนนี้

<Listing number="17-21" caption="การสร้าง stream จาก iterator และพิมพ์ค่าออกมา" file-name="src/main.rs">

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch17-async-await/listing-17-21/src/main.rs:stream}}
```

</Listing>

เราเริ่มต้นด้วยอาเรย์ของตัวเลข ซึ่งเราแปลงเป็น iterator แล้วเรียกใช้ `map` เพื่อเพิ่มค่าทั้งหมดเป็นสองเท่า จากนั้นเราแปลง iterator ให้กลายเป็น stream โดยใช้ฟังก์ชัน `trpl::stream_from_iter` ถัดมาเราวนลูปผ่านรายการข้อมูลใน stream เมื่อข้อมูลมาถึงด้วยลูป `while let`

น่าเสียดายที่เมื่อเราพยายามรันโค้ด มันกลับคอมไพล์ไม่ผ่าน แต่รายงานว่าไม่มีเมธอด `next` ให้ใช้งานแทน:

<!-- manual-regeneration
cd listings/ch17-async-await/listing-17-21
cargo build
copy only the error output
-->

```text
error[E0599]: no method named `next` found for struct `tokio_stream::iter::Iter` in the current scope
  --> src/main.rs:10:40
   |
10 |         while let Some(value) = stream.next().await {
   |                                        ^^^^
   |
   = help: items from traits can only be used if the trait is in scope
help: the following traits which provide `next` are implemented but not in scope; perhaps you want to import one of them
   |
1  + use crate::trpl::StreamExt;
   |
1  + use futures_util::stream::stream::StreamExt;
   |
1  + use std::iter::Iterator;
   |
1  + use std::str::pattern::Searcher;
   |
help: there is a method `try_next` with a similar name
   |
10 |         while let Some(value) = stream.try_next().await {
   |                                        ~~~~~~~~
```

ตามที่ผลลัพธ์นี้อธิบายไว้ เหตุผลที่เกิดข้อผิดพลาดในการคอมไพล์คือ เราต้องนำ trait ที่ถูกต้องเข้ามาอยู่ในขอบเขต (scope) เพื่อให้สามารถใช้เมธอด `next` ได้ จากสิ่งที่เราพูดคุยกันมา คุณอาจคาดหวังว่า trait นั้นควรจะเป็น `Stream` แต่จริงๆ แล้วมันคือ `StreamExt` ย่อมาจาก _extension_ ซึ่ง `Ext` เป็นรูปแบบ (pattern) ที่ใช้กันทั่วไปในชุมชน Rust สำหรับการขยายความสามารถของ trait หนึ่งด้วยอีก trait หนึ่ง

`Stream` trait นิยามอินเทอร์เฟซระดับต่ำ (low-level interface) ซึ่งรวมเอาความสามารถของ `Iterator` และ `Future` trait เข้าด้วยกันอย่างมีประสิทธิภาพ ส่วน `StreamExt` จะมอบคุณสมบัติ API ระดับสูงกว่าอยู่บน `Stream` รวมถึงเมธอด `next` ตลอดจนเมธอดอรรถประโยชน์ (utility methods) อื่นๆ ที่คล้ายกับเมธอดที่มีใน `Iterator` trait ปัจจุบัน `Stream` และ `StreamExt` ยังไม่ได้เป็นส่วนหนึ่งของไลบรารีมาตรฐาน (standard library) ของ Rust แต่ crate ส่วนใหญ่ในระบบนิเวศจะใช้นิยามที่คล้ายกันนี้

การแก้ไขข้อผิดพลาดในการคอมไพล์ทำได้โดยการเพิ่มคำสั่ง `use` สำหรับ `trpl::StreamExt` ดังแสดงในโค้ดตัวอย่างที่ 17-22

<Listing number="17-22" caption="การใช้ iterator เป็นพื้นฐานในการสร้าง stream ได้สำเร็จ" file-name="src/main.rs">

```rust
{{#rustdoc_include ../listings/ch17-async-await/listing-17-22/src/main.rs:all}}
```

</Listing>

เมื่อนำชิ้นส่วนทั้งหมดมารวมกัน โค้ดนี้ก็ทำงานได้ตามที่เราต้องการ! ยิ่งไปกว่านั้น เมื่อตอนนี้เรามี `StreamExt` อยู่ในขอบเขตแล้ว เรายังสามารถใช้เมธอดอรรถประโยชน์ทั้งหมดของมันได้ เช่นเดียวกับ iterator

[17-02-messages]: ch17-02-concurrency-with-async.html#message-passing
[iterator-trait]: ch13-02-iterators.html#the-iterator-trait-and-the-next-method
