server { 
  listen 80;
  server_name www.abc.com;
  rewrite ^ https://$server_name$request_uri permanent;
}

server {
listen 443; server_name www.abc.com;

access_log /var/log/nginx/abc.access.log;
error_log /var/log/nginx/abc.error.log;

ssl on;
ssl_certificate /home/ec2-user/ssl/x509/abc.crt;
ssl_certificate_key /home/ec2-user/ssl/x509/abc.key;
ssl_protocols TLSv1 TLSv1.1 TLSv1.2; # do not use SSLv3 ref: POODLE

client_max_body_size 20M;

root    /data/web/www;
index   index.php index.html;
location / {
    root    /data/web/www;
    index   index.php index.html;
}
location ~ \.php$ {
    fastcgi_pass 127.0.0.1:9003;
    fastcgi_index index.php;
    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    include fastcgi_params;
}
if (-f $request_filename/index.html){  
    rewrite (.*) $1/index.html break;  
}  
if (-f $request_filename/index.php){  
    rewrite (.*) $1/index.php;  
}  
if (!-f $request_filename){  
    rewrite (.*) /index.php;  
}  
location ~/\.ht {
    deny all;
}
}
