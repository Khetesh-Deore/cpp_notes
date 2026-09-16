# C++ String Input & Processing — Master Notes

> **Scope:** Input handling, parsing, splitting, extraction, conversion, and common `std::string` operations.  
> **Not covered:** String problem-solving algorithms/logic such as KMP, Z-algorithm, sliding window, etc.

---

## 1. Core String Setup

```cpp
#include <bits/stdc++.h>
using namespace std;

string s;
```

A C++ `string` is a sequence of characters.

```cpp
string s = "hello";
```

Character access:

```cpp
s[0]       // 'h'
s.at(0)    // 'h' (bounds-checked)
s.front()  // first character
s.back()   // last character
s.size()   // length
s.length() // length
```

---

# 2. Reading String Input

## One word / token

```cpp
string s;
cin >> s;
```

Input:

```text
hello world
```

Reads only:

```text
hello
```

`cin >>` stops at whitespace.

---

## Complete line

```cpp
string s;
getline(cin, s);
```

Input:

```text
hello world cpp
```

Reads the complete line.

---

## `cin` + `getline` problem

After:

```cpp
int n;
cin >> n;
```

the newline remains in the input buffer.

Use:

```cpp
cin.ignore(numeric_limits<streamsize>::max(), '\n');
getline(cin, s);
```

Header:

```cpp
#include <limits>
```

Usually `<bits/stdc++.h>` already includes it.

---

# 3. Reading Multiple Strings

## `n` words

```cpp
int n;
cin >> n;

vector<string> a(n);

for (int i = 0; i < n; i++) {
    cin >> a[i];
}
```

## `n` complete lines

```cpp
int n;
cin >> n;
cin.ignore(numeric_limits<streamsize>::max(), '\n');

vector<string> a(n);

for (int i = 0; i < n; i++) {
    getline(cin, a[i]);
}
```

## Unknown number of lines

```cpp
string line;

while (getline(cin, line)) {
    // process line
}
```

## Unknown number of whitespace-separated tokens

```cpp
string word;

while (cin >> word) {
    // process word
}
```

---

# 4. Character Traversal

```cpp
for (int i = 0; i < (int)s.size(); i++) {
    char ch = s[i];
}
```

Range-based loop:

```cpp
for (char ch : s) {
    // process ch
}
```

Modify characters:

```cpp
for (char &ch : s) {
    ch = tolower(ch);
}
```

---

# 5. Character Classification

Useful `<cctype>` functions:

```cpp
isdigit(ch)  // 0-9
isalpha(ch)  // A-Z / a-z
isalnum(ch)  // letter or digit
isspace(ch)  // space, tab, newline, etc.
islower(ch)  // lowercase
isupper(ch)  // uppercase
```

Conversion:

```cpp
tolower(ch)
toupper(ch)
```

Typical classification:

```cpp
for (char ch : s) {
    if (isalpha(ch)) {
        // letter
    }
    else if (isdigit(ch)) {
        // digit
    }
    else if (isspace(ch)) {
        // whitespace
    }
    else {
        // special character
    }
}
```

---

# 6. `find()` — Locate Characters / Substrings

```cpp
size_t pos = s.find(':');
```

Find substring:

```cpp
size_t pos = s.find("hello");
```

Find from a starting position:

```cpp
size_t pos = s.find(':', start);
```

Not found:

```cpp
if (s.find(':') == string::npos) {
    // not found
}
```

Important:

```cpp
string::npos
```

means "not found".

---

## Find last occurrence

```cpp
size_t pos = s.rfind(':');
```

---

# 7. `substr()` — Extract Part of a String

Syntax:

```cpp
s.substr(start, length);
```

Example:

```cpp
string s = "abcdef";

s.substr(0, 3); // "abc"
s.substr(2, 2); // "cd"
s.substr(3);    // "def"
```

If length is omitted, extracts until the end.

---

# 8. Universal `key:value` Parsing

Input:

```text
name1:25
```

```cpp
int pos = s.find(':');

string key = s.substr(0, pos);
string value = s.substr(pos + 1);
```

Result:

```text
key   = name1
value = 25
```

This same pattern works for:

```text
key:value
key=value
key-name
key/value
```

Just change the delimiter.

---

# 9. `stringstream`

Header:

```cpp
#include <sstream>
```

Convert a string into a stream:

