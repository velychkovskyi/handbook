# handbook

#############################################################

sudo apt-get update
sudo apt-get install htop

sudo apt-get -y install language-pack-en
sudo locale-gen en_US.UTF-8
sudo update-locale LANG="en_US.UTF-8" LANGUAGE="en_US.UTF-8" LC_ALL="en_US.UTF-8" LC_CTYPE="C"
#   sudo update-locale LANGUAGE=en_US:en
#   sudo update-locale LC_ALL=en_US.UTF-8
exit
locale
sudo apt-get install mc
mc
sudo apt-get install apt-transport-https ca-certificates software-properties-common


##############################################################
	# SWAP
	sudo dd if=/dev/zero of=/var/myswap bs=1M count=512
	sudo mkswap /var/myswap
	sudo chmod 600 /var/myswap
	sudo swapon /var/myswap
	free -h
	sudo nano /etc/fstab
	#   /var/myswap             swap     swap   defaults                0 0
	cat /etc/fstab


##############################################################
# ZABBIX
# Ubuntu 16.04
wget https://repo.zabbix.com/zabbix/3.4/ubuntu/pool/main/z/zabbix-release/zabbix-release_3.4-1+xenial_all.deb
sudo dpkg -i zabbix-release_3.4-1+xenial_all.deb
sudo apt update
sudo apt install zabbix-agent -y

	#-------------------------------------------------------
	# UBUNTU 18.04
	wget http://repo.zabbix.com/zabbix/3.4/ubuntu/pool/main/z/zabbix-release/zabbix-release_3.4-1+bionic_all.deb
	sudo dpkg -i zabbix-release_3.4-1+bionic_all.deb 
	sudo apt-get install zabbix-agent

sudo nano /etc/zabbix/zabbix_agentd.conf
#    Server=35.157.39.64
sudo systemctl enable zabbix-agent.service
sudo systemctl start zabbix-agent.service


###############################################################
	# Install Postgres-CLI
	sudo apt-get install postgresql-contrib
	psql --version
	psql -h <host> -U postgres <DB_name>

		#----------------------------------------------------
		# Grant superuser access for user
		create role <new-user> with password 'MY_PASS' login;
		# For AWS RDS
		grant rds_superuser to <new-user>;
		# GRANT Super privileges on tables
		GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO <new_user>;
			# For local DB
			ALTER USER <new-user> WITH SUPERUSER;
		# Check User roles
		SELECT rolname, rolsuper FROM pg_roles;
		# Chenge password
		ALTER USER user_name WITH PASSWORD 'new_password';



#------------------------------------------------------------ 
#Install Postgress in Docker
docker pull postgres
mkdir -p $HOME/docker/volumes/postgres
docker run --rm   --name pg-docker -e POSTGRES_PASSWORD=docker -d -p 5432:5432 -v $HOME/docker/volumes/postgres:/var/lib/postgresql/data  postgres
# POSTGRES_PASSWORD sets the superuser password for PostgreSQL
# POSTGRES_USER sets the superuser name. If not provided, the superuser name defaults to postgres.
# POSTGRES_DB sets the name of the default database to setup. If not provided, it defaults to the value of POSTGRES_USER.
psql -h localhost -U postgres -d postgres



	##############################################################
	# AWS-CLI
	# Make new PROFILE
	aws configure --profile <NEW-USER>
	aws s3 cp <MY-FILE> s3://<BUCKET-NAME> --profile <NEW-USER>
	aws s3api list-buckets --query "Buckets[].Name" --profile <NEW-USER>



################################################################
# Install REDIS-CLI
# https://docs.aws.amazon.com/en_us/AmazonElastiCache/latest/red-ug/GettingStarted.ConnectToCacheNode.html
sudo apt-get install gcc
wget http://download.redis.io/redis-stable.tar.gz
tar xvzf redis-stable.tar.gz
cd redis-stable
sudo apt install make
make distclean
make
src/redis-cli -c -h cashfder-stage-redis.u6uotq.0001.euc1.cache.amazonaws.com -p 6379


	################################################################
	# SSH Tunnel 
	# To remove tunnel find and kill PID
	ssh -f -N -L6379:<your redis node endpoint>:6379 <your EC2 node that you use to connect to redis>
	redis-cli -h 127.0.0.1 -p 6379

		#----------------------------
		# From my local office Laptop
		ssh -i "/home/velik/KEY/Defigo-DEV-Stockholm.pem" -f -N -L6379:defigo-dev-redis.0u3tp7.0001.eun1.cache.amazonaws.com:6379 ubuntu@ec2-13-53-194-125.eu-north-1.compute.amazonaws.com
		redis-cli -h 127.0.0.1 -p 6379

	#----------------------------
	# Frankfurt Defigo stage
	ssh -i "Defigo-Stockholm-Redis-tunnel.pem" -f -N -L6379:defigo-dev-redis.0u3tp7.0001.eun1.cache.amazonaws.com:6379 ubuntu@ec2-13-53-188-35.eu-north-1.compute.amazonaws.com
	ssh -i "Defigo-Stockholm-Redis-tunnel.pem" ubuntu@ec2-13-53-188-35.eu-north-1.compute.amazonaws.com


###############################################################
# Install Docker
sudo apt-get install -y docker.io
sudo systemctl start docker
sudo systemctl enable docker
sudo docker run hello-world
sudo groupadd docker
sudo usermod -aG docker $USER
exit
docker images

	#----------------------------------------------------------------
	# Install Docker-compose
	sudo curl -L https://github.com/docker/compose/releases/download/1.24.0-rc1/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
	sudo chmod +x /usr/local/bin/docker-compose
	docker-compose --version

# ---------------------------------------
# DOCKER LOGS

docker ps
docker inspect --format='{{.LogPath}}' 'ID'
du -h 
# посмотреть сколько занимают места логи докера
docker ps -qa | xargs docker inspect --format='{{.LogPath}}' | xargs ls -hl
# Remove not used images
docker rmi $(docker images -f "dangling=true" -q)
docker rm $(docker ps --filter=status=exited --filter=status=created -q)




	#############################################################
	# Clone GIT repo with SSH-key not ID_RSA
	#
	GIT_SSH_COMMAND='ssh -i ~/.ssh/lanars-invoices-ci-cd' git clone git@gitlab.lanars.com:Lanars-Internal-Tools/invoices-backend.git


##############################################################
# NGINX GZIP check
sudo nano /etc/nginx/nginx.conf
# gzip on;
#   gzip_disable "msie6";
#   gzip_vary on;
#   # gzip_proxied any;
#   gzip_comp_level 6;
#   gzip_buffers 16 8k;
#   gzip_http_version 1.1;
#   gzip_types text/plain text/css application/json application/javascript application/x-javascript text/xml application/xml application/xml+rss text/javascript;
sudo nginx -t
sudo systemctl reload nginx.service


	#####################################################
	# SWAPPINESS
	cat /proc/sys/vm/swappiness
	sudo sysctl vm.swappiness=10
	sudo nano /etc/sysctl.conf
	# add line     	vm.swappiness=10


#####################################################
# Update Mattermost server 
mv mattermost/server/ mattermost/server-old
mv mattermost54/server/ mattermost/server

sudo systemctl list-timers

#############################################################

# NGINX iOS release cruck
# Pre-release iOS change servers for specific versions
#               if ($http_accept_version = "1.0.0") {
#                       proxy_pass http://stage-api.memento.lanars.com;
#               }
