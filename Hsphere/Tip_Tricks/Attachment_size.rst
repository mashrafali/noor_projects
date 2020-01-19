{{{
Author       : Karim Farrag
Version      : 1.1
Creation date: 2014-01-22
Latest update: 2014-01-22
}}}
<<TableOfContents(7)>>

Backup the configuration file before edit such as following:
{{{
cp /hsphere/local/config/httpd2/php.ini /hsphere/local/config/httpd2/php.ini_backup
}}}

Then edit the PHP files as shown below:
{{{
/hsphere/local/config/httpd2/php.ini
/hsphere/local/config/httpd2/php5/php.ini
/hsphere/local/config/httpd2/php4/php.ini
/hsphere/local/config/httpd/php.ini
/hsphere/local/config/httpd/php5/php.ini
/hsphere/local/config/httpd/php4/php.ini

Find the following Options and change them to

upload_max_filesize = 12M
post_max_size = 36M
}}}
