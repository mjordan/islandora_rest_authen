# Islandora REST Authen

Utility module to provide username/key authentication against the Islandora REST interface, with the option to restrict access to a list of IP addresss.

## Requirements

* [Islandora](https://github.com/Islandora/islandora)
* [Islandora REST](https://github.com/discoverygarden/islandora_rest)

## Configuration

Enable this module as you would any other, and configure it at `admin/islandora/tools/rest_authen`.

There is only one configuration option, "REST users", which is a list of username/key pairs, one per line. The username half of the pair corresponds to an existing Drupal user. The key half of the pair is used to authenticate REST requests made by the user. Keys can be of any length, and can contain any characters except colons and pipes (which are both used as delimters; more on pipes below).

A `username:key` string registered in the "REST users" admin setting must accompany REST requests in an "X-Authorization-User" HTTP header. If the string in the header matches one of the registered `username:key` pairs, the user is authenticated and the request continues. If it doesn't, the reqeust is denied.

Users in the list are regular Drupal users, and should be assigned appropriate permissions in the "Islandora REST" section of `admin/people/permissions`. The only difference between these users and other users on the site is that when identified in REST requests, they are authenticated using the accompanying key. In other words, this authentication bypasses the normal Drupal login mechanism for REST requests. In order to log into the Drupal website like any other user would, they need to enter their username and password into the Drupal login form. They cannot authenticate via the login form using using their key. If you do not want REST users to be able to log into your site using the login form, set their account status to "Blocked".

Optionally, you can restrict access to the Islandora REST interface for each user from either a specific IP address or ranges of addresses. The IP whitelist is separated from the username/key pair with a pipe (`|`). For example, if your username is 'resty' and your key is 'iamarandom7key', an entry restricting allowing requests from the 10.0.0.2 IP address would look like:

```
resty:iamarandom7key|10.0.2.2
```

an entry restricting access from an entire IP range would look like:

```
resty:iamarandom7key|199.60.1.0:199.60.18.255
```

and from multiple ranges:

```
resty:iamarandom7key|199.60.1.0:199.60.18.255,142.58.224.0:142.58.255.255
```

IP whitelists only apply to REST requests and not to logging into the Drupal website.

## Security implications

You may want to consider the following before using this module:

* API keys cannot be used to log in via Drupal's login form - they only apply to REST API requests.
* Using keys over a non-HTTPs connection is no less secure than using Drupal's standard basic authentication. If someone is sniffing traffic to your site, they have access to all data that passes between the client and server. Using HTTPS on your site is much safer.
* API keys as implemented by this module are included in HTTP request headers, which are not normally logged by web servers and don't appear in emails pasted into emails/chat/etc., unlike API keys implemented as URL parameters.
* Restricting access from specific IP addresses or ranges is good practice. Do it.
* Setting up a special user for REST requests and setting their account status to "Blocked" is also good practice. These users should be given minimal permissions, other than those required by the Islandora REST module.


## Maintainer

* [Mark Jordan](https://github.com/mjordan)

## Development and feedback

Bug reports, use cases and suggestions are welcom. If you want to open a pull request, please open an issue first.

## License

 [GPLv3](http://www.gnu.org/licenses/gpl-3.0.txt)
