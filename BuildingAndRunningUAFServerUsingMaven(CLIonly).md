# Building and Running UAF Server Using Maven (CLI only)

This guide will help you out in setting up the FIDO UAF Server, in order to have it up and running locally on a Unix based system. For the sake of having a real life platform example I will describe the process using a Mac OS X system running El Capitan 10.11.5. But it should be fairly identical on a Unix/Linux system.

## Prerequisites
Make sure that you have installed:

- **Java SDK** (installed and java binary lookup in the PATH)

On Linux (Debian/Ubuntu), installing Git client is quite trivial:
```bash
$ sudo apt-get update
$ sudo apt-get install default-jdk
```

```bash
$ which java
/usr/bin/java

$ java -version
java version "1.8.0_91"
Java(TM) SE Runtime Environment (build 1.8.0_91-b14)
Java HotSpot(TM) 64-Bit Server VM (build 25.91-b14, mixed mode)
```

```bash
$ /usr/libexec/java_home -V
Matching Java Virtual Machines (1):
    1.8.0_91, x86_64:	"Java SE 8"	/Library/Java/JavaVirtualMachines/jdk1.8.0_91.jdk/Contents/Home

/Library/Java/JavaVirtualMachines/jdk1.8.0_91.jdk/Contents/Home
```

- **Git client**

Git client is installed by default on Mac OS X

```bash
$ which git
/usr/bin/git

$ git --version
git version 2.7.4 (Apple Git-66)
```

On Linux (Debian/Ubuntu), installing Git client is quite trivial:

```bash
$ sudo apt-get update
$ sudo apt-get install git
```

- **Maven**

Maven relies on Java, so before installing Maven make sure Java is enabled.

```bash
$ mvn -version
Apache Maven 3.3.9 (bb52d8502b132ec0a5a3f4c09453c07478323dc5; 2015-11-10T17:41:47+01:00)
Maven home: /opt/local/share/java/maven3
Java version: 1.8.0_91, vendor: Oracle Corporation
Java home: /Library/Java/JavaVirtualMachines/jdk1.8.0_91.jdk/Contents/Home/jre
Default locale: en_US, platform encoding: UTF-8
OS name: "mac os x", version: "10.11.5", arch: "x86_64", family: "mac"
```

Installing Maven using MacPorts (OSX) and selecting Maven3 as default:

```bash
$ sudo port -v install maven3 -universal
$ sudo port select --set maven maven3
Selecting 'maven3' for 'maven' succeeded. 'maven3' is now active.
$ which mvn
/opt/local/bin/mvn
```

Installing Maven using Homebrew (OSX):

```bash
$ brew install maven
```

Installing Maven on Linux (Debian/Ubuntu):

```bash
$ sudo apt-get update
$ sudo apt-get install maven
```