```cpp
stringstream ss(s);
```

---

## Split by whitespace

Input:

```text
apple banana mango
```

```cpp
stringstream ss(s);
string token;

while (ss >> token) {
    cout << token << '\n';
}
```

Handles multiple spaces:

```text
apple    banana      mango
```

---

## Parse whitespace-separated values directly

```cpp
stringstream ss("John 25 50000");

string name;
int age;
int salary;

ss >> name >> age >> salary;
```

---

# 10. Split by a Specific Delimiter

Input:

```text
apple,banana,mango
```

```cpp
stringstream ss(s);
string token;

while (getline(ss, token, ',')) {
    cout << token << '\n';
}
```

Other delimiters:

```cpp
getline(ss, token, ':');
getline(ss, token, ';');
getline(ss, token, '|');
getline(ss, token, '/');
```

### Rule

```text
Whitespace-separated
    -> ss >> token

Specific delimiter
    -> getline(ss, token, delimiter)
```

---

# 11. Split into a Vector

```cpp
vector<string> parts;

stringstream ss(s);
string token;

while (getline(ss, token, ',')) {
    parts.push_back(token);
}
```

Now:

```cpp
parts[0]
parts[1]
parts[2]
...
```

For whitespace:

```cpp
stringstream ss(s);

while (ss >> token) {
    parts.push_back(token);
}
```

---

# 12. Nested Parsing

Input:

```text
John:20,Alice:30,Bob:40
```

Outer delimiter:

```text
,
```

Inner delimiter:

```text
:
```

Pattern:

```cpp
stringstream ss(s);
string record;

while (getline(ss, record, ',')) {

    size_t pos = record.find(':');

    string name = record.substr(0, pos);
    string value = record.substr(pos + 1);

}
```

Mental model:

```text
whole input
   ↓
split records
   ↓
John:20
Alice:30
Bob:40
   ↓
split fields
   ↓
name + value
```

---

# 13. Multiple Delimiters

Input:

```text
10,20;30:40|50
```

If all separators mean the same thing, normalize them:

```cpp
for (char &ch : s) {
    if (ch == ',' || ch == ';' || ch == ':' || ch == '|') {
        ch = ' ';
    }
}
```

Now:

```text
10 20 30 40 50
```

Then:

```cpp
stringstream ss(s);

int x;

while (ss >> x) {
    // process x
}
```

---

# 14. Replace Characters

```cpp
replace(s.begin(), s.end(), ',', ' ');
```

Example:

```text
a,b,c
```

becomes:

```text
a b c
```

---

# 15. Remove All Occurrences of a Character

```cpp
s.erase(remove(s.begin(), s.end(), '-'), s.end());
```

Example:

```text
12-34-56
```

becomes:

```text
123456
```

---

# 16. Remove Whitespace

Remove all whitespace:

```cpp
s.erase(remove_if(s.begin(), s.end(), ::isspace), s.end());
```

Example:

```text
a b c
```

becomes:

```text
abc
```

**Note:** This removes whitespace everywhere, not just at the ends.

---

# 17. Trim Leading / Trailing Whitespace

Useful helper:

```cpp
string trim(string s) {
    size_t start = s.find_first_not_of(" \t\r\n");

    if (start == string::npos)
        return "";

    size_t end = s.find_last_not_of(" \t\r\n");

    return s.substr(start, end - start + 1);
}
```

Example:

```text
"   hello world   "
```

becomes:

```text
"hello world"
```

---

# 18. `find_first_of()` / `find_last_of()`

Find first character from a set:

```cpp
size_t pos = s.find_first_of(",;:|");
```

Find last:

```cpp
size_t pos = s.find_last_of(",;:|");
```

Useful when multiple delimiters are possible.

---

# 19. Fixed-Format Parsing

Input:

```text
2026-09-16
```

Known format:

```text
YYYY-MM-DD
```

```cpp
string year = s.substr(0, 4);
string month = s.substr(5, 2);
string day = s.substr(8, 2);
```

Use fixed positions only when the format is guaranteed.

---

# 20. String ↔ Number Conversion

## String → int

```cpp
int x = stoi("123");
```

## String → long long

```cpp
long long x = stoll("123456789");
```

## String → float

```cpp
float x = stof("12.5");
```

## String → double

```cpp
double x = stod("12.5");
```

