The SAFE template has the ability to easily deploy to a Docker container. The details of the additions to the FAKE script are shown here.

## FAKE

1. **Bundle** - all necessary artifacts for both Server and Client are collected for following `Docker` target.
1. **Docker** - based on present `Dockerfile`, docker image is built and tagged using `dockerUser` and `dockerImageName` values from the script.
