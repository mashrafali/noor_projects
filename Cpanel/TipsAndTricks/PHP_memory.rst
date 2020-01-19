{{{
Author       : Ayman Tohamy
Version      : 1.1
Creation date: 2015-01-18
Latest update: 2015-01-18
}}}
<<TableOfContents(3)>>

= How increase PHP memory =
== Modify website's PHP.ini ==
The php memory resource can be increased in the php.ini located in the public_html.  The following steps will explain how to set the php.ini to allow a larger amount of memory use.

 . 1- Login to your cPanel
 . 2-  Go to the File Manager.
  . Select the Web root (public_html/www) directory and click Go.
 . 3- Find the php.ini file in the public_html. Note! If you do not have a php.ini in your public_html files, you can have our tech support staff restore the php.ini to your public_html directory. Open the php.ini with the code editor.
 . 4-  Find the following section in the php.ini file.
  . {{{
    max_execution_time = 30
    max_input_time = 60
    memory_limit = 128M
}}}
  Try increase the memory_limit value to 256M. If the php memory_limit is already at 256M, you can increase it to 512M. Save the changes.

== Make the php.ini recursive ==
 . 5-  In order for your memory limit to take effect you will need to make the php.ini recursive. Making your php.ini file affect all child folders as well is referred to as making the file "recursive".  To make your public_html/php.ini file recursive, edit (or create the file if it doesn't exist) your public_html/.htaccess file and add the following code:
  . {{{
    suPHP_ConfigPath /home/username/public_html
}}}
  {{{
    Be sure to replace username with your cPanel username.
}}}

This line you entered is specifying the directory where the php.ini is located that you want to make recursive.

 . Now visit the site. You should not see the "Allowed memory size" error anymore.

 . If the error still shows on your website, there may be a setting within the software itself that is overriding the change or the php.ini in the public-html may be overriden by another setting elsewhere

== Testing your PHP settings ==
Creating a phpinfo Page

If you're not familiar with how to create a php page, follow the steps below to create a phpinfo page using your File Manager.

 . 1- Log into your cPanel.
 . 2- Open your File Manager.
 . 3- Navigate to the directory you are working with. This is important because each folder can actually be set to have different php settings.
  . In this example, we are viewing the php settings for our main domain, so we are navigating to the public_html folder.
 . 4- In the top menu, click New File.
 . 5- When prompted for the file name, enter phpinfo.php (it can actually be named anything, phpinfo.php is simply a common name for the file). 
 . 6- Find the phpinfo.php file in your list of files (it should have automatically updated). Right click on it and choose "Edit".

If you see a "Text Editor" prompt, choose "utf-8" from the drop down list and then click "Edit".

 . 7- Enter the following text:
  . {{{
        <? phpinfo(); ?>
}}}
  . (it is recommended to change phpinfo.php after testing as it is common used).
  . Then click Save Changes.

 . 8- Viewing your PHP Settings

 . You can now access this page from your browser. If you created the file in your public_html folder, then you would visit
  . {{{
       http://YourDomain.com/phpinfo.php.
}}}

   . To find the specific value of a setting, search the page for what you're looking for.
   . In this case, we used our browser's search feature ( Ctrl + f ) and searched for memory_limit.
   .
   . The first value you see is what is set for the current directory (local value), and the setting value is the master value.  The local value is the actual setting and is the important value, because the local values will override the master value:
