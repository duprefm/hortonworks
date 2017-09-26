# hortonworks
Déploiement d'une plateforme Hortonworks a l'aide de containers Dockers.

https://github.com/sequenceiq/docker-ambari
http://blog.sequenceiq.com/blog/2014/06/17/ambari-cluster-on-docker/
https://docs.hortonworks.com/HDPDocuments/Ambari-2.1.1.0/bk_Installing_HDP_AMB/content/_setup_options.html
https://ambari.apache.org/1.2.1/installing-hadoop-using-ambari/content/ambari-chap6-1.html
https://docs.hortonworks.com/HDPDocuments/Ambari-2.1.0.0/bk_ambari_reference_guide/content/_install_the_ambari_agents_manually.html


$ docker build -t fabricedupre/ambari-server .
$ docker images  # permet de retrouver l'image construite.
$ docker tag <> fabricedupre/ambari-server:latest
$ docker login
$ docker push fabricedupre/ambari-server


docker run -d --name some-postgres -e POSTGRES_DB=ambari -e  -d postgres
docker run --privileged -d -p 8080:8080 -h ambari-server --name ambari-server fabricedupre/ambari-server 

# Setup apache
yum install -y httpd && yum clean all
systemctl enable httpd
systemctl start httpd

ambari-server setup -s
ambari-server start
systemctl enable ambari-server


echo "n 2 y n" | ambari-server setup


docker build -t fabricedupre/ambari-agent .


-------------------------------------------------------------------------------------------------------------------------------------------
- Network:
$ docker network create --driver overlay --attachable BigData-network
- Volumes:
$ docker volume create Volumepgdata
- Base Posgres:
$ docker run --name postgres -h postgres --net BigData-network -v pgdata:/var/lib/postgresql/data -e POSTGRES_PASSWORD=bigdata -e POSTGRES_USER=ambari -e POSTGRES_DB=ambari -d postgres
- PhpMyadmin:
$ docker run --name myadmin -d --link postgres:db -p 8081:80 --net BigData-network phpmyadmin/phpmyadmin

- Ambari server (contruction):
$ cd /home/ec2-user/docker-ambari-I2SLabs/Ambari-server
Création d'un Dockerfile.
$ docker build -t fabricedupre/ambari-server .
$ docker tag 2c0b3d13e367 fabricedupre/ambari-server:latest
$ docker run --privileged -d -p 8080:8080 -h ambari-server --net BigData-network --name ambari-server fabricedupre/ambari-server
$ docker exec -it 9e458e9e3033 /bin/bash
....
[root@ambari-server tmp]# ambari-server setup (initilisation standard avec configuration de la base postgresql)
....
[root@ambari-server tmp]# ambari-agent reset ambari-server
....
[root@ambari-server tmp]# ambari-agent start
....
[root@ambari-server tmp]# ambari-server start
....
[root@ambari-server tmp]# systemctl enable ambari-server
....
[root@ambari-server tmp]# systemctl enable ambari-agent
....
[root@ambari-server tmp]# ambari-server setup --jdbc-db=postgres --jdbc-driver=/tmp/postgres-jdbc-driver.jar
....
$ docker commit d7e70a283d74 fabricedupre/ambari-server:latest
$ docker tag 5cbcd0332f1bb0eb17f1a924737310de6641db9f824657c3f7d7d70241dd236a fabricedupre/ambari-server:latest
$ docker push fabricedupre/ambari-server:latest

$ docker run -d -p 8080:8080 -h ambari-server --net BigData-network --name ambari-server fabricedupre/ambari-server

- Ambari agent (construction):
cd /home/ec2-user/docker-ambari-I2SLabs/Ambari-agent
Création d'un Dockerfile.
$ docker build -t fabricedupre/ambari-agent .
$ docker tag 4a6907e7bf82 fabricedupre/ambari-agent:latest
$ docker run --privileged -d -h ambari-agent --net BigData-network --name ambari-agent fabricedupre/ambari-agent
$ docker exec -it 5c1436d9e384 /bin/bash
....
[root@ambari-agent tmp]# ambari-agent reset ambari-server
....
[root@ambari-agent tmp]# ambari-agent start
....
[root@ambari-agent tmp]# systemctl enable ambari-agent
....
$ docker commit 5c1436d9e384 fabricedupre/ambari-agent:latest
$ docker tag 8a6acb6270f0cd3a49c72728e14a92c7a383d383042bdeaf928b24cc7a08ad17 fabricedupre/ambari-agent:latest
$ docker push fabricedupre/ambari-agent:latest

Sur un autre noeud de mon cluster :
$ docker run --privileged -d -h ambari-agent --net BigData-network --name ambari-agent fabricedupre/ambari-agent
