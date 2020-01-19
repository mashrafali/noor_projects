## page was renamed from Enterprise/IT/Docs/HowTo/Linux/H-sphere/Tip_Tricks/increase_phpmyadmin
{{{
Author       : Ayman Tohamy
Version      : 1.1
Creation date: 2014-06-02
Latest update: 2014-06-02
}}}
<<TableOfContents(7)>>

1-      Locate php.ini

{{{
# locate php.ini
/hsphere/local/config/httpd2/php5/php.ini
}}}
Backup

{{{
# cp /hsphere/local/config/httpd2/php5/php.ini /hsphere/local/config/httpd2/php5/php.ini.bak
}}}
2-      Edit file

{{{
# vi /hsphere/local/config/httpd2/php5/php.ini

post_max_size = 11M
upload_max_filesize = 11M
}}}
3-      Check

{{{
web ~]# cat /hsphere/local/config/httpd2/php5/php.ini |  grep "upload"
         file_uploads = 1
        upload_tmp_dir =
        upload_max_filesize = 10M
}}}
{{{
 web ~]# cat /hsphere/local/config/httpd2/php5/php.ini |  grep "post"
        post_max_size = 11M
        always_populate_raw_post_data = 1
}}}
4-      Restart Service

{{{
# /etc/init.d/httpd restart
}}}
5-      TEST

 . Test from Phpmyadmin web interface upload size while importing database
 http://mysql.noor.com/phpMyAdmin/index.php
 .
 . Test from website php info page after copy hsphpinfo.php to customer root directory For Example:

{{{
        cp /hsphere/shared/apache/htdocs/hsphpinfo.php /hsphere/local/home/wsadmin4/woodsteel.com/
        chmown –R wsadmin4: /hsphere/local/home/wsadmin4/woodsteel.com/hsphpinfo.php
        chmod 644 /hsphere/local/home/wsadmin4/woodsteel.com/hsphpinfo.php

        http://woodsteel.com/hsphpinfo.php
}}}
