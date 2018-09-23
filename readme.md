# Tomcat 8 URL Rewriting

This repository explores different setups for [URL Rewrite Valve](https://tomcat.apache.org/tomcat-8.0-doc/rewrite.html) introduced in Tomcat 8. This should not be confused with Apache Web Server's [mod_rewrite](https://httpd.apache.org/docs/current/mod/mod_rewrite.html). They are both essentially the same but instead of having a Apache Web Server in front of a Tomcat for url rewriting, Tomcat can now do it on its own.

# Why URL Rewriting

1. `Context` block in Tomcat does not support Regex
2. SPA application deployed in Tomcat needs to always be pointed to index.html inside `webapps/spa-application`, regardless of URL displayed in browser

# Checklist

1. Tomcat 8
2. Rewrite Valve enabled (global or per app)
3. rewrite.config for url rewriting rules created (global or per app)

# Basics

1. Rewrite Valve can be enabled on Tomcat globally by adding `org.apache.catalina.valves.rewrite.RewriteValve` to `$catalina_home/conf/context.xml` (This context.xml is automatically loaded for every application in the Tomcat)

   ```xml
   <Context>
       <Valve className="org.apache.catalina.valves.rewrite.RewriteValve"/>
   </Context>
   ```

2. It can also be enabled on a per-application basis by adding this `Valve` to the `Context` block in `$catalina_home/conf/server.xml`

   ```xml
         <Host appBase="webapps" autoDeploy="true" name="localhost" unpackWARs="true">

         <!-- Activating rewrite valve for AppOne only -->
         <Context docBase="H:\tools\apache-tomcat-8.5.3\webapps\applicationOne" path="/AppOne" reloadable="true" >
               <Valve className="org.apache.catalina.valves.rewrite.RewriteValve"/>
         </Context>
         <Context docBase="H:\tools\apache-tomcat-8.5.3\webapps\applicationTwo" path="/AppTwo" reloadable="true"/>
         </Host>
   ```

3. `rewrite.config` file will be read globally (on all contexts/applications) if it's created in `$catalina_home/webapps/ROOT/WEB-INF` or per-app if created in e.g. `$catalina_home/webapps/applicationOne/WEB-INF`.
   > Note: If `rewrite.config` is created on app-level, it will only start url-matching after the application's context. e.g. _http://localhost:8084/applicationOne/paths-after-this-will-be-matched_. On global level, _http://localhost:8084/anything-after-this-will-be-matched_

# Setup 1

## Different entry urls pointing to 1 application

Approach:

- Global level rewrite.config (assuming this tomcat has only 1 application)
- rewrite.config location: `$catalina_home/webapps/ROOT/WEB-INF`

```
RewriteCond %{REQUEST_URI} /path/two/(.*) [OR]
RewriteCond %{REQUEST_URI} /path/one/(.*)
RewriteRule ^/(path\/(one||two))/(.*)  /applicationOne/$3
```

Explanation:

- Two different possible URLs are matched
- If any of them matches, the path that's in `(.*)` will replace `$3` in RewriteRule
- e.g. `http://localhost:8084/path/one/dashboard?query=11` will be rewritten as `http://localhost:8084/applicationOne/dashboard?query=11`

Alternatively, `RewriteCond` can be skipped with this rule, both behaves similarly

```
RewriteRule ^/path/two/(.*) /applicationOne/$1
RewriteRule ^/path/one/(.*) /applicationOne/$1
```

# Setup 2

Different entry urls pointing to different applications

Approach

- Global level rewrite.config (necessary since it needs to be higher level than any application's context)
- rewrite.config location: `$catalina_home/webapps/ROOT/WEB-INF`

```
RewriteRule ^/path/one/ui/(.*)  /applicationTwo/$1
RewriteRule ^/path/one/(.*)  /applicationOne/$1
RewriteRule ^/path/two/(.*)  /applicationOne/$1
```

Explanation:

- Assumes backend application (`$catalina_home/webapps/applicationOne`) has multiple entry urls.
- Assumes ui application is an SPA deployed in the same Tomcat
- /path/one/ui has to be higher up in the rule order so that it does not get overridden by `RewriteRule` under it
