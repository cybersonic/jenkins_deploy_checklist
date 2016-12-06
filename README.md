# Automatic Deployments with Jenkins
## Keys

1.  Create a ssh key for jenkins to use:   `ssh-keygen -t rsa -b 4096 -C "your_email@example.com"` and create a *deployer_rsa* and *deployer_rsa.pub* file 
1. Go to Github (or Github enterprise)
1. Go to your repository and click on `Settings`, then on the left tab look for `Deploy Keys`
1.  Create a new key and copy the contents of deployer_rsa.pub into the field. 

## Jenkins configuration
1. Install the github plugin in jenkins: 	https://wiki.jenkins-ci.org/display/JENKINS/GitHub+Plugin (it’s usually part of it) 
1. Let’s add the private key to jenkins. In Jenkins, on the left click on `Credentials`->`System` then click Global `Credentials (unrestricted)` and click `Add Credentials`
1. For Kind: set it to `SSH Username with private key`
1. Private Key: enter directly and copy the contents of deployer_rsa into the Key field and click ok. 
1. Create a new task, under the Github project you can enter the URL to the project. This is just a link
1. Under Source Code Management , select git and put the repository URL (such as git@github.com:cybersonic/jenkins_deploy.git ) 
1. It might refresh and say: ` 
Failed to connect to repository : Command "git ls-remote -h git@github.com:cybersonic/jenkins_deploy.git HEAD" returned status code 128: stdout:  stderr: Host key verification failed. `
1. Under credentials select Jenkins, this should clear the error as we are now using that credential 
1. Set the branches to build, for example `*/master`
1. under *Build Triggers* now you can do Build when a change is pushed to Github. 
1. To enable this from github end we have to go back to our project in GitHub and go to Settings, Integrations & services. There click Add Service and find the Jenkins (Github plugin) service. When it asks you for the url enter the name of your server with “github-webhook/“ at the end, for example: “http://jenkins.markdrew.pagekite.me/github-webhook/“ and save it. 
1.  Now we can test if it automatically runs by changing a file in the repo. 

## Deploying the code
So far we are now just checking out the code to the Jenkins workspace for this task. You should be able to see your code in the Workspace folder of the task in Jenkins. What we will do now is actually sync it from the workspace folder to another buy telling jenkins to run a build script

1. Go to the root of Jenkins and click `Manage Jenkins` and  `Global Tool Configuration`.
1. Under Ant, click Add Ant, and give it a name like `Global Ant installation`
1. Make sure that `Install automatically` is ticket and click `Save`
1. In the task, click Configure and under the *Build* and `Add build step`
1. Select `Invoke Ant` 
1. Select the *Ant Version* we defined (`Global Ant Installation`)
1. Ignore the *Targets* for the moment, you can keep it clear. What this will do is call the `build.xml` file with the default target. We will want to pass some variables to this in a bit but let's let it run to test.
1. Using the ant file in this project it should sync the files from `workspace/source_dir` to `workspace/destination_dir`

The build script in this project is designed to be customizable, so you can set which files get moved where, by setting the `destination` and `source` variables in your Jenkins task. 

1. Edit the task configuration again. And go down to the build section
1. click on the `Advanced...` to expand everything 
1. edit the Properties field and click on the down arrow and add the following, changing the destination path for what you need:

```
souce=.
destination=/var/jenkins_home/temp

```
