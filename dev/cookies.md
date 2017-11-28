# Cookies

This helper is using standard PHP's [setcookie() method](http://www.php.net/manual/en/function.setcookie.php), but with few additions.

To improve security, every value stored within the cookie will be encrypted and when you get your value back in request,
you can read the value and decrypt value. If end-user modified encrypted value of cookie, framework's [Crypt](crypt.md)
helper will throw `\Koldy\Crypt\MalformedException`.


## Setting Cookie

To set the cookie, use `Cookie::set()` method:

```
use Koldy\Cookie;

$encryptedValue = Cookie::set('my_cookie', 'My value');
```

`$encrytedValue` is generated using [key from configuration](configuration.md#key) and using
[openssl_default_method](configuration.md#security) from [security configuration block](configuration.md#security). Read more about
encrypting [here](crypt.md).

> PLEASE NOTE: If you generate encrypted cookie values and then change [key in configuration](configuration.md#key),
all cookie values generated before the key change won't be decrypted. Instead, `\Koldy\Crypt\MalformedException` will
be thrown. We recommend that you generate key once, make it secret and unique enough. Change key only if you know what
consequences it brings and only if your security is compromised.

If for any reason you need to avoid value encryption, use `Cookie::rawSet()` which is the same as using standard
[setcookie()](http://www.php.net/manual/en/function.setcookie.php). It accepts the same parameters as `Cookie::set()`.


## Getting Cookie

To get the cookie value simply use `Cookie::get()`:

```
use Koldy\Cookie;

echo Cookie::get('my_cookie'); // would print "My value"
```

If for any reason you need to access raw cookie values, use `Cookie::rawGet()`.


## Checking And Deleting Cookie

To check if cookie exists, use:

```
\Koldy\Cookie::has('my_cookie');
```

To delete the cookie, use:

```
\Koldy\Cookie::delete('my_cookie');
```