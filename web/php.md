# PHP

## LFI to RCE

If you have access to a `phpinfo()` page, you may be able to [use it to upload](https://www.fatalerrors.org/a/php-local-file-inclusion-rce-with-phpinfo.html) a file\
An LFI in the `<?php` context will suddenly become an RCE!

If the LFI is an `include` statement, you may be able to sneak PHP code in via ephemeral data on disk. Access logs. PHP session files (location differs between PHP versions and distributions and configurations). `/proc/self/fd/0` is stdin.

## Command Injection

### preg\_replace

In older versions of PHP (pre-7.0.0), the regex parameter of `preg_replace()` supports the `/e` modifier, which will **execute php code** in the replacement string. By design. If you are able to control the first two parameters of `preg_replace`, you basically have `eval()`.

```
preg_replace("/.+/e", 'system("\0");', $text);
```

## Parameter Pollution

[Source](https://twitter.com/PaulosYibelo/status/1425731971188248581?s=19)\
PHP internally uses `parse_str()` __ to parse parameters so it sees the characters `[` and `_` __ as the same. When duplicate GET parameters are passed, PHP will use the last one. Frontend validation may be possible to bypass this way:

`.../document.php?user_name=good&user[name`

