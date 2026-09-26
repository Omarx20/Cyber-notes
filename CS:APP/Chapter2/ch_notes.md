# CS:APP — Chapter 2 Notes
## Representing and Manipulating Information

---

# 1. الصورة الكبيرة

Chapter 2 يدور حول سؤال أساسي:

> كيف تمثل أجهزة الكمبيوتر المعلومات داخل **bits / bytes**، وكيف تتغير طريقة تفسير هذه الـbits حسب نوع البيانات والعمليات التي نجريها عليها؟

```text
Data
  ↓
Bits
  ↓
Bytes
  ↓
Representation
  ↓
Interpretation
  ↓
Operations
```

نفس مجموعة الـbits يمكن أن تمثل قيمًا مختلفة حسب الطريقة التي نفسرها بها.

---

# 2. Bits وBytes

## Bit

أصغر وحدة معلومات:

```text
0 أو 1
```

## Byte

```text
1 byte = 8 bits
```

لذلك:

```text
1 byte  → 2^8  = 256 patterns
2 bytes → 2^16 = 65,536 patterns
4 bytes → 2^32 patterns
8 bytes → 2^64 patterns
```

> عدد الـpatterns يعتمد على عدد الـbits، وليس على نوع البيانات.

---

# 3. Hexadecimal

كل Hex digit يمثل:

```text
4 bits
```

مثال:

```text
A = 1010
F = 1111
```

لذلك:

```text
1 byte = 8 bits = 2 hexadecimal digits
```

مثال:

```text
0x39 = 0011 1001
```

Hex مجرد **طريقة عرض للـbits**؛ الكمبيوتر نفسه يخزن bits، وليس "Hex".

---

# 4. Memory عبارة عن Bytes

يمكن تصور الذاكرة كمجموعة bytes، وكل byte له address:

```text
Address       Byte
0x1000   →    3A
0x1001   →    7F
0x1002   →    00
0x1003   →    FF
```

عندما نخزن قيمة متعددة الـbytes، يظهر سؤال:

> بأي ترتيب توضع هذه الـbytes في الذاكرة؟

وهنا يأتي **Endianness**.

---

# 5. Byte Order / Endianness

## Little Endian

في Little Endian، الـ**least significant byte** يوجد في العنوان الأقل.

مثال:

```text
int x = 0x12345678;
```

القيمة منطقيًا:

```text
12 34 56 78
```

لكن في الذاكرة على جهاز Little Endian:

```text
Address
0x00 → 78
0x01 → 56
0x02 → 34
0x03 → 12
```

أي:

```text
78 56 34 12
```

## Big Endian

```text
12 34 56 78
```

تظهر الـmost significant byte في العنوان الأقل.

## Mental Model

لا تفكر أن الكمبيوتر "يعكس الرقم". فكر:

> **الرقم قيمة واحدة، وعند وضعه في الذاكرة نحتاج لترتيب الـbytes.**

---

# 6. مثال `show_bytes` الذي استخدمناه

```text
int x = 12345
```

بالـhex:

```text
12345 = 0x00003039
```

وفي Little Endian تظهر الـbytes:

```text
39 30 00 00
```

لذلك عندما استخدمنا `show_bytes` ظهر:

```text
39 30 00 00
```

وليس:

```text
00 00 30 39
```

لأن الـleast significant byte يأتي أولًا.

---

# 7. Binary representation

كل bit يمثل power of 2.

مثال:

```text
00000101
```

يساوي:

```text
2^2 + 2^0
= 4 + 1
= 5
```

فكل رقم يمكن تفكيكه إلى powers of 2 حسب الـ1 bits.

---

# 8. Signed vs Unsigned

نفس الـbit pattern يمكن تفسيره كـ:

```text
Unsigned
```

أو:

```text
Signed
```

## Unsigned

لـ `w` bits:

```text
0 → 2^w - 1
```

مثال 8-bit:

```text
0 → 255
```

## Signed

في C، التمثيل المعتاد للأعداد signed هو **two's complement**.

لـ `w` bits:

```text
-2^(w-1) → 2^(w-1)-1
```

مثال 8-bit:

```text
-128 → 127
```

---

# 9. Two's Complement

مثال:

```text
00000101
```

يمثل:

```text
+5
```

