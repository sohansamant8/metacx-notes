
## Below are the steps for application build and deployment to dev environment.  

### Steps for Building into dev environment

* Run below command to start the build process. 
`git tag-jupiter`

* Select envoronment:
`dev`

* Operation: Build current commit id (NOT build-then-deploy)

* Service selection. 
    * When you get to the list of services, press the "a" key twice to first highlight all, then deselect all. 
    * Then go to service which needs to be deployed and select that service by hitting `space bar` key.
    * Hit `enter` to build the service.

* Select `Yes` for attaching `tag`

* Select `Yes` to push the build. 

* Go to cloud build and see the build running or not. 

### Steps for Deploying the service into dev environment

* Run below command to start the build process. 
`git tag-jupiter`

* Select envoronment:
`dev`

Operation: Deploy current commit id 

* Service selection. 
    * When you get to the list of services, press the "a" key twice to first highlight all, then deselect all. 
    * Then go to service which needs to be deployed and select that service by hitting `space bar` key.
    * Hit `enter` to build the service.

* Select `Yes` for attaching `tag`

* Select `Yes` to push the build. 

* Go to cloud build and see the build running or not. 

