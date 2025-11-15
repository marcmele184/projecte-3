




####   

# **1. Instal·lació màquina Zorin**

 

- El primer pas serà instal·lar la màquina Zorin amb un mínim de 4 GB de RAM i 2 nuclis de processador.

![](img/26.png)

- Per poder començar amb la gestió de discos, primer haurem de crear-los. En aquest cas, jo he creat 3 discos de 10 GB. Un cop els tenim creats, ja podem entrar a la màquina Zorin per començar a treballar-hi.

![](img/27.png)

# **2. Configuració Inicial**

- Abans de poder començar haurem d’instal·lar **fdisk** amb la comanda:

`sudo apt install fdisk`

- Un cop dins de la terminal, executarem la comanda: `fdisk -l` Això ens permet veure els diferents discos. En aquest cas, podem observar que tenim el disc principal de 25 GB (sda) i els 3 discos de 10 GB creats prèviament (sdb, sdc, sdd).

![](img/28.png)

- Quan ja hem confirmat que podem veure els 3 discos, haurem de crear els volums físics amb la comanda `pvcreate` **(Physical Volume Create)**.

- Per poder fer servir aquesta comanda, haurem d’instal·lar **lvm2** amb:

`sudo apt install lvm2`

- I després crearem els volums físics amb:

	`pvcreate /dev/sdb`  
	`pvcreate /dev/sdc`  
	`pvcreate /dev/sdd`

![](img/29.png)

- Un cop creats els volums físics, toca crear el **grup de volums**. Això ens permet ajuntar tots els volums dels discos en un sol conjunt per disposar d’un únic espai des del qual podrem crear els volums lògics.

- A la primera captura podem veure com creo el grup de volums amb un sol disc fent servir la comanda:  
  `vgcreate nom_del_grup /dev/sdb`

![](img/30.png)

- A continuació, amb la comanda:

  `vgextend “nom del grup de volum” “la ruta del disc volum logic del disc` 

- Per així poder afegir els discos dins del grup de volum, ja que quan l’hem creat únicament hem afegit un sol disc

![](img/31.png)

- Per comprovar la informació del grup, utilitzarem:  
  `vgdisplay`

![](img/32.png)

- Després de crear el grup de volums, toca crear els volums lògics amb la comanda:

`lvcreate -L 200M -n lv01 volgrup`

- Amb aquesta comanda podrem indicar l’espai que volem fer servir, en aquest cas sera de
  200 Mib amb el nom lv01

![](img/33.png)

- Amb `vgdisplay` podem veure que l’espai està sent utilitzat.

![](img/34.png)

- Ara que ja hem creat el volum lògic, el següent pas és formatejar-lo, ja que els volums lògics funcionen com particions dins del sistema d’arxius.  
- Per poder fer això el primer pas sera crear la carpeta per poder muntar el volum, per fer això farem la següent comanda

`mkdir /mnt/lv01`

- Tot seguit farem servir la comanda `mkfs.ext4` per poder formategar el volum, tal com es pot veure a la captura de pantalla

![](img/35.png)

- Un com que ja tenim el volum formategat, el següent pas sera muntar el disc, i això ho farem amb la comanda `mount` per poder muntar el volum a la carpeta creada anteriorment

![](img/36.png)

- Això ja funcionaria, però cada cop que reiniciem la màquina caldria tornar a muntar el volum manualment. Per evitar-ho, farem un muntatge permanent editant l’arxiu **/etc/fstab** i afegint l’entrada corresponent.

![](img/37.png)

# **3. Alta Disponibilitat**

- Ara utilitzarem una tècnica de redundància per augmentar la seguretat: **el mirror**, que és similar al RAID 1 però aplicat a volums lògics.

- Per fer això, primer hem d’eliminar els volums lògics i el grup de volums.

Per poder fer això seguirem aquests passos:

- Desmuntarem el volum lògic amb la comanda `umount /mnt/lv01` per desmuntar el volum lògic i `lvremove` per eliminar-lo

![](img/38.png)

- I l’eliminarem amb: `vgremove volgrup`

![](img/39.png)

