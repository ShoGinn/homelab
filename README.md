# proxmox
Here we will discuss how to setup your gmail relay so that you can receive e-mails during updates or just when a backup fails.

During setup I had already specified my e-mail as my server gmail (if you have not check under the users menu and check the root email address)

Also 

* Install libsasl2-modules
 
    `apt install libsasl2-modules`

* Backup your current postfix configuration

    `cp /etc/postfix/main.cf /etc/postfix/main.cf.bak`

* Modify your postfix configuration as follows:

Modify the line:

    relayhost = [smtp.gmail.com]:587

Add the following lines to the end:

    smtp_sender_dependent_authentication = yes

    sender_dependent_relayhost_maps = hash:/etc/postfix/sender_relayhost.hash
    smtp_sasl_auth_enable = yes

    smtp_sasl_password_maps = hash:/etc/postfix/sasl_auth.hash

    smtp_sasl_security_options = noanonymous

    smtp_use_tls = yes

    smtp_tls_CAfile = /etc/ssl/certs/ca-certificates.crt

* Create your authorization hash file:
    
   ` echo [smtp.gmail.com]:587 your_username@gmail.com:yourpassword > /etc/postfix/sasl_auth.hash`

* Create your sender_relayhost file (this makes sure that you always use your gmail as the sender:

    `echo your_username@gmail.com [smtp.gmail.com]:587 > /etc/postfix/sender_relayhost.hash`

<i>Make sure you always include the [] around the gmail servers</i>

* Now postmap the files

    postmap /etc/postfix/sender_relayhost.hash
    postmap /etc/postfix/sasl_auth.hash

* Make sure to make your password only readable by root

    `chmod 400 /etc/postfix/sasl_auth.*`

* Restart Postfix and you should be good

   `postfix reload`




