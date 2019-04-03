# Solution

## HOST HEADER ATTACK

### PROXY

`proxy_set_header Host $Host`

```
http {
    proxy_set_header Host              $host;
    proxy_set_header X-Real-IP         $remote_addr;
    proxy_set_header X-Forwarded-For   $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;

    server {
        ...
    }
}
```

> nginx conf