- Un cop que ja hem esborrat tant els volums lògics com el grup de volum tocarà crear un nou grup de volum amb el nom vg\_mirror tal com veurem a la següent comanda 

![](img/40.png)

- Un cop que ja hem creat el grup de volum tocarà fer el mirror

![](img/41.png)

- Un cop que ja tenim fet això podem observar com el volum lògic està format pels miralls i dels logs que serveixen per mantenir la sincronització  
- Tot això fent servir la següent comanda tal com es veu a la guia

![](img/42.png)

- Un cop que ja tenim el mirror creat correctament simularem una falla per poder recuperar el disc perdut.  
- Per començar, haurem d’apagar la màquina virtual i desconnectar un disc, per fer un exemple treure el 2n disc

![](img/43.png)

- Un cop que hem entrat a la màquina farem servir la comanda `lvdisplay` i veurem com hi ha un problema amb el disc sdb, ja que no està

![](img/44.png)

- Un cop confirmat que falta un disc apagarem la màquina i afegirem un disc, en aquest cas he afegit un disc nou que es diu copia

![](img/45.png)

Un cop fet que tornem a iniciar la maquina podrem veure que automàticament tornarem a tindre el disc restaurat

# **4. Instantànies** 

- Un cop que ja sabem com fer el mirror continuarem fent una snapshot, per començar eliminarem el volum anterior tal com hem fet anteriorment.

- Un cop fet això tornarem a crear un volum lògic, aquest cop amb el nom `lvm_dades`

![](img/46.png)

- Un cop fet això el formatarem i el muntem a `/mnt/lv01` 

![](img/47.png)

- Quan ja el tenim muntat farem uns arxius de prova amb la comanda touch per poder comprovar que la snapshot es fa correctament

![](img/48.png)

- Un cop que ja tenim els arxius creats farem la snapshot amb la següent comanda 

![](img/49.png)

- Un cop que ja tenim la snapshot creada haurem d’aconseguir veure-la, per fer això haurem de fer el següent:

- Primer crear la carpeta al directori `/mnt`

![](img/50.png)

- Un cop fet això muntem al disc a la carpeta que acabem de crear i un cop fet això ja podrem veure els arxius

![](img/51.png)

- Un cop fet això podem veure com hem creat correctament la snapshot.

- Ara provarem que la snapshot, serveix per recuperar les dades.  
- Per tant, el primer pas sera esborrar un arxiu del disc per poder comprovar que podem recuperar les dades, ho farem amb la comanda `rm`

![](img/52.png)

- Per continuar el següent pas sera desmuntar els 2 discos amb la comanda `umount`

![](img/53.png)

- Un cop que ja tenim els discos desmuntats farem servir la snapshot per poder recuperar el file04 amb les següents comandes, podem veure que hem pogut recuperar amb èxit el file04, per tant, hem pogut restaurar correctament la snapshot

![](img/54.png)

# **5. Escalabilitat**

- Per acabar, explicaré com podem ampliar un volum que ja hem muntat.  
- Per començar, s’ha de desmuntar el disc, això ho farem amb la comanda `umount.`  
- Un cop que ja tenim el disc desmuntat farem servir la comanda `lvextend`, aquesta comanda ens servirà per estendre el volum

![](img/55.png)

- Un cop que ja tenim el volum ampliat el següent pas sera ampliar el sistema de fitxers, per poder fer això seguirem 2 comandes, la primera serà.

`e2fsck -f /dev/volgrup/lvm_dades`

![](img/56.png)

- Aquesta comanda serveix per comprovar que no hi ha error abans de modificar el sistema d’arxius per poder evitar qualsevol problema  
- I la segona comanda sera 

`resize2fs /dev/volgrup/lvm_dades`

![](img/57.png)

# **6. Preparació Maquina virtual  WINDOWS**

![](img/1.png)

- Anem a **Configuració → Almacenament** a la màquina virtual.
   
![](img/2.png)

- Premem el **\+ del disc dur** per afegir un nou disc.
  
![](img/3.png)

- Seleccionem **Crear**.![][image39]

- Assignem **10 GB** i el creem.

![](img/4.png)

