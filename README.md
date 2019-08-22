Example Spring Boot project for building inside GitLab CI
=============================

### GitLab CI Docker-in-Docker example

See [.gitlab-ci.yml](.gitlab-ci.yml):

```
# This .gitlab-ci.yml is an extension of the example provided in: 
# https://github.com/jonashackt/restexamples/blob/master/.gitlab-ci.yml
# We use Docker in Docker here with a docker executor instead of the shell one
image: docker:stable
services:
  - docker:dind
variables:
  DOCKER_HOST: tcp://docker:2375
  DOCKER_DRIVER: overlay2

# One of the new trends in Continuous Integration/Deployment is to:
# (see https://docs.gitlab.com/ee/ci/docker/using_docker_build.html)
#
# 1. Create an application image
# 2. Run tests against the created image
# 3. Push image to a remote registry
# 4. Deploy to a server from the pushed image

stages:
  - build
  - test
  - push
  - deploy

# see usage of Namespaces at https://docs.gitlab.com/ee/user/group/#namespaces
variables:
  REGISTRY_GROUP_PROJECT: $CI_REGISTRY/root/restexamples

# see how to login at https://docs.gitlab.com/ee/ci/docker/using_docker_build.html#using-the-gitlab-container-registry
before_script:
  - printenv
  - docker login -u $CI_REGISTRY_USER -p $CI_JOB_TOKEN $CI_REGISTRY

build-image:
  stage: build
  script:
    - mvn clean install

test-image:
  stage: test
  script:
    - echo Insert fancy API test here!

push-image:
  stage: push
  script:
    - docker push $REGISTRY_GROUP_PROJECT/restexamples:latest

deploy-2-dev:
  stage: deploy
  script:
    - echo You should use Ansible here!
  environment:
    name: dev
    url: https://dev.jonashackt.io


```


### Simple REST Spring Boot app

Start the app and go to [http://localhost:8080/v2/api-docs](http://localhost:8080/v2/api-docs) to see all endpoints as JSON.

UI-Documentation you will find under [http://localhost:8080/swagger-ui.html](http://localhost:8080/swagger-ui.html).