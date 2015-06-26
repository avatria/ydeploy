# ydeploy
*A build and deployment framework for hybris®*

## Introduction
ydeploy is a Build and Deployment Framework for the hybris® commerce suite.

ydeploy integrates with the hybris platform build framework and attempts to reduce the amount of effort necessary to implement automated build and deployment tasks of hybris software across your environment landscape. Like the hybris build framework, ydeploy is built on Apache Ant and should be easy to pickup for most Java developers.

ydeploy is intended to be simple, yet extensible. 

The default implementation provides a set of configurable build targets intended to address the most common build and deployment requirements. When necessary, the framework can be customized and extended to meet various other operational tasks related to administering hybris software.

## Benefits
At the highest level, the ydeploy framework provides you simplified and standardized way to:
1. Build and generate "build once, deploy everywhere" hybris release packages from your build server.
2. Deploy your hybris release packages out to any number of servers or environments from your build server.
3. Perform standard hybris server administrative (e.g. start, stop, restart) tasks from your build server.

If you dig in a bit deeper, you will find that ydeploy actually provides you quite a bit more.

1. A configurable framework for building and deploying your hybris solutions.
  * Continuous integration build support
  * Ability to perform multiple concurrent build, release, and deployment jobs from a single build server. Great if you have multiple development teams developing out of different environments and/or branches at the same time.
  * Support for automated deployment and installation of the hybris platform software on new servers.
  * Support for performing builds and deployments using different hybris platform release versions (e.g. 5.2, 5.3, etc) concurrently
  * Support for automated deployment of hybris platform software upgrade on existing servers.
and environment release and deploy your custom hybris extensions as well as full hybris platform upgrades. 
2. A flexible way to organize,  manage, and deploy your hybris application and server related configurations.
  * An optimal approach to manage and deploy your global, environment specific, and application instance specific hybris property configurations. Gone are the days of managing and maintaining countless local.properties files.
  * Ability to dynamically derive or discover your hybris configurations at build or deploy time. This is extremely useful in cloud based or auto-scale environments, for example, where server and infrastructure configurations may change often.
  * Automatic packaging and deployment of your custom server configuration files (tomcat, tcServer, solr), hybris License files, or hybris platform customization override files. For goodness sake, no need to manage these files manually!
3. An extensible framework for easily implementing other hybris related administrative tasks which typically require custom scripting.
  * By default, platform agnostic support for starting, stopping, and restarting your hybris servers is also included


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
3. Application Instance-specific Configuration Properties (aka Node configurations)

These configuration levels should reside within the ${HYBRIS_CONFIG_DIR}/ydeploy/localproperties directory. An example structure is provided below but is also available in the ydeploy distribution at the ydeploy/template/hybris/config location.
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
 
Whenever a hybris build is invoked, ydepoy will merge the three levels of properties files together in the order specified above to generate the effective local.properties at the ${HYBRIS_CONFIG_DIR}/local.properties location. When a property value is is defined at multiple levels, later levels override earlier, more global levels.

You will find that this flexible structure allows for you to greatly reduce duplication of local.properties configurations which ultimately helps reduce maintenance and risk of errors or omissions.

#### global.properties
global.properties refers to the properties file that contains configuration that will apply to every environment and every node in every environment unless that property is overridden at a lower level (i.e. environment or environment node). 

#### <ENV>.properties
\<ENV>.properties refers to a Properties file that contains the configuration specific to an entire environment.  The file must be named using the convention <ENV>/<ENV>.properties name where <ENV> is the Environment ID that is supplied to the ydeploy framework via an environment variable (${HYBRIS_ENV_ID}) or passed to ydeploy via a command line parameter (-Dhybris_env_id=<ENV>). This Environment ID must be the name of the folder that the Environment-specific Configuration Properties reside in and must also be the name of the file that contains the Environment-specific Configuration Properties.  e.g. Given an Environment ID of dev, the following must exist in the config directory.

#### <SERVER_ID>.properties
\<SERVER_ID>.properties refers to a Properties file that contains the configuration instance-specific configuration for an individual hybris JVM.  The file must be named using the convention <ENV>/<SERVER_ID>.properties name where <ENV> is the Environment ID which the server belongs, and <SERVER_ID> reperesents the unique identifier for the hybris JVM process. These variables are supplied to the ydeploy framework via environment variables (${HYBRIS_ENV_ID} and ${HYBRIS_SERVER_ID}) or passed to ydeploy via a command line parameters (-Dhybris_env_id=<ENV>, -Dhybris_server_id=<SERVER_ID>).

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