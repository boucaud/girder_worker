.DEFAULT_GOAL := initialize


initialize:
	pip install -U pip
	pip install -U -r requirements.txt
	ansible-galaxy install --force --roles-path ./scripts/roles girder.girder

# Build the girder worker images locally so run relies on
# these images rather than the (potentially old) images on
# dockerhub.
	cd ../../ && docker build -t girder/girder_worker:latest .
	cd ../../ && docker build -f Dockerfile.py3 -t girder/girder_worker:latest-py3 .

run2: 
	docker-compose -f docker-compose.yml up -d; \
	cd scripts/ && ansible-playbook -i inventory setup.yml


run3: 
	docker-compose -f docker-compose.yml -f docker-compose.py3.yml up -d; \
	cd scripts/ && ansible-playbook -i inventory -e ansible_python_interpreter=python3 setup.yml

test:
	pytest -v -x -n4

# The following are convienence functions for local development of
# integration tests.

worker_restart2:
	docker-compose -f docker-compose.yml restart girder_worker

worker_restart3:
	docker-compose -f docker-compose.yml -f docker-compose.py3.yml restart girder_worker


clean:
	docker-compose -f docker-compose.yml stop
	docker-compose -f docker-compose.yml rm -vf
	docker-compose -f docker-compose.yml -f docker-compose.py3.yml stop
	docker-compose -f docker-compose.yml -f docker-compose.py3.yml rm -vf


# Note that this will force a re-pull of girder/girder:latest{-py3}
# and girder/girder_worker:latest{-py3} be REMOVING these images from
# your local system.

nuke: clean
	-docker rmi \
	girder/girder:gw_integration_test \
	girder/girder_worker:gw_integration_test \
	girder/girder:gw_integration_test-py3 \
	girder/girder_worker:gw_integration_test-py3 \
	girder/girder:latest \
	girder/girder:latest-py3 \
	girder/girder_worker:latest \
	girder/girder_worker:latest-py3




.PHONY: initialize run2 run3 test test3 worker_restart2 worker_restart3 clean nuke
