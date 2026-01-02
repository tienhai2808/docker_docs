.PHONY:build
build:
	echo "FROM scratch" > Dockerfile
	docker build -t my-scratch-image .
	-rm Dockerfile

.PHONY: push-dockerhub
push-dockerhub:
	docker tag my-scratch-image tienhai2808/my-scratch-image:latest
	docker push tienhai2808/my-scratch-image:latest