Installing Maven from source: [https://maven.apache.org/install.html]()

- **Tomcat** (we will use latest release of version 8 - i.e. 8.5.68)

Download the bits from: [https://downloads.apache.org/tomcat/tomcat-8/]() <br>
*Note:* Depending on your locale and IP address you might experience different mirror URLs.

_-1- Grab the binaries (using wget or curl), extract the archive, and optionaly rename the Tomcat root directory:_

```bash
$ mkdir ~/opt
$ cd ~/opt
$ wget wget https://downloads.apache.org/tomcat/tomcat-8/v8.5.68/bin/apache-tomcat-8.5.68.tar.gz
$ tar xvzf apache-tomcat--8.5.68.tar.gz
$ rm apache-tomcat--8.5.68.tar.gz
$ mv apache-tomcat-8.5.68 tomcat8
```

_-2- Start the Tomcat server:_

```bash
$ cd tomcat8/bin
$ ./startup.sh
Using CATALINA_BASE:   /Users/ubik/opt/tomcat8
Using CATALINA_HOME:   /Users/ubik/opt/tomcat8
Using CATALINA_TMPDIR: /Users/ubik/opt/tomcat8/temp
Using JRE_HOME:        /Library/Java/JavaVirtualMachines/jdk1.8.0_91.jdk/Contents/Home
Using CLASSPATH:       /Users/ubik/opt/tomcat8/bin/bootstrap.jar:/Users/ubik/opt/tomcat8/bin/tomcat-juli.jar
Tomcat started.
```

_-3- Check the server is running (by default Tomcat listens on HTTP port 8080. See conf/server.xml):_

```bash 
$ netstat -tuanp tcp | grep 8080
tcp46      0      0  *.8080                 *.*                    LISTEN

$ grep 8080 ../conf/server.xml
         Define a non-SSL/TLS HTTP/1.1 Connector on port 8080
    <Connector port="8080" protocol="HTTP/1.1"
               port="8080" protocol="HTTP/1.1"
```               

_-4- For the time being stop the Tomcat server:_

```bash
$ ./shutdown.sh
Using CATALINA_BASE:   /Users/ubik/opt/tomcat8
Using CATALINA_HOME:   /Users/ubik/opt/tomcat8
Using CATALINA_TMPDIR: /Users/ubik/opt/tomcat8/temp
Using JRE_HOME:        /Library/Java/JavaVirtualMachines/jdk1.8.0_91.jdk/Contents/Home
Using CLASSPATH:       /Users/ubik/opt/tomcat8/bin/bootstrap.jar:/Users/ubik/opt/tomcat8/bin/tomcat-juli.jar
```

## Get the Code
Recommended way is to fork the repo first: [https://github.com/eBay/UAF](https://github.com/eBay/UAF).

Then use git client to clone the forked repo in your local file system. This will get you [all projects code](https://github.com/eBay/UAF#implementation-details).

In the example below, I just did a clone of the repository on a local directory (no previous fork). In order to be able to directly push modifications to the master branch.

```bash
$ cd ~/opt
$ git clone https://alain2sf@github.com/eBay/UAF.git
Cloning into 'UAF'...
remote: Counting objects: 834, done.
remote: Compressing objects: 100% (13/13), done.
remote: Total 834 (delta 1), reused 0 (delta 0), pack-reused 818
Receiving objects: 100% (834/834), 2.66 MiB | 618.00 KiB/s, done.
Resolving deltas: 100% (344/344), done.
Checking connectivity... done.

$ ls -l
drwxr-xr-x  13 ubik  staff  442 May 12 10:01 UAF
drwxr-xr-x  14 ubik  staff  476 May 12 12:01 tomcat8

$ ls -al UAF
total 72
drwxr-xr-x  13 ubik  staff    442 May 12 10:01 .
drwxr-xr-x   3 ubik  staff    102 May 12 10:01 ..
drwxr-xr-x  13 ubik  staff    442 May 12 10:01 .git
-rw-r--r--   1 ubik  staff     40 May 12 10:01 .gitignore
-rw-r--r--   1 ubik  staff     15 May 12 10:01 .travis.yml
-rw-r--r--   1 ubik  staff  11355 May 12 10:01 LICENSE
-rw-r--r--   1 ubik  staff   1895 May 12 10:01 README.md
-rw-r--r--   1 ubik  staff   2875 May 12 10:01 RELEASE.md
-rw-r--r--   1 ubik  staff     33 May 12 10:01 build.gradle
drwxr-xr-x  11 ubik  staff    374 May 12 10:01 fido-uaf-core
drwxr-xr-x   7 ubik  staff    238 May 12 10:01 fidouaf
drwxr-xr-x   8 ubik  staff    272 May 12 10:01 fidouafclient
-rw-r--r--   1 ubik  staff     93 May 12 10:01 settings.gradle

$ ls UAF/fido-uaf-core/
FAQ.md              README.md           favorites.xml       pom.xml
LICENSE.md          build.gradle        findbugsExclude.xml src

$ ls UAF/fidouaf
README.md    build.gradle pom.xml      src
```

## Building "fido-uaf-core" project from CLI

Change directory to the 'fido-uaf-core' root folder.

```
$ cd ~/opt/UAF/fido-uaf-core
```

Using Maven CLI command, cleanup the target folder (useless the first time), and build/package/install the "fido-uaf-core" project.

```bash
### mvn clean; mvn package; mvn install ###
$ mvn clean install
..
..
..
Results : Tests run: 66, Failures: 0, Errors: 0, Skipped: 0

[INFO]
[INFO] --- jacoco-maven-plugin:0.6.3.201306030806:report (post-unit-test) @ fido-uaf-core ---
[INFO] Skipping JaCoCo execution due to missing execution data file
[INFO]
[INFO] --- maven-jar-plugin:2.4:jar (default-jar) @ fido-uaf-core ---
[INFO] Building jar: /Users/ubik/opt/UAF/fido-uaf-core/target/fido-uaf-core-0.0.1-SNAPSHOT.jar
[INFO]
[INFO] --- maven-install-plugin:2.4:install (default-install) @ fido-uaf-core ---
[INFO] Installing /Users/ubik/opt/UAF/fido-uaf-core/target/fido-uaf-core-0.0.1-SNAPSHOT.jar to /Users/ubik/.m2/repository/org/ebayopensource/fido-uaf-core/0.0.1-SNAPSHOT/fido-uaf-core-0.0.1-SNAPSHOT.jar
[INFO] Installing /Users/ubik/opt/UAF/fido-uaf-core/pom.xml to /Users/ubik/.m2/repository/org/ebayopensource/fido-uaf-core/0.0.1-SNAPSHOT/fido-uaf-core-0.0.1-SNAPSHOT.pom
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 5.563 s
[INFO] Finished at: 2016-05-19T12:15:31+02:00
[INFO] Final Memory: 21M/219M
[INFO] ------------------------------------------------------------------------
```

If you can see the 'BUILD SUCCESS' info message at the end of the process, it means all went hunky-dory. You should have a new 'target' directory:

```
$ ls -l target
drwxr-xr-x   3 ubik  staff    102 May 19 12:15 classes
-rw-r--r--   1 ubik  staff  80008 May 19 12:15 fido-uaf-core-0.0.1-SNAPSHOT.jar
drwxr-xr-x   3 ubik  staff    102 May 19 12:15 maven-archiver
drwxr-xr-x   3 ubik  staff    102 May 19 12:15 maven-status
drwxr-xr-x  54 ubik  staff   1836 May 19 12:15 surefire-reports
drwxr-xr-x   3 ubik  staff    102 May 19 12:15 test-classes
```

The 'target' directory contains the latest project built materials, more specifically the JAR file 'fido-uaf-core-0.0.1-SNAPSHOT.jar' that the "fidouaf" build process will use (see "fidouaf" pom.xml file).

```bash
$ cat ../fidouaf/pom.xml
...
                <dependency>
                        <groupId>org.ebayopensource</groupId>
                        <artifactId>fido-uaf-core</artifactId>
                        <version>0.0.1-SNAPSHOT</version>
                </dependency>
...
```

Actually this project target directory will not be directly used during the "fidouaf" project build process, as a matter of fact the 'mvn install' phase has compiled, packaged (JAR) and installed the target materials into the default Maven local repository (~/.m2). 

To decompose this into 2 steps you can first perform a 'mvn package' (compile classes and package a JAR, then push to project target directory), then 'mvn install' (push to Maven repository, under ~/.m2 directory).

```bash
$ ls -l /Users/ubik/.m2/repository/org/ebayopensource/fido-uaf-core/0.0.1-SNAPSHOT/
total 184
-rw-r--r--  1 ubik  staff    204 May 19 12:15 _remote.repositories
-rw-r--r--  1 ubik  staff  80008 May 19 12:15 fido-uaf-core-0.0.1-SNAPSHOT.jar
-rw-r--r--  1 ubik  staff   2848 May 12 09:57 fido-uaf-core-0.0.1-SNAPSHOT.pom
-rw-r--r--  1 ubik  staff    717 May 19 12:15 maven-metadata-local.xml
```

That being precised, you are now good to go to perform the second build process, the "fidouaf" project.

## Building "fidouaf" project from CLI
Change directory to the 'fidouaf' root folder.

```
$ cd ~/opt/UAF/fidouaf
```

Using Maven CLI command, cleanup the target folder (useless the first time), and build/package/install the "fidouaf" project.

```bash
### mvn clean; mvn package; mvn install ###
$ mvn clean install
[INFO] Scanning for projects...
[INFO]
[INFO] ------------------------------------------------------------------------
[INFO] Building fidouaf 0.0.1-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO]
[INFO] --- maven-clean-plugin:2.5:clean (default-clean) @ fidouaf ---
[INFO] Deleting /Users/ubik/opt/UAF/fidouaf/target
[INFO]
[INFO] --- maven-resources-plugin:2.6:resources (default-resources) @ fidouaf ---
[WARNING] Using platform encoding (UTF-8 actually) to copy filtered resources, i.e. build is platform dependent!
[INFO] skip non existing resourceDirectory /Users/ubik/opt/UAF/fidouaf/src/main/resources
[INFO]
[INFO] --- maven-compiler-plugin:3.1:compile (default-compile) @ fidouaf ---
[INFO] Changes detected - recompiling the module!
[WARNING] File encoding has not been set, using platform encoding UTF-8, i.e. build is platform dependent!
[INFO] Compiling 17 source files to /Users/ubik/opt/UAF/fidouaf/target/classes
[INFO]
[INFO] --- maven-resources-plugin:2.6:testResources (default-testResources) @ fidouaf ---
[WARNING] Using platform encoding (UTF-8 actually) to copy filtered resources, i.e. build is platform dependent!
[INFO] skip non existing resourceDirectory /Users/ubik/opt/UAF/fidouaf/src/test/resources
[INFO]
[INFO] --- maven-compiler-plugin:3.1:testCompile (default-testCompile) @ fidouaf ---
[INFO] Changes detected - recompiling the module!
[WARNING] File encoding has not been set, using platform encoding UTF-8, i.e. build is platform dependent!
[INFO] Compiling 1 source file to /Users/ubik/opt/UAF/fidouaf/target/test-classes
[INFO]
[INFO] --- maven-surefire-plugin:2.12.4:test (default-test) @ fidouaf ---
[INFO] Surefire report directory: /Users/ubik/opt/UAF/fidouaf/target/surefire-reports

-------------------------------------------------------
 T E S T S
-------------------------------------------------------
Running org.ebayopensource.fidouaf.res.util.StorageImplTest
Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.039 sec

Results :

Tests run: 1, Failures: 0, Errors: 0, Skipped: 0

[INFO]
[INFO] --- maven-war-plugin:2.2:war (default-war) @ fidouaf ---
[INFO] Packaging webapp
[INFO] Assembling webapp [fidouaf] in [/Users/ubik/opt/UAF/fidouaf/target/fidouaf-0.0.1-SNAPSHOT]
[INFO] Processing war project
[INFO] Copying webapp resources [/Users/ubik/opt/UAF/fidouaf/src/main/webapp]
[INFO] Webapp assembled in [88 msecs]
[INFO] Building war: /Users/ubik/opt/UAF/fidouaf/target/fidouaf-0.0.1-SNAPSHOT.war
[INFO] WEB-INF/web.xml already added, skipping
[INFO]
[INFO] --- maven-install-plugin:2.4:install (default-install) @ fidouaf ---
[INFO] Installing /Users/ubik/opt/UAF/fidouaf/target/fidouaf-0.0.1-SNAPSHOT.war to /Users/ubik/.m2/repository/org/ebayopensource/fidouaf/0.0.1-SNAPSHOT/fidouaf-0.0.1-SNAPSHOT.war
[INFO] Installing /Users/ubik/opt/UAF/fidouaf/pom.xml to /Users/ubik/.m2/repository/org/ebayopensource/fidouaf/0.0.1-SNAPSHOT/fidouaf-0.0.1-SNAPSHOT.pom
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 1.981 s
[INFO] Finished at: 2016-05-19T12:19:15+02:00
[INFO] Final Memory: 23M/310M
[INFO] ------------------------------------------------------------------------
```

If you can see the 'BUILD SUCCESS' info message at the end of the process, it means all went hunky-dory. You should have a new 'target' directory:

```bash
$ ls -l target
drwxr-xr-x  3 ubik  staff      102 May 19 12:19 classes
drwxr-xr-x  4 ubik  staff      136 May 19 12:19 fidouaf-0.0.1-SNAPSHOT
-rw-r--r--  1 ubik  staff  6238350 May 19 12:19 fidouaf-0.0.1-SNAPSHOT.war
drwxr-xr-x  3 ubik  staff      102 May 19 12:19 maven-archiver
drwxr-xr-x  3 ubik  staff      102 May 19 12:19 maven-status
drwxr-xr-x  4 ubik  staff      136 May 19 12:19 surefire-reports
drwxr-xr-x  3 ubik  staff      102 May 19 12:19 test-classes
```

The 'target' directory contains the latest project built materials, more specifically the WAR file 'fidouaf-0.0.1-SNAPSHOT.war' that has to be deployed to the Tomcat server. Actually this WAR file was also pushed to the Maven local repository under ~/.m2 during the 'mvn install'.

To decompose this into 2 steps you can first perform a 'mvn package' (compile classes and package a WAR, then push to project target directory), then 'mvn install' (push to Maven repository, under ~/.m2 directory).

We should always use the Maven local repository (~/.m2) copy of the WAR file to perform the application deployment to the Tomcat server.

Double checking the Maven local repository under ~/.m2 we can verify that the "fidouaf" project build has effectively pushed the final WAR file (fidouaf-0.0.1-SNAPSHOT.war).

```bash
$ tree ~/.m2/repository/org/ebayopensource
/Users/ubik/.m2/repository/org/ebayopensource
├── fido-uaf-core
│   ├── 0.0.1-SNAPSHOT
│   │   ├── _remote.repositories
│   │   ├── fido-uaf-core-0.0.1-SNAPSHOT.jar
│   │   ├── fido-uaf-core-0.0.1-SNAPSHOT.pom
│   │   └── maven-metadata-local.xml
│   └── maven-metadata-local.xml
└── fidouaf
    ├── 0.0.1-SNAPSHOT
    │   ├── _remote.repositories
    │   ├── fidouaf-0.0.1-SNAPSHOT.pom
    │   ├── fidouaf-0.0.1-SNAPSHOT.war
    │   └── maven-metadata-local.xml
    └── maven-metadata-local.xml

4 directories, 10 files
```

## Deploy the FIDO UAF application (WAR) to Tomcat

Change directory to the "fidouaf" build folder into the Maven local repository:

```bash
$ cd ~/.m2/repository/org/ebayopensource/fidouaf/0.0.1-SNAPSHOT
```

If it wasn't done, stop the Tomcat server and copy/rename the "fidouaf" WAR file to the Tomcat server application directory:

```bash
$ ~/opt/tomcat8/bin/shutdown.sh

$ cp fidouaf-0.0.1-SNAPSHOT.war   ~/opt/tomcat8/webapps/fidouaf.war
```
Change directory to the Tomcat server root folder, check the WAR file is present:

```bash
$ cd ~/opt/tomcat8

$ ls -l webapps
drwxr-xr-x  19 ubik  staff      646 Mar 18 20:32 ROOT
drwxr-xr-x  55 ubik  staff     1870 Mar 18 20:32 docs
drwxr-xr-x   7 ubik  staff      238 Mar 18 20:32 examples
-rw-r--r--   1 ubik  staff  6238350 May 19 12:23 fidouaf.war
drwxr-xr-x   7 ubik  staff      238 Mar 18 20:32 host-manager
drwxr-xr-x   8 ubik  staff      272 Mar 18 20:32 manager
```

_Note:_ If you rebuild the FIDO UAF application you will have to re-deploy the latest resultant WAR file. Although not mandatory for the following deployments of the 'fidouaf' WAR file, I recommend to remove the “fidouaf” folder that was previously created. Therefore after stopping the Tomcat server and before restarting it again, do a recursive 'rm' of the 'fidouaf' directory.

```bash
$ rm -rf webapps/fidouaf
```

Restart the Tomcat server to acieve the WAR file deployment:

```bash
$ ./bin/startup.sh

$ ls -l webapps
drwxr-xr-x  19 ubik  staff      646 Mar 18 20:32 ROOT
drwxr-xr-x  55 ubik  staff     1870 Mar 18 20:32 docs
drwxr-xr-x   7 ubik  staff      238 Mar 18 20:32 examples
drwxr-xr-x   4 ubik  staff      136 May 19 12:26 fidouaf
-rw-r--r--   1 ubik  staff  6238350 May 19 12:23 fidouaf.war
drwxr-xr-x   7 ubik  staff      238 Mar 18 20:32 host-manager
drwxr-xr-x   8 ubik  staff      272 Mar 18 20:32 manager
```

As you can verify above, the WAR file was deployed as we now have a 'fidouaf' folder (WAR file was expanded). You can verify the WAR file deployment process from the Tomcat log file. 

```bash
$ cat ../logs/catalina.out
...
19-May-2016 15:11:29.775 INFO [localhost-startStop-1] org.apache.catalina.startup.HostConfig.deployWAR Deploying web application archive /Users/ubik/opt/tomcat8/webapps/fidouaf.war
...
```

## Sanity tests using CLI commands

* Get UAF Server history list:
 
```bash
$ curl -s http://localhost:8080/fidouaf/v1/history | python -m json.tool
[]
```

> You should get an empty JSON object list as a response:
```
[]
```

* Get UAF Server parameters:

```bash
$ curl -s http://localhost:8080/fidouaf/v1/public/regRequest/TestUserNamePathParam | python -m json.too
[
    {
        "challenge": "JDJhJDEwJHhUMnZLNEpPQVVZL3ducTduQktpZ08",
        "header": {
            "appID": "http://localhost:8080/fidouaf/v1/public/uaf/facets",
            "op": "Reg",
            "serverData": "YVE0UHAtTVdQTm9lZUtqQnFVcGxzZDNOb2dMbUNYMXB6YjJXUk1WLW5SNC5NVFEyTXpjMU1UUTJOalkwT0EuVkdWemRGVnpaWEpPWVcxbFVHRjBhRkJoY21GdC5Ta1JLYUVwRVJYZEtTR2hWVFc1YVRFNUZjRkJSVmxaYVRETmtkV05VWkhWUmEzUndXakE0",
            "upv": {
                "major": 1,
                "minor": 0
            }
        },
        "policy": {
            "accepted": [
                [
                    {
                        "aaid": [
                            "EBA0#0001"
                        ]
                    }
                ],
                [
                    {
                        "aaid": [
                            "0015#0001"
                        ]
                    }
                ],
                [
                    {
                        "aaid": [
                            "0012#0002"
                        ]
                    }
                ],
                [
                    {
                        "aaid": [
                            "0010#0001"
                        ]
                    }
                ],
                [
                    {
                        "aaid": [
                            "4e4e#0001"
                        ]
                    }
                ],
                [
                    {
                        "aaid": [
                            "5143#0001"
                        ]
                    }
                ],
                [
                    {
                        "aaid": [
                            "0011#0701"
                        ]
                    }
                ],
                [
                    {
                        "aaid": [
                            "0013#0001"
                        ]
                    }
                ],
                [
                    {
                        "aaid": [
                            "0014#0000"
                        ]
                    }
                ],
                [
                    {
                        "aaid": [
                            "0014#0001"
                        ]
                    }
                ],
                [
                    {
                        "aaid": [
                            "53EC#C002"
                        ]
                    }
                ],
                [
                    {
                        "aaid": [
                            "DAB8#8001"
                        ]
                    }
                ],
                [
                    {
                        "aaid": [
                            "DAB8#0011"
                        ]
                    }
                ],
                [
                    {
                        "aaid": [
                            "DAB8#8011"
                        ]
                    }
                ],
                [
                    {
                        "aaid": [
                            "5143#0111"
                        ]
                    }
                ],
                [
                    {
                        "aaid": [
                            "5143#0120"
                        ]
                    }
                ],
                [
                    {
                        "aaid": [
                            "4746#F816"
                        ]
                    }
                ],
                [
                    {
                        "aaid": [
                            "53EC#3801"
                        ]
                    }
                ]
            ]
        },
        "username": "TestUserNamePathParam"
    }
]
```

> You should get a JSON object list as a response.

> Containing some UAF parameters:
> 
```bash
        "challenge": "JDJhJDEwJHhUMnZLNEpPQVVZL3ducTduQktpZ08",
        "header": {
            "appID": "http://localhost:8080/fidouaf/v1/public/uaf/facets",
            "op": "Reg",
            "serverData": "YVE0UHAtTVdQTm9lZUtqQnFVcGxzZDNOb2dMbUNYMXB6YjJXUk1WLW5SNC5NVFEyTXpjMU1UUTJOalkwT0EuVkdWemRGVnpaWEpPWVcxbFVHRjBhRkJoY21GdC5Ta1JLYUVwRVJYZEtTR2hWVFc1YVRFNUZjRkJSVmxaYVRETmtkV05VWkhWUmEzUndXakE0",
            "upv": {
                "major": 1,
                "minor": 0
            }
        },
        "policy": {
            "accepted": [
```

> And a list of Authenticator Attestation IDs:
> 
```bash
                        "aaid": [
                            "53EC#3801"
```

* Get the trusted facet IDs stored into the UAF Server:

```bash
$ curl -s http://localhost:8080/fidouaf/v1/public/uaf/facets | python -m json.tool
{
    "trustedFacets": [
        {
            "ids": [
                "https://www.head2toes.org",
                "android:apk-key-hash:Df+2X53Z0UscvUu6obxC3rIfFyk",
                "android:apk-key-hash:bE0f1WtRJrZv/C0y9CM73bAUqiI",
                "android:apk-key-hash:Lir5oIjf552K/XN4bTul0VS3GfM",
                "https://openidconnect.ebay.com"
            ],
            "version": {
                "major": 1,
                "minor": 0
            }
        }
    ]
}
```

## Congrats
You have now a UAF server up and running on your local system.