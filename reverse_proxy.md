```conf
upstrem anyname {
	server localhost:8000;
}

server{
	listen 80

	server_name api.x.com

	location / {
		proxy_pass http://anyname;
	}
}





```
