Després de l’exitosa experiència a nivell de formació, el nostres clients de **Digicore** estan tan satisfets amb la nostra feina que ens encarreguen la implantació des de zero dels seus serveis de DNS interns.

Actualment, l'agència fa servir adreces IP per accedir als seus servidors de desenvolupament, bases de dades i eines de gestió interna. Aquest mètode és ineficient i propens a errors:

* **Usabilitat deficient**: Els empleats han de memoritzar o buscar constantment adreces IP complexes (p. ex., 192.168.10.25).  
* **Manteniment feixuc**: Si un servidor canvia la seva IP, cal notificar i actualitzar manualment la configuració a tots els equips i aplicacions que s'hi connecten.  
* **Manca de professionalitat**: En un entorn professional, tots els serveis haurien de ser accessibles mitjançant noms fàcils de recordar.

Per tant, la nostra missió és implementar un Sistema de Noms de Domini (DNS) intern robust. L'objectiu és que els servidors i aplicacions de l'agència es puguin accedir utilitzant noms de domini amigables (p. ex., bbdd.digicore.lan o wiki.digicore.lan).


**El vostre repte**

La recomanació com a consultora és utilitzar BIND9, l'estàndard de facto de servidor de noms a Linux, per la seva fiabilitat i flexibilitat.

La vostra missió serà instal·lar i configurar un servidor DNS primari (màster) amb BIND9 en un sistema Linux. Haureu de crear una Zona Directa (Forward Zone) i una Zona Inversa (Reverse Zone) per al domini privat de DigiCore, garantint que tant els noms es resolguin a IPs com les IPs es resolguin a noms.

Per fer una prova de concepte, usareu el domini **digicore-XX.test** on **XX** serà el vostre **número de llista**.

**Pas previ.** Configurar un Ubuntu Server amb 4 GB de RAM i 20 GB de disc. Aquesta màquina virtual haurà de tenir una interfície en **adaptador pont** (la configuració que s’haurà d’utilitzar s’indicarà a l’inici del repte) i una segona en **host-only**. Un cop configurat, caldrà instal·lar el paquet bind9. Instal·leu el servei **ssh** al server, perquè després caldrà exportar els arxius de configuració al vostre repositori de GitHub.

**Accions a realitzar**

1. Configurar l’arxiu **named.conf.options** perquè accepti consultes recursives de la seva xarxa local, haurà d’usar com reenviador la IP 8.8.8.8. Mostra la captura. Un cop fet, reiniciar el servei i comprovar que funciona (mostra l’estat).  
2. Utilitzar un client, pot ser la màquina Zorin que has usat anteriorment, canviant l’adaptador a **adaptador pont**. La configuració que s’haurà d’utilitzar s’indicarà a l’inici del repte, però cal que servidor de noms (DNS) sigui la IP del vostre servidor. Comprova que el client té resolució a Internet (obrir una pàgina al navegador o fes un dig google.com)  
3. Editar l’arxiu **named.conf.local** per definir dues zones, les corresponents a la **zona directa** del domini **digicore-XX.test** i la corresponent a la **zona inversa** de la xarxa local, a la prova de concepte, l’adreça de xarxa local que s’està utilitzant.  
4. Crear l’arxiu corresponent a la zona directa (ha d’estar dins una carpeta que s’anomenarà **zones** que cal crear prèviament a /etc/bind. Per comoditat els pot crear copiant l’arxiu **db.local**.  
5. Configurar aquest arxiu amb els següents registres:  
   1. SOA (amb les dades correctament configurades)  
   2. NS que sigui el vostre servidor  
   3. Un registre A anomenat **server** amb la IP del servidor.  
   4. Un registre A anomenat **dbserver** amb la IP del client.  
   5. Un registre àlies (CNAME) amb nom **data** al registre **dbserver**  
6. Crear l’arxiu corresponent a la zona inversa (ha d’estar dins una carpeta que s’anomenarà **zones** que cal crear prèviament a /etc/bind. Per comoditat es pot crear copiant l’arxiu **db.127.**  
7. Configurar aquest arxiu:  
   1. SOA i NS adients.  
   2. Registres PTR corresponents al **server** i al **dbserver**.  
8. Reiniciar servei i fer les comprovacions des del client als diferents fent consultes directes i inverses.  
9. Editar l’arxiu **named.conf.local** per permetre la transferència  de la zona directa als companys de l’equip.  
10. Fer les configuracions necessàries per tenir una zona secundària del domini d’un dels companys. Forçar la transferència i comprovar el funcionament des del client.

**Activitat d’avaluació del repte**

Per demostrar que sou uns tècnics competents haureu de passar una **avaluació pràctica** al final del repte. Durant aquesta avaluació només podreu usar com a material de suport un **full manuscrit** amb les vostre anotacions, que lliurareu en finalitzar la prova.

