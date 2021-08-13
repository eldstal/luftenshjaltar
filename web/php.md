# PHP

## Parameter Pollution

[Source](https://twitter.com/PaulosYibelo/status/1425731971188248581?s=19)  
PHP internally uses `parse_str()` __to parse parameters so it sees the characters `[` and `_` __as the same. When duplicate GET parameters are passed, PHP will use the last one. Frontend validation may be possible to bypass this way:

`.../document.php?user_name=good&user[name`



