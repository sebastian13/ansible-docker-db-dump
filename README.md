# Ansible Docker Database Dump

This playbook deploys backups scripts to backup docker container running a mysql or postgres database. It deploys the scripts [docker-mysql-dump.sh](https://gist.github.com/sebastian13/cc58304f7b177ac1d16d7671dc67efb9#file-docker-mysql-dump-sh) and [docker-postgres-dump.sh](https://gist.github.com/sebastian13/c313c055a59342acb9f0a7a3d5d03c09), and creates entries in crontab to run the scripts each morning.

## How to use

1. Clone this repo in `roles`

	```
	cd roles
	git clone https://github.com/sebastian13/ansible-docker-db-dump.git
	```

1. Create a playbook `docker-db-dump.yml`

	```yaml
	---
	- name: Docker Databse Dump
	  hosts: "{{ hostname | default('docker-db-dump') }}"
	  become: true
	
	  roles:
	    - docker-db-dump
	```

1. Define hosts in the `hosts` file

	```
	[docker-db-dump]
	v01
	v02
	```
	
1. Create a file for each host in `host_vars`

	```yaml
	docker_mysql:
	  - name: wordpress.example.com
	    path: '/docker/wordpress.example.com'
	  - name: joomla.example.com
	    path: '/docker/joomla.example.com'
	docker_postgres:
	  - name: confluence.example.com
	    path: '/docker/confluence.example.com'
	```
	
1. Run the playbook. Optionally limit the deployment to a single server.

	```
	ansible-playbook docker-db-dump.yml --limit v01
	```

## Requirements

The `docker-compose.yml` within the specified path must include a service called `mysql` or `postgres`. The environment varaibles must be linked and specified in the file `.env`.

- **Example `docker-compose.yml`**

	```yaml
	services:
	  mysql:
	    image: mariadb
	    env_file: .env
	    
	  other:
	    image: wordpress
	    environment:
	      - WORDPRESS_DB_PASSWORD=${MYSQL_ROOT_PASSWORD}
	      - WORDPRESS_DB_NAME=${MYSQL_DATABASE}
	```
	
	```yaml
	services:
	  postgres:
	    image: postgres:9.6
	    env_file: .env
	```

- **Example `.env`**

	```
	MYSQL_DATABASE=example
	MYSQL_ROOT_PASSWORD=123456
	```
	
	```
	POSTGRES_USER=example
	POSTGRES_DB=example
	POSTGRES_PASSWORD=123456
	```