## Number → string

```cpp
string s = to_string(123);
```

---

# 21. Parsing CSV

Input:

```text
John Doe,25,Software Engineer
```

Use the actual field delimiter:

```cpp
stringstream ss(s);

string name;
string age;
string job;

getline(ss, name, ',');
getline(ss, age, ',');
getline(ss, job, ',');
```

Result:

```text
name = "John Doe"
age  = "25"
job  = "Software Engineer"
```

Convert when necessary:

```cpp
int ageValue = stoi(age);
```

### Important

If a field itself contains spaces, don't use `ss >> token`.

Use the format's delimiter.

---

# 22. Empty Tokens

Input:

```text
a,,b,c
```

```cpp
stringstream ss(s);
string token;

while (getline(ss, token, ',')) {

    if (token.empty()) {
        // empty field
    }
}
```

Possible tokens:

```text
a
""
b
c
```

Do not automatically discard empty tokens unless the problem says to.

---

# 23. Consecutive Delimiters

Example:

```text
a:::b
```

`getline(..., ':')` gives empty fields between consecutive delimiters.

Whether empty fields matter depends on the input specification.

---

# 24. Leading / Trailing Delimiters

Examples:

```text
,a,b
a,b,
,a,b,
```

When parsing, check whether empty first/last fields are meaningful.

---

# 25. Manual Tokenization

When delimiters or token rules are more complicated, parse character-by-character.

General template:

```cpp
string current;

for (char ch : s) {

    if (/* ch belongs to current token */) {
        current += ch;
    }
    else {

        if (!current.empty()) {
            // process current token
        }

        current.clear();
    }
}

if (!current.empty()) {
    // process final token
}
```

### Important

The final token may not be followed by a delimiter, so always check it after the loop.

---

# 26. Extract Numbers from Text

Input:

```text
abc12xyz34test567
```

Basic pattern:

```cpp
string current;

for (char ch : s) {

    if (isdigit(ch)) {
        current += ch;
    }
    else {

        if (!current.empty()) {
            int x = stoi(current);
            // process x
            current.clear();
        }
    }
}

if (!current.empty()) {
    int x = stoi(current);
    // process x
}
```

This demonstrates **token building**.

---

# 27. Handling Signed Numbers

If `-` can belong to a number, don't treat every `-` as a separator.

Conceptually:

```text
-25
```

is one token, not:

```text
-
25
```

Check the input specification before parsing.

---

# 28. Character Replacement / Normalization

Sometimes different characters represent the same separator.

Example:

```text
10,20;30|40
```

Normalize:

```cpp
for (char &ch : s) {
    if (ch == ',' || ch == ';' || ch == '|')
        ch = ' ';
}
```

Then parse normally.

---

# 29. Useful String Modification Operations

## Append

```cpp
s += "abc";
s.append("abc");
```

## Insert

```cpp
s.insert(position, "abc");
```

## Erase

```cpp
s.erase(position, length);
```

## Clear

```cpp
s.clear();
```

## Remove last character

```cpp
s.pop_back();
```

## Push character

```cpp
s.push_back('a');
```

---

# 30. Compare Strings

```cpp
if (a == b) {}
if (a != b) {}
if (a < b) {}
if (a > b) {}
```

Lexicographical comparison is used for `<`, `>`, etc.

---

# 31. Useful Search / Comparison Operations

```cpp
s.find("abc");
s.rfind("abc");

s.compare(t);
```

C++20:

```cpp
s.starts_with("abc");
s.ends_with("xyz");
```

For older standards:

```cpp
s.rfind("abc", 0) == 0
```

can check a prefix.

---

# 32. Reverse / Sort

Reverse:

```cpp
reverse(s.begin(), s.end());
```

Sort:

```cpp
sort(s.begin(), s.end());
```

These are basic string container operations, not input parsing.

---

# 33. Character Frequency Storage

For lowercase English letters:

```cpp
vector<int> freq(26, 0);

for (char ch : s) {
    freq[ch - 'a']++;
}
```

For ASCII characters:

```cpp
vector<int> freq(256, 0);

for (unsigned char ch : s) {
    freq[ch]++;
}
```

---

# 34. `map` / `unordered_map` for Parsed Data

For key-value strings:

```text
name=John age=25 city=Pune
```

```cpp
unordered_map<string, string> data;

stringstream ss(s);
string token;

while (ss >> token) {

    size_t pos = token.find('=');

    string key = token.substr(0, pos);
    string value = token.substr(pos + 1);

    data[key] = value;
}
```

For repeated numeric values:

```cpp
unordered_map<string, int> data;
```

Then:

```cpp
data[key] += value;
```

---

# 35. Common Input Formats and Correct Tool

| Input format | Typical method |
|---|---|
| `hello` | `cin >> s` |
| `hello world` | `getline(cin,s)` |
| `a b c` | `stringstream` + `>>` |
| `a,b,c` | `getline(..., ',')` |
| `a:b` | `find()` + `substr()` |
| `a:b c:d` | outer split + inner split |
| `a,b;c:d` | normalize or nested parsing |
| `2026-09-16` | `substr()` / delimiter parsing |
| `abc123xyz` | character-by-character |
| unknown lines | `while(getline(...))` |
| unknown tokens | `while(cin >> token)` |
| `n` followed by lines | `cin.ignore()` + `getline()` |

---

# 36. Most Important Parsing Patterns

## Pattern 1 — Word input

```cpp
cin >> s;
```

---

## Pattern 2 — Full line

```cpp
getline(cin, s);
```

---

## Pattern 3 — Whitespace split

```cpp
stringstream ss(s);

while (ss >> token) {
}
```

---

## Pattern 4 — Delimiter split

```cpp
stringstream ss(s);

while (getline(ss, token, ',')) {
}
```

---

## Pattern 5 — Two fields

```cpp
size_t pos = s.find(':');

string a = s.substr(0, pos);
string b = s.substr(pos + 1);
```

---

## Pattern 6 — Nested parsing

```text
records
   ↓
split records
   ↓
split each record into fields
```

---

## Pattern 7 — Manual parsing

```cpp
for (char ch : s) {
    // classify / collect / separate
}
```

---

## Pattern 8 — Normalize separators

```cpp
for (char &ch : s) {
    if (ch == ',' || ch == ';')
        ch = ' ';
}
```

Then use `stringstream`.

---

# 37. The 6 Questions to Ask Before Parsing

Whenever you see a string input, identify:

### 1. What is one record?

Example:

```text
name1:10
```

or:

```text
John Doe,25,Engineer
```

### 2. What separates records?

Possible:

```text
space
,
;
|
newline
```

### 3. What separates fields inside a record?

Possible:

```text
:
=
-
/
,
```

### 4. Can fields contain spaces?

If yes, don't blindly use:

```cpp
ss >> token;
```

### 5. Can fields be empty?

Example:

```text
a,,b
```

### 6. What type should each extracted field become?

Examples:

```text
"25" → int
"25.5" → double
"John" → string
```

---

# 38. The Universal Processing Pipeline

```text
                 INPUT
                   ↓
          ┌─────────────────┐
          │ How is it read? │
          └─────────────────┘
                   ↓
       ┌───────────┼───────────┐
       ↓           ↓           ↓
     word        line       multiple lines
       ↓           ↓           ↓
   cin >> s    getline()   while(getline)
                   ↓
             Identify delimiter
                   ↓
       ┌───────────┼───────────┐
       ↓           ↓           ↓
   whitespace   delimiter    no clear
       ↓           ↓         delimiter
 stringstream  getline()    manual scan
       ↓           ↓           ↓
       └───────────┼───────────┘
                   ↓
              Extract fields
                   ↓
              substr/find
                   ↓
              Convert types
                   ↓
        stoi / stoll / stod
                   ↓
            Store / process
```

---

# 39. Quick Reference — `std::string`

```cpp
s.size()
s.length()

s[i]
s.at(i)
s.front()
s.back()

s.empty()
s.clear()

s.find(x)
s.rfind(x)
s.find_first_of(x)
s.find_last_of(x)

s.substr(pos, len)

s += t
s.append(t)

s.push_back(ch)
s.pop_back()

s.insert(pos, t)
s.erase(pos, len)

s.starts_with(t)   // C++20
s.ends_with(t)     // C++20

s == t
s != t
s.compare(t)
```

---

# 40. Quick Reference — Algorithms Used with Strings

