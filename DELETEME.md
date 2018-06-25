# Gitlab plugin guides

## How to install Gitlab

#### In order to deploy it on our lab machines use the following CI job:

[Gitlab CI Job](http://mydtbld0101.hpeswlab.net:8888/jenkins/job/Octane-Plugin-Root-Gitlab-full-master/)

#### Alternatively deploy it from scratch:
Gitlab doesn't support installing external plugins. Every plugin(project service) must
become part of the Gitlab source code by commiting a merge request. The ALM Octane project service is
on its way to be reviewed and adopted by the Gitlab main repository. For the meanwhile, the easiest way
to install a Gitlab server that includes the ALM Octane project service is using the Docker image below:

##### docker pull mstekel/octane-gitlab
Use the command below to run a container:

##### docker run -d --name $container_name -p $gitlab_port:$gitlab_port -p $gitlab_shell_ssh_port:22 -e GITLAB_OMNIBUS_CONFIG="external_url 'http://$DEPLOY_TARGET:$gitlab_port'; gitlab_rails['gitlab_shell_ssh_port']=$gitlab_shell_ssh_port;" mstekel/octane-gitlab

#### Typical values for the variables are:

export container_name=gitlab_30080\
export gitlab_port=30080\
export gitlab_shell_ssh_port=30022\
export DEPLOY_TARGET=myd-vm09141.hpeswlab.net

## How to configure the agent

Read about [Gitlab Runner](https://docs.gitlab.com/runner/).

The simplest flow is:

1. Install Gilab Runner on the agent machine.
2. Register Gitlab Runner:
  * On the agent machine run the following command: `gitlab-runner register`
  * Fill out the required fields according to what's specified by the 'Runners Settings'->'Specific Runners'->'Setup a specific Runner manually' on the [Runners Page](http://myd-vm09141.hpeswlab.net:30080/root/demo/settings/ci_cd)
    * In the last step select (executor) - type 'shell'.
  * Restart the runner by: gitlab-runner restart.
  * Make sure the runner is presented and green in the 'Runners Settings'->'Specific Runners'->'Runners activated for this project' on the [Runners Page](http://myd-vm09141.hpeswlab.net:30080/root/demo/settings/ci_cd)

## How to install the plugin
No plugin installation is supported. The ALM Octane project service must be part of the
Gitlab server source code.

## How to configure the plugin

#### How to create a job that reports test results to ALM Octane 
Gitlab project should define a pipeline producing test result files and
adding them to job artifacts. Then the "test result pattern" field of the
connection should be set accordingly to send them to ALM Octane.

The easiest way to achieve that is to import(New Project->Import Project->Repo by URL) the project below:

https://gitlab.com/mstekel-public/demo.git

#### How to connect the plugin to ALM Octane
Go to Project->Settings->Integrations->ALM Octane, fill out the form and save. The form parameters are:

1. Server URL - the URL of your ALM Server including the sharedspace. Example: http://myd-vm06161.hpeswlab.net:8081/ui/?p=1001 
2. Client ID - an ALM Octane client ID authentication token with "CI/CD Integration" role
3. Client Secret - the an ALM Octane client secret
4. Results pattern - [glob](https://en.wikipedia.org/wiki/Glob_%28programming%29) pattern that specifies the files that should be scanned for test results.
Example: {test_results,junit_results}/*.xml

## How to create the dev environment
See [Gitlab Development Kit](https://gitlab.com/gitlab-org/gitlab-development-kit).
Our GDK repository is [here](https://gitlab.com/mstekel-public/gitlab-ce).

#### Preconfigured VMWare image for the dev environment
Download the image from [here](file:///tobedone/)
* Login: osboxes.org
* Password: osboxes.org
* Git workspace: ~/projects/gitlab-development-kit/gitlab
* IntelliJ is installed.
  * Server can be debugged with IntelliJ (debug configuration is included)
  * AlmOctaneTaskWorker cannot be debugged - use logging.
* GDK run command:
  * cd ~/projects/gitlab-development-kit/
  * gdk run
* Logs:
  * ~/projects/gitlab-development-kit/gitlab/logs

## Troubleshooting

#### HTTP Proxy
On a production enviroment - add the following lines to /etc/gitlab/gitlab.rb:

`gitlab_rails['env'] = {
   'http_proxy' => 'http://web-proxy.il.hpecorp.net:8080',
   'https_proxy' => 'http://web-proxy.il.hpecorp.net:8080'
}`

#### Logs
Log files(last 2000 lines) are available by: http://gitlab.example.com/admin/logs.
GDK log folder: <gdk>/gitlab/log  
Production log folder: /var/gitlab/log

#### Error analysis
Each error is shown in the log file with its detailed message and stacktrace.
The errors are usually self-explained.

## Demo video
https://hpe-my.sharepoint.com/personal/moshe_stekel_hpe_com/Documents/gitlab-docs/gitlab-octane.mp4?e=4%3ab82126f25b8f46b4bedff49841e3ca93
