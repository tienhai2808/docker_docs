### DOCKER COMPOSE COMMANDS

.PHONY: compose-build
compose-build:
	docker compose build

.PHONY: compose-up
compose-up:
	docker compose up

.PHONY: compose-up-build
compose-up-build:
	docker compose up --build

.PHONY: compose-down
compose-down:
	docker compose down

### DOCKER CLI COMMANDS

DOCKERCONTEXT_DIR:=../web_application/
DOCKERFILE_DIR:=../web_application/

.PHONY: docker-build-all
docker-build-all:
	docker build -t client-react-vite -f ${DOCKERFILE_DIR}/client-react/Dockerfile.0 ${DOCKERCONTEXT_DIR}/client-react/

	docker build -t client-react-nginx -f ${DOCKERFILE_DIR}/client-react/Dockerfile ${DOCKERCONTEXT_DIR}/client-react/

	docker build -t api-node -f ${DOCKERFILE_DIR}/api-node/Dockerfile ${DOCKERCONTEXT_DIR}/api-node/

	docker build -t api-golang -f ${DOCKERFILE_DIR}/api-golang/Dockerfile ${DOCKERCONTEXT_DIR}/api-golang/

DATABASE_URL:=postgres://postgres:hahaha@db:5432/postgres

.PHONY: docker-run-all
docker-run-all:
	echo "$$DOCKER_COMPOSE_NOTE"

	# Stop and remove all running containers to avoid name conflicts
	$(MAKE) docker-stop

	$(MAKE) docker-rm

	docker network create my-network

	docker run -d \
		--name db \
		--network my-network \
		-e POSTGRES_PASSWORD=hahaha \
		-v pgdata:/var/lib/postgresql/data \
		-p 5432:5432 \
		--restart unless-stopped \
		postgres:15.1-alpine

	docker run -d \
		--name api-node \
		--network my-network \
		-e DATABASE_URL=${DATABASE_URL} \
		-p 3000:3000 \
		--restart unless-stopped \
		api-node

	docker run -d \
		--name api-golang \
		--network my-network \
		-e DATABASE_URL=${DATABASE_URL} \
		-p 8080:8080 \
		--restart unless-stopped \
		api-golang

	docker run -d \
		--name client-react-vite \
		--network my-network \
		-v ${PWD}/client-react/vite.config.js:/app/vite.config.js \
		-p 5173:5173 \
		--restart unless-stopped \
		client-react-vite

	docker run -d \
		--name client-react-nginx \
		--network my-network \
		-p 80:8080 \
		--restart unless-stopped \
		client-react-ngnix

.PHONY: docker-stop
docker-stop:
	-docker stop pgdb
	-docker stop api-node
	-docker stop api-golang
	-docker stop client-react-vite
	-docker stop client-react-nginx

.PHONY: docker-rm
docker-rm:
	-docker container rm pgdb
	-docker container rm api-node
	-docker container rm api-golang
	-docker container rm client-react-vite
	-docker container rm client-react-nginx
	-docker network rm my-network

define DOCKER_COMPOSE_NOTE

🚨🚨🚨🚨🚨🚨🚨🚨🚨🚨🚨🚨🚨🚨🚨🚨🚨🚨🚨🚨🚨🚨🚨🚨🚨🚨🚨🚨🚨🚨

❯ NOTE:

This command runs the example app with a bunch
of individual docker run commands. This is much
easier to manage with docker-compose (see 
docker-compose.yml and compose make targets above)

🚨🚨🚨🚨🚨🚨🚨🚨🚨🚨🚨🚨🚨🚨🚨🚨🚨🚨🚨🚨🚨🚨🚨🚨🚨🚨🚨🚨🚨🚨


endef
export DOCKER_COMPOSE_NOTE