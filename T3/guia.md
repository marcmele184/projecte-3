
####    

1. # **Instal·lació màquina Zorin**

 

- El primer pas serà instal·lar la màquina Zorin amb un mínim de 4 GB de RAM i 2 nuclis de processador.

![][image1]

- Per poder començar amb la gestió de discos, primer haurem de crear-los. En aquest cas, jo he creat 3 discos de 10 GB. Un cop els tenim creats, ja podem entrar a la màquina Zorin per començar a treballar-hi.

![][image2]

2. # **Configuració Inicial**

- Abans de poder començar haurem d’instal·lar **fdisk** amb la comanda:

`sudo apt install fdisk`

- Un cop dins de la terminal, executarem la comanda: `fdisk -l` Això ens permet veure els diferents discos. En aquest cas, podem observar que tenim el disc principal de 25 GB (sda) i els 3 discos de 10 GB creats prèviament (sdb, sdc, sdd).

![][image3]

- Quan ja hem confirmat que podem veure els 3 discos, haurem de crear els volums físics amb la comanda `pvcreate` **(Physical Volume Create)**.

- Per poder fer servir aquesta comanda, haurem d’instal·lar **lvm2** amb:

`sudo apt install lvm2`

- I després crearem els volums físics amb:

	`pvcreate /dev/sdb`  
	`pvcreate /dev/sdc`  
	`pvcreate /dev/sdd`

![][image4]

- Un cop creats els volums físics, toca crear el **grup de volums**. Això ens permet ajuntar tots els volums dels discos en un sol conjunt per disposar d’un únic espai des del qual podrem crear els volums lògics.

- A la primera captura podem veure com creo el grup de volums amb un sol disc fent servir la comanda:  
  `vgcreate nom_del_grup /dev/sdb`

![][image5]

- A continuació, amb la comanda:

  `vgextend “nom del grup de volum” “la ruta del disc volum logic del disc` 

- Per així poder afegir els discos dins del grup de volum, ja que quan l’hem creat únicament hem afegit un sol disc

![][image6]

- Per comprovar la informació del grup, utilitzarem:  
  `vgdisplay`

![][image7]

- Després de crear el grup de volums, toca crear els volums lògics amb la comanda:

`lvcreate -L 200M -n lv01 volgrup`

- Amb aquesta comanda podrem indicar l’espai que volem fer servir, en aquest cas sera de         200 Mib amb el nom lv01

![][image8]

- Amb `vgdisplay` podem veure que l’espai està sent utilitzat.

![][image9]

- Ara que ja hem creat el volum lògic, el següent pas és formatejar-lo, ja que els volums lògics funcionen com particions dins del sistema d’arxius.  
- Per poder fer això el primer pas sera crear la carpeta per poder muntar el volum, per fer això farem la següent comanda

`mkdir /mnt/lv01`

- Tot seguit farem servir la comanda `mkfs.ext4` per poder formategar el volum, tal com es pot veure a la captura de pantalla

![][image10]

- Un com que ja tenim el volum formategat, el següent pas sera muntar el disc, i això ho farem amb la comanda `mount` per poder muntar el volum a la carpeta creada anteriorment

![][image11]

- Això ja funcionaria, però cada cop que reiniciem la màquina caldria tornar a muntar el volum manualment. Per evitar-ho, farem un muntatge permanent editant l’arxiu **/etc/fstab** i afegint l’entrada corresponent.

![][image12]

3. # **Alta Disponibilitat**

- Ara utilitzarem una tècnica de redundància per augmentar la seguretat: **el mirror**, que és similar al RAID 1 però aplicat a volums lògics.

- Per fer això, primer hem d’eliminar els volums lògics i el grup de volums.

Per poder fer això seguirem aquests passos:

- Desmuntarem el volum lògic amb la comanda `umount /mnt/lv01` per desmuntar el volum lògic i `lvremove` per eliminar-lo

![][image13]

- I l’eliminarem amb: `vgremove volgrup`

![][image14]

- Un cop que ja hem esborrat tant els volums lògics com el grup de volum tocarà crear un nou grup de volum amb el nom vg\_mirror tal com veurem a la següent comanda 

