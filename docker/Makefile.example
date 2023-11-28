#  This Makefile is a tool for building, up, down, running, and cleaning the project data

## SECTION #1

# APP_ENV is a current environment name
ifndef APP_ENV
	include .env
	ifneq ("$(wildcard .env.local)","")
		include .env.local
	endif
endif

# The APP_NAME is a microservice or application name.
APP_NAME=$(APP_NAME)
ifndef APP_NAME
$(error APP_NAME is not set)
endif




### PROCESS #1.1 - GET ENVIRONMENT: domain, service, node, get actual ENVs, check required vars & files
# Use 'docker compose' as engine instead a legacy plugin 'docker-compose'.
# Actually it is the same thing, but the first one is a new way to use docker-compose.
# [docs](https://docs.docker.com/compose/migrate/)
COMPOSE=docker --log-level debug compose
DOCKER=docker --log-level debug


# The APPLICATION is a microservice name.
#APPLICATION=$(APPLICATION)
APPLICATION=
#ifndef APPLICATION
#$(error APPLICATION is not set)
#endif



# The LOCAL microservice database is used only for current purposes.
# You may find its value in the docker-compose.yml file in the database service section.
# Often it is a container name unique name or other unique sensitive param.
DATABASE=




# The GLOBAL database is used for all microservices purposes.
MAIN_DATABASE=





# Absolute path to apps working directory inside the (PHP) container.
# You may find its value in the docker-compose.yml file in the php service section if you need to change it or override it.
# In fact it is a  path to the project (PHP) root directory inside the apps container.
# Define or override it if you need to change it. (e.g. /var/www/html/sub.domain.zone/ or /application)
WORKING_DIR=/var/www/html




# The app's service name prefix.
# An optional name of the docker-compose.yml services, containers name prefix. Used for tags building, running, and cleaning the project.
# Usefully when you need to run multiple projects, and micro-systems on the same host.
PREFIX=$(APP_NAME)

ifndef APP_ENV
	include .env
	ifneq ("$(wildcard .env.local)","")
		include .env.local
	endif
endif




# Container names
PHP=$(PREFIX).application.php
APACHE=$(PREFIX).application.apache
#ZOOKEEPER=$(PREFIX).application.zookeeper
#KAFKA=$(PREFIX).application.kafka
REDIS=$(PREFIX).application.redis
#MYSQL=$(PREFIX).application.mysql
#ELASTICSEARCH=$(PREFIX).application.elasticsearch
#KIBANA=$(PREFIX).application.kibana
#LOGSTASH=$(PREFIX).application.logstash
#FILEBEAT=$(PREFIX).application.filebeat
#NGINX=$(PREFIX).application.nginx
#NGINX_PROXY=$(PREFIX).application.nginx-proxy


#MYSQL_PASSWORD=
#MYSQL_ROOT_PASSWORD=
#MYSQL_DATABASE=
#MYSQL_USER=root
#MYSQL_PORT=3306
#MYSQL_HOST=localhost


logs:
	$(COMPOSE) logs -f --tail all
	@echo "logs"
up:
	$(COMPOSE) up -d
	@echo "up"
ps:
	$(COMPOSE) ps
	@echo "ps"
restart:
	$(COMPOSE) restart
	@echo "restart"
stop:
	$(COMPOSE) stop
	@echo "stop"
start:
	$(COMPOSE) start
	@echo "start"
down:
	$(COMPOSE) down
	@echo "down"
top:
	$(COMPOSE) top
	@echo "top"
kill:
	$(COMPOSE) kill
	@echo "kill"

ll:
	$(DOCKER) ps \
		--all \
		--filter 'name=$(PREFIX)' \
		--format "table {{ .Names }}\t{{ .Image }}\t{{ .Status }}\t{{ .Ports }}\t{{ .Size }}"


htop:
	$(DOCKER) stats \
		--all \
		--no-trunc \
		--format "table {{.Name}}\t{{.CPUPerc}}\t{{.MemUsage}}\t{{.BlockIO}}\t{{.MemPerc}}\t{{.PIDs}}"


build:
	$(COMPOSE) down -v --rmi local --remove-orphans
	$(COMPOSE) up -d --build --force-recreate
	@echo "build"


install: composer


update: composer


clean:
	$(COMPOSE) down -v --rmi all --remove-orphans
	@echo "clean"


prune:
	$(COMPOSE) down -v --rmi all --remove-orphans
	$(DOCKER) system prune --all --volumes --force
	@echo "prune"


fresh:
	$(COMPOSE) stop -t 1
	$(COMPOSE) down -v --rmi all --remove-orphans
	$(COMPOSE) build --no-cache
	$(COMPOSE) up -d --force-recreate
	@echo "fresh"


composer:
	$(DOCKER) exec -it $(PHP) rm -rf $(WORKING_DIR)/vendor/
	$(DOCKER) exec -it $(PHP) bash -c 'cd $(WORKING_DIR) && composer install --ignore-platform-reqs --no-interaction'
	$(DOCKER) exec -it $(PHP) bash -c 'cd $(WORKING_DIR) && composer clear-cache --no-interaction'
	@echo "composer"


status:
	@echo "APPLICATION:\t$(APP_NAME)"
	@echo "PHP:\t"
	$(DOCKER) exec -it $(PHP) php -v
	@echo "DATABASE:\n"
	$(DOCKER) exec -it $(DATABASE) mysql -vvv --user=$(MYSQL_USER) --password=$(MYSQL_ROOT_PASSWORD) --execute='SHOW DATABASES'
	$(DOCKER) ps --all

#---------------------------------------------------------------------------------------------------------------------


#compose := $(COMPOSE) --project-name $(COMPOSE_PROJECT_NAME) -f docker-compose.yml \
#		-f ./docker/docker-compose.override.yml \
#		-f ./docker/docker-compose.$(APP_ENV).yml \
#		-f ./docker/docker-compose.$(APP_ENV).override.yml \
#		-f ./docker/docker-compose.$(APP_ENV).local.yml \
#		-f ./docker/docker-compose.$(APP_ENV).local.override.yml \
#		-f ./docker/common/docker-compose.$(APP_ENV).telegram.$(COMPOSE_PROJECT_NAME).yml \
#		-f ./docker/common/docker-compose.$(APP_ENV).skype.$(COMPOSE_PROJECT_NAME).yml \
#		-f ./docker/common/docker-compose.$(APP_ENV).callback.$(COMPOSE_PROJECT_NAME).yml \
#		-f ./docker/common/docker-compose.$(APP_ENV).local.$(COMPOSE_PROJECT_NAME).yml \
#	--env-file .env.example \
#	--env-file .env.example.local \
#	--env-file .env.example.$(APP_ENV)




#---------------------------------------------------------------------------------------------------------------------
php:
	$(DOCKER) exec -it $(PHP) bash

fish:
	$(DOCKER) exec -it $(PHP) fish


app-new:
	$(DOCKER) exec -it $(PHP) bash -c 'symfony new $(APP_NAME) --no-git --docker --debug --version="stable" --php="8.2" --dir="/var/www/html" '

app-new-html:
	symfony new html --no-git --docker --debug \
    	--version='stable' \
    	--php='8.2' \
    	--dir='/var/www/html'