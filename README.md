# tlulinuxiabi
Kasulikud asjad informaatika kursuse õpilasele, et kasutada linuxit koolitöödeks

#######################################################################

TLU siseveebi (greeny ja tunneli ühendus ehk PUTTY ja WINSCP asendus)

#######################################################################

Kuna mina kasutan fedora 41te, mis on veits uuemäärmuslik siis võib olla, et osasid samme ei ole vaja. Tasub proovida sialgu ühendada, ehk toimib. Kui ei toimi saab hakata confi asju uurima.

##############
Putty asendus:
##############

Terminal lahti ja toksida sisse (ilma dollarimärgita) ning sisestada oma paroolid järgmistele käskudele:

Greeny:
$ ssh -L 8022:greeny.cs.tlu.ee:22 sinu-tlu-emaili-kasutajanimi@lin2.tlu.ee

$ ssh greeny.cs.tlu.ee -l greeny-kasutajanimi\r

Tunnel:
ssh -L 3128:cache.tlu.ee:3128 sinu-tlu-emaili-kasutajanimi@lin2.tlu.ee

Sa saad teha ka kiire bashi skripti mis logib automaatselt sind sisse ning esitada selle kodutööna Operatsioonisüsteemidesse. Minul tekkis probleeme uute terminali akende loomisega, milleks pidin teise terminali installima ning paroolide sisestamiseks
ja automatiseerimiseks pidin kasutusele võtma expecti.


###############
WINSCP asendus:
###############

Ehk sftp ühendus.
Gnome brauseril Nautilus on sftp klient sisse ehitatud. Sa lihtsalt paned tunneli käima, lähed kausta 'Network' või  osadel vanematel 'other location', toksid sisse 
sftp://localhost:8022
ja kasutad seda nagu see oleks folder sinu enda arvutis. 

##############
Conf:
##############

Ju siis ei toiminud. 
Kuna fedora41, nagu ennist mainitud, veits radikaalsem ja uuem, siis sa ei taha su OS esialgu lubada ühendust kooli serveritega turvariskide tõttu. Selleks tuleb lisada ssh conf faili hostinimed ja võtmed ning 
downgradeda turvatserte. Õnneks asi on kiire ja lihtne.

SSH faili conf: 
Kui faili ei ole, tuleb luua.
Siin on lisatud nii greeny ja ka teise semestril vaja minev TLU SFTP ühendus. Kui sul tekib mõne kooli teenusega ühendusprobleem uuel süsteemil, tasub alati leida viis ühendust terminalist testida, et näha kas on vaja siia uusi hoste lisada.

Terminali conf:
Kuna fedora certid on liiga uued, siis tuleb superuser doga sisse lükata käsk, et ssh kasutaks legacy või fedora39 tserte. See tuli välja nõnda et ükskõik mis hosti faili configa ta ei toiminud ja osade confidega andis tserti errorit.



###############
Postrgres conf:
################

Teine semester OOPis tuleb kasutusele postgres ja kuna linuxit default kasutatakse tavaliselt selle serverina, siis on asjad veidi teised: tasub tirida PGadmin4 software centerist ja postgresi server terminalist eraldi.
Toon siinkohal välja, etpgadmin4 ei ole server vaid ainult gui sellele. Need on eraldatud linuxil ja mis kõige tüütum, sa pead postgresi andmebaasi servu ümber
confima, et ta saaks localhostis pgadminniga räägitud. Selleks tuleb auth method muuta conf failis ümber md5ks. Väga huvitav ja tore protsess, mida ei soovita jätta tunni ajal tegemiseks.



Esialgu aga postgre serveri install: Järgmised käsud viisakalt ükshaaval terminali toksida: (doltsimärk ees tähistab käsku)
(Link kust käsud varastati: https://developer.fedoraproject.org/tech/database/postgresql/about.html
teine kasulik link alternatiivse lähenemisega: https://docs.fedoraproject.org/en-US/quick-docs/postgresql/)

$ sudo dnf install postgresql postgresql-server    # installib clienti/serveri
$ sudo postgresql-setup --initdb --unit postgresql # initsialiseerib.
$ sudo systemctl start postgresql                  # Stardib serveri. Seda pead tegema iga kord kui tahad postgresi kasutada. Saab ka sättima autostartima, kui sa tahad miskipärast seda oma gamerrigil teha.
$ sudo su - postgres                               #siseneb postgresi
$ createuser kasutajanimisiia -P                   #selle järel küsib paroole. Nendega saad pgadminnis ligi. Soovitan kuulata õpetaja soovitusi parooli ja kasutajanime määramisel. Meil oli nt kasutaja postgres ja parool 1234 või root.

Confifaili saab nanoga muuta kiirelt siit:
(allikas:  https://idroot.us/install-postgresql-fedora-41/)

$ sudo nano /var/lib/pgsql/data/pg_hba.conf

Siis tasub kerida alla, ning veenduda, et ülemine blokk näeks välja selline, kus viimane tulp muudetud lihtsalt MD5'ks

type    database       user              method

local   all             all                                     md5

host    all             all             127.0.0.1/32            md5

host    all             all             ::1/128                 md5



Ja nüüd saad lihtsalt startida postgresi käsuga
$ sudo systemctl start postgresql 
ja logida sinna sisse pgadmin4jast. 