![][image15]

- Un cop que ja hem creat el grup de volum tocarà fer el mirror

![][image16]

- Un cop que ja tenim fet això podem observar com el volum lògic està format pels miralls i dels logs que serveixen per mantenir la sincronització  
- Tot això fent servir la següent comanda tal com es veu a la guia

![][image17]

- Un cop que ja tenim el mirror creat correctament simularem una falla per poder recuperar el disc perdut.  
- Per començar, haurem d’apagar la màquina virtual i desconnectar un disc, per fer un exemple treure el 2n disc

![][image18]

- Un cop que hem entrat a la màquina farem servir la comanda `lvdisplay` i veurem com hi ha un problema amb el disc sdb, ja que no està

![][image19]

- Un cop confirmat que falta un disc apagarem la màquina i afegirem un disc, en aquest cas he afegit un disc nou que es diu copia

![][image20]

- Un cop que hem confirmat que tornem a tenir 3 discos dins de la màquina amb la comanda `fdisk -l`, el següent pas sera inicialitzar el pv

![][image21]

- Tot seguit haurem de tornar a afegir el disc a `vg_mirror`

![][image22]

- I fer la següent comanda per restablir el mirror

![][image23]

- Un cop fet això ja hauríem restablert el mirror en el nostre disc de manera exitosa

4. # **Instantànies** 

- Un cop que ja sabem com fer el mirror continuarem fent una snapshot, per començar eliminarem el volum anterior tal com hem fet anteriorment.

- Un cop fet això tornarem a crear un volum lògic, aquest cop amb el nom `lvm_dades`

![][image24]

- Un cop fet això el formatarem i el muntem a `/mnt/lv01` 

![][image25]

- Quan ja el tenim muntat farem uns arxius de prova amb la comanda touch per poder comprovar que la snapshot es fa correctament

![][image26]

- Un cop que ja tenim els arxius creats farem la snapshot amb la següent comanda 

**![][image27]**

- Un cop que ja tenim la snapshot creada haurem d’aconseguir veure-la, per fer això haurem de fer el següent:

- Primer crear la carpeta al directori `/mnt`

![][image28]

- Un cop fet això muntem al disc a la carpeta que acabem de crear i un cop fet això ja podrem veure els arxius

![][image29]

- Un cop fet això podem veure com hem creat correctament la snapshot.

- Ara provarem que la snapshot, serveix per recuperar les dades.  
- Per tant, el primer pas sera esborrar un arxiu del disc per poder comprovar que podem recuperar les dades, ho farem amb la comanda `rm`

![][image30]

- Per continuar el següent pas sera desmuntar els 2 discos amb la comanda `umount`

![][image31]

- Un cop que ja tenim els discos desmuntats farem servir la snapshot per poder recuperar el file04 amb les següents comandes, podem veure que hem pogut recuperar amb èxit el file04, per tant, hem pogut restaurar correctament la snapshot

![][image32]

5. # **Escalabilitat**

- Per acabar, explicaré com podem ampliar un volum que ja hem muntat.  
- Per començar, s’ha de desmuntar el disc, això ho farem amb la comanda `umount.`  
- Un cop que ja tenim el disc desmuntat farem servir la comanda `lvextend`, aquesta comanda ens servirà per estendre el volum

![][image33]

- Un cop que ja tenim el volum ampliat el següent pas sera ampliar el sistema de fitxers, per poder fer això seguirem 2 comandes, la primera serà.

`e2fsck -f /dev/volgrup/lvm_dades`

![][image34]

- Aquesta comanda serveix per comprovar que no hi ha error abans de modificar el sistema d’arxius per poder evitar qualsevol problema  
- I la segona comanda sera 

`resize2fs /dev/volgrup/lvm_dades`

![][image35]

6. # **Preparació Maquina virtual  WINDOWS**

**![][image36]**

- Anem a **Configuració → Almacenament** a la màquina virtual.  
  ![][image37]

- Premem el **\+ del disc dur** per afegir un nou disc.  
  ![][image38]

- Seleccionem **Crear**.![][image39]

