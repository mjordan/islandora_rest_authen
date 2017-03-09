# Islandora REST Authen

Utility module to provide username/API key authentication against the Islandora REST interface, with options to restrict access to a list of IP addresss and to set an expiry date on the key.

## Requirements

* [Islandora](https://github.com/Islandora/islandora)
* [Islandora REST](https://github.com/discoverygarden/islandora_rest)

## Configuration and usage

### Overview

Enable this module as you would any other, and configure it at `admin/islandora/tools/rest_authen`.

The configuration option "REST users" is a list of username/key pairs, delimited by a colon (`|`), one pair per line. The username half of the pair corresponds to an existing Drupal user. The key half of the pair, which is encrypted using the same method that Drupal uses to encrypt passwords, is used to authenticate REST requests made by the user:

```
resty|$S$D/zZv63BjzJo4rdKASjRkfbZrdNc1mcf8RFZfR4m0mmieIbbnPjM`
```

### Encrypting keys

It is important to remember that you distribute plaintext keys to people using your REST API, and save the encrypted version of those keys in the "REST users" field.

To encrypt a key, use the utility provided within the admin settings form. Enter the plaintext key and click the "Encrypt key" button, then copy the resulting string into the record in the "REST users" field.

### Making REST requests using a key

A `username/plaintext key` string registered in the "REST users" admin setting must accompany REST requests in an "X-Authorization-User" HTTP header, e.g.:

`curl -H "X-Authorization-User: resty|6f172401-a806-4b0a-920b-032cf3a06a56" "http://localhost:8000/islandora/rest/v1/object/book:16"`

If the encrypted version of the key in the header matches the encrypted key with the username, the user is authenticated and the request continues. If it doesn't match, the reqeust is denied.

### User management

Usernames in the list correspond to existing Drupal users, who should be assigned appropriate permissions in the "Islandora REST" section of `admin/people/permissions`. The only difference between these users and other users on the site is that when identified in REST requests with an accompanying `X-Authorization-User` header, they are authenticated using the accompanying key. In other words, the `username/key` authentication bypasses the normal Drupal login mechanism for REST requests. In order to log into the Drupal website like any other user would, these users need to enter their username and password into the Drupal login form. They cannot authenticate via the login form using using their key. If you do not want REST users to be able to log into your site by entering their regular Drupal credentials into the login form (see "Security implications", below), set their account status to "Blocked".

### IP whitelists and key expiry dates

Optionally, you can restrict access to the Islandora REST interface for each user from either a specific IP address or ranges of addresses. The IP whitelist is separated from the username/key pair with a pipe (`|`). For example, if your username is 'resty' and your key is "$S$D/zZv63BjzJo4rdKASjRkfbZrdNc1mcf8RFZfR4m0mmieIbbnPjM", an entry restricting allowing requests from the 10.0.0.2 IP address would look like:

```
resty|$S$D/zZv63BjzJo4rdKASjRkfbZrdNc1mcf8RFZfR4m0mmieIbbnPjM|10.0.2.2
```

an entry restricting access from an entire IP range would look like:

```
resty|$S$D/zZv63BjzJo4rdKASjRkfbZrdNc1mcf8RFZfR4m0mmieIbbnPjM|199.60.1.0:199.60.18.255
```

and from multiple ranges:

```
resty|$S$D/zZv63BjzJo4rdKASjRkfbZrdNc1mcf8RFZfR4m0mmieIbbnPjM|199.60.1.0:199.60.18.255,142.58.224.0:142.58.255.255
```

IP whitelists only apply to REST requests and not to logging into the Drupal website.

You can also apply an expiry date to API keys. If you add a date in ISO 8601 format (e.g. 2017-03-06T19:23:48-08:00) to the end of the username/key string, the API key will only authenticate requests up until that time. If you add an expiry date and do not specify an IP range or address, you need to seprate the expiry date from the `username:key` pair with double pipes (`||`), which in effect defines an empty IP range. For example:

Expiry date with IP address or range:

```
resty|$S$D/zZv63BjzJo4rdKASjRkfbZrdNc1mcf8RFZfR4m0mmieIbbnPjM|199.60.1.0:199.60.18.255|2017-03-06T19:23:48-08:00
```

Expiry date without IP address/range:

```
resty|$S$D/zZv63BjzJo4rdKASjRkfbZrdNc1mcf8RFZfR4m0mmieIbbnPjM||2017-03-06T19:23:48-08:00
```


A second configuration option, "Log API key authentication attempts", adds an entry to the Drupal system log every time a request is made to the REST interface by one of the registered users. It is enabled by default.

## Security implications

You may want to consider the following before using this module:

* Using API keys over an unencrypted HTTP connection is no less secure than using Drupal's standard cookie-based authentication. If someone is sniffing traffic to your site, they have access to all data that passes between the client and server. Using HTTPS on your site is much more secure in general.
* API keys as implemented by this module are included in HTTP request headers, which are not normally logged by web servers and don't appear accidently in URLs pasted into emails/chat/etc., unlike API keys implemented as URL parameters.
* As stated above, API keys as implemented by this module cannot be used to log in via Drupal's login form. They only apply to REST API requests.
* Use API keys that are difficult to guess. UUID version 4 strings make good API keys.
* Keys are stored in the database encrypted using the same function that Drupal uses to encrypt passwords.
* Creating special Drupal users for REST requests and setting their account status to "Blocked" is good practice. These users should be given minimal Drupal permissions, specifically, only those permissions defined by the Islandora REST module.
* Restricting access from specific IP addresses or IP ranges is good practice. Do it.
* If the client does not need ongoing access to your REST interface, apply an expiry date.
* Enable logging of authentication requests using API keys.


## Maintainer

* [Mark Jordan](https://github.com/mjordan)

## Development and feedback

Bug reports, use cases and suggestions are welcome. If you want to open a pull request, please open an issue first.

## License

 [GPLv3](http://www.gnu.org/licenses/gpl-3.0.txt)
