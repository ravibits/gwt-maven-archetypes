gwt-maven-archetypes
====================

This project contains Maven archetypes for modular GWT projects.

[![Build Status](https://secure.travis-ci.org/tbroyer/gwt-maven-archetypes.png?branch=master)](http://travis-ci.org/tbroyer/gwt-maven-archetypes)

How to use
----------

### Generate a project

    mvn archetype:generate \
       -DarchetypeCatalog=https://oss.sonatype.org/content/repositories/snapshots/ \
       -DarchetypeGroupId=net.ltgt.gwt.archetypes \
       -DarchetypeArtifactId=<artifactId> \
       -DarchetypeVersion=1.0-SNAPSHOT

where the available `<artifactIds>` are:

* `modular-webapp`
* `modular-requestfactory`
* `guice-rf-activities`

This uses the snapshot deployed to Sonatype OSS. Alternatively, and/or if you want to
hack on / contribute to the archetypes, you can clone and install the project locally:

    git clone https://github.com/tbroyer/gwt-maven-archetypes.git
    cd gwt-maven-archetypes && mvn clean install

You'll then use the `mvn archetype:generate` command from above, except for the
`-DarchetypeCatalog` argument which you'll remove, as you now want to use your local
catalog.

Note that tests are sensitive to line endings, so if you're on Windows make sure
your clone has Windows (CRLF) end of lines. In case it's not enough, you can simply
delete the `src/test` folder thus bypassing tests (provided the tests pass on the
continuous integration platform, it's not a big _risk_).


### Start the development mode

Change directory to your generated project and issue the following commands:

1. `mvn clean package -Dgwt.compiler.skip`
2. In one terminal window: `cd *-client && mvn gwt:run -Ddev`
3. In another terminal window: `mvn tomcat7:run -Ddev`

The same is available with `tomcat6` instead of `tomcat7`.

Or if you'd rather use Jetty than Tomcat:

1. `mvn clean install -Dgwt.compiler.skip`
2. In one terminal window: `cd *-server && mvn jetty:start -Ddev`
3. In another terminal window: `cd *-client && mvn gwt:run -Ddev`

### Profiles

There are two profiles defined in the POM file of client module:

* `dev` is to speed-up development with `gwt:run` and `jetty:start` by not
  requiring a restart when a change to the `${rootArtifactId}-shared` is made
* `draft` is to speed-up GWT compilation, and will only compile (by default)
  for WebKit-based browsers (Safari, Chrome)

To activate `dev` or `draft` you can provide the `-Ddev` or `-Ddraft` system
properties respectively, or use `-Pdev` or `-Pdraft`.

Note that the DevMode (`gwt:run`) won't work after a compilation made with the
`draft` profile. You'll have to `mvn clean` the project first and start over.

### Productivity tips

When working on the server-side code exclusively, you don't need GWT's DevMode.
You can then compile the GWT app using `mvn package` or `mvn package -Ddraft`
and then `mvn tomcat7:run` or `cd *-server && mvn jetty:start -Ddev`. The
webapp will be redeployed automatically when you change a class (either
compiled by your IDE, or by `mvn compile`) in either the
`${rootArtifactId}-server` or `${rootArtifactId}-shared` module (be careful
though when changing classes in `shared` that you do not break the GWT client
code, particularly when using GWT-RPC).

When working on the client-side code exclusively, to quickly test it in a
browser in production mode, use `mvn package -Ddraft`. You can use `mvn package
-Ddraft -pl :${rootArtifactId}-client -am` while the Tomcat or Jetty server is
running (launched by \mvn tomcat7:run` or `cd *-server && mvn jetty:start -Ddev`),
and then simply hit `F5` in your browser. Make sure you use a browser compatible
with the one declared in the
`${rootArtifactId}-client/src/main/java/${package}/${module}_dev.gwt.xml` file
(by default, Safari or Chrome).

Compatibility
-------------

To use variable interpolation in parameters during `mvn archetype:generate`,
you need at least version 2.2 of the maven-archetype-plugin. Archetypes use
`${module.toLowerCase()}` as the default value for the `module-short-name`
parameter, so if you don't use version 2.2 or above of the
maven-archetype-plugin, make sure you provide a value and do not use the
default one for that parameter. You can also make sure you use version 2.2 of
the plugin by using `mvn
org.apache.maven.plugins:maven-archetype-plugin:2.2:generate` instead of `mvn
archetype:generate`. It should be noted that variable interpolation also does
not work in M2Eclipse's wizard, despite using recent versions of Maven thus
(probably) a recent-enough version of the maven-archetype-plugin.
