ARCH=amd64 
NODE_VERSION=16
OS=buster-slim
NODE_RED_VERSION:=$(shell grep -oE "\"node-red\": \"(\w*.\w*.\w*.\w*.\w*.)" package.json | cut -d\" -f4)

DOCKERHUB_ID:=janvda
SERVICE_NAME:=node-red
SERVICE_VERSION:=${NODE_RED_VERSION}-${NODE_VERSION}-${OS}


# if you don't want the build to use cached layers replace next line by :
#        CACHING:= --no-cache
CACHING:=

# check if docker context points to my macbook (= cross platform image build machine)
current_docker_context:=$(shell docker context ls | grep -e "*" | cut -d ' ' -f1 )
ifneq (${current_docker_context},colima)
    $(error "Current docket context [=$(current_docker_context)] is not 'colima'.")
endif

define HELP
=========================================================================================
Make Commands:

1. make build

    Docker hub base image = node:$(NODE_VERSION)-$(OS)
    Node-red version = $(NODE_RED_VERSION)
    Builds the image $(DOCKERHUB_ID)/$(SERVICE_NAME):$(SERVICE_VERSION)
    for architectures: $(ARCH)
    and publishes it to docker hub.

2. make push_readme

     Pushes README.md to Docker Hub repository $(DOCKERHUB_ID)/$(SERVICE_NAME):$(SERVICE_VERSION)

3. make all

     Combination of build and push_readme

Before running these make commands assure you are using your local docker version (so DOCKER_HOST or docker context is not pointing
to a remote host)
=========================================================================================
endef
export HELP

default:
	@echo "$$HELP"

all: build push push_readme

build:
	@echo "Starting the build ..."
	docker build --rm $(CACHING) \
       --build-arg ARCH=$(ARCH) \
       --build-arg NODE_VERSION=$(NODE_VERSION) \
       --build-arg NODE_RED_VERSION=${NODE_RED_VERSION} \
       --build-arg OS=$(OS) \
       --build-arg BUILD_DATE="$(date +"%Y-%m-%dT%H:%M:%SZ")" \
       --build-arg TAG_SUFFIX=default \
       --file Dockerfile.debian \
       --tag $(DOCKERHUB_ID)/$(SERVICE_NAME):$(SERVICE_VERSION) .

push: login
	@echo "Pushing the image $(DOCKERHUB_ID)/$(SERVICE_NAME):$(SERVICE_VERSION) to docker hub ..."
	docker push $(DOCKERHUB_ID)/$(SERVICE_NAME):$(SERVICE_VERSION)

push_readme: login
	@echo "Push README.md file to Docker Hub ..."
	docker pushrm $(DOCKERHUB_ID)/$(SERVICE_NAME)

login:
	@echo "build requires that you must login to dockerhub in order to update readme"
	@echo "So, please enter your dockerhub access token here below."
	docker login -u $(DOCKERHUB_ID)

.PHONY: default buildx push_readme login all