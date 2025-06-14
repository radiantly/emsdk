# A Makefile to build, test, tag and publish the Emscripten SDK Docker container.

# Emscripten version to build: Should match the version that has been already released.
# i.e.:  1.39.18
version =
alias =
only_alias =
platforms =

image_name ?= radiantly/emsdk

.TEST:
ifndef version
	$(error argument 'version' is not set. Please call `make version=SOME_VERSION ...`)
endif

build: Dockerfile .TEST
	cd .. && docker build --progress=plain --network host --build-arg=EMSCRIPTEN_VERSION=${version} -t ${image_name}:${version} -f docker/$< .

test: test_dockerimage.sh .TEST
	# test as non-root
	# test fallback env variables by overriding the entrypoint
	docker run --rm -u `id -u`:`id -g` -w /emsdk/docker --net=host --entrypoint /bin/bash ${image_name}:${version} $<

push: .TEST
ifndef only_alias
	docker push ${image_name}:${version}
endif
ifdef alias
	docker tag ${image_name}:${version} ${image_name}:${alias}
	docker push ${image_name}:${alias}
endif

push-multiplatform: .TEST
ifndef platforms
	$(error argument 'platforms' is not set. Please call `make platforms="SOME_PLATFORM1 SOME_PLATFORM2..." ...`)
endif
	docker manifest create ${image_name}:${version} $(foreach platform,$(platforms),--amend ${image_name}:${version}-$(platform))
	docker manifest push ${image_name}:${version}