- Assignem **10 GB** i el creem.

  ![][image40]

- Repetim el procés dues vegades més fins a tenir **3 discos virtuals**.  
  ![][image41]

- Seleccionem cada disc i fem clic a **Seleccionar** per afegir-los a la màquina virtual.  
  ![][image42]

- Repetirem el procés amb els altres 2 discos, quedaria així.  
  ![][image43]

7. # **Creació del Pool d'Emmagatzematge**

- Encenem la nostra màquina de Windows i fem clic dret al símbol de Windows; una vegada aquí premem **Administració de discos**.  
  ![][image44]

- Un cop dins, ens sortirà aquesta finestra; fem clic a **Acceptar**, el que inicialitzarà els discos i els farà utilitzables.  
  ![][image45]

- Ara cercarem a la barra de cerca de Windows **Administrar espacios de almacenamiento**.  
  ![][image46]

- Entrarem a un menú on premem **Crear un nuevo grupo y espacios de almacenamiento**, això serà per a la creació del grup de discos.  
  ![][image47]

- Crearem un grup amb els 3 discos.  
  ![][image48]


8. # **Resiliència mirall doble**

- Dins del grup posarem el nom que vulguem i la lletra de unitat que vulguem. A **Resistencia** posarem **Reflejo doble**, i a **Tamaño máximo** posarem **14 GB** finalmente picarem a crear espacio de almacenamiento.  
  ![][image49]

- Ja tenim el **mirall doble**.  
  ![][image50]

- Per simular que ha fallat un disc, hem desconnectat un d’ells.  
  ![][image51]

- Quan tornem a encendre la màquina, com podem veure, tots els arxius s’han mantingut sense cap problema.  
  ![][image52]

  # 

9. # **Resiliència de paritat**

El **mirall** guarda una còpia exacta de les dades als 2 discos; té una **alta disponibilitat** perquè si es perd un disc, no es perd cap informació.  
Al contrari, la **paritat** no duplica totes les dades, sinó que guarda informació de paritat que permetrà reconstruir-les si un dels discs falla; per tant, és més eficient que el mirall pel fet que no duplica totes les dades, cosa que fa que no es gasti tant d’espai.

- Ara, per fer la paritat, entrarem un altre cop a **Espacios de almacenamiento** i premem **Cambiar configuración**, que ens deixarà modificar-lo; li donarem a **Eliminar**.  
  ![][image53]


- Un cop donat **Eliminar**, tindrem aquest menú on premem **Eliminar espacio de almacenamiento**, el que eliminarà el nostre mirall doble per crear-ne un de nou.  
  ![][image54]

- Ara tindrem el grup lliure; li donarem a **Crear un espacio de almacenamiento**.  
  ![][image55]

- Ara configurarem un nou espai d’emmagatzematge: en **Tipo de resistencia** posarem **Paridad** i li assignarem un espai, en aquest cas **14 GB**.  
  ![][image56]

- Podem veure que aquí tenim el nostre **espai en paritat** ben configurat.  
  ![][image57]

- Ara repetirem el procés d'eliminar un disc i el tornarem a posar, cosa que ens permetrà observar que les dades estan intactes  
  ![][image58]


10. # **Resiliència mirall triple**

- Ara seguirem el mateix procés que abans per eliminar la paritat i farem un **triple mirall**. Per poder fer el triple mirall, haurem de posar **2 discos més** i repetir els passos del principi, fent un grup d’espai en aquest cas de **5 discos**.  
  ![][image59]

- Quan el creem, podrem crear el nou espai de **triple mirall**.  
  ![][image60]

  

- Podrem veure que tenim el **triple mirall**.  
  ![][image61]

- Ara repetirem el procés d'eliminar un disc i el tornarem a posar el que ens permetrà observar que les dades estan intactes  
  ![][image62]

- Dins del propi menú d’espais d’emmagatzematge podem gestionar moltes coses, així com veure l’estat dels discos i quant espai estan donant si premem a **Unitats físiques**.

  ![][image63]

- Per exemple si aquest disc dins d'una paritat falla ens sortirà quin és el que falla com a una advertència   
  ![][image64]
