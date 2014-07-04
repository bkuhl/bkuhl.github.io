---
title: How to remotely manage an Amazon RDS instance with PHPMyAdmin
layout: post
date: 2010-12-10 16:55:16
categories: aws phpmyadmin
---

The biggest thing I was considering with Amazon Relational Database Service was how to manage it. A command line interface is NOT efficient for database management so I needed to be sure that I would be able to utilize software on my computer to manage my data.  PHPMyAdmin was my software of choice.  It supports multiple servers and has pretty much everything I need.

So what does it take to get everything up and running? First, [signup for Amazon RDS](http://aws.amazon.com/rds/) and get your instance up and running.  [Amazon’s RDS Getting Started Guide](http://docs.amazonwebservices.com/AmazonRDS/latest/GettingStartedGuide/) is a great resource and I’d highly recommend it.  The one thing I had trouble with was the DB Security Group setup.  When you go to add access for an CIDR/IP it provides a recommended value.  It took some messing around to determine that this default value isn’t actually what needed to be there.  _If you’re not able to connect to your instance when it’s all said and done, be sure to double check this value*.  The IP they provided did not match the IP address that was provided to us by our ISP.  Once you’ve created your DB Instance and setup the security group you’re good to go.

I’m going to assume you’ve already got PHPMyAdmin up and running.  What you need to do is modify **config.inc.php** to recognize the new server.  Your config file should look something like this:

```
/* Configure according to dbconfig-common if enabled */
if (!empty($dbname)) {
    /* Authentication type */
    $cfg['Servers'][$i]['auth_type'] = 'config';
    $cfg['Servers'][$i]['user'] = 'root';
    $cfg['Servers'][$i]['password'] = 'changeme';
    $cfg['Servers'][$i]['hide_db'] = '(mysql|information_schema|phpmyadmin)';
    /* Server parameters */
    if (empty($dbserver)) $dbserver = 'localhost';
    $cfg['Servers'][$i]['host'] = $dbserver;

    if (!empty($dbport)) {
        $cfg['Servers'][$i]['connect_type'] = 'tcp';
        $cfg['Servers'][$i]['port'] = $dbport;
    }
    //$cfg['Servers'][$i]['compress'] = false;
    /* Select mysqli if your server has it */
    $cfg['Servers'][$i]['extension'] = 'mysqli';
    /* Optional: User for advanced features */
    //$cfg['Servers'][$i]['controluser'] = $dbuser;
    //$cfg['Servers'][$i]['controlpass'] = $dbpass;
    /* Optional: Advanced phpMyAdmin features */
    $cfg['Servers'][$i]['pmadb'] = $dbname;
    $cfg['Servers'][$i]['bookmarktable'] = 'pma_bookmark';
    $cfg['Servers'][$i]['relation'] = 'pma_relation';
    $cfg['Servers'][$i]['table_info'] = 'pma_table_info';
    $cfg['Servers'][$i]['table_coords'] = 'pma_table_coords';
    $cfg['Servers'][$i]['pdf_pages'] = 'pma_pdf_pages';
    $cfg['Servers'][$i]['column_info'] = 'pma_column_info';
    $cfg['Servers'][$i]['history'] = 'pma_history';
    $cfg['Servers'][$i]['designer_coords'] = 'pma_designer_coords';

    /* Uncomment the following to enable logging in to passwordless accounts,
     * after taking note of the associated security risks. */
    // $cfg['Servers'][$i]['AllowNoPassword'] = TRUE;

    /* Advance to next server for rest of config */
    $i++;
}
```

PHPMyAdmin uses **$cfg['Servers'][$i]** so that it can support multiple servers on one installation.  Having more than 1 server will give you the option to select a server when you login.  After that last } you’ll want to add the following code, but of course with your own Amazon RDS instance URL.

```
$cfg['Servers'][$i]['auth_type'] = 'HTTP';
    $cfg['Servers'][$i]['hide_db'] = '(mysql|information_schema|phpmyadmin)';
    /* Server parameters */
    $cfg['Servers'][$i]['host'] = 'xxxxx.l2kj35ncj3.us-east-1.rds.amazonaws.com';
```

You’re ready to go, simply refresh your PHPMyAdmin page if you’re already logged in and you’ll see the new server.

Remember, if you have trouble connecting, your IP/Permissions must be wrong!