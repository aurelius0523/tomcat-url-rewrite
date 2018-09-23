# Tomcat 8 URL Rewriting

This repository explores different setups for [URL Rewrite Valve](https://tomcat.apache.org/tomcat-8.0-doc/rewrite.html) introduced in Tomcat 8. This should not be confused with Apache Web Server's [mod_rewrite](https://httpd.apache.org/docs/current/mod/mod_rewrite.html). They are both essentially the same but instead of having a Apache Web Server in front of a Tomcat for url rewriting, Tomcat can now do it on its own.

# Checklist

1. Tomcat 8
2. Rewrite Valve enabled (global or local)
3. rewrite.config for url rewriting rules created (global or local)

# Basics

1. Rewrite Valve can be enabled on Tomcat globally by adding `org.apache.catalina.valves.rewrite.RewriteValve` to `$catalina_home/conf/context.xml` (This context.xml is automatically loaded for every application in the Tomcat)

   ```
   <Context>
       <Valve className="org.apache.catalina.valves.rewrite.RewriteValve"/>
   </Context>
   ```

2. It can also be enabled on a per-application basis by adding this `Valve` to the `Context` block in `$catalina_home/conf/server.xml`

```
      <Host appBase="webapps" autoDeploy="true" name="localhost" unpackWARs="true">

        <!-- SingleSignOn valve, share authentication between web applications
             Documentation at: /docs/config/valve.html -->
        <!--
        <Valve className="org.apache.catalina.authenticator.SingleSignOn" />
        -->

        <!-- Access log processes all example.
             Documentation at: /docs/config/valve.html
             Note: The pattern used is equivalent to using pattern="common" -->
        <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs" pattern="%h %l %u %t &quot;%r&quot; %s %b" prefix="localhost_access_log" suffix=".txt"/>

      <Context docBase="H:\tools\apache-tomcat-8.5.3\wtpwebapps\java-web-service-base" path="/Webservice" reloadable="true" source="org.eclipse.jst.jee.server:java-web-service-base"/>
      <Context docBase="H:\tools\apache-tomcat-8.5.3\wtpwebapps\spring-rest-api-backend" path="/spring-rest-api-backend" reloadable="true" source="org.eclipse.jst.jee.server:spring-rest-api-backend"/>
      </Host>
```

# Setup 1

## Different entry urls pointing to 1 application

```

```