أما:

```text
11111011
```

فيمثل:

```text
-5
```

طريقة الحصول على `-5` من `+5`:

```text
+5
00000101

invert
11111010

+1
11111011
```

> في two's complement لا تتعامل مع الـsign bit كأنه جزء منفصل تمامًا عن التمثيل؛ افهم قيمة الـbit pattern ككل.

---

# 10. Sign Extension

عند توسيع signed value إلى عدد bits أكبر، نحافظ على الإشارة بتكرار الـsign bit.

مثال:

```text
8-bit:
11111011   = -5
```

إلى 16-bit:

```text
11111111 11111011
```

وليس:

```text
00000000 11111011
```

لأن الثانية تمثل قيمة موجبة.

---

# 11. Zero Extension

عند توسيع unsigned value، نضيف zeros من اليسار.

مثال:

```text
8-bit:
11111011   = 251 unsigned
```

إلى 16-bit:

```text
00000000 11111011
```

---

# 12. Unsigned / Signed Comparison

أحد أشهر مصادر الأخطاء في C:

> نفس الـbit pattern يمكن أن يعطي قيمة مختلفة حسب الـtype.

مثلاً:

```text
11111111
```

كـunsigned 8-bit:

```text
255
```

كـsigned 8-bit:

```text
-1
```

إذن **نوع المتغير يدخل في معنى القيمة** وفي نتيجة بعض العمليات والمقارنات.

---

# 13. Integer Casting

عند عمل cast اسأل دائمًا:

1. هل تغير عدد الـbits؟
2. هل تغير تفسير الـbits؟
3. هل حدث truncation؟
4. هل حدث sign extension أو zero extension؟

Casting ليس دائمًا مجرد "تغيير اسم النوع".

---

# 14. Truncation

عندما نحول قيمة إلى نوع أصغر، قد تضيع الـhigh-order bits.

مثال:

```text
int x = 0x12345678
```

إذا أخذنا 8 bits فقط:

```text
0x78
```

أي:

```text
0x12345678
       ↓
     0x78
```

> الأجزاء العليا تم حذفها.

---

# 15. Integer Overflow

إذا كانت القيمة أكبر من المجال الذي يمكن تمثيله، يحدث overflow بحسب نوع العملية.

مثال unsigned 8-bit:

```text
255 + 1
```

```text
256 = 1 00000000
```

وبوجود 8 bits فقط يبقى:

```text
00000000
```

أي:

```text
0
```

---

# 16. Modular Arithmetic — Unsigned

يمكن فهم unsigned arithmetic على أنه modulo `2^w`.

مثال 8-bit:

```text
255 + 1
= 256
mod 256
= 0
```

ومثال آخر:

```text
254 + 3
= 257
mod 256
= 1
```

---

# 17. Signed Overflow

لا تتعامل مع signed overflow على أنه modulo تلقائي كما في unsigned.

الأفضل:

> راجع نوع العملية وقواعد C؛ لا تفترض أن كل signed overflow يعمل wrap-around.

هذه نقطة مهمة جدًا في C وخصوصًا عند كتابة code آمن.

---

# 18. Bitwise Operators

أهم العمليات:

```c
&
|
^
~
```

---

## 18.1 AND `&`

```text
1 & 1 = 1
1 & 0 = 0
0 & 1 = 0
0 & 0 = 0
```

مثال:

```text
10110110
&
00001111
----------
00000110
```

يمكن استخدامه لعزل bits معينة.

---

## 18.2 OR `|`

إذا كان أي bit يساوي `1` فالنتيجة تكون `1`.

```text
10010000
|
00000101
----------
10010101
```

مفيد لتشغيل bits معينة.

---

## 18.3 XOR `^`

يكون 1 عندما يختلف الـbitان:

```text
0 ^ 0 = 0
1 ^ 1 = 0
0 ^ 1 = 1
1 ^ 0 = 1
```

خواص مهمة:

```text
x ^ 0 = x
x ^ x = 0
```

---

## 18.4 NOT `~`

يقلب كل الـbits:

```text
0 → 1
1 → 0
```

مثال:

```text
~00001111
=11110000
```

لكن يجب الانتباه إلى **نوع الـoperand والـwidth** في C.

---

# 19. Masks

