# ydeploy
*A build and deployment framework for hybris®*

## Introduction
ydeploy is a Build and Deployment Framework for the hybris® commerce suite.

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

#### A configurable framework for building and deploying your hybris solutions
  * Quick and easy integration with your continuous integration server
  * Ability to execute multiple build, release, and deployment jobs concurrently from a single build server
  * Support for automated deployment and installation of the hybris platform software on new servers
  * Support for automated deployment of hybris platform software upgrades on existing servers
  * Ability to utilize different hybris platform release versions across your development branches and environments

#### A flexible way to organize,  manage, and deploy your hybris application and server related configurations
  * An optimal approach to manage and deploy your global, environment specific, and application instance specific hybris property configurations.
  * Ability to dynamically derive or discover your hybris configuration values at build or deploy time.
  * Automatic packaging and deployment of your custom server configuration files (tomcat, tcServer, solr), hybris License files, or hybris platform customization override files.

#### An extensible framework to simplify your operational and administrative tasks 
  * Platform agnostic support for starting, stopping, and restarting your hybris servers
  * Many other similar use cases can be easily implemented

## Usage Example

TODO

## Using ydeploy
TODO

### Downloading
TODO

### Installing / Checking into Source Control
TODO

### Directory Structure
TODO

### ydeploy Configuration
TODO

### Running ydeploy
TODO

### Managing hybris Property Configuration

One of the key benefits of ydeploy is that it provides the ability to manage hybris Configuration Properties in an optimal manner by allow you to define your configuration values at at any one of three levels.

1. Global Configuration Properties
2. Environment-specific Configuration Properties
3. Application Instance-specific Configuration Properties

These configuration levels should reside within the ${HYBRIS\_CONFIG\_DIR}/ydeploy/localproperties directory. An example structure is provided below but is also available in the ydeploy distribution at the ydeploy/template/hybris/config location.

```
${HYBRIS_CONFIG_DIR}
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
 
Whenever a hybris build is invoked, ydepoy will merge the three levels of properties files together in the order specified above to generate the effective local.properties at the ${HYBRIS\_CONFIG\_DIR}/local.properties location. When a property value is is defined at multiple levels, later levels override earlier, more global levels.

You will find that this flexible structure allows for you to greatly reduce duplication of local.properties configurations which ultimately helps reduce maintenance and risk of errors or omissions.

#### global.properties
global.properties refers to the properties file that contains configuration that will apply to every environment and every node in every environment unless that property is overridden at a lower level (i.e. environment or environment node). 

#### \<ENV\>.properties
\<ENV>.properties refers to a Properties file that contains the configuration specific to an entire environment.  The file must be named using the convention <ENV>/<ENV>.properties name where <ENV> is the Environment ID that is supplied to the ydeploy framework via an environment variable (${HYBRIS\_ENV\_ID}) or passed to ydeploy via a command line parameter (-Dhybris\_env\_id=\<ENV\>). This Environment ID must be the name of the folder that the Environment-specific Configuration Properties reside in and must also be the name of the file that contains the Environment-specific Configuration Properties.  e.g. Given an Environment ID of dev, the following must exist in the config directory.

#### \<SERVER_ID\>.properties
\<SERVER_ID>.properties refers to a Properties file that contains the configuration instance-specific configuration for an individual hybris JVM.  The file must be named using the convention <ENV>/<SERVER_ID>.properties name where <ENV> is the Environment ID which the server belongs, and <SERVER_ID> represents the unique identifier for the hybris JVM process. These variables are supplied to the ydeploy framework via environment variables (${HYBRIS\_ENV\_ID} and ${HYBRIS\_SERVER\_ID}) or passed to ydeploy via a command line parameters (-Dhybris\_env\_id=\<ENV\>, -Dhybris\_server\_id=\<SERVER_ID\>).

### Managing Other Configurations

#### Tomcat / TCServer
TODO

#### Solr
TODO

#### hybris License files
TODO

#### Platform customizations
TODO

## History
ydeploy was originally developed by Avatria, Inc. (http://www.avatria.com) and donated to the community as an Open Source project under the Apache 2.0 License. The project continues to be maintained by the Avatria team, however, contributions and involvement by all organizations and individuals is welcomed.

## Disclaimers
hybris® is the trademark of SAP SE in Germany and in several other countries. 
ydeploy is an independently developed open source software solution which is maintained and supported by the ydeploy community and distributed under the Apache 2.0 License. ydeploy is not a supported hybris® or SAP SE solution.  SAP is neither an author, contributor, or sponsor to the ydeploy project.
