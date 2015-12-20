---
title: "Grand nettoyage du cache des images nova, _base"
slug: nova-image-cache-openstack
date: 20-12-2015 09:00
published: true
taxonomy:
  tag: [nova, cache, glance]
metadata:
  description: "OpenStack Nova et la gestion du cache des images _base"
  keywords: "OpenStack, Nova, Glance, imagecache, cachemanager, _base"
  author: "Leslie-Alexandre DENIS"
image: base.jpg
---
#<i class="fa fa-cubes"></i> Mais c'est quoi \_base dans Nova ?

Avant toute chose, clarifions le rôle du répertoire \_base sur votre hyperviseur. Lorsque vous instanciez une VM, le service **nova-compute** va initier une connexion HTTP vers Glance et récupérer l'image que vous avez spécifiez au *nova boot*.  
C'est là que rentre en jeu $instances_path/\_base ! L'image sera mise en cache pour pouvoir simplement être réutilisée ultérieurement par d'autre instanciation sur le même HV. Ca évite bien des transferts HTTP, cool non ? :)

Vous pouvez naviguer dans $instances_path/\_base et retrouver les UUID des images Glance. Petit conseil, pour profiter au maximum du **cache manager**, essayez d'utiliser au maximum les mêmes images, ça évitera d'avoir un max d'exemplaires !

La définition de l'option :  

> (StrOpt) Where cached images are stored under $instances_path. This is NOT the full path - just a folder name. For per-compute-host cached images, set to \_base_$my_ip

#<i class="fa fa-fire"></i> Bon c'est cool ton truc, mais ça pète pas un peu le DF ?

Et bien si, plus le nombre d'images différentes est grand, plus \_base sera grand, duh.  
C'est la que la puissance d'*OpenStack* fait plaisir, avec un bout de code qui permet de nettoyer régulièrement le bordel inutilisé.  

Dans *nova.conf* vous devez jouer sur :  

- remove_unused_base_images = True

> (BoolOpt) Should unused base images be removed?

- remove_unused_original_minimum_age_seconds = 86400

> (IntOpt) Unused unresized base images younger than this will not be removed

- image_cache_manager_interval = 2400

> (IntOpt) Number of seconds to wait between runs of the image cache manager. Set to -1 to disable. Setting this to 0 will run at the default rate.

- image_cache_subdirectory_name = \_base

> (StrOpt) Where cached images are stored under $instances_path. This is NOT the full path - just a folder name. For per-compute-host cached images, set to \_base_$my_ip

- (optionnel) remove_unused_resized_minimum_age_seconds = 3600

> Unused resized base images younger than this will not be removed

- (optionnel) checksum_base_images = false

> Write a checksum for files in \_base to disk

- (optionnel) checksum_interval_seconds = 3600

> How frequently to checksum base images

Avec la configuration par défaut, le cachemanager passera toutes les 2400sec pour opérer un gros rm sur la gueule des images orphelines.  
Faites gaff quand même à une chose, si par mégarde une image de base utilisée est supprimée, les instances s'y référant seront bonnes à jeter. Les QCOW2 (*sparse*) des instances ne contiennent que la différence avec la base de l'image.  

La feature a été évoqué dans ce blueprint [blueprint](https://wiki.openstack.org/wiki/Nova-image-cache-management) et le code, pour libvirt, est ici [imagecache.py](https://github.com/openstack/nova/blob/master/nova/virt/libvirt/imagecache.py).

#<i class="fa fa-coffee"></i> Tout pareil mais à la mano

Ok, imagecache.py c'est cool, mais vous n'avez peut-être pas confiance vu la criticité ?  
Et bien dans ce cas vous pouvez faire la même chose en 2 lignes de shell et un bon café.  

```shell
for i in /instances/*-*-*-*-*/disk;do qemu-img info $i | grep "backing file" >> used-images.log;done;
for i in /instances/_base/*;do if [[ $(grep -c "$i" /instances/used-images.log) == 0 ]];then echo "$i is unused" ;fi;done;
```

Après ça, plus qu'à supprimer gentillement les images de cache qui *back* aucune instance.  

Bon nettoyage de block,  
LAD