الـmask هو pattern من الـbits نستخدمه لاستخراج أو تغيير bits معينة.

مثال:

```text
mask = 00001111
```

لعزل أقل 4 bits:

```c
x & 0x0F
```

---

# 20. Extracting a Bit

للحصول على bit رقم `n`:

```c
(x >> n) & 1
```

الفكرة:

```text
x
 ↓
shift
 ↓
الـbit المطلوب ينتقل إلى position 0
 ↓
& 1
 ↓
0 أو 1
```

---

# 21. Setting a Bit

لتشغيل bit رقم `n`:

```c
x | (1U << n)
```

مثلاً bit رقم 3:

```text
1 << 3
=
00001000
```

---

# 22. Clearing a Bit

```c
x & ~(1U << n)
```

---

# 23. Toggling a Bit

```c
x ^ (1U << n)
```

إذا كان الـbit:

```text
0 → 1
1 → 0
```

---

# 24. Logical vs Bitwise

لا تخلط:

```c
&&
||
!
```

مع:

```c
&
|
~
^
```

الأولى **Logical**، والثانية **Bitwise**.

مثلاً:

```c
x && y
```

يعمل logical operation.

بينما:

```c
x & y
```

يعمل bit-by-bit.

---

# 25. Shift Operators

## Left Shift

```c
x << n
```

يحرك الـbits إلى اليسار.

مع بعض القيم، وبدون مشاكل overflow/undefined behavior، يمكن أن يشبه الضرب في:

```text
2^n
```

لكن لا تحفظها كقاعدة مطلقة لكل الحالات.

---

## Right Shift

```c
x >> n
```

يحرك الـbits إلى اليمين.

يجب التمييز بين:

```text
logical right shift
```

و:

```text
arithmetic right shift
```

---

# 26. Logical Right Shift

الـbits الجديدة من اليسار تكون `0`.

مثال:

```text
10110000 >> 2
```

```text
00101100
```

---

# 27. Arithmetic Right Shift

مع signed values، يتم عادةً الحفاظ على sign extension.

مثال تمثيلي:

```text
10110000 >> 2
```

قد يصبح:

```text
11101100
```

بحسب النوع وقواعد العملية.

الفكرة:

> نحافظ على الإشارة أثناء التحريك لليمين.

---

# 28. Shift Amount

لا تفترض أن أي shift amount صالح أو أن النتيجة دائمًا كما تتوقع.

قبل:

```c
x << n
x >> n
```

اسأل عن:

```text
نوع x
نوع n
width
قواعد C الخاصة بالshift
```

---

# 29. Pointers

الـpointer يحتوي على **عنوان في الذاكرة**.

مثال:

```c
int x = 10;
int *p = &x;
```

الصورة:

```text
x
│
├── value = 10
└── address = 0x...

p
│
└── stores address of x
```

إذن:

```text
x  → value
&x → address of x
p  → address stored in p
*p → value at that address
```

---

# 30. `&` و`*` في C

## `&`

```c
&x
```

تعني **address of x**.

لكن:

```c
a & b
```

تعني **bitwise AND**.

## `*`

```c
int *p;
```

تعني أن `p` pointer to `int`.

أما:

```c
*p
```

فتعني dereference: اقرأ القيمة الموجودة في العنوان الذي يشير إليه `p`.

---

# 31. Pointer Arithmetic

إذا كان:

```c
int *p;
```

فإن:

```c
p + 1
```

لا يعني بالضرورة `+1 byte`.

بل ينتقل إلى العنصر التالي من نوع `int`.

إذا كان `int` حجمه 4 bytes، فالعنوان يتحرك 4 bytes.

Mental model:

```text
pointer arithmetic = movement by elements of the pointed-to type
```

---

# 32. Dereferencing

مثال:

```c
int x = 42;
int *p = &x;
```

فإن:

```c
*p
```

تعني:

> اذهب إلى memory address الموجود في `p` واقرأ القيمة هناك.

النتيجة:

```text
42
```

---

# 33. Arrays وPointers

مثال:

```c
int a[4] = {10, 20, 30, 40};
```

في expressions معينة، اسم الـarray يرتبط بعنوان أول عنصر:

```text
a ≈ &a[0]
```

و:

```c
a[2]
```

مرتبطة بفكرة:

```c
*(a + 2)
```

لكن:

> الـarray والـpointer ليسا نفس النوع، رغم العلاقة القوية بينهما.

---

# 34. Pointer Mental Model

```text
Memory
──────────────────────────────
Address       Content
0x1000       2A
0x1001       00
0x1002       00
0x1003       00
──────────────────────────────

p = 0x1000

*p
↓
2A
```

فكر دائمًا:

```text
pointer    → address
*pointer   → contents at that address
```

---

# 35. Aliasing

إذا كان:

```c
int x = 10;
int *p = &x;
int *q = &x;
```

فإن:

```text
p ──┐
    ├──→ x
q ──┘
```

أي يمكن لأكثر من pointer أن تشير إلى نفس memory location.

---

# 36. Structures وMemory Layout

مثال:

```c
struct S {
    int a;
    int b;
};
```

الـstruct لها representation في الذاكرة، لكن قد يوجد **padding/alignment**.

لذلك لا تفترض دائمًا أن:

```text
sizeof(struct S)
```

يساوي فقط مجموع أحجام الأعضاء بدون أي زيادة.

---

# 37. Characters

في C، الـcharacter يُمثل رقميًا حسب encoding المستخدم.

مثلاً:

```c
char c = 'A';
```

يمكن التعامل معه عدديًا.

لذلك عند debugging يجب التمييز بين:

```text
character
numeric value
bit pattern
```

---

# 38. C Strings

الـC string ليست type مستقلًا مثل بعض اللغات؛ هي sequence من characters تنتهي عادةً بـ:

```text
'\0'
```

مثال:

```text
"ABC"
```

يمكن تمثيلها في الذاكرة تقريبًا:

```text
41 42 43 00
```

أي أن الـnull terminator جزء أساسي من تمثيل C string.

---

# 39. Floating Point

الـfloating-point representation مختلفة عن integer representation.

التمثيل يتعامل مع أجزاء مثل:

```text
sign
exponent
fraction
```

لذلك لا تفترض أن floating point يعمل بقواعد integer نفسها.

---

# 40. Floating-Point Precision

بعض القيم العشرية لا يمكن تمثيلها بدقة في binary floating point.

لهذا قد ترى:

```text
0.1 + 0.2
```

ينتج قيمة قريبة من `0.3` ولكن ليست مطابقة لها bit-for-bit في بعض السياقات.

مثال شائع عند الطباعة:

```text
0.30000000000000004
```

الفكرة الأساسية:

> الـrepresentation نفسها تقريبية لبعض القيم، وليست المشكلة في الحساب الرياضي نفسه.

---

# 41. Integer vs Floating Point

```text
Integer
→ exact within representable range

Floating point
→ huge range + fractional values
→ بعض القيم approximate
```

لذلك المقارنة والحساب تحتاجان understanding للـrepresentation.

---

# 42. Byte-Level Thinking

من أهم مهارات Chapter 2:

بدل أن تقول:

> "المتغير قيمته 12345."

اسأل:

```text
ما النوع؟
كم byte؟
ما الـbit pattern؟
ما الـhex representation؟
كيف يخزن في الذاكرة؟
ما الـendianness؟
كيف سيفسره type آخر؟
```

مثال:

```text
int x = 12345

Value:
12345

Hex:
0x00003039

Little-endian bytes:
39 30 00 00
```

---

# 43. Same Bits, Different Meaning

نفس memory bytes قد تحمل معنى مختلف حسب نوع الـinterpretation.

إذن:

```text
bit pattern ≠ value by itself
```

بل:

```text
bit pattern + type/interpretation = value
```

---

# 44. `show_bytes` Mental Model

الفكرة الأساسية:

```c
void show_bytes(unsigned char *start, size_t len)
{
    for (size_t i = 0; i < len; i++)
        printf("%.2x ", start[i]);
}
```

إذا كانت:

```text
int x = 12345;
```

الدالة لا تطبع `12345` كقيمة عشرية، بل تقرأ **raw bytes من object representation**.

مثال على Little Endian:

```text
39 30 00 00
```

---

# 45. `unsigned char` وRaw Bytes

عند دراسة object representation، الوصول byte-by-byte باستخدام `unsigned char *` مفيد جدًا لفهم الذاكرة والـrepresentation.

