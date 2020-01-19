{{{
Author       : Karim Farrag
Version      : 1.0
Creation date: 2014-05-05
Latest update: 2015-05-05
}}}
<<TableOfContents(3)>>
== Default time zone in the server php.ini ==
the default time zone in the php.ini is UTS which is GMT time I changed that from the following pass

Home » Service Configuration » PHP Configuration Editor, and clicking the Advanced Mode radio button. 
The setting to change is date.timezone. ((change the UTS to be Africa/Cairo))

The below function in a web test test.php  page which will show the date as per the php.ini page
{{{
<!DOCTYPE html>
<html>
<body>

<?php echo date('l jS \of F Y h:i:s A'); ?>
<?php echo gmdate('d/m/Y'); ?>


</body>
</html>
}}}
== Change Upload Max Size ==
to Change the Maz Upload Size <<BR>>
Home » Service Configuration » PHP Configuration Editor, and clicking the Advanced Mode radio button.  <<BR>>
The setting to change is upload_max_filesize
