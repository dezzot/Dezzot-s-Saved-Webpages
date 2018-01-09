
[Source](https://www.juliogonzalez.es/lets-encrypt-ssl-certificates-at-cpanel-without-native-support-for-example-at-namecheap/352 "Permalink to Let’s encrypt SSL certificates at cPanel automatically and without native support (for example at Namecheap) « Julio González Gil")

Julio González Gil

# Let’s encrypt SSL certificates at cPanel automatically and without native support (for example at Namecheap)

Maybe you are already aware that [StartSSL][1] free certificates are no longer an option after [Google][2] and [Mozilla][3] decided to distrust [StartCom][4], and only certificates issued before October 21st will be valid, while any others issued after that date will provoke alerts at Google Chrome, Mozilla Firefox, and potentially any other software.

Fortunately, we have [Let's Encrypt][5] free certificates, if you are able to automate renewals (as the certificates expire after just three months).

Usually, this is not a problem if you control your systems. There are a lot of different solutions to automate renewals and installation, using python, bash and a lot of other different languages, and with good integration with several different services such as [Apache HTTP][6] or [nginx][7].

However playing with shared hosting as I do for some of my webpages, is a different matter.

If they support Let's Encrypt out of the box, then it will be a piece of cake. But If they do not (and my provider does not), maybe it won't be easy to configure all the stuff.

In my case, one of the providers is [Namecheap][8], an they decided not to integrate Let's Encrypt support into [cPanel][9] because (they say) [it requires a big amount of changes to their infrastructure][10].

I am happy enough with their service, given the features I get for the money I pay. So despite my first idea was to switch to another hosting, I decided to see if I could automate all the needed parts, to renew the certificate and uploaded it to cPanel.

After some time I was able to generate a [set of scripts][11] to use [acme.sh][12] to renew the certificates and install them when needed, so here is my small guide on how to configure everything end to end.

**1\. Get SSH access to your shared hosting.**

Usually you just need to contact your tech support and request it. Then at cPanel you need to configure your SSH public key, and then access with your preferred client.

**2\. Get and configure acme.sh**

[acme.sh][12] is a bash script able to use the [ACME protocol][13] to verify domain and subdomain ownership and create Let's Encrypt certificates.

The main benefit is that it does not need any external dependencies and, at least for me, it works at Namecheap without doing anything special.

I suggest you create a git folder outside your public_html to place the git repositories we are going to create, so…

> cd ~  
mkdir git  
cd git  
git clone https://github.com/Neilpang/acme.sh.git  
cd acme.sh

Then you need to configure acme, so it can verify ownership for the domains or subdomains that you will include at your certificate(s).

For my scripts to work, you should generate one certificate for each set of domain and subdomains. For example one for domain1.com, www.domain1.com, www2.domain1.com and another certificate for domain2.com, www.domain2.com, www2.domain2.com.

Make sure that the domain is the first instance of -d parameter when you call acme.sh

The method you decide to use for validation is up to you.

Personally I use DNS method, but keep in mind that you won't be able to use it if any of the subdomains is a CNAME (see [RFC1912][14] section 2.4 for reference). If you have this problem, try to use the webroot method.

At this point, you should check acme.sh help and documents, to make sure how to configure it.

**3\. Get and configure cp-installssl**

Now it is time to clone my scripts to automate everything.

> cd ~  
cd git  
git clone https://github.com/juliogonzalez/cp-installssl.git  
cd cp-installssl

Configure cp-installssl by copying the file .cp-installssl.ini to your home and changing the content:

> cp .cp-installssl.ini ~/  
edit ~/.cp-installssl.ini

Finally open the CRON configuration:

> crontab -e

And add a new line:

> 0 0 * * * ${HOME}/git/cp-installssl/wrapper/cp-installssl-wrapper -d '[@.,www.,www2.]domain1.com [@.,www.,www2.]domain2.com' -a ~/git/acme.sh/ -c ~/git/cp-installssl/ > /dev/null

Note that you will need to adjust the time, and the content of -d parameter, according to the domains and subdomains you have.

To see the format of -d parameter, have a look at cp-installssl-wrapper help:

> git/cp-installssl/wrapper/cp-installssl-wrapper -h

Now, for the most tech-savvy, notice that there are two scripts at my git repository

* _cp-installssl_ is a PHP script which takes care of installing SSL certificates to cPanel using its [JSON API][15]. It's based on [code][16] by [Rob Parham][17].
* _cp-installssl-wrapper_ is a BASH script will take care of calling acme.sh to renew the certificates when needed, and if a renewal happened will install them using _cp-installssl_

**4\. Test**

Just run the command you added at CRON, without the schedule and without '_> /dev/null_' so you can see the output.

**5. Enjoy!**

From this point my scripts will take care of trying to renew the SSL certificates with the schedule you configured at CRON.

Note that the certificates are only renewed when needed

 

 

 

 

[1]: https://www.startssl.com/
[2]: https://security.googleblog.com/2016/10/distrusting-wosign-and-startcom.html
[3]: https://blog.mozilla.org/security/2016/10/24/distrusting-new-wosign-and-startcom-certificates/
[4]: https://www.startcom.org/?lang=es
[5]: https://letsencrypt.org/
[6]: https://httpd.apache.org/
[7]: https://nginx.org/
[8]: https://www.namecheap.com/
[9]: https://www.cpanel.net
[10]: https://gist.github.com/stopspazzing/c871f935771f14933c63
[11]: https://github.com/juliogonzalez/cp-installssl
[12]: https://github.com/Neilpang/acme.sh
[13]: https://letsencrypt.github.io/acme-spec/
[14]: http://www.simpledns.com/rfc/rfc1912.txt
[15]: https://documentation.cpanel.net/display/SDK/Guide+to+cPanel+API+2
[16]: https://geneticcoder.blogspot.com.es/2014/07/using-cpanels-json-api-with-php-curl-to.html
[17]: https://plus.google.com/118182322818308613582

  
