# 字符类(Character Class)

考虑以下的实际任务 -- 我们有一个电话号码形如 `"+7(903)-123-45-67"`, 而我们需要把它转换为纯数字号码: `79035419441`。

为了实现这个转换, 我们需要寻找并删除所有非数字的符号。 字符类可以帮助实现这个操作。

一个 *字符类* 是一个特殊的能够匹配来自一个特定集合的所有符号的标记。

首先, 让我们探究一下 "数字(digit)" 类。 它被写作 `pattern:\d` , 与 "所有单一数字"相一致.

例如, 寻找电话号码里第一个数字字符:

```js run
let str = "+7(903)-123-45-67";

let regexp = /\d/;

alert( str.match(regexp) ); // 7
```

如果没有 `pattern:g`标记, 这个正则表达式只会搜索第一个匹配, 即第一个数字 `pattern:\d`。

添加 `pattern:g` 标记寻找所有的数字:

```js run
let str = "+7(903)-123-45-67";

let regexp = /\d/g;

alert( str.match(regexp) ); // array of matches: 7,9,0,3,1,2,3,4,5,6,7

// 求只含数字的电话号码:
alert( str.match(regexp).join('') ); // 79035419441
```

这就是数字的字符类。这里还有一些其它的字符类。

最常用的是:

`pattern:\d` ("d" 是 "数字(digit)"的首字母)
: 一个数字: 从`0` 到 `9`的字符

`pattern:\s` ("s" 是"空格(space)"的首字母)
: 一个空格符号: 包括空格, 制表符 `\t`, 换行符 `\n` 以及其它一些很少用到的符号, 例如 `\v`, `\f` and `\r`.

`pattern:\w` ("w" 是 "文字(word)"的首字母)
: 一个文字字符: 一个拉丁字母或一个数字或一个下划线 `_`。非拉丁字母 (例如古斯拉夫字母或印度语)不属于`pattern:\w`。

例如, `pattern:\d\s\w` 代表一个文字符号后接一个空格符号后接一个文字符号, 例如 `match:1 a`。

**一段正则表达式可能同时包含常规字符和字符类**

例如, `pattern:CSS\d` 匹配一段字符串 `match:CSS` 后接一个数字字符:

```js run
let str = "Is there CSS4?";
let regexp = /CSS\d/

alert( str.match(regexp) ); // CSS4
```

我们也可以同时使用许多字符类:

```js run
alert( "I love HTML5!".match(/\s\w\w\w\w\d/) ); // ' HTML5'
```

匹配项 (每个正则表达式字符类都有对应的字符结果) :

![](love-html5-classes.svg)

## 相反类

每个字符类都有一个 "相反类", 用同一字母的大写形式表示。

"相反" 指匹配所有的其它字符, 例如:

`pattern:\D`
: 非数字字符: 除了 `pattern:\d`的所有其它字符, 例如一个字母。

`pattern:\S`
: 非空格字符: 除了`pattern:\s`的所有其它字符, 例如一个字母。

`pattern:\W`
: 非文字字符: 除了`pattern:\w`的所有其它字符, 例如一个非拉丁字母或一个空格。

在本章的开头我们展示了如何将一个字符串形如 `subject:+7(903)-123-45-67`转换为纯数字字符形式: 寻找所有的数字字符并连接它们。

```js run
let str = "+7(903)-123-45-67";

alert( str.match(/\d/g).join('') ); // 79031234567
```

另一种更短的方法是, 找到所有 `pattern:\D` 并把它们从字符串中移除:

```js run
let str = "+7(903)-123-45-67";

alert( str.replace(/\D/g, "") ); // 79031234567
```

## 一个句点是"任何字符"

一个句点 `pattern:.` 是一个能匹配"除了换行符外所有字符"的特殊字符类.

例如:

```js run
alert( "Z".match(/./) ); // Z
```

或在一段正则表达式中:

```js run
let regexp = /CS.4/;

alert( "CSS4".match(regexp) ); // CSS4
alert( "CS-4".match(regexp) ); // CS-4
alert( "CS 4".match(regexp) ); // CS 4 (空格不是一个字符)
```

请注意句点表示"所有字符", 但不是 "一个字符的缺省"。 必须有一个能够匹配它的字符:

```js run
alert( "CS4".match(/CS.4/) ); // 无效, 不匹配因为没有能够与句点匹配的字符
```

### Dot as literally any character with "s" flag

By default, a dot doesn't match the newline character `\n`.

For instance, the regexp `pattern:A.B` matches `match:A`, and then `match:B` with any character between them, except a newline `\n`:

```js run
alert( "A\nB".match(/A.B/) ); // null (no match)
```

There are many situations when we'd like a dot to mean literally "any character", newline included.

That's what flag `pattern:s` does. If a regexp has it, then a dot `pattern:.` matches literally any character:

```js run
alert( "A\nB".match(/A.B/s) ); // A\nB (match!)
```

````warn header="Not supported in Firefox, IE, Edge"
Check <https://caniuse.com/#search=dotall> for the most recent state of support. At the time of writing it doesn't include Firefox, IE, Edge.

Luckily, there's an alternative, that works everywhere. We can use a regexp like `pattern:[\s\S]` to match "any character".

```js run
alert( "A\nB".match(/A[\s\S]B/) ); // A\nB (match!)
```

The pattern `pattern:[\s\S]` literally says: "a space character OR not a space character". In other words, "anything". We could use another pair of complementary classes, such as `pattern:[\d\D]`, that doesn't matter.

This trick works everywhere. Also we can use it if we don't want to set `pattern:s` flag, in cases when we want a regular "no-newline" dot too in the pattern.
````

````warn header="Pay attention to spaces"
Usually we pay little attention to spaces. For us strings `subject:1-5` and `subject:1 - 5` are nearly identical.

But if a regexp doesn't take spaces into account, it may fail to work.

Let's try to find digits separated by a hyphen:

```js run
alert( "1 - 5".match(/\d-\d/) ); // null, no match!
```

Let's fix it adding spaces into the regexp `pattern:\d - \d`:

```js run
alert( "1 - 5".match(/\d - \d/) ); // 1 - 5, now it works
// or we can use \s class:
alert( "1 - 5".match(/\d\s-\s\d/) ); // 1 - 5, also works
```

**A space is a character. Equal in importance with any other character.**

We can't add or remove spaces from a regular expression and expect to work the same.

In other words, in a regular expression all characters matter, spaces too.
````

## Summary

There exist following character classes:

- `pattern:\d` -- digits.
- `pattern:\D` -- non-digits.
- `pattern:\s` -- space symbols, tabs, newlines.
- `pattern:\S` -- all but `pattern:\s`.
- `pattern:\w` -- Latin letters, digits, underscore `'_'`.
- `pattern:\W` -- all but `pattern:\w`.
- `pattern:.` -- any character if with the regexp `'s'` flag, otherwise any except a newline `\n`.

...But that's not all!

Unicode encoding, used by JavaScript for strings, provides many properties for characters, like: which language the letter belongs to (if it's a letter) it is it a punctuation sign, etc.

We can search by these properties as well. That requires flag `pattern:u`, covered in the next article.
