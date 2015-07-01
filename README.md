# ydeploy
*A continuous build and deployment framework for hybris®*

## Introduction
ydeploy is a continuous build and deployment framework for the hybris® commerce suite.

ydeploy integrates with the hybris platform build framework and attempts to reduce the amount of effort necessary to implement automated build and deployment tasks for hybris software across your environment landscape. Like the hybris build framework, ydeploy is built on Apache Ant and should be easy to pickup for most Java developers.

ydeploy is intended to be a simple, yet extensible tool. 

The standard distribution provides a set of configurable build targets intended to address the most common build and deployment requirements. When necessary, the framework can be customized and extended to meet various other operational tasks related to administering hybris software.

## Why ydeploy?
At the most basic level, the ydeploy framework provides you a simple way to perform the following tasks:

1. Build and generate "build once, deploy everywhere" hybris release packages
2. Deploy your hybris release packages out to any number of servers or environments
3. Perform standard hybris server administrative (e.g. start, stop, restart) tasks from your build server.

However, if you dig in a bit deeper you will find that ydeploy actually provides you with quite a bit more.

### Features & Benefits

#### Build & Deployment
  * Quick and easy setup of hybris build & deployment jobs from your continuous integration server
  * Support for starting, stopping, and restarting your hybris servers from your build server
  * Ability to execute multiple build, release, and deployment jobs concurrently from a single build server
  * Support for automated deployment and installation of the hybris platform software on new servers
  * Support for automated deployment of hybris platform upgrades on existing servers
  * Ability to utilize different hybris platform release versions across your development branches and environments
  * Framework is platform agnostic and works across most Windows, Linux, and Unix variants

#### Configuration Management
  * An optimal approach to manage and deploy your global, environment specific, and application instance specific hybris property configurations.
  * Ability to dynamically derive or discover your hybris configuration values at build or deploy time.
  * Automatic packaging and deployment of your custom server configuration files (tomcat, tcServer, solr), hybris License files, or platform customization files.

#### Extending ydeploy 
  * Many other similar administrative & operational tasks can be simplified by implementing them on the ydeploy framework.

### ydeploy is NOT...
ydeploy is not a comprehensive all-in-one solution intended to address all of your hybris continuous deployment needs. There are many very capable general-purpose tools available which can be used to address individual parts of your deployment strategy. ydeploy is intended to be an open tool that you can use to fill the functional gap between these general-purpose tools and the limited continuous deployment capabilites that the hybris plaform provides out of the box. 

Below is a sampling of capabilities that ydpeloy does not provide natively. We leave it up to you to decide the best tool to meet these needs for your organization.
  * **Continuous Integration Server**: While ydeploy orchestrates a lot of the activities that happen after kicking of a build task, we highly recommend using a CI server such as Jenkins, AntHill, or Bamboo (to name a few) to provide a simple interface for kicking off your ydeploy builds, managing build lifecycles, addressing authentication & access control needs, etc.
  * **Source Control**: ydeploy is not a source control tool and does not itself checkout code, tag releases, or perform any other source control related operations. Most CI servers have very strong capabilities releated to source control needs, so we recommend using these capabilites when available.
  * **Artifact Repository**: ydeploy does not itself manage or archive your build artifacts. CI server typically provides these capabilities or will have integration with such solutions. 
  * **File transfer**: When performing deployments, ydeploy does not actually distribute (i.e. copy) release artifacts out to target server. Again, most CI servers typically have very strong capabilities to copy files and invokes processes on remote servers in your environment landscape.

## Usage Example

Before diving into the low level details regarding how to setup ydeploy, you may be wanting to see a more concrete example regarding what the ydeploy tool can do for you. In this section we'll show you the standard capabilities of the solution, but gloss over the fine print regarding how you can customize and configure the tool to meet your needs. For detailed information regarding integrating ydeploy into your hybris solution, please see the "Using ydeploy" section.

### The build command

ydeploy is designed to be checked into your source control repository along with your hybris custom extensions and configuration directories.

```
hybris-repo
	/config
		/localextensions.xml
		[ . . . ]
	/custom
		/custext1
		/custext2
		[ . . . ]
	/ydeploy
```

After checking out the contents of your repo, we can use ydeploy to perform a full build of your custom hybris extension using the `build` command. 

