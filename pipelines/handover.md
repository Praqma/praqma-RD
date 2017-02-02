# Pipelines research

## Spin up

- clone the Jenkins as Code Praqma repo
- clone this repo
- you would need to create some folders for Jenkins as code as the README says. To pick up the work where it was left off, take the jenkins-backup folder from this repo and copy it to the proper place as the Jenkins as code README says. When you spin up jenkins, it will have the jobs used before.

## What was done

Important findings

Pipelines do not support TFS scm (important in regards to the original reason for the project)
We cannot run job dsl in the pipeline script -> to use pipeline, we need to either call the existing, job dsled jenkins jobs, or recreate them in pipeline scripts
We can run existing jobs in the pipeline script
if we do use the build “jobname” feature of the pipeline, we lose some of the pipeline functionality with using the other plugins as they run in a different space from the build “jobname” (for example cannot use the stash feature to pass on the artifacts to another job/stage)
stages in pipelines are not fitting to our needs. Stages are grouping jobs that can be run in parallel, but also all of them needs to be done before moving to the next stage unless we use 2 pipelines. This blocking feature makes it not fitting to our needs to run the build of product 1 without wait for component D.
The solution is 2 pipelines and the combination of jobs.

![pipeline](/images/pipeline.JPG)

build-prep-1 is a job that triggers the jobs building component A, B and C. A, B and C upon the completion of their builds(running in parallel) are pushing to Artifactory an artifact, named like this -, for example "ffhdhgsytyd12-A”. When A, B and C are built, they check back in with build-prep-1 to report that the build was successful (build-prep-1 has a post build step of collecting results). When build-prep-1 is successful, it is triggering the build of product 1. This build-p1 is pulling from artifactory all binaries, having the sha in their title. like "ffhdhgsytyd12-*” and builds from there.

In parallel with the same scm trigger build-prep-1 started, build-prep-2 is triggered. This has the same mechanism as the similarly named one before (running the build of component D, pushing to Artifactory). On success this is triggering build-p2. Build-p2 is pulling from Artifactory A, B, C and D, following the naming convention in the file names. We need to pull form Artifactory, as these jobs might run on different nodes, not sharing workspaces and paths.

The assumption is, that A, B and C are built before D finishes.

## Next steps

This model described needs to be produced and run by colleagues for feedback and optimizing.
