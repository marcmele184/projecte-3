# 🧩 T04 — Serveis de Directori LDAP

**Autor:** Marc Melendo Vicens  
**Data:** 25/09/2025  

---

## 🖥️ Configuració inicial del servidor

### 1. Modificació del hostname
Edita l'arxiu /etc/hosts i canvia la segona línia afegint:
server.innovatechXX.test server
(Substitueix XX pel número de llista)

![Edició de /etc/hosts](images/hosts_config.png)

---

### 2. Configuració de les interfícies de xarxa
Adaptador 1: NAT (per defecte)  
Adaptador 2: Host-Only → activar abans d’iniciar la màquina

Edita /etc/netplan/<nom_arxiu>.yaml i activa DHCP a enp0s8

Executa:
sudo netplan apply

![Configuració del fitxer netplan](images/netplan_setup.png)

---

## 🧱 Instal·lació i configuració d’OpenLDAP

Instal·lació:
sudo apt install slapd ldap-utils

Contrasenya: p@ssw0rd

Comprovació:
systemctl status slapd
slapcat

Si cal:
sudo dpkg-reconfigure slapd

![Comprovació slapd](images/slapd_status.png)

---

## 🗂️ Creació d’OUs (users i groups)

Exemple OU_users.ldif:
dn: ou=users,dc=innovatech,dc=test
objectClass: organizationalUnit
ou: users

Afegir:
sudo ldapadd -x -D cn=admin,dc=innovatech,dc=test -W -f OU_users.ldif

Validar:
ldapsearch -x -b dc=innovatech,dc=test ou

![Verificació OU](images/ldapsearch_ou.png)

---

## 🌐 Instal·lació i configuració de LAM

Instal·lar:
sudo apt install ldap-account-manager

Accés via navegador:
http://192.168.56.101/lam

Login configuració:
Usuari → lam
Contrasenya → lam

Modificar:
- Domini: innovatech.test
- Admin DN: cn=admin,dc=innovatech,dc=test

Canviar tipus d’usuari i grup segons configuració

Nou login:
Usuari → admin
Contrasenya → p@ssw0rd

![Configuració LAM](images/lam_config.png)

---

## 👥 Creació de grups i usuaris

Grups:
tech
manager

Usuaris:
tech01 → grup primari tech01 + secundari tech  
manager01 → grup primari manager01 + secundari manager  

Contrasenya: 1234 (forçar canvi)

![Usuaris creats](images/users_created.png)

---

## 🧩 Configuració del client (Zorin OS)

Afegir Host-Only

Modificar:
- /etc/hosts
- /etc/hostname

Reiniciar i validar:
hostname -f
dig server.innovatechXX.test

![Configuració hosts client](images/client_hosts.png)

---

## 🔗 Integració LDAP al client

Instal·lar:
sudo apt install libnss-ldap libpam-ldap ldap-utils nscd

Domini LDAP:
innovatech.test  
Protocol: v3  
Admin DN: cn=admin,dc=innovatech,dc=test  
Contrasenya: p@ssw0rd

Validació:
getent passwd

![Integració LDAP](images/client_ldap_setup.png)

---

## 🔒 Configuració PAM

Editar /etc/pam.d/common-password → eliminar "use_authtok"

Editar /etc/pam.d/common-session → afegir al final:
session required pam_mkhomedir.so skel=/etc/skel/ umask=0022

Reiniciar:
sudo systemctl restart nscd

![Configuració PAM](images/pam_config.png)

---

## 🧠 Validació final

Iniciar sessió: Not listed?  
Usuari: tech01  
Contrasenya: 1234

Comprovar:
id

![Comprovació grups](images/id_check.png)

---

## ✅ Resultat Final

✔ LDAP configurat  
✔ LAM operatiu  
✔ 2 OUs, 2 grups, 2 usuaris  
✔ Client Zorin autenticant contra LDAP

---

## 📂 Estructura recomanada del projecte

T04-LDAP/
├── README.md
├── images/
│   ├── hosts_config.png
│   ├── netplan_setup.png
│   ├── slapd_status.png
│   ├── ldapsearch_ou.png
│   ├── lam_home.png
│   ├── lam_config.png
│   ├── lam_account_types.png
│   ├── groups_created.png
│   ├── users_created.png
│   ├── client_hosts.png
│   ├── client_ldap_setup.png
│   ├── pam_config.png
│   ├── id_check.png
│   └── ...
└── .gitignore

---

## ⚙️ Fitxer .gitignore suggerit

*.swp
*.log
*.tmp
.DS_Store
Thumbs.db
.vscode/
.idea/

---

📌 Només falta afegir les imatges a la carpeta "images/" amb els mateixos noms perquè tot es vegi correctament a GitHub ✅