```
$ cd ydeploy
$ ant build
Buildfile: /opt/build/workspace/ydeploy/build.xml
   [echo] Initializing ydeploy

   [ . . . ]
   
   BUILD SUCCESSFUL
   Total time: 49 seconds
```

That's it! ydeploy has successfully performed a full build of your hybris solution for you. 

You can see the built solution by looking into the `ydeploy/temp/build` directory.

```
$ cd ydeploy/temp/build/hybris
$ ls
bin config data log sampleconfigurations temp
```

At this point you may be scratching your head and asking *"why not just invoke the hybris build directly from the hybris/bin/platform directory?"*

Great question! To answer, lets think through all of the steps we would have had to perform to achieve the same thing without ydeploy.

1. First, we would need to have the hybris software setup on the machine beforehand. In the example above, hybris was not even setup on the machine prior to running the ydeploy `build` command. ydeploy automatically set everything up
2. After setting up hybris we would need to deploy the custom extensions and config directories from the checkout location to the proper locations within the hybris install
3. Next, we would likely need to update the local.properties files and other configuration within the hybris/config directory to ensure the build process runs correctly on this particular machine.
4. Almost ready to build. But first, we need to go into the hybris/bin/platform directory and setup the environment using that `setantenv.sh` script
5. At this point we *should* be able to run the standard hybris build process

Clearly ydeploy has helped simplify the process. Next, lets look at how to generate a release using ydeploy.

### The genrelease command

In this section we'll look at how to build and generate a hybris release package using ydeploy. The release packages generated by the `genrelease` command encapsulate all of the software and configuration required to deploy hybris across your environment landscape. This includes the hybris platform software, any custom extensions, and the hybris server configuration.

Similar to the `build` task, `genrelease` can be done in a single command

```
$ cd ydeploy
$ ant genrelease
Buildfile: /opt/build/workspace/ydeploy/build.xml
   [echo] Initializing ydeploy
   [ . . . ] 
   BUILD SUCCESSFUL
   Total time: 1 minute 18 second
```

Just as with the `build` command, the `genrelease` command has setup hybris on the fly, performed the build, and generated a release package which can be used to deploy hybris to any of our environments. 
You can see the generated release package by looking into the ydeploy/temp/genrelease directory.

```
$ cd ydeploy/temp/genrelease
$ ls
hybrisServer-Release-0.zip
```

### The deploy command

The `deploy` command is used to deploy the hybris release package which is generated by the `genrelease` command to servers in your environment landscape. Specifically, ydeploy performs the following steps:
1. Detects the location of the hybris platform on the target server (if installed) and stops the server (if running). 
  * *Note that hybris does not actually need to already been installed. ydeploy will automatically install and configure the hybris platform as part of the deploy task.*
2. Extracts the contents of the hybris release packages and deploys them to proper hybris installation location.
3. Deploys the proper server specific configuration for the target server and invokes the hybris build.
4. Starts the hybris build server on the new release.

One thing to note is that ydeploy isn't designed to actually copy the hybris release package from your build server to the target server, but once the package is copied to the destination server, ydeploy can handle the rest.

*Note: To copy the hybris release package from your build server to your target server, we recommend using the capabilities of your continuous integration server. Further discussions regarding how to integrate ydeploy into Jenkins can be found in the Integrating ydeploy with Jenkins section.*

For the example below, I've copied the hybrisServer-Release-0.zip which we generated above to the target server. As the ydeploy package is embedded into the hybris release package, we first need to extract the zip before we can invoke ydeploy.

```
$ cd /opt/build/releases
$ ls
hybrisServer-Release-0.zip
$ unzip  hybrisServer-Release-0.zip & cd ydeploy & ant deployrelease
Buildfile: /opt/build/releases/ydeploy/build.xml
   [echo] Initializing ydeploy
   [ . . . ] 
   BUILD SUCCESSFUL
   Total time: 2 minutes 25 second
```

At this point our new release has been deployed to the target server. 

## Setting up ydeploy
In this section we will provide a quick walk through of how to integrate ydeploy into your hybris solution.

### Downloading the release

You can download a copy of the current ydeploy release using the `master` branch of the Git repository. You can either clone the repo using your Git client of choice or you can download a zip of the repo content using the link below.

https://github.com/avatria/ydeploy/archive/master.zip

### Integration into your solution

Before we get started with integrating ydeploy into your hybris solution, lets do a quick review of the contents of the distribution.