- Repetim el procés dues vegades més fins a tenir **3 discos virtuals**.
  
![](img/5.png)

- Seleccionem cada disc i fem clic a **Seleccionar** per afegir-los a la màquina virtual.  

![](img/6.png)

- Repetirem el procés amb els altres 2 discos, quedaria així.  

![](img/7.png)

# **7. Creació del Pool d'Emmagatzematge**

- Encenem la nostra màquina de Windows i fem clic dret al símbol de Windows; una vegada aquí premem **Administració de discos**.  

![](img/8.png)

- Un cop dins, ens sortirà aquesta finestra; fem clic a **Acceptar**, el que inicialitzarà els discos i els farà utilitzables.  

![](img/9.png)

- Ara cercarem a la barra de cerca de Windows **Administrar espacios de almacenamiento**.  

![](img/10.png)

- Entrarem a un menú on premem **Crear un nuevo grupo y espacios de almacenamiento**, això serà per a la creació del grup de discos.  

![](img/11.png)

- Crearem un grup amb els 3 discos.  

![](img/12.png)


# **8. Resiliència mirall doble**

- Dins del grup posarem el nom que vulguem i la lletra de unitat que vulguem. A **Resistencia** posarem **Reflejo doble**, i a **Tamaño máximo** posarem **14 GB** finalmente picarem a crear espacio de almacenamiento.  

![](img/13.png)

- Ja tenim el **mirall doble**.  

![](img/14.png)

- Per simular que ha fallat un disc, hem desconnectat un d’ells.  

![](img/15.png)

- Quan tornem a encendre la màquina, com podem veure, tots els arxius s’han mantingut sense cap problema.  

![](img/16.png)

  # 

# **9. Resiliència de paritat**

El **mirall** guarda una còpia exacta de les dades als 2 discos; té una **alta disponibilitat** perquè si es perd un disc, no es perd cap informació.  
Al contrari, la **paritat** no duplica totes les dades, sinó que guarda informació de paritat que permetrà reconstruir-les si un dels discs falla; per tant, és més eficient que el mirall pel fet que no duplica totes les dades, cosa que fa que no es gasti tant d’espai.

- Ara, per fer la paritat, entrarem un altre cop a **Espacios de almacenamiento** i premem **Cambiar configuración**, que ens deixarà modificar-lo; li donarem a **Eliminar**.  

![](img/17.png)


- Un cop donat **Eliminar**, tindrem aquest menú on premem **Eliminar espacio de almacenamiento**, el que eliminarà el nostre mirall doble per crear-ne un de nou.  

![](img/18.png)

- Ara tindrem el grup lliure; li donarem a **Crear un espacio de almacenamiento**.  

![](img/19.png)

- Ara configurarem un nou espai d’emmagatzematge: en **Tipo de resistencia** posarem **Paridad** i li assignarem un espai, en aquest cas **14 GB**.  

![](img/20.png)

- Podem veure que aquí tenim el nostre **espai en paritat** ben configurat.  

![](img/21.png)

- Ara repetirem el procés d'eliminar un disc i el tornarem a posar, cosa que ens permetrà observar que les dades estan intactes  

![](img/26.png)


# **10. Resiliència mirall triple**

- Ara seguirem el mateix procés que abans per eliminar la paritat i farem un **triple mirall**. Per poder fer el triple mirall, haurem de posar **2 discos més** i repetir els passos del principi, fent un grup d’espai en aquest cas de **5 discos**.  

![](img/22.png)

- Quan el creem, podrem crear el nou espai de **triple mirall**.  

![](img/23.png)

  

- Podrem veure que tenim el **triple mirall**.  

![](img/24.png)

- Ara repetirem el procés d'eliminar un disc i el tornarem a posar el que ens permetrà observar que les dades estan intactes  

![](img/16.png)

- Dins del propi menú d’espais d’emmagatzematge podem gestionar moltes coses, així com veure l’estat dels discos i quant espai estan donant si premem a **Unitats físiques**.


![](img/24.png)

- Per exemple si aquest disc dins d'una paritat falla ens sortirà quin és el que falla com a una advertència   

![](img/25.png)
