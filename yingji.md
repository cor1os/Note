`touch -t 201702160000 start`

`touch -t 201702162359 end`

`find / -newer start -a ! -newer end -exec ls -lt {} \;`