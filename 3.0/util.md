# Util

This is set of useful methods. Most of it is used by framework internally, but you're free to use it for
yourself as well.

## a

Parses given string and replaces URLs in string with `<a href="url">url</a>`. You can set HTML `target` attribute
if you provide it as second parameter.

## apos

Converts all apostrophes `'` into HTML's `&apos;` in given string.

## attributeValue

Useful if you want to print value to HTML's attribute and you want to be sure that there won't be any characters
that might destroy your HTML structure.

Example:

```
<a href="/home" title="<?= \Koldy\Util::attributeValue('Place any "text" here and <b>nothing unsafe</b> will happen!') ?>">
    Some value
</a>
```

## camelCase

Gets back camel-case string. You can read more about this on [Wikipedia](https://en.wikipedia.org/wiki/Camel_case).

## cleanString

This method cleans given string from new line and tab characters and it removes double spaces.

```
$string = "This   Really made
my    day  ";

echo Util::cleanString($string); // prints: "This Really made my day" 
```

## endsWith

Returns `true` if string given as first parameter ends with the string given as second parameter.

Please note that you can use PHP's native __str_ends_with()__ function instead.

## isAssociativeArray

Returns `true` if given array is associative array.

## isBinary

Returns `true` if method detects some binary specific characters in given string.

## p

This is simple parser which "detects" blocks of texts in your string and puts them within `<p>` tags.

Example: if you have this text:

```
Lorem ipsum

dolor sit amet
period.
```

It'll become:

```
<p>Lorem ipsum</p><p>dolor sit amet<br/>period.</p>
```

## quotes

This simply replaces quotes `"` in your string and replaces it with `&quot;`.

## randomString

Generates random string using openssl_random_pseudo_bytes(). It'll return all possible English lower and uppercase
letters with numbers, not just hexadecimal string.

First parameter is the length of the string you want to get back as result.

## slug

This method returns string prepared to be used in URLs as slugs. It is useful when you want to convert your
article's title into SEO friendly slug that needs to be used in URLs.

This method does much more than regular lowercase, dash instead of space and etc. Here are few examples:

| input | output |
| --- | --- |
| `The article title` | `the-article-title` |
| `It wasn't the best - idea` | `it-wasn-t-the-best-idea` |
| `Driver made $1500 of damage` | `driver-made-usd1500-of-damage` |
| `ß` | `ss` |
| `Ŕ` | `r` |

## startsWith

Returns `true` if string given as first parameter starts with the string given as second parameter.

Please note that you can use PHP's native __str_starts_with()__ function instead.

## str2hex

Gets the hex value representation of given string.

## tags

This simply replaces HTML tag characters `<` and `>` into HTML's `&lt;` and `&gt;`.

## truncate

This is standard string truncating operation, when you need to truncate long string but without breaking the
text in the middle of word.
