---
title:  "Определение тела friend функции внутри класса"
date:   2021-01-12 12:00:00 +0700
comments: true
tags: [c++, it]
---

Век живи - век учись. Сейчас внезапно для себя обнаружил, что в C++ тело
friend-функции можно определять прямо внутри класса:
{{< highlight cpp >}}
struct cls {
    friend void foo(cls & c) {
    }
};

// ...

cls c;
foo(c);
{{< /highlight >}}

<!--more-->
