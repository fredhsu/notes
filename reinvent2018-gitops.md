# Container power hour table - Abby, Claire, Jess,
# GitOps
versioned ci/cd on top of declarative infra, stop scripting/ start shipping
everything is managed as code - git workflow
* config, ref to env vars
* work with branches locally
    * git --> PR -> merge
    * running tests
    * trigger ci/cd build
* treat infra the same way
    * avoid config drift, use templates, etc..

# CI,preview env, and PR bots - Clair - principal eng container svcs
## CI + Containers
* Start with classic CI
    * source branch -> trigger -> build -> results -> dev -> push -> source branch
* Better w/ containers -> easier!
    * Dockerfiles give standardized way to build/describe
    * docker build to package up
    * Jenkins file : 

    ```
    node { 
        checout scn 
        docker build
    }
    ```
* Now can swap out source branch with pull request
* PR CI with containers - built on containers
* whats' next
    * continuous testing in PRs
    * Use containers and infra as code to validate entire environment before merging

# GitHub Actions
* No longer have to poll Github, can do bot things in github
* workflows in the .github dir in your repo
* excmple terraform that runs in a conatiner and wrraps it all to deploy to aws fargate
* github api for secrets
