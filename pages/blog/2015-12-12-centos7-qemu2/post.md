---
title: "CentOS 7 et QEMU 2.x pour chauffer votre CPU"
slug: centos7-et-qemu2-openstack
date: 13-12-2015 00:40
published: true
taxonomy:
    tag: [centos, qemu]
metadata:
    description: "Your page description goes here"
    keywords: "HTML, CSS, XML, JavaScript"
    author: "Leslie-Alexandre DENIS"
image: qemu.jpg
---
#Pourquoi les gens confondent qemu et qemu-kvm ?

Il est vrai, qu'avant la version 1.3 de QEMU un fork nommé qemu-kvm était développé en parallèle afin d'exploiter au maximum la puissance du module KVM.  
Cependant, et oui, depuis la 1.3 les deux projets ont fusionné.  

>  QEMU 1.3
>  - as of the 1.3 release all qemu-kvm features have been merged into upstream QEMU

Donc pour les conservateurs, inutile de rester sur un traditionnel qemu-kvm, le code upstream qemu intègre bien l'ensemble.  

Voir plus ici : [Wiki QEMU](http://wiki.qemu.org/KVM)  

#Pourquoi sous CentOS 7 les gars sont bloqués en Aôut 2013 ?

Bien souvent, QEMU est installé depuis les dépôts officiels par défaut et malheureusement qemu-kvm est en version 1.5.3, certainement avec certains backport de l'upstream mais ça sucks quand même.  
Bien caché, CentOS propose désormais un dépôt orienté virtualisation maintenu par le groupe [Virt SIG](https://wiki.centos.org/SpecialInterestGroup/Virtualization), composé de gars d'oVirt notamment.  

Le dépôt contient à ce jour *qemu-kvm-ev-2.1.2-23.el7_1.8.1*, par ici : [virt](http://mirror.centos.org/centos/7.1.1503/virt/x86_64/kvm-common/)  

#Finalité

Gardez bien en tête que **QEMU** est la base de votre cloud, si jamais vous en faites hein, et qu'il est nécessaire d'y prêter beaucoup d'attention.  
Etre bien à jour avec ce dernier c'est profiter des backports de code upstream. A ce sujet, RedHat a présenté comment ils contribuent via RHEV aux projets upstream et comment ils gèrent QEMU/KVM, c'est fascinant.  

Le talk sur YouTube ici : [Red Hat Enterprise Virtualization Hypervisor: KVM now & in the future](https://youtu.be/bOeH-bjTSLw)  

Sur ce, bonnes mises à jour.  
LAD
