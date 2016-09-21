An unmanaged extension to allow the neo4j user to create additional users.
More details are in the accompanying blog post.
Neo4j: Graphs for Everyone


How to build:
mvn clean install -Dlicense.skip=true -Dlicensing.skip=true

How are users managed?
org.neo4j.server.rest.dbms.UserService provides a JAX-RS endpoint at /user to expose two AuthManager operations, a GET of an AuthorizationRepresentation of a user by passing the username on the path. The password of a user can be set by POSTing to /{username}/password – this can only be done when the requesting principal matches the username.

HOW DO I ADD MORE USERS?
That is a very good question (avoiding the obviously dirty solution of directly modifying the data/dbms/auth file). As already mentioned the UserRepository has the ability to create a User, as used by the AuthManager newUser method, but where is this exposed?

Sadly the answer is that as far as I could tell it’s not at present, so is it a case of neo4j-shell & Groovy to the rescue?
Can we write a script that can obtain the protected AuthManager from the AbstractServer – nice idea in principle, but the shell uses RMI. It’s time for an unmanaged extension.

The code is available from GitHub here, in essence it adds a new REST endpoint to allow the neo4j user to creating other user accounts using a POST request to /useradd/{username} and including password=some_password within the payload.

You can build the extension using Maven’s package target, and copy the resultant jar file from target/neo4j-server-useradd-2.2.1.jar to the /plugins directory of the Neo4j server.

Additionally you’ll need to add the following line to conf/neo4j-server.properties:
org.neo4j.server.thirdparty_jaxrs_classes=org.neo4j.extension.server.unmanaged=/unmanaged




Neo4j Document:


Neo4j is the world’s leading Graph Database. It is a high performance graph store with all the features expected of a mature and robust database, like a friendly query language and ACID transactions. The programmer works with a flexible network structure of nodes and relationships rather than static tables — yet enjoys all the benefits of enterprise-quality database. For many applications, Neo4j offers orders of magnitude performance benefits compared to relational DBs.

Learn more on the Neo4j website.

Using Neo4j

Neo4j is available both as a standalone server, or an embeddable component. You can download or try online.

Extending Neo4j

We encourage experimentation with Neo4j. You can build extensions to Neo4j, develop library or drivers atop the product, or make contributions directly to the product core. You’ll need to sign a Contributor License Agreement in order for us to accept your patches.

For a quick overview of the components in this repository, you can read the RepositoryOverview.asciidoc.

Note
This GitHub repository contains mixed GPL and AGPL code. Our Community edition (in the community/ directory) is GPLv3. Our Enterprise edition (enterprise/) is differently licensed under the AGPLv3.
Dependencies

Neo4j is built using Apache Maven version 3.0.x and a recent version of supported VM. Bash and Make is also required. Note that maven needs more memory than the standard configuration, this can be achieved with export MAVEN_OPTS="-Xmx512m".

OSX users need to have Homebrew installed.

With brew on OSX

brew install graphviz libtool maven
With apt-get on Ubuntu

sudo apt-get install graphviz maven make openjdk-8-jdk devscripts debhelper rpm
Building Neo4j

A plain mvn clean install will only build the individual jar files.

Test execution is, of course, part of the build.

To add execution of integration tests, use: mvn clean install -DrunITs.

In case you just want the jars, without running tests, this is for you: mvn clean install -DskipTests.

To skip building the browser module, use -DskipBrowser (note that you might not be able to build the server without it).

To build everything, including running all tests, producing documentation and assembling product packages, use mvn clean install -DfullBuild.

To build the documentation see the Neo4j manual.

When building on Windows, use -Dlicensing.skip to avoid problems related to line endings.

The license header check can be skipped by appending the following to the command line: -Dlicense.skip=true.

If you are running into problems building on Windows you can try building Neo4j in a Ubuntu virtual machine.

You may need to increase the memory available to Maven: export MAVEN_OPTS="-Xmx512m".

Running Neo4j

After running a mvn clean install -DfullBuild cd into packaging/standalone/target and extract the version you want, then

bin/neo4j start
in the extracted folder to start Neo4j on localhost:7474. On Windows you want to run

bin\Neo4j.bat
instead.

Or, if you are not interested in the tarballs and just want to run Neo4j you can instead run a mvn clean install -DminimalBuild and then cd into community/server and run

mvn clean compile exec:java
to start Neo4j.

Licensing

Neo4j is an open source product. We support a Community edition under the GPLv3 license. The Enterprise edition is available under the AGPLv3 license for open source projects otherwise under a commercial license from Neo Technology.
