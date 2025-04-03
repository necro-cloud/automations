# necronizer's cloud self hosted github runner automations

This repository mainly contains various scripts for automations, pipelines and workflows used in combination with [self hosted github runners](https://github.com/necro-cloud/runners)

# Requirements and Dependencies

The following is required to start using this repository:

1. [Docker](https://www.docker.com/) - We provision self hosted github runners using docker compose
2. [Self Hosted GitHub Runners](https://github.com/necro-cloud/runners) - Contains all scripts and configuration files to bring up self hosted github runners

# Usage Instructions:

## Build and Store Docker Images on Quay.IO Repositories using public GitHub Runners

**Workflow Usage:**

```yaml
build_push_image:
  name: Building and storing PhotoAtom Runner Docker Image
  uses: necro-cloud/automations/.github/workflows/build-docker-image.yml@main
  with:
    dev_version_name: <DEVELOPMENT VERSION NAME HERE>
    image_name: <IMAGE NAME HERE>
    image_proper_name: <PROPER IMAGE NAME HERE>
    build_path: <BUILD PATH HERE>
    repository: <QUAY.IO REPOSITORY NAME HERE>
  secrets:
    DOCKER_USERNAME: ${{ secrets.DOCKER_USERNAME }}
    DOCKER_PASSWORD: ${{ secrets.DOCKER_PASSWORD }}
```

**Inputs:**
1. `image_proper_name`: Proper name for the image
2. `image_name`: Name of the image to be built
3. `dev_version_name`: Name to attached to the development build version, defaults to `dev`
4. `pre_build_script`: Script to run before image build process
5. `build_path`: Directory to build docker image from
6. `repository`: Repository to push the image in

**Secrets:**
1. `DOCKER_USERNAME`: Docker username for Authentication
2. `DOCKER_PASSWORD`: Docker password for Authentication

## Execute Shell Scripts which interacts with the self hosted kubernetes cluster

```yaml
execute_scripts:
  name: Scripts Deployment onto Self Hosted K3d Cluster
  uses: necro-cloud/automations/.github/workflows/execute-script.yml@main
  with:
    deployment_name: Scripts Deployment
    runners: self-hosted-runner
    shell_script: |
      whoami
  secrets:
    KUBECONFIG: ${{ secrets.KUBECONFIG }}
```

**Inputs:**
1. `deployment_name`: Name of the deployment to be performed
2. `runners`: Self Hosted Runners to be used for deployment
3. `shell_script`: Script for executing within runner and K3D context

**Secrets:**
1. `KUBECONFIG`: KubeConfig to be used for deployment for K8s resources

## OpenTofu Deployment onto Self Hosted Kubernetes Cluster

```yaml
namespace_deploy:
  name: Namespace Deployment onto Self Hosted K3d Cluster
  uses: necro-cloud/automations/.github/workflows/tofu-deploy.yml@main
  with:
    deployment_name: Namespace
    folder_path: namespace
    runners: self-hosted-runner
  secrets:
    KUBECONFIG: ${{ secrets.KUBECONFIG }}
    TFVARS: ${{ secrets.TFVARS }}
```

**Inputs:**
1. `deployment_name`: Name of the deployment to be performed
2. `folder_path`: Path for the deployment to be performed
3. `runners`: Self Hosted Runners to be used for deployment
4. `pre_init_script`: Script to run before running Tofu Init
5. `pre_plan_script`: Script to run before running Tofu Plan
6. `pre_apply_script`: Script to run before running Tofu Apply
7. `post_apply_script`: Script to run after running Tofu Apply
8. `artifact_version`: Artifact Version for deploying using OpenTofu

**Secrets:**
1. `KUBECONFIG`: KubeConfig to be used for deployment for Kubernetes resources
2. `TFVARS`: TFVARS File to be used for deployment for Kubernetes resources
