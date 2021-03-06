# GoCD Trial

Table of Contents
=================

  * [How to use](#how-to-use)
  * [Dependencies](#dependencies)
  * [Installation](#installation)
  * [Use GoCD](#use-gocd)
  * [Next steps](#next-steps)
  * [Shutdown](#shutdown)
  * [License](#license) 
 
# How to use 

This trial is intended to be used to quickly and easily try out GoCD. It is *not* intended for production use. 

Once you start the GoCD trial it will show you example pipelines as well as execution data and value stream maps. You will also be able to edit the configuration and run builds. 

Use this template in combination with the [GoCD documentation](https://www.gocd.org/getting-started/part-1/) to understand more about GoCD's capabilities. 

If you need support while trying GoCD feel free chat to us on [Gitter](https://gitter.im/gocd/gocd) or join our [Google Group](https://groups.google.com/forum/#!forum/go-cd).

# Dependencies

* Install Git 
* Install Docker 
* Install Docker Compose 
* Ensure that Docker is running 

# Installation

Open terminal

Clone the repo, by typing:

```
git clone https://github.com/gocd/gocd-trial.git
```

Navigate to the template directory, by typing: 

```
cd gocd-trial
```


Start the GoCD server, by typing:

```
docker-compose up -d
```

This starts 3 docker containers that are hosting:
- GoCD server
- GoCD agent
- git server (which is hosting "repo1" that contains the GoCD config and materials for this trial)

You will see: 


```
Creating network "gocd-trial_default" with the default driver
Creating gocd-trial_gitserver_1 ... done
Creating gocd-trial_server_1    ... done
Creating gocd-trial_agent_1     ... done
```

Access the server by going to: http://localhost:8153

You may see a "starting" message, if so, wait a few minutes for the server and agent to start. Once the server is ready to try you will see a few example pipelines: 

![GoCD Trial](/images/GoCD_Docker_Template_image_1.png)

# Use GoCD

Now would be a good time to take a look at [Introduction to GoCD](https://www.gocd.org/getting-started/part-1/) to understand what you are looking at. 

## Value stream map

You can view a value stream map of these pipelines at http://localhost:8153/go/pipelines/value_stream_map/deploy/1

## Run builds

You can make a change to the [materials](https://docs.gocd.org/current/introduction/concepts_in_go.html#materials) in the git repo to kick off the builds and see the pipeline progress.

First checkout the configuration repository, by typing: 

```
cd gocd-trial
```
```
git clone http://localhost:8155/git/repo1.git/
```

Next navigate to the repo and make a change, by typing:

```
cd repo1
```
```
touch tmp.txt
```
```
git add tmp.txt
```
```
git commit -m "first commit"
```
```
git push
```

## Update the configuration

This GoCD server has been configured using GoCD's [config as repo feature](https://docs.gocd.org/current/advanced_usage/pipelines_as_code.html). The configuration is located in this repository. 

Navigate to the repo, by typing: 

```
cd gocd-trial
```
```
cd repo1
```

Then open the configuration file, by typing: 

```
open deploy.gopipeline.json
```
Change the configuration and make the first deploy stage manual, by changing: 

```javascript
{
      "name": "ready_to_deploy_stage",
      "fetch_materials": true,
      "never_cleanup_artifacts": false,
      "clean_working_directory": false,
      "environment_variables": [],
      "jobs": [
        {
          "name": "ready_to_deploy_job",
          "environment_variables": [],
          "tabs": [],
          "resources": [],
          "artifacts": [],
          "properties": [],
          "run_instance_count": null,
          "timeout": 0,
          "tasks": [
            {
              "type": "exec",
              "command": "ls"
            }
          ]
        }
      ]
    },
```

to be:

```javascript
  {
      "name": "ready_to_deploy_stage",
      "fetch_materials": true,
      "never_cleanup_artifacts": false,
      "clean_working_directory": false,
      "environment_variables": [],
      "approval": {
        "type": "manual",
        "roles": [],
        "users": []
      },
      "jobs": [
        {
          "name": "ready_to_deploy_job",
          "environment_variables": [],
          "tabs": [],
          "resources": [],
          "artifacts": [],
          "properties": [],
          "run_instance_count": null,
          "timeout": 0,
          "tasks": [
            {
              "type": "exec",
              "command": "ls"
            }
          ]
        }
      ]
    },
  
```

Return to terminal, type: 

```
git add deploy.gopipeline.json
```
```
git commit -m "updated configuration"
```
```
git push
```
If you return to  http://localhost:8153 you will see that the pipeline "deploy" now has two [manual stages](https://docs.gocd.org/current/configuration/dev_choose_when_stage_runs.html). 

Also as the the build_test pipeline has been configured to use this repository as the [material](https://docs.gocd.org/current/configuration/quick_pipeline_setup.html) you will also that the pipelines are now running. 

# Next steps

This GoCD trial has given you a quick, yet limited introduction to GoCD. 

If you would like to understand more about the capabilities of GoCD and try them out on your own project we recommend downloading and [installing GoCD](https://www.gocd.org/download) and following our complete [introduction](https://www.gocd.org/getting-started/part-1/) to getting up your own GoCD server. 

# Shutdown

When finished trying GoCD bring the server down, by typing:

```
docker-compose down
```

# License

```plain
Copyright 2018, ThoughtWorks, Inc.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

   http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```
