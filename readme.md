# The H5BP ant build script Zend Framework integration
[![Build Status](https://secure.travis-ci.org/muiku/h5bp-antbs-zendframework.png?branch=zfint)](http://travis-ci.org/muiku/h5bp-antbs-zendframework)

### Install

    mkdir my-zfproject/public/build
    cd my-zfproject/public/build
    wget --no-check-certificate https://github.com/muiku/h5bp-antbs-zendframework/tarball/v3.0-zfint -O - | tar -xz --strip 1
    
### Development

Just edit any file as you have done before:

- Put your styles into public/css/style.css
- Add some images under public/img/
- JavaScript goes into public/js/scripts.js
- etc. etc.

### Deployment (Apache 2)

#### Step 0. Preparation

Make sure that APPLICATION_PATH is correctly defined in index.php when under production.

    <?php      
        // Define application environment
        defined('APPLICATION_ENV') || define('APPLICATION_ENV',  	
            (getenv('APPLICATION_ENV') ? getenv('APPLICATION_ENV') : 'production'));
	
        // Define path to application directory
        defined('APPLICATION_PATH') || define('APPLICATION_PATH',
            realpath(dirname(__FILE__) . ((APPLICATION_ENV !== 'production') ? '' : '/..') . '/../application'));

        ...

#### Step 1. Run the H5BP build script

    cd public/build/
    ant # The H5BP build script will begin to run and compress your files.


#### Step 2. Change the site docroot to point to public/publish/

    <VirtualHost *:80>
        # ...

        SetEnv APPLICATION_ENV production

    	DocumentRoot /home/me/workspace/myproject/public/publish
        DirectoryIndex index.php

        <Directory /home/me/workspace/myproject/public/publish>
	    	AllowOverride All
		    Order allow,deny
    		Allow from all
	    </Directory>

        # ...
    </VirtualHost>

#### Step 3. Edit application.ini

Change the production layouts folder in application.ini

    [production]
    phpSettings.display_startup_errors = 0
    phpSettings.display_errors = 0
    includePaths.library = APPLICATION_PATH "/../library"
    bootstrap.path = APPLICATION_PATH "/Bootstrap.php"
    bootstrap.class = "Bootstrap"
    appnamespace = "Application"
    resources.frontController.controllerDirectory = APPLICATION_PATH "/controllers"
    resources.frontController.params.displayExceptions = 0

    ; Published (H5BP build script generated) layouts
    resources.layout.layoutPath = APPLICATION_PATH "/layouts/scripts/publish"

    ; Ensure that view encoding is UTF-8 and that view helpers use HTML5
    resources.view.encoding = "UTF-8"
    resources.view.doctype = "HTML5"

    [staging : production]

    [testing : production]
    phpSettings.display_startup_errors = 1
    phpSettings.display_errors = 1
    resources.layout.layoutPath = APPLICATION_PATH "/layouts/scripts"

    [development : production]
    phpSettings.display_startup_errors = 1
    phpSettings.display_errors = 1
    resources.frontController.params.displayExceptions = 1
    resources.layout.layoutPath = APPLICATION_PATH "/layouts/scripts"


## Requirements

Out of the box, the build script requires Java 1.6. 

Ant itself requires the Java JDK, version 1.4 or later. 1.5 or later is strongly recommended. 

Closure Compiler, our tool for script minification, requires Java 1.6. 

This means that OS X versions prior to 10.6 are no longer supported out of the box. 
[SoyLatte][soylatte] provides 10.4 and 10.5 builds of OpenJDK 7 for Intel OS X machines. However, only OS X 10.5 builds of OpenJDK 7 are available for PowerPC based Macs due to a bug in the 10.4 Compiler.  
( Be sure to read the Download link as the archives are password protected "to provide a click though agreement" of the JDK licensing. )

[soylatte]: http://landonf.bikemonkey.org/static/soylatte/ 

Alternatively, YUI Compressor, which requires Java > 1.4, could be swapped out for Closure Compiler.
