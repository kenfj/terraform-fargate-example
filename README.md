# Fargate Terraform example remix version

* Remix version of great Fargate terraform examples
* Setup ECS Fargate Cluster, ECR and releted IAM etc.
* Build and deploy sample Python Flask app


## Reference sample code

* https://github.com/Oxalide/terraform-fargate-example
* https://github.com/afedulov/terraform-fargate
* https://040code.github.io/2018/01/30/fargate_with_terraform/


## Init repository and create Fargate cluster

```bash
terraform init
# first create repository for dependency
terraform apply --target aws_ecr_repository.main
# after repository, create other resources
terraform apply
```

## Set repository_url

```bash
# check the repository_url
terraform output repository_url
# set environment variable
export REPO_URL=$(terraform output repository_url)
```

## Build docker image

```bash
cd app
# after update the app code
docker build -t my-python-app .
# check if it runs well locally
docker run --name my-running-app -p 80:5000 --rm my-python-app
open http://0.0.0.0/
# CTRL+C to quit
```

## Push the image to ECR

```bash
# login ECR
$(aws ecr get-login --region ap-northeast-1 --no-include-email)
# push the image to ECR
docker tag my-python-app ${REPO_URL}:latest
docker push ${REPO_URL}:latest
# check the result
aws ecr list-images --repository-name tf-example-app
```

## Deploy cycle

* make some code change
* make sure you set repository_url
* Build docker image
* Push the image to ECR
* in console
  - ECS > Task Definitions > tf-example-app
  - select Revision > Actions > Update Service
  - select Cluster and check `Force new deployment`
  - click `Next Step` to proceed
  - click `Update Service`
* check the result
  - ECS > Clusters > tf-example-app > Tasks tab
  - you should see some PROVISIONING tasks
* repeat above


## Clean up

```bash
terraform destroy
# run this if you are okay to clean up all images
docker system prune -a
```

## Reference

* https://hub.docker.com/_/python/
* http://flask.pocoo.org/
* https://dev.classmethod.jp/cloud/aws/first-aws-fargate/
* https://hoshinotsuyoshi.com/post/terraform-fargate/
* https://qiita.com/takeshi_hirosue/items/90051144aae8bb5a646f
* https://qiita.com/yukkyun/items/7c114ddf77e0d7bee4ba
