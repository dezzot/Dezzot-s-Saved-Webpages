
[Source](https://www.kosinix.com/install-lets-encrypt-certificate-on-shared-hosting/ "Permalink to Install Lets Encrypt Certificate on Shared Hosting")

# Install Lets Encrypt Certificate on Shared Hosting

March 15, 2016 [ Nico Amarilla ][1] ![Install Lets Encrypt on Shared Host][2]

The gist here is to generate the Let's Encrypt certificate locally and install it to your server using cPanel. This is the exact steps I used for this domain.

## Things Needed

* **An OS to install and run the Let's Encrypt client**. In this example I will use Ubuntu 14.04 VM on Windows.
* **cPanel access**. Your cPanel should allow SSL certificates to be installed manually.
* **File upload access to the server**. I used SFTP.

### Installing the Let's Encrypt Client

1. Fire up the terminal and update Ubuntu. 
    
        sudo apt-get update

Then install letsenceypt client using git:
    
        git clone https://github.com/letsencrypt/letsencrypt

**Note:** If you don't have git installed, type: `sudo apt-get install git`

2. Once its done, go to the directory where the client is installed. For me its: cd letsencrypt

 

### Generating the Lets Encrypt Certificate

3. Inside the letsencrypt directory, type: 
    
        ./letsencrypt-auto certonly -a manual --rsa-key-size 4096 -d kosinix.com -d www.kosinix.com

**Note:** Make sure to change kosinix.com to the domain of your website.

4. The client will prompt you if its OK to log your IP. Choose Yes. Confirm IP logging ![][3]
5. Now, whatever happens, do not press anything yet or you will have to restart the generation process!

### Confirming Domain Ownership

Notice that below the prompt is an instruction on how to confirm ownership of your domain: Make sure your web server displays the following content at http_://kosinix.com/.well-known/acme-challenge/xuJ_RPQbkGG356A7WRya9JiJ59Lj1dWjS7OG40oQMOM before continuing: xuJ_RPQbkGG356A7WRya9JiJ59Lj1dWjS7OG40oQMOM.oUqbjO_-ZMUbmaC1JeWUVUrSB0ulSIDoHekYe_jffyk

The domain confirmation test
![][4]

6. Connect to your server and create the following directory inside your server's public directory: .well-known/acme-challenge/
7. Inside it create a text file named xuJ_RPQbkGG356A7WRya9JiJ59Lj1dWjS7OG40oQMOM that contains the string xuJ_RPQbkGG356A7WRya9JiJ59Lj1dWjS7OG40oQMOM.oUqbjO_-ZMUbmaC1JeWUVUrSB0ulSIDoHekYe_jffyk

**Note:** The strings will be different for you.

8. Once you are done, go back to the terminal and press the enter. LE will generate one more confirmation. Just repeat step number 6.

### Installing the Certificate in cPanel

cPanel SSL panel

![][5]

9. Type: 
    
        sudo nautilus

10. Using the file browser as root, navigate to `/etc/letsencrypt/live/kosinix.com`
11. Login to your cPanel. Copy and paste the SSL info from these files into the cPanel SSL panel: 
    
        Certificate (CRT) is cert.pem
    Private Key is privkey.pem
    Certificate Authority Bundle: (CABUNDLE) or CA is chain.pem

I used gedit to open and copy the contents of the .pem files.![][6]

Installing the certificate in cPanel

**Note:** You can also use the `cat` command to view text files in the terminal. Eg. `cat cert.pem`

## Bonus: Redirect all request to https

Add this in your .htaccess:
    
    
    # BEGIN Force SSL
    # This should be the first rule before other rules
    
        RewriteEngine On
    
        RewriteCond %{HTTPS} !=on
        RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
    
    # END Force SSL

## Additional Resources/References

* https://community.letsencrypt.org/t/tutorial-for-os-x-local-certificates-and-shared-hosting/6859
* https://wiki.apache.org/httpd/RewriteHTTPToHTTPS

[1]: https://www.kosinix.com/author/kosinix/
[2]: https://www.kosinix.com/wp-content/uploads/2016/03/le-featured.jpg
[3]: https://www.kosinix.com/wp-content/uploads/2016/03/le-confirm-ip.jpg
[4]: https://www.kosinix.com/wp-content/uploads/2016/03/le-domain-confirm.jpg
[5]: https://www.kosinix.com/wp-content/uploads/2016/03/le-cpanel-ssl.jpg
[6]: https://www.kosinix.com/wp-content/uploads/2016/03/le-cpanel-ssl-form.jpg
  
