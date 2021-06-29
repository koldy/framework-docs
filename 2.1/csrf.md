# CSRF

If you don't know what CSRF is, please read more [here](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)).

In order to make CSRF protection work properly, you need to know about few parts:

1. there is random key stored on backend in session under [session_key_name](configuration.md#security) - You are
responsible about decision on when this key has to be generated and regenerated
2. generate value has to be passed to the client's browser and remembered there - it's done using the cookie header -
name of the cookie is set in [cookie_name](configuration.md#security) setting
3. then, when you have the CSRF protection value on client, you need to send it back to server via standard parameters
where server has to check if the given CSRF protection value is the same as the one generated on server, otherwise it
should throw an exception

Every time when you send any payload to server that has some parameter, you should check the validity of CSRF. To make
this easier for you, you should use [Validator class](validator.md) where you can make quick and efficient validation
of your data. You don't have to explicitly mention CSRF every time in your validator parameters. If you have CSRF
protection enabled in [configuration](configuration.md#security), then [Validator class](validator.md) will do this
automatically for you.