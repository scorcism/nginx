
```conf
upstream proxies {
      # main server endpoints

        server localhost:8000;
        server localhost:8001;

}

server {
        listen 7000;

        server_name _;


        location /  {
                add_header Cache-Control 'no-store, no-cache';
                proxy_pass http://proxies;
        }

}
```
