
# T04: Serveis de directori. LDAP


Innovatech, una start-up tecnològica emergent, està experimentant un ràpid creixement i pateix un caos en la gestió dels seus usuaris i accessos.
Actualment, cada servei intern (servidor de fitxers, wiki de documentació, etc.) utilitza la seva pròpia base de dades d'usuaris i contrasenyes i a més als ordinadors clients s’usa autentificació local. Això genera diversos problemes crítics:
Ineficiència Operativa: Cada cop que s'incorpora o marxa un empleat, l'equip tècnic ha de crear o eliminar el compte en múltiples sistemes.
Risc de Seguretat: Els usuaris sovint acaben reutilitzant contrasenyes entre serveis per evitar l'oblit.
Manca d'Escalabilitat: A mesura que Innovatech afegeix nous serveis, el problema es fa insostenible.

---

El CEO d’Innovatech ha contactat amb EverPia per tal d’implementar una solució d’autenticació centralitzada. La solució proposada és utilitzar OpenLDAP (Lightweight Directory Access Protocol) per ser una solució robusta i de codi obert, que s’alinea amb l’esperit d’Innovatech, ja que tots els ordinadors de l’empresa usen GNU-Linux.
La vostra missió serà implementar el servei OpenLDAP en un servidor Linux. Això implica instal·lar el servei, configurar el domini base, crear la jerarquia d'unitats organitzatives i, finalment, integrar usuaris i grups que posteriorment s'utilitzaran per donar accés a altres serveis de xarxa. A més, configurareu un equip client per tal que utilitzi el directori per autenticar els usuaris.
S’ha redactat un document on s’especifica clarament la feina que s’ha de desenvolupar, el teniu disponible en el plec de condicions tècniques (també el podeu trobar al Moodle de l’assignatura).
Material de classe (disponible al Moodle)

[UD04.AA1 Serveis de Directoriñ](https://docs.google.com/presentation/d/1x5i7JaRtzXtdNADtBevuX1HIMJkICo4O/edit?usp=drive_link&ouid=104728425662496836733&rtpof=true&sd=true)

[UD04.AA2 Instal·lació OpenLDAP](https://docs.google.com/presentation/d/1k3qzGN8Zp8jQYH6sqNJeEfj5os0ziksD/edit?usp=drive_link&ouid=104728425662496836733&rtpof=true&sd=true)

[UD04.AA3 Configuració del directori](https://docs.google.com/presentation/d/1y4Av3fDSca9K3Oij-fQKJ6GsoaoKc0S-/edit?usp=drive_link&ouid=104728425662496836733&rtpof=true&sd=true)

[UD04.AA5 Agregar client al directori](https://docs.google.com/presentation/d/1vlN5itS7RyiTEq4Do6xnBe-bPzMdl5nt/edit?usp=drive_link&ouid=104728425662496836733&rtpof=true&sd=true)

---

# Solució
