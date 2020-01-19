{{{
Author       : Ayman Tohamy
Version      : 1.0
Title        : Reset cPanel Bandwidth Limit.
Creation date: 2016-12-05
Latest update: 2016-12-05
}}}
 . <<TableOfContents(4)>>

{{{

1-	ssh to your server

2-	cd /var/cpanel/bandwidth.cache/

3-	vi account (vim /var/cpanel/bandwidth.cache/nooradmin)

4-	delete the numbers in that file and retype 0 instead of the number

5-	save and quit and show bandwidth usage in WHM you will find its 0

}}}
