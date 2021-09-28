# PHP

## LFI to RCE

If you have access to a `phpinfo()` page, you may be able to [use it to upload](https://www.fatalerrors.org/a/php-local-file-inclusion-rce-with-phpinfo.html) a file  
An LFI in the `<?php` context will suddenly become an RCE!

## Parameter Pollution

[Source](https://twitter.com/PaulosYibelo/status/1425731971188248581?s=19)  
PHP internally uses `parse_str()` __to parse parameters so it sees the characters `[` and `_` __as the same. When duplicate GET parameters are passed, PHP will use the last one. Frontend validation may be possible to bypass this way:

`.../document.php?user_name=good&user[name`



