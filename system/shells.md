# Shells

## Reverse Shells

[Generator](https://www.revshells.com)

## Upgrade a simple shell to a proper one

`/usr/bin/script -qc /bin/bash /dev/null`

```
$ su - oracle
su: must be run from a terminal
$ /usr/bin/script -qc /bin/bash /dev/null
www-data@funbox7:/$ su - oracle
su - oracle
Password:
```

Every so often, that trick fails to work. Here are some other ones:

[Source](https://netsec.ws/?p=337)

```
python -c 'import pty; pty.spawn("/bin/sh")'

echo os.system('/bin/bash')

/bin/sh -i

perl —e 'exec "/bin/sh";'

perl: exec "/bin/sh";

ruby: exec "/bin/sh"

lua: os.execute('/bin/sh')
```
