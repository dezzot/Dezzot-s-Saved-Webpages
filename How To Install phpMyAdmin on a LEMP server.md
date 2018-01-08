
[Source](https://www.digitalocean.com/community/tutorials/how-to-install-phpmyadmin-on-a-lemp-server "Permalink to How To Install phpMyAdmin on a LEMP server")

# How To Install phpMyAdmin on a LEMP server

### About phpMyAdmin

phpMyAdmin is a free software to work with MySQL on the web—it provides a convenient visual front end to the capabilities of MySQL.

## Setup

Prior to installing phpMyAdmin, be sure that you have LEMP installed on your VPS. If you do not, you can see how to do that [here][1].

Once you have finished installing LEMP on your virtual private server, you can start on installing phpMyAdmin.

## Step One—Install phpMyAdmin

Start off by downloading the program from apt-get.
    
    
    sudo apt-get install phpmyadmin

During the installation, phpmyadmin will ask you if you want to configure the database with dbconfig. Go ahead and choose yes.

Input MySQL's database password when prompted and click ok.

When phpmyadmin prompts you to choose a server (either apache or lighttpd) hit tab, and select neither one.

## Step Two—Configure phpMyAdmin

You now have phpMyAdmin installed on your server. In order to access it, you need to take one more step.

Create a symbolic link between phpMyAdmin and your site's directory. If you were using the previous tutorial, it may be still located in the nginx default directory, otherwise link it with the appropriate place:
    
    
    sudo ln -s /usr/share/phpmyadmin/ /usr/share/nginx/www

Restart nginx:
    
    
    sudo service nginx restart

You should now be able to access phpMyAdmin by going to yourdomain/phpmyadmin. The screen will look like [this][2].

Log in with your MySQL username and password.

By Etel Sverdlov

[1]: https://www.digitalocean.com/community/articles/how-to-install-linux-nginx-mysql-php-lemp-stack-on-ubuntu-12-04
[2]: https://assets.digitalocean.com/tutorial_images/eVC8A.png

  
