
# Traceability of Maven builds

The `withMaven` pipeline step will capture in the logs of the build all the details of the execution:

* Version of the JVM
   * Plugin initialization: `[withMaven] use JDK installation JDK8`
   * `mvn` executable invocation: `Java version: 1.8.0_102, vendor: Oracle Corporation`
* Version of Maven
   * Plugin initialization: `[withMaven] use Maven installation 'M3'`
   * `mvn` executable invocation: `Apache Maven 3.3.9 (bb52d8502b132ec0a5a3f4c09453c07478323dc5; 2015-11-10T16:41:47+00:00)`
* Name or path of the Maven settings.xml and Maven global settings.xml file.
   * Plugin initialization: `[withMaven] use Maven settings provided by the Jenkins Managed Configuration File 'maven-settings-for-supply-chain-build-job' 
* When using the Maven settings.xml and global settings.xml files provided by the [Jenkins Config File Provider Plugin](https://wiki.jenkins-ci.org/display/JENKINS/Config+File+Provider+Plugin),
details of the Jenkins credentials injected in the MAven build.
   * Plugin initialization: `[withMaven] use Maven settings.xml 'maven-settings-for-supply-chain-build-job' with Maven servers credentials provided by Jenkins (replaceAll: true): [mavenServerId: 'nexus.beescloud.com', jenkinsCredentials: 'beescloud-nexus-deployment-credentials', username: 'deployment', ...]` 


Sample:

```
[withMaven] use JDK installation JDK8
[withMaven] use Maven installation 'M3'
[withMaven] use Maven settings provided by the Jenkins Managed Configuration File 'maven-settings-for-supply-chain-build-job' 
[withMaven] use Maven settings.xml 'maven-settings-for-supply-chain-build-job' with Maven servers credentials provided by Jenkins (replaceAll: true): 
     [mavenServerId: 'nexus.beescloud.com', jenkinsCredentials: 'beescloud-nexus-deployment-credentials', username: 'deployment', type: 'UsernamePasswordCredentialsImpl'], 
     [mavenServerId: 'github.beescloud.com', jenkinsCredentials: 'github-enterprise-api-token', username: 'dev1', type: 'UsernamePasswordCredentialsImpl']
...
[...] Running shell script
+ mvn clean deploy
----- withMaven Wrapper script -----
Picked up JAVA_TOOL_OPTIONS: -Dmaven.ext.class.path=".../pipeline-maven-spy.jar" -Dorg.jenkinsci.plugins.pipeline.maven.reportsFolder="..." 
Apache Maven 3.3.9 (bb52d8502b132ec0a5a3f4c09453c07478323dc5; 2015-11-10T16:41:47+00:00)
Maven home: /home/ubuntu/jenkins-aws-home/tools/hudson.tasks.Maven_MavenInstallation/M3
Java version: 1.8.0_102, vendor: Oracle Corporation
Java home: /home/ubuntu/jenkins-aws-home/tools/hudson.model.JDK/JDK8/jre
Default locale: en_US, platform encoding: UTF-8
OS name: "linux", version: "3.13.0-109-generic", arch: "amd64", family: "unix"
```




# Maven Build Phases Reporters

Maven build executions inside the `withMaven(){...}` wil be detected and Jenkins will transparently
 * Archive and fingerprint generated Maven artifacts and Maven attached artifacts
 * Publish JUnit / Surefire reports (if the [Jenkins JUnit Plugin](http://wiki.jenkins-ci.org/display/JENKINS/JUnit+Plugin) is installed) 
 * Publish Findbugs reports (if the [Jenkins FindBugs Plugin](http://wiki.jenkins-ci.org/display/JENKINS/FindBugs+Plugin) is installed) 

(i) The detection of Maven builds require to use Maven 3.2+.

 
<table>
<tr>
    <th>Reporter</th>
    <th>Description</th>
    <th>Required Jenkins Plugin (1)</th>
    <th>Marker file to disable the feature (2)</th>
<tr>
<tr>
    <td>Generated Artifact</td>
    <td>Archiving and the fingerprinting of the artifacts and attached artifacts generated by the Maven build (jar, sources jar, javadocs jar...)</td>
    <td> &nbsp; </td>
    <td> `.skip-archive-generated-artifacts` </td>
</tr>
<tr>
    <td>Generated JUnit and Surefire reports</td>
    <td>Publishing of the JUnit / Surefire reports generated by the Maven build</td>
    <td> [JUnit Plugin](http://wiki.jenkins-ci.org/display/JENKINS/JUnit+Plugin) </td>
    <td> `.skip-publish-junit-results` </td>
</tr>
<tr>
    <td>Generated Findbugs reports</td>
    <td>Publishing of the Findbugs reports generated by the Maven build</td>
    <td> [FindBugs Plugin](https://wiki.jenkins-ci.org/display/JENKINS/FindBugs+Plugin) </td>
    <td> `.skip-publish-findbugs-results` </td>
</tr>
</table>

(1) Jenkins Plugin to publish the reports on the Jenkins build page

(2) Experimental feature. 
Marker file to temporarily disable the feature for a specific Maven build. 
Typically used to disable a reporter for a specific build that would generate too much data for the default configuration of the reporter (e.g. too many generated artifacts...) or 
to workaround a bug in the `withMaven` waiting for a fix. These marker file must be located in the home directory of the build

# Adding more Maven Reporters

The API for Maven reporters is still experimental. Please open a Request for Enhancement Jira issue to discuss how to add Maven reporters.

We want to quickly add reporters for CheckStyle, Jacoco...


```
mvn test -Dtest=org.jenkinsci.plugins.pipeline.maven.WithMavenStepTest#maven_build_on_master_succeeds&>mvn.log
```