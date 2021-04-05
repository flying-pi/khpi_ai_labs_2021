WORKDIR := $(shell pwd)

IMAGE_NAME?=khapi_ai
CONTAINER_NAME?=khapi_ai


help: ## Display help message
	@echo "Please use \`make <target>' where <target> is one of"
	@perl -nle'print $& if m{^[\.a-zA-Z_-]+:.*?## .*$$}' $(MAKEFILE_LIST) | sort | awk 'BEGIN {FS = ":.*?## "}; {printf "\033[36m  %-25s\033[0m %s\n", $$1, $$2}'


run_app_notebook: .build/app_image stop_app ## Run app notebook
	docker run \
	  --gpus all \
	  --rm \
	  -d \
	  -v $(WORKDIR)/data:/data/ \
	  -v $(WORKDIR)/app:/app/ \
	  -e TF_FORCE_GPU_ALLOW_GROWTH=true \
	  --name $(CONTAINER_NAME) \
	  -p 48324:48324 \
	  -p 48325:48325 \
	  $(IMAGE_NAME) \
	  bash -c "PYTHONPATH=/app jupyter lab \
		--no-browser --ip 0.0.0.0 --port 48324 --notebook-dir=/app --allow-root  \
		--NotebookApp.password=sha1:2669bd30792f:3be957e7b0b51fd7903f4e44c825502bf34e7f91 \
		--NotebookApp.token=blank"

stop_app: ## stop mongo
	docker stop  $(CONTAINER_NAME) || true

.build/app_image: .build Dockerfile requarements.txt
	docker build -t $(IMAGE_NAME) -f Dockerfile   $(WORKDIR)
	touch $@

.build:
	mkdir ".build"