#### ydeploy Directory Structure

Below is the basic directory structure of the ydeploy software.

```
ydeploy/
	/core
		/conf
			/core.properties
		/resources
	/custom
		/conf
			/ydeploy.properties
			/env
		/resources
		
	/template
		/hybris
			/config
				/buildcallbacks.xml
				/ydeploy
						
```

The `ydeploy/core` directory contains configuration and resources which are integral to the framework and typically should not be edited.

The `ydeploy/custom` directory should contain all user specific configurations and customizations. The intent is to keep user specific customizations separate from core framework code and configuration. `ydeploy.properties` and the `env` directory will be where most of your configuration customizations will reside.

The `ydeploy/template` directory contains files and sample configurations that are intended to copied in your standard hybris install structure. Currently all template files should be placed under your `hybris/config` directory (i.e. `${HYBRIS_CONFIG_DIR}`).

#### Checking ydeploy into source control

It is recommended that you add the full contents of the ydeploy distribution to your source code repository right along side your hybris customizations directory (e.g. `custom`) and your `hybris/config` directory.

Additionally, you should copy the contents of the `ydeploy/template/hybris/config` into your `hybris/config` directory. This step is necessary to allow ydeploy to hook into certain behaviors of the hybris build framework.

Below is an example of how your source control repo contents may look after integrating ydeploy.

```
hybris-repo
	/config
		/buildcallbacks.xml	[from ydeploy distribution template dir]
		/ydeploy		[from ydeploy distribution template dir]
		
	/custom
		/custext1
		/custext2
		[ . . . ]
	/ydeploy			[full ydeploy distribution]
```

### ydeploy Configuration
Once you have copied ydeploy into your source control repository, you will need to review and edit some of the common configurations. Below are the primary configuration you should review and edit.

#### ydeploy/custom/conf/ydeploy.properties
These are the ydeploy framework configurations that likely may need editing.

```
# Where your hybris 'custom' extensions and 'config' folders are stored in source control in relation
# to the ydeploy directory.
yd.hybris.custom.ext.dir=${yd.root.dir}/../custom
yd.hybris.custom.config.dir=${yd.root.dir}/../config

# hybris build tasks to perform when the ydeploy 'build' target is invoked
yd.tasks.build.hybrisbuildtargets=customize all

# hybris build tasks to perform when the ydeploy 'genrelease' target is invoked
yd.tasks.genrelease.hybrisbuildtargets=customize all production

# hybris build tasks to perform when the ydeploy 'deployrelease' target is invoked on the target server
yd.tasks.deployrelease.hybrisbuildtargets=all
```

#### ydeploy/custom/conf/env/global.properties
This file contains your global ydeploy framework configurations. 

```
# Where hybris platform zip releases are stored
yd.hybris.platform.releases.dir=/opt/build/hybris/releases

# The hybris platform zip file to use for 'build' and 'genrelease' targets. 
# Must exist in the ${yd.hybris.platform.releases.dir} location
yd.hybris.platform.release.file=hybris-commerce-suite-5.4.0.1.min.zip

# Location at which the 'deployrelease' and server related targets will operate.
yd.hybris.platform.deploy.dir=/opt/hybriscs
```

The first two settings described above (`yd.hybris.platform.releases.dir`, `yd.hybris.platform.release.file`) are very important and tell ydeploy where to find the standard hybris platform release zip to use to setup hybris 'on the fly' for the build and genrelease commands.

The last setting (`yd.hybris.platform.deploy.dir`) tells ydeploy where to install hybris on your server when the deploy command is executed.

The configuration values set in global.properties will be used unless you choose to provide different configurations to be used at an environment or server specific level.

```
ydeploy
	/custom
		/conf
			/env
				/global.properties
				/dev
					/dev.properties
					/dev01.properties
					/dev02.properties
				/qa
					/qa.properties
```

When you invoke ydeploy it will determine what configurations to use based upon any environment or server specific configurations you provide. This is described more in the next section.

### Running ydeploy
When you invoke ydeploy, it will first look to detect any environment or server specific configuration values which you may have provided via environmental variables or passed in via command line parameters.
  * Environmental Variables
    * HYBRIS_ENV
    * HYBRIS_SERVER_ID
  * Command Line Parameters
    * -Dhybris_env
    * -Dhybris_server_id

Typically, we recommended only using the command line parameter approach for initial testing and debugging purposes. 

