# JSON

This is helper for encoding string to JSON and vice versa. It is using standard PHP functions like [json_encode()](http://php.net/json_encode) and [json_decode()](http://php.net/json_decode), but it's wrapped with checks if that job was done right. If it wasn't successful, it'll throw an exception telling you what's wrong.

All exceptions thrown by these methods are instance of `\Koldy\Json\Exception`.


## encode

Makes JSON string from the data you provide.

```
\Koldy\Json::encode(['any', 'kind', 'of', 'parsable', 'data']);
```


## decode

Decodes JSON string to PHP array. It'll throw an exception if it fails.

## decodeToObj

Same as [decode](#decode), but it'll return an instance of `stdClass` instead of PHP array.