Monolog Viewer 
==============
[![Build Status](https://travis-ci.org/Syonix/monolog-viewer.svg?branch=master)](https://travis-ci.org/Syonix/monolog-viewer)
[![Total Downloads](https://poser.pugx.org/syonix/monolog-viewer/downloads.png)](https://packagist.org/packages/syonix/monolog-viewer)
[![Latest Stable Version](https://poser.pugx.org/syonix/monolog-viewer/v/stable.png)](https://packagist.org/packages/syonix/monolog-viewer)
[![SensioLabsInsight](https://insight.sensiolabs.com/projects/c22e8c7d-c543-4e56-8f10-ad24ca14859f/mini.png)](https://insight.sensiolabs.com/projects/c22e8c7d-c543-4e56-8f10-ad24ca14859f)

A viewer to nicely display log files generated by [Monolog](https://github.com/Seldaek/monolog).

[![Screenshot](https://github.com/Syonix/monolog-viewer/raw/master/web/img/screenshot.png)](#installation)
*[Mockup Credits](https://dribbble.com/shots/994811-Apple-pack)*

# Installation & Setup
1. Download [Composer](http://getcomposer.org/)
1. Execute `php composer.phar create-project syonix/monolog-viewer install-directory/` (make sure to replace `install-directory` with the name of the directory you want to install monolg viewer into.
1. Upload the files to your webspace
1. Make sure PHP has write access to the directory `/app/config/secure` - This is where the password hash will be stored (see [below](#password-management)). Write access can be achieved by either changing the folder's chmod to 777 (less secure) or making sure that the owner of the file is the same as the one the server is using.
1. Create a configuration file (rename or copy the `config_example.yaml`).
1. Open Monolog Viewer in the browser
1. Enter a password of your choice twice and click "Create login".
1. Done. You can now log in and use your installation of Monolog Viewer

MonologViewer requires PHP 5.4 or higher. If you are using Apache, make sure to enable the `mod_rewrite` module.

For configuration under nginx please refer to [issue #14](https://github.com/Syonix/monolog-viewer/issues/14)

# Configuration
The config file is a YAML file containing the paths to your log files. Log files can be grouped. These groups are called clients, but you could also see them as sites or whatever makes sense to you.

To set up log files, make sure to fill in the config file following this structure:
```yaml
debug: false
timezone: "Europe/Zurich"
dateFormat: d.m.Y, H:i:s
logs:
    Demo:
        Demo-Log-File:
            type: local
            path: /path/to/your/monolog-viewer/installation/test/SyonixLogViewer/res/test.log
    Acme:
        Acme App:
            type: ftp
            host: acme.com
            username: logs
            password: $upersecur3
            path: app/logs/prod.log
```

The field `type` determines how the app will try to access the log file. For this, [Flysystem](http://flysystem.thephpleague.com/) is used. Currently implemented are `local` and `ftp`, but any other Flysystem Adapter can easily be added to the `Syonix\LogViewer\LogFile.php` constructor. Feel free to contribute.

**Note:** If your `config.yaml` is invalid or none of your log files are readable, Monolog Viewer will display an error message. Also, if a client does not contain any logs, the client will not be listed in the navigation. If your config file is invalid and you don't know why, check if [there are any tab characters in it](http://www.yaml.org/faq.html).

## Configuration values
The following configuration values are available:

Name | Type | Description
---- | ---- | -----------
`timezone` | `string` | Timezone string according to [PHP Manual](http://php.net/manual/en/timezones.php).
`dateFormat` | `string` | Date format for log entries according to [PHP Manual](http://php.net/manual/en/function.date.php).
`logs` | `array` | List of all log files, categorized by "clients" (see above)
`display_logger` | `boolean` | Turn log channel (logger) display in log view on or off

### Flysystem Types
##### `local`
Accesses log files on the server's local file system.

Config values | Description
------------- | -----------
`path` | The absolute file path. (You can use PHP's `realpath()` to get the absolute path of a file.)

##### `ftp`
Accesses log files on the server's local file system.

Config values | Description
------------- | -----------
`host` | The host to connect to (e.g. `ftp.acme.com`)
`username` | The ftp user to connect with
`password` | The ftp user's password.
`path` | The file path, relative to the FTP user's root directory.

##### All types

Config values | Description
------------- | -----------
`pattern` | A regex for a custom log line pattern.<br>For example if your log lines don't end in `[] {}` you could use `/\[(?P<date>.*)\] (?P<logger>\w+).(?P<level>\w+): (?P<message>[^\[\{]+)/`

# Password management
My goal was to keep this tool so simple, that it can be installed on any shared hosting. Therefore I decicded not to use a database to store the password. Instead it is saved within a folder that is protected by a `.htaccess` file and therefore not accessible by the public.

The only way to get the password hash is via FTP and even if someone gets the file, it is still hashed using PHPs `password_hash()` function. If you use a secure password, this is pretty safe.

Multiple users might be implemented in the future.

## Change password
To change your password, simply delete the `/app/config/secure/passwd` file and open Monolog Viewer to set a new password.

# Mobile devices
The App is fully optimized for tablets and smart phones and can even be installed to the home screen on iOS devices. It then works like a native app and features an app icon as well as a beautiful splash screen.

# Enforcing HTTPS
This should be done on a server configuration level. You could for example add this to the `.htaccess` file, right below `RewriteEngine On`:

```
RewriteCond %{HTTPS} !=on
RewriteRule ^ https://%{HTTP_HOST}%{REQUEST_URI} [L,R=301]
```
