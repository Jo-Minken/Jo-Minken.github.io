---
layout: post
title: Learn Regular Expression (codecademy)
date: 2020-07-26
categories: [codecademy]
---

This post is a note for what I have learned from [codecademy - Learn the Basics of Regular Expressions](https://www.codecademy.com/learn/introduction-to-regular-expressions).

1. literals
2. alternation `|`
3. character set 
   - `[]`
   - `^` (caret) not
4. wildcard
   - `.` for letter, number, symbol, whitespace
   - `\.` for actual period
   - `\` for escape
5. range `[a-z]`, `[A-Z]`, `[0-9]`
6. shorthand character class
   - `\w` word `[A-Za-z0-9]`
   - `\d` digit `[0-9]`
   - `\s` whitespace `[ \t\r\n\f\v]`
     - `\t` tab
     - `\r` carrige return
     - `\n` line break
     - `\f` form seed
     - `\v` vertical tab
7. with Capital letter → non-
- `\W` non-word `[^A-Za-z0-9]`
- `\D` non-digit `[^0-9]`
- `\S` non-whitespace `[^ \t\r\n\f\v]`
8. grouping `()`
9. quantifiers
   - `{}` times
     - `{1} one time`
     - `{1:4} one to four times`
   - `?` 0 or 1 time
   - `+` 1 or more time
   - `*` 0 or more time
10. Anchors
    - `^` start
    - `$` end
