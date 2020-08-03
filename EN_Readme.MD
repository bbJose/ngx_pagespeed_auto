# Use Nginx_Pagespeed under the pagoda panel to accelerate the front end of the website

## Introduction:
  I have encountered a website optimization problem these days. The boss needs to speed up the website, but the thinkphp3.1.1 version used by our website backend does not support any cache plug-ins, so the php cache can be excluded.
        
  After thinking about it, I think of a Nginx module I have learned before: PageSpeed, which can speed up the front end of the website, especially suitable for the situation where there are a lot of pictures and requests from the homepage of our website.
        
  After referring to the installation document provided by ngx_pagespeed, it is found that the provided automatic installation script is not applicable to LNMP panel, pagoda panel, OneInStack and other one-click installation LNMP environments. Since our website is built and maintained through the pagoda panel, I was worried at first (for fear that these one-click environments will not be able to control Nginx after recompilation). After exploring, I discovered that installing this script on the pagoda is better than on the LNMP panel. Installation is easier!

## Introduction to Nginx_PageSpeed ​​Module
  ngx_pagespeed is an Nginx extension module developed by Google. In fact, this module was developed very early, but there were always bugs before. It has been in the beta version, and the stable version is only available in 16 years (suspect).
    
ngx_pagespeed official website: [http://ngxpagespeed.com/](http://ngxpagespeed.com/)
Project Github homepage: [https://github.com/pagespeed/ngx_pagespeed](https://github.com/pagespeed/ngx_pagespeed)
GoogleDevelopers: [https://developers.google.com/speed/docs/mod_pagespeed](https://developers.google.com/speed/docs/mod_pagespeed/build_ngx_pagespeed_from_source)

### The main function

`*`Image optimization: strip metadata, dynamically adjust, and recompress
`*`CSS and JavaScript compression, merge, cascade, inline
`*` Small resource inline
`*`Defer image and JavaScript loading
`*` Rewrite HTML, compress spaces, remove comments, etc.
`*` Improve the cache cycle
`*`and so on...


## Module installation:

### lab environment

**Operating System:** Debian8 64-bit
**Management Panel:** Pagoda 5.9 Free Edition
**Environment: **nginx 1.14 compile and install

### Installation process
`*`**1. Preparation before installation**
  * Check current status: system & root authority & GCC version (>=4.8)
  * Update yum/apt source & dependencies required to install pagespeed
  * Increase swap space (many small memory VPS will have compilation crash caused by insufficient memory)

`*`**2. Download module**
  * Download the ngx_pagespeed module
  * Download psol (module optimization management)

`*`**Three. Install the module**
  * Get the current nginx configuration file
  * Add the ngx_pagespeed module in the configuration
  * make & make install

`*`**Four. Verification Module**
  * Restart nginx (reloading configuration does not work, you must restart)
  * Verify that the module is running

Based on the above manual installation process, I wrote an **automatic installation shell**, which can be installed with one line of command:

```shell
 wget https://raw.githubusercontent.com/madlifer/ngx_pagespeed_auto/master/nps-auto.sh && bash nps-auto.sh
```

## Module configuration
  Since the adopted nginx version is 1.14, it does not seem to support dynamic modules, so there is no need to write dynamic modules. Paste the required function commands in the server section of the nginx configuration document of the website directly, and then reload the Nginx configuration.

```TXT
# Enable ngx_pagespeed    
pagespeed on;    
pagespeed FileCachePath /tmp/cache/ngx_pagespeed_cache;    
# Disable CoreFilters    
pagespeed RewriteLevel PassThrough;    
# Enable compressed blank filter    
pagespeed EnableFilters collapse_whitespace;    
# Enable JavaScript library uninstall    
pagespeed EnableFilters canonicalize_javascript_libraries; #Google is walled, not sure if this setting has side effects 
# Combine multiple CSS files into one CSS file    
pagespeed EnableFilters combine_css;    
# Combine multiple JavaScript files into one JavaScript file    
pagespeed EnableFilters combine_javascript;    
# Delete tags with default attributes    
pagespeed EnableFilters elide_attributes;    
# Improve the cacheability of resources    
pagespeed EnableFilters extend_cache;    
# Replace @import of imported files, streamline CSS files    
pagespeed EnableFilters flatten_css_imports;    
pagespeed CssFlattenMaxBytes 5120;    
# Delay loading pictures invisible to the client    
pagespeed EnableFilters lazyload_images;    
# Enable JavaScript minification mechanism    
pagespeed EnableFilters rewrite_javascript;    
# Enable image optimization mechanism    
pagespeed EnableFilters rewrite_images;    
# Pre-resolved DNS query    
pagespeed EnableFilters insert_dns_prefetch;    
# Rewrite CSS, first load the CSS rules for rendering the page    
pagespeed EnableFilters prioritize_critical_css; 

```