هذا هو النوع من التفكير الذي استخدمناه مع `show_bytes`.

---

# 46. Type Punning / Reinterpreting Bytes

يمكن أحيانًا النظر إلى نفس bytes بتفسير مختلف، لكن يجب الحذر من:

```text
strict aliasing
alignment
object representation
undefined behavior
```

الفكرة المهمة:

> نفس الـmemory لا تعني نفس القيمة؛ الـtype وطريقة الوصول مهمان.

---

# 47. Host Order vs Network Order

في networking قد يكون الـhost:

```text
Little Endian
```

بينما **network byte order** هو:

```text
Big Endian
```

لذلك تظهر الدوال:

```c
htons()
ntohs()
htonl()
ntohl()
```

Mental model:

```text
Host order
    ↕
Network order
```

والفكرة ليست تغيير القيمة المنطقية، بل ترتيب الـbytes بالشكل المطلوب عند التعامل مع network protocols.

---

# 48. CS:APP ↔ Networking

هذا الربط مهم جدًا لمسارك.

في networking عندك:

```text
IPv4 address
Port
Sequence number
TCP fields
```

وفي النهاية كلها bits وbytes.

إذن:

```text
CS:APP
bits / bytes / memory / endianness
            ↓
Networking
protocol headers / fields / packets
```

عندما تقرأ TCP/IP packet، أنت عمليًا تفسر مجموعة bytes وفق format محدد.

---

# 49. C Sockets Connection

عند استخدام:

```c
struct sockaddr_in
struct sockaddr_in6
```

ودوال:

```c
htons()
ntohs()
htonl()
ntohl()
```

فأنت تطبق أفكار Chapter 2 مباشرة:

```text
integers
+
byte order
+
memory representation
```

---

# 50. Protocol Parsing

أي network protocol يمكن التفكير فيه كـbytes منظمة حسب format.

مثال protocol خاص بك:

```text
┌────────┬────────┬────────────┐
│ Length │ Type   │ Payload    │
│ 4 B    │ 1 B    │ variable   │
└────────┴────────┴────────────┘
```

الـparser يحتاج أن يعرف:

```text
where the field starts
how many bytes it occupies
how bytes are ordered
how to interpret the integer
```

وهنا تظهر:

```text
endianness
shifts
masks
integer widths
```

---

# 51. Security Relevance

Chapter 2 ليس Chapter Security، لكنه أساس لأشياء كثيرة في Security.

## Memory Bugs

فهم:

```text
bytes
pointers
memory layout
```

أساسي لفهم:

```text
buffer overflow
memory corruption
use-after-free
out-of-bounds access
```

## Integer Bugs

أخطاء مثل:

```text
overflow
truncation
signed/unsigned conversion
```

يمكن أن تسبب مشاكل عند استخدامها في:

```text
allocation sizes
length checks
array indexes
protocol parsing
```

---

# 52. Debugging Checklist

عندما ترى قيمة غير متوقعة، لا تكتفِ بـ"الرقم غلط".

راجع:

```text
[ ] Type
[ ] Width / number of bits
[ ] Signed أم unsigned
[ ] Hex representation
[ ] Raw bytes
[ ] Endianness
[ ] Casts
[ ] Truncation
[ ] Sign extension / zero extension
[ ] Shifts
[ ] Masks
```

---

# 53. Common Mistakes

## 53.1 Hex is not storage

Hex مجرد طريقة عرض.

```text
Computer stores → bits
Human displays → binary / hex / decimal
```

## 53.2 Pointer is not the object

```text
p  → address
*p → value at address
```

## 53.3 `&` is context-dependent

```c
&x       // address
x & y    // bitwise AND
```

## 53.4 `&&` is not `&`

```c
x && y   // logical
x & y    // bitwise
```

## 53.5 Do not blindly treat shifts as arithmetic

```text
x << 1 ≈ x*2
x >> 1 ≈ x/2
```

ليست قاعدة عامة بلا شروط.

## 53.6 Do not assume signed overflow wraps

افهم قواعد النوع والعملية بدل حفظ قاعدة واحدة.

---

# 54. Practical Mini Labs

## Lab 1 — `show_bytes`

اكتب function تطبع bytes لأي object:

