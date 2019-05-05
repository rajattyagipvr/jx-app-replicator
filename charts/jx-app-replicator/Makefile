CHART_REPO := http://jenkins-x-chartmuseum:8080
NAME := kubernetes-replicator
OS := $(shell uname)

CHARTMUSEUM_CREDS_USR := $(shell cat /builder/home/basic-auth-user)
CHARTMUSEUM_CREDS_PSW := $(shell cat /builder/home/basic-auth-pass)

build: clean
	helm dep build
	helm lint

install: clean build
	helm install . --name ${NAME}

upgrade: clean build
	helm upgrade ${NAME} .

delete:
	helm delete --purge ${NAME}

clean:
	rm -rf requirements.lock
	rm -rf charts
	rm -rf ${NAME}*.tgz

release: clean
	sed -i -e "s/version:.*/version: $(VERSION)/" Chart.yaml
	echo "  version: $(VERSION)" >> requirements.yaml
	helm dep build
	helm package .
	curl --fail -u $(CHARTMUSEUM_CREDS_USR):$(CHARTMUSEUM_CREDS_PSW) --data-binary "@$(NAME)-$(VERSION).tgz" $(CHART_REPO)/api/charts