Below is an example of running ydeploy using the command line argument approach.

```
$ cd ydeploy
$ ant build -Dhybris_env=dev -Dhybris_server_id=dev01
```

In this example, the effective ydeploy framework configuration will be loaded in the following order with later configuration values taking precedence.

1. ydeploy/custom/conf/env/global.properties
2. ydeploy/custom/conf/env/dev/dev.properties
3. ydeploy/custom/conf/env/dev/dev01.properties

Once you have all of the configuration layers setup properly, we recommend setting up the environmental variables across all of the build and target hybris application servers that ydeploy will run. This will allow for the ydeploy framework to infer the proper configurations to use from the environment itself such that the command line parameters can be omitted.

### Managing hybris Property Configuration

One of the key benefits of ydeploy is that it provides the ability to manage hybris Configuration Properties in an optimal manner by allow you to define your configuration values at at any one of three levels.

1. Global Configuration Properties
2. Environment-specific Configuration Properties
3. Application Instance-specific Configuration Properties

These configuration levels should reside within the `${HYBRIS\_CONFIG\_DIR}/ydeploy/localproperties` directory. An example structure is provided below but is also available in the ydeploy distribution at the `ydeploy/template/hybris/config` location.

```
/config		[ ${HYBRIS_CONFIG_DIR} ]
	/ydeploy
		/localproperties
			/global.properties
			/dev
				/dev.properties
				/dev01.properties
				/dev02.properties
			/qa
				/qa.properties
				/qa01.properties
				/qa02.properties
			[... etc ...]
```
 
Whenever a hybris build is invoked, ydepoy will merge the three levels of properties files together in the order specified above to generate the effective local.properties at the `${HYBRIS_CONFIG_DIR}/local.properties` location. When a property value is is defined at multiple levels, later levels override earlier, more global levels.

You will find that this flexible structure allows for you to greatly reduce duplication of local.properties configurations which ultimately helps reduce maintenance and risk of errors or omissions.

#### global.properties
global.properties refers to the properties file that contains configuration that will apply to every environment and every node in every environment unless that property is overridden at a lower level (i.e. environment or environment node). 

#### \<ENV\>.properties
\<ENV>.properties refers to a Properties file that contains the configuration specific to an entire environment.  The file must be named using the convention <ENV>/<ENV>.properties name where <ENV> is the Environment ID that is supplied to the ydeploy framework via an environment variable (`${HYBRIS_ENV_ID}`) or passed to ydeploy via a command line parameter (`-Dhybris_env_id=<ENV>`). This Environment ID must be the name of the folder that the Environment-specific Configuration Properties reside in and must also be the name of the file that contains the Environment-specific Configuration Properties.  e.g. Given an Environment ID of dev, the following must exist in the config directory.

#### \<SERVER_ID\>.properties
\<SERVER_ID>.properties refers to a Properties file that contains the configuration instance-specific configuration for an individual hybris JVM.  The file must be named using the convention <ENV>/<SERVER_ID>.properties name where <ENV> is the Environment ID which the server belongs, and <SERVER_ID> represents the unique identifier for the hybris JVM process. These variables are supplied to the ydeploy framework via environment variables (`${HYBRIS_ENV_ID}` and `${HYBRIS_SERVER_ID}`) or passed to ydeploy via a command line parameters (`-Dhybris_env_id=<ENV>`, `-Dhybris_server_id=<SERVER_ID>`).

### Managing Other Configurations

#### Tomcat / TCServer
TODO

#### Solr
TODO

#### hybris License files
TODO

#### Platform customizations
TODO

## Integrating ydeploy into Jenkins
TODO

## History
ydeploy was originally developed by Avatria, Inc. (http://www.avatria.com) and donated to the community as an Open Source project under the Apache 2.0 License. The project continues to be maintained by the Avatria team, however, contributions and involvement by all organizations and individuals is welcomed.

## Support and Contact
For issues, suggestions, or feature requests, please post an issue (https://github.com/avatria/ydeploy/issues).

For comments, questions, or feedback please send a message to @zmcmahon-avatria.

## Disclaimers
hybris is the trademark of SAP SE in Germany and in several other countries. 
ydeploy is an independently developed open source software solution which is maintained and supported by the ydeploy community and distributed under the Apache 2.0 License. ydeploy was not developed by hybris or SAP.
