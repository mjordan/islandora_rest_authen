# Islandora REST Authen

Utility module to provide username/token authentication against the Islandora REST interface, with the option to restrict access to a list of IP addresss.

## Requirements

* [Islandora](https://github.com/Islandora/islandora)
* [Islandora REST](https://github.com/discoverygarden/islandora_rest)

## Configuration

Enable this module as you would any other, and configure it at `admin/islandora/tools/rest_authen`.

There are two configuration fields. The first ("REST users") is a list of username/token pairs, one per line. The username half of the pair corresponds to an existing Drupal user. These users are regular Drupal users except that when identified in REST requests, they are authenticated using the accompanying token and not their Drupal password. In fact, this authentication bypasses the normal Drupal login mechanism for REST requests.

A `username:token` string registered in the "REST users" admin setting must accompany REST requests in an "X-Authorization-User" HTTP header. If the string in the header matches one of the registered strings, the user is authenticated and the request continues. If it doesn't, the reqeust is denied.

Tokens can be of any length, and can contain any characters, including spaces.

As stated above, REST users are regular Drupal users. You will need to assign them appropriate permissions in the "Islandora REST" section of `admin/people/permissions`. In order to log into the Drupal website like any other user would, they need to enter their username and password into the login form. They cannot log in using their token. If you do not want REST users to be able to log into your site using the login form, set their account status to "Blocked".

The second configuration field takes a list of IP address ranges or individual IP addresses that the REST users are allowed to access the REST interface from. This restriction only applies to REST requests and not to logging into the Drupal website.

## Maintainer

* [Mark Jordan](https://github.com/mjordan)

## Development and feedback

Bug reports, use cases and suggestions are welcom. If you want to open a pull request, please open an issue first.

## License

 [GPLv3](http://www.gnu.org/licenses/gpl-3.0.txt)
