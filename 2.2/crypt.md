# Crypt

`\Koldy\Crypt` is another helper which can be used to easily `encrypt` data and `decrypt` it back to its original form.

To crypt your data, use:

```
$encryptedText = \Koldy\Crypt::encrypt('plain text', 'encryptionKey', 'method');
```

1. First parameter on `encrypt()` method is mandatory.
2. If you don't provide encryption key, [application key](configuration.md#key) will be used.
3. If you don't provide method, it'll be taken from [security](configuration.md#security) configuration block. If it's
not there, then framework's default method is `aes-256-cbc`. You can find list of available methods [here](http://php.net/manual/en/function.openssl-get-cipher-methods.php).

Same is when decrypting encrypted payload.

```
$plainText = \Koldy\Crypt::decrypt('encryptedText', 'encryptionKey', 'method');
```

If for some reason you pass wrong encryption method, `\Koldy\Crypt\CryptException` will be thrown.

If encrypted payload is modified or corrupted by any other way, `\Koldy\Crypt\MalformedException` will be thrown
from `decrypt` method.