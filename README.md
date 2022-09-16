# Jenkins Configuration-as-a-code

# Dockerfile
First, start a Jenkins instance with the [Configuration as Code](https://plugins.jenkins.io/configuration-as-code) plugin installed.

- Those running Jenkins as a [Docker](https://github.com/jenkinsci/docker) container (and maybe also [pre-installing plugins](https://github.com/jenkinsci/docker#preinstalling-plugins)), do include [Configuration as Code](https://plugins.jenkins.io/configuration-as-code) plugin.

Second, the plugin looks for the `CASC_JENKINS_CONFIG` environment variable. The variable points to a comma-separated list of any of the following:

- Path to a folder containing a set of config files. For example, `/var/jenkins_home/casc_configs`.
- A full path to a single file. For example, `/var/jenkins_home/casc_configs/jenkins.yaml`.
- A URL pointing to a file served on the web. For example, `https://acme.org/jenkins.yaml`.

If an element of `CASC_JENKINS_CONFIG` points to a folder, the plugin will recursively traverse the folder to find file(s) with .yml,.yaml,.YAML,.YML suffix. It will exclude hidden files or files that contain a hidden folder in **any part** of the full path. It follows symbolic links for both files and directories.
<details><summary>Exclusion examples</summary>

`CASC_JENKINS_CONFIG=/jenkins/casc_configs`  
:heavy_check_mark: `/jenkins/casc_configs/jenkins.yaml`  
:heavy_check_mark: `/jenkins/casc_configs/dir1/config.yaml`  
:x: `/jenkins/casc_configs/.dir1/config.yaml`  
:x: `/jenkins/casc_configs/..dir2/config.yaml`  
  
`CASC_JENKINS_CONFIG=/jenkins/.configs/casc_configs` contains hidden folder `.config`  
:x: `/jenkins/.configs/casc_configs/jenkins.yaml`  
:x: `/jenkins/.configs/casc_configs/dir1/config.yaml`  
:x: `/jenkins/.configs/casc_configs/.dir1/config.yaml`  
:x: `/jenkins/.configs/casc_configs/..dir2/config.yaml`  
</details>

#### plugins.txt
Contains list of plugin names which will be installed during docker build

#### jenkins.yaml
Contains jenkins configuration as a code

#### Starting jenkins
`
docker-compose up --build
`

#### sample-config folder
Placed possible configuration as a code in this directory which has been tested

#### dev-config folder
Placed configuration file in this directory to use in personal machine

#### Docker build and push
docker build -t vibakar/jenkins-casc:latest
docker push vibakar/jenkins-casc:latest

### Useful Notes
#### How to list the plugins installed on the jenkins
```
JENKINS_HOST=username:password@myhost.com:port
curl -sSL "http://$JENKINS_HOST/pluginManager/api/xml?depth=1&xpath=/*/*/shortName|/*/*/version&wrapper=plugins" | perl -pe 's/.*?<shortName>([\w-]+).*?<version>([^<]+)()(<\/\w+>)+/\1 \2\n/g'|sed 's/ /:/'
```

or

```
Jenkins.instance.pluginManager.plugins.each{
  plugin -> 
    println ("${plugin.getDisplayName()} (${plugin.getShortName()}): ${plugin.getVersion()}")
}
```
---