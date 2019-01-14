Playbook que crea i configura la màquina virtual on s'allotjarà el repositori. Serveix com exemple per desplegar màquines. Definir variables:

Globals (Infraestructura)
=========================

* VMWARE_DATACENTER
* VMWARE_CLUSTER

Globals (Accounting)
=========================

users: llista d'usuaris amb camps

* name: Nom
* sudo: True o False
* key: clau SSH


docker_gitlab_dir: directori on s'instal·la el gitlab


Secretes (Emprau credencials AWX o Ansible Vault)
=========================

* secret_vmware_host
* secret_vmware_user
* secret_vmware_password
* secret_users_pass: llista d'usuaris amb camps:
 * pass: md5sum de la contrasenya 


Locals (Per màquina)
====================

* ip
* hostname
* template_name
* template_folder
* destination_folder
* netmask
* gateway
* network
* eth0_dnsserver

Creau un inventari amb totes aquestes variables a AWX.


# Docker

Fitxers a roles/gitlabjap/files/gitlabjap per aixecar els contendors del repositori de codi.

Pre-requisits
=============

Instalar docker i docker-compose. Cercar a la documentació oficial de Docker
de com fer-ho. La manera preferida es afegir el repostori de docker i instaŀlar
el docker-engine. Despres amb el curl agafar el compose i deixar-lo a
/usr/local/bin.


Assumpcions
===========
Aquest servei s'aixeca al port 8000 per http. S'asumeix que algú altre (Caddy)
esta per davant fent el https. Si es vol posar aquest mateix amb https
es pot afegir el caddy al docker-compose o be exposar el port 443 i posar
certificats TLS.


Com desplegar
=============

Crear carpeta de dades i de logs al costat del docker-compose.yml

    mkdir data
    mkdir logs

Copiar el fitxer `dot_env` a `.env` i ajustar les variables d'entorn

    cp dot_env .env
    vim .env

Finalment arrancar el docker

    docker-compose up -d

Operacions
==========

Per executar qualsevol de les operacions s'ha de fer des de dins la carpeta
repositori/docker/gitlab com a root:

    cd repositori/docker/gitlab

Comprovar estat
---------------

    docker-compose ps

Arrancar (primer cop)
--------

    docker-compose up -d

Arrancar
--------

    docker-compose start

Aturar
------

    docker-compose stop


Reiniciar
---------
Recomenat emprar aquest quan s'ha tocat alguna configuració del gitlab
per tal d'aplicar-la. Recordar que el gitlab pot estar una estona a aconseguir
aixecar-se.

    docker-compose restart

Veure logs
----------

    docker-compose logs -f


Entrar al container
-------------------
No hauria de ser MAI necessari per canviar configuracions però pot ser
útil per debug.

    docker-compose exec web bash


Corregir permisos del gitlab
----------------------------
Els de gitlab suggereixen que si ten problemes de permisos facis quelcom

    docker-compose run web update-permissions

Fer un backup
-------------

    docker-compose web gitlab-rake gitlab:backup:create

Apareixarà dins la carpeta `data/backups` amb un timestamp.

Esborrar container penjats
--------------------------
Si hem emprat el run es possible que algun container quedi penjat.
Els podem identificar amb el `docker-compose ps` i despres esborrar amb docker.

    docker stop repositori_web_run_X
    docker rm repositori_web_run_X