```cpp
reverse(s.begin(), s.end());

sort(s.begin(), s.end());

replace(s.begin(), s.end(), oldChar, newChar);

s.erase(remove(s.begin(), s.end(), ch), s.end());

s.erase(
    remove_if(s.begin(), s.end(), ::isspace),
    s.end()
);
```

---

# 41. Quick Reference — Conversions

```cpp
stoi(s)
stoll(s)
stof(s)
stod(s)

to_string(x)
```

---

# 42. Quick Reference — Character Functions

```cpp
isdigit(ch)
isalpha(ch)
isalnum(ch)
isspace(ch)
islower(ch)
isupper(ch)

tolower(ch)
toupper(ch)
```

For safest use with potentially signed `char` values:

```cpp
isdigit(static_cast<unsigned char>(ch))
```

---

# 43. Common Mistakes

## Mistake 1

```cpp
cin >> n;
getline(cin, s);
```

### Fix

```cpp
cin >> n;
cin.ignore(numeric_limits<streamsize>::max(), '\n');
getline(cin, s);
```

---

## Mistake 2

Using:

```cpp
cin >> s;
```

when input contains spaces.

Use:

```cpp
getline(cin, s);
```

---

## Mistake 3

Splitting a CSV field with spaces using:

```cpp
ss >> token;
```

Use the actual delimiter:

```cpp
getline(ss, token, ',');
```

---

## Mistake 4

Assuming there is always a delimiter.

Before:

```cpp
int pos = s.find(':');
```

understand whether `:` is guaranteed.

---

## Mistake 5

Ignoring empty tokens.

For:

```text
a,,b
```

the empty field may be meaningful.

---

## Mistake 6

Forgetting the final manually built token.

Always check after the loop:

```cpp
if (!current.empty()) {
    // process
}
```

---

## Mistake 7

Mixing signed/unsigned indexes unnecessarily.

Prefer:

```cpp
size_t pos = s.find(':');
```

for positions returned by `find()`.

For ordinary indexing:

```cpp
for (int i = 0; i < (int)s.size(); i++)
```

is common in competitive programming.

---

# 44. Master Templates

## Template A — Read one full line

```cpp
string s;
getline(cin, s);
```

## Template B — Split whitespace

```cpp
stringstream ss(s);
string token;

while (ss >> token) {
    // token
}
```

## Template C — Split delimiter

```cpp
stringstream ss(s);
string token;

while (getline(ss, token, ',')) {
    // token
}
```

## Template D — Parse `key:value`

```cpp
size_t pos = token.find(':');

string key = token.substr(0, pos);
string value = token.substr(pos + 1);
```

## Template E — Convert value

```cpp
int x = stoi(value);
long long y = stoll(value);
double z = stod(value);
```

## Template F — Character scan

```cpp
for (char ch : s) {

    if (isdigit(ch)) {
        // digit
    }
    else if (isalpha(ch)) {
        // letter
    }
    else {
        // separator/special
    }
}
```

---

# 45. Final Mental Cheat Sheet

```text
STRING INPUT
│
├── One token
│   └── cin >> s
│
├── Whole line
│   └── getline(cin, s)
│
├── Many lines
│   └── while(getline(cin, s))
│
├── Space-separated
│   └── stringstream + >>
│
├── Comma/colon/pipe separated
│   └── getline(ss, token, delimiter)
│
├── key:value
│   └── find + substr
│
├── key:value key:value
│   └── outer split + inner split
│
├── Multiple separators
│   └── normalize + split
│
├── Fixed format
│   └── substr by position
│
├── Complex/unknown format
│   └── character-by-character
│
├── String → number
│   └── stoi / stoll / stod
│
└── Number → string
    └── to_string
```

---

# 46. The Core Rule

> **Don't start processing until you understand the input structure.**

For:

```text
name1:1 name2:1 name1:3
```

think:

```text
Whole line
    ↓
space = record separator
    ↓
name1:1
name2:1
name1:3
    ↓
: = field separator
    ↓
name1 | 1
name2 | 1
name1 | 3
```

For:

```text
John Doe,25,Software Engineer
```

think:

```text
Whole line
    ↓
, = field separator
    ↓
John Doe
25
Software Engineer
```

For:

```text
abc123xyz456
```

think:

```text
No explicit delimiter
    ↓
character-by-character
    ↓
build tokens based on character type
```

Once you can identify the **record separator → field separator → field type**, the C++ syntax for processing it becomes straightforward.