```c
void show_bytes(unsigned char *start, size_t len);
```

وجرب:

```text
int
short
long
float
double
```

ثم قارن:

```text
value
hex
raw bytes
```

---

## Lab 2 — Bit Extraction

اكتب:

```c
int get_bit(unsigned int x, int n);
```

بحيث ترجع `0` أو `1` باستخدام bitwise operations.

---

## Lab 3 — Bit Manipulation

اكتب:

```text
set_bit()
clear_bit()
toggle_bit()
```

ثم اختبرها على values مختلفة.

---

## Lab 4 — Endianness Detector

اكتب برنامج C يحدد هل الجهاز:

```text
Little Endian
أم
Big Endian
```

فكرة بسيطة:

```c
int x = 1;
```

ثم افحص أول byte في memory.

---

## Lab 5 — Network Header Parser

صمم byte array يدويًا:

```text
[ length ][ type ][ flags ][ payload ]
```

ثم اكتب parser يستخرج:

```text
length
type
flags
payload
```

واستخدم:

```text
shifts
masks
endianness
```

---

# 55. Review Questions

## Basic

1. لماذا 1 byte = 8 bits؟
2. كم pattern يمكن تمثيله بـ4 bytes؟
3. ما الفرق بين value وrepresentation؟
4. ما الفرق بين signed وunsigned؟
5. ما هو two's complement؟

## Endianness

6. كيف يُخزن `0x12345678` في Little Endian؟
7. لماذا ظهر `39 30 00 00` عندما فحصنا `12345`؟

## Bitwise

8. ماذا يفعل `x & 1`؟
9. كيف تختبر bit رقم `n`؟
10. كيف تشغل/تطفي/تعكس bit؟

## Pointers

11. ما الفرق بين `p` و`*p` و`&x`؟
12. لماذا `p + 1` قد يتحرك أكثر من byte واحد؟

## C / Debugging

13. ماذا يحدث عند تحويل integer إلى نوع أصغر؟
14. لماذا signed/unsigned mixing قد يكون خطيرًا؟
15. لماذا لا يمكن افتراض أن signed overflow مجرد modulo؟

## Networking

16. لماذا نحتاج `htons()` و`ntohs()`؟
17. كيف يرتبط packet parsing بالـmasking والـshifts؟
18. لماذا endianness مهم عند قراءة protocol headers؟

---

# 56. Final Mental Models

### Model 1 — Everything becomes bits

```text
Data → bits
```

### Model 2 — Memory is bytes with addresses

```text
Memory → byte array + addresses
```

### Model 3 — Value ≠ Representation

```text
same value
↓
multiple human representations
```

### Model 4 — Pointer

```text
pointer → address
dereference → contents at address
```

### Model 5 — Bit manipulation

```text
mask + shift + AND/OR/XOR
→ extract/change bits
```

### Model 6 — Network protocols

```text
Network packet
→ bytes
→ fields
→ interpretation
```

---

# 57. Final Chapter 2 Summary

إذا أردت مراجعة Chapter 2 بسرعة، استخدم الخريطة التالية:

```text
                    Information
                         │
                         ↓
                       Bits
                         │
                         ↓
                       Bytes
                         │
             ┌───────────┴───────────┐
             ↓                       ↓
       Representation           Interpretation
             │                       │
       Endianness               Signed/Unsigned
             │                       │
             ↓                       ↓
       Memory layout           Two's complement
             │                       │
             └───────────┬───────────┘
                         ↓
                  Bit Manipulation
                         │
                  ┌──────┼──────┐
                  ↓      ↓      ↓
                  &      |      ^
                  │      │      │
                  └──┬───┴───┬──┘
                     ↓       ↓
                   Masks    Shifts
                     │       │
                     └──┬────┘
                        ↓
                    Pointers
                        │
                        ↓
                    C Memory
                        │
                        ↓
                 Network Parsing
```

## Chapter 2 في جملة واحدة

> **الكمبيوتر يخزن bits وbytes، والـtype وطريقة تفسير هذه الـbytes هي التي تحدد معناها؛ وفهم الذاكرة والـendianness والـinteger representations والـbitwise operations والـpointers هو الأساس لفهم C والـsystems programming وقراءة البيانات على مستوى الـbytes والـnetwork protocols.**

---
