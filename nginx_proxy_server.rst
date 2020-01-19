{{{
Author       : Ahmed AbdelSattaar
Version      : 1.0
Creation date: 2015-02-05
Latest update: 2012-02-05
}}}
<<TableOfContents(3)>>

== Install Nginx ==
{{{
apt-get install nginx
}}}

== Edit proxy_params ==
edit file /etc/nginx/proxy_params
{{{
nano /etc/nginx/proxy_params
}}}

{{{
proxy_set_header Host $http_host;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
proxy_set_header X-Forwarded-Proto $scheme;

}}}
== Edit Site Configuration file ==

edit file /etc/nginx/sites-available/default

{{{
nano /etc/nginx/sites-available/default
}}}

{{{
server {
        server_name _;
        return 444;
}

server_tokens off;
server {

        listen 80;

        server_name safarkhan.com www.safarkhan.com;

        location / {
        include proxy_params;
        proxy_pass http://www.safarkhan.com;

        }

    ##### Block SQL injections
    set $block_sql_injections 0;
    if ($query_string ~ "union.*select.*\(") {
        set $block_sql_injections 1;
    }
    if ($query_string ~ "union.*all.*select.*") {
        set $block_sql_injections 1;
    }
    if ($query_string ~ "concat.*\(") {
        set $block_sql_injections 1;
    }

    if ($query_string ~ "\b(declare|varchar|exec|execute|set)\b") {
        set $block_sql_injections 1;
    }

    if ($query_string ~ "\b(char|nchar|varchar|nvarchar|alter|begin|cast|create|cursor)\b") {
        set $block_sql_injections 1;
    }
    if ($query_string ~ "\b(delete|drop|end|fetch|insert|kill|open|set|cast|select|sys|sysobjects)\b") {
        set $block_sql_injections 1;
    }
    if ($query_string ~ "\b(open|set|cast|select|sys|sysobjects|syscolumns|table|update)\b") {
        set $block_sql_injections 1;
    }


    if ($block_sql_injections = 1) {
        return 403;
    }
    ##### Block SQL injections

    ##### Block file injections

    set $block_file_injections 0;
    if ($query_string ~ "[a-zA-Z0-9_]=http://") {
        set $block_file_injections 1;
    }
    if ($query_string ~ "[a-zA-Z0-9_]=(\.\.//?)+") {
        set $block_file_injections 1;
    }
    if ($query_string ~ "[a-zA-Z0-9_]=/([a-z0-9_.]//?)+") {
        set $block_file_injections 1;
    }
    if ($block_file_injections = 1) {
        return 403;
    }

    ##### Block file injections
}

}}}
