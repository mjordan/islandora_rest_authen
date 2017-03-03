# Islandora REST Authen

Utility module to provide username/token authentication against the Islandora REST interface, with the option to restrict access to a list of IP addresss.

## Requirements

* [Islandora](https://github.com/Islandora/islandora)
* [Islandora REST](https://github.com/discoverygarden/islandora_rest)

## Configuration

Enable this module as you would any other, and configure it at `admin/islandora/tools/rest_authen`.

There is only one configuration option, "REST users", which is a list of username/token pairs, one per line. The username half of the pair corresponds to an existing Drupal user. The token half of the pair is used to authenticate REST requests made by the user. Tokens can be of any length, and can contain any characters except colons and pipes (which are both used as delimters; more on pipes below).

A `username:token` string registered in the "REST users" admin setting must accompany REST requests in an "X-Authorization-User" HTTP header. If the string in the header matches one of the registered `username:token` pairs, the user is authenticated and the request continues. If it doesn't, the reqeust is denied.

Users in the list are regular Drupal users, and should be assigned appropriate permissions in the "Islandora REST" section of `admin/people/permissions`. The only difference between these users and other users on the site is that when identified in REST requests, they are authenticated using the accompanying token. In other words, this authentication bypasses the normal Drupal login mechanism for REST requests. In order to log into the Drupal website like any other user would, they need to enter their username and password into the Drupal login form. They cannot authenticate via the login form using using their token. If you do not want REST users to be able to log into your site using the login form, set their account status to "Blocked".

Optionally, you can restrict access to the Islandora REST interface for each user from either a specific IP address or ranges of addresses. The IP whitelist is separated from the username/token pair with a pipe (`|`). For example, if your username is 'resty' and your token is 'iamarandom7token', an entry restricting allowing requests from the 10.0.0.2 IP address would look like:

```
resty:iamarandom7token|10.0.2.2
```

an entry restricting access from an entire IP range would look like:

```
resty:iamarandom7token|199.60.1.0:199.60.18.255
```

and from multiple ranges:

```
resty:iamarandom7token|199.60.1.0:199.60.18.255,142.58.224.0:142.58.255.255
```

IP whitelists only apply to REST requests and not to logging into the Drupal website.

## Maintainer

* [Mark Jordan](https://github.com/mjordan)

## Development and feedback

Bug reports, use cases and suggestions are welcom. If you want to open a pull request, please open an issue first.

## License

 [GPLv3](http://www.gnu.org/licenses/gpl-3.0.txt)
