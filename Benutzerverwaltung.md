---
title: Benutzerverwaltung
description: 
published: true
date: 2026-06-01T16:48:46.979Z
tags: anlegen, bearbeiten, benutzer, editieren,, löschen, passwort
editor: markdown
dateCreated: 2026-06-01T15:39:18.494Z
---

```plaintext
Während der Installation vom CRANIX werden mehrere Benutzer angelegt,
die für verschiedene Funktionen des Servers notwendig sind:
```

| Benutzername | Rolle | Berechtigung / Gruppenzugehörigkeit | Funktion | Bemerkung |
| --- | --- | --- | --- | --- |
| root | \-- | darf alles machen | Systemadministration von der Linuxkonsole | Nur direkte Anmeldung am CRANIX oder Fernwartung per ssh. |
| Administrator | sysadmins | Domain Administrator | Verwaltung der Domäne, Webadministration | Keine direkte Anmeldung am Server. Passwort nach der Installation identisch mit dem vom **root** . Ersetzt den Benutzer **admin** von OSS-3.4.2 |
| register | internal | Administrators | Ausführung automatischer Domänenadministration ( z.B. Domainbeitritt nach dem Klonen ) | Erhält ein zufälliges Passwort während der Installation, welches in <code>/opt/cranix-java/conf/cranix-api.properties</code> gespeichert wird. Setzt man die Systemvariable **RESET\_REGISTER\_PASSWORD** auf **yes**, wird sein Passwort jeden Tag neu generiert um die Sicherheit des Systems zu erhöhen. Standardeinstellung ist **no** . |
| ossreader | internal | Domain User | Ausführung automatischer Arbeiten, welche nur lesende Rechte benötigen. | Passwort ist **ossreader** und darf nicht geändert werden. |
| cephalix | internal | Domain Administrator | Verwaltung des Servers vom CEPHALIX-Server aus. | Der CEPHALIX-Server greift über diesen Benutzer auf den Server zu. Alle Objekte (Benutzer, Gruppen, Softwares ...), die vom **cephalix** erstellt werden, können nur vom dem CEPHALIX Server aus verwaltet werden. Auch der Benutzer **Administrator** hat keinen Zugriff auf diese Objekte. |
| tteachers | teachers | Domain User, templates | Templatebenutzer für die Lehrer. | Beim Anlegen eines Lehrers wird sein Homeverzeichnis in das Homeverzeichnis des neuen Benutzers kopiert. |
| tstudents | students | Domain User, templates | Templatebenutzer für die Schüler. | Beim Anlegen eines Schülers wird sein Homeverzeichnis in das Homeverzeichnis des neuen Benutzers kopiert. |
| tadministration | administration | Domain User, templates | Templatebenutzer für die Verwaltung. | Beim Anlegen eines Benutzers für die Verwaltung wird sein Homeverzeichnis in das Homeverzeichnis des neuen Benutzers kopiert. |
| tworkstations | workstations | Domain User, templates | Templatebenutzer für die Arbeitsplatzbenutzer | Bei der Registrierung eines Gerätes mit einer Gerätekonfiguration des Typs **FatClient** wird ein Workstation-Benutzer mit dem Namen und Passwort des Gerätes erstellt. Dieser Benutzer kann sich nur an diesem Gerät anmelden. |

## Systembenutzer vs Samba-Benutzer

Normalerweise pflegt Linux die Systembenutzer UNIX-gemäß in den Dateien /etc/passwd und /etc/shadow. Nur der Benutzer **root** vom CRANIX-Server ist in diesen Dateien zu finden. Der CRANIX-Server ist ein AD-Samba-Server und die weiteren Benutzer werden in der SAMBA-LDAP-Datenbank gespeichert. Damit diese auf Linux-Ebene auch sichtbar sind: wird auf dem SAMBA-Server auch der **winbind** Dienst gestartet. Dieser sorgt dafür, dass die SAMBA-Benutzer und Gruppen-SIDs in UNIX **uidNumber** und **gidNumber** umgewandelt werden. Dies geschieht durch folgende Einstellungen in der Datei **/etc/nsswitch.conf**:

```plaintext
passwd: compat winbind
group:  compat winbind
```

Zwar sind die Benutzer auf dem CRANIX-Server sichtbar und die Dateien können zugeordnet werden, jedoch ist zunächst eine Authentifizierung für Dienste über eine PAM-Schnittstelle (ssh, login, sftp, ftp, cyrus ...) nicht möglich. Wenn Sie zum Beispiel den SAMBA-Benutzern auch ssh Zugang zum CRANIX-Server gewähren möchten, muss dafür auch PAM konfiguriert werden:

```plaintext
 pam-config -a --winbind
```

## Benutzer einzeln anlegen

Wählen Sie im Hauptmenü **Benutzer**. Hier erhalten Sie eine Liste der aktuellen Benutzer. Klicken Sie rechts oben auf das Pluszeichen, um einen Benutzer anzulegen. <br> Folgende Felder müssen beim Anlegen eines neuen Benutzers unbedingt ausgefüllt bzw. gesetzt werden:

-   **Benutzername:** Wurde kein **Benutzerkürzel** (UID: User-ID) angegeben, wird dieser aus dem Nach- und Vornamen und ggf. Geburtsjahr  
    ermittelt. Dieser Vorgang wird durch die Systemvariable *LOGIN\_SCHEME* unter *Server->Konfiguration->Einstellungen* gesteuert. Der Standardwert ist N4V4. Das bedeutet, dass der Benutzername aus den ersten vier Buchstaben des Nachnamens und den ersten vier Buchstaben des Vornamens gebildet wird. Existiert im System schon ein Benutzer mit derselben UID, wird eine Zahl an die UID angehängt, damit diese eindeutig ist. Um das Geburtsjahr als Identifikator zu verwenden, müssen Sie den Buchstaben „Y“ gefolgt von den Ziffern 2 oder 4 angeben. Andere Zahlen sind für das Geburtsjahr nicht erlaubt.
-   **Kennung** In diesem Feld kann ein externer Identifikator, zB.  
    Personalnummer verwendet werden.
-   **Nachname**
-   **Vorname**
-   **Geburtstag**
-   **Rolle:** Wählen Sie eine primäre Gruppe (Rolle), der der neue Benutzer angehören soll. Weitere Gruppen können Sie später über das Menü Gruppen zuordnen. Sofern Sie noch keine Rollen angelegt haben, können Sie hier nur die Rollen Schüler, Lehrer, Verwaltung, Sysadmins oder Template Benutzer wählen.
-   **Klasse:**: Benutzer können zu einer beliebigen Anzahl von Klassen gehören. Ist der neu angelegte Benutzer ein Schüler, muss er jedoch mind. einer Klasse zugeordnet werden. Mit CTRL+A können Sie alle Klassen auswählen, um zB. einen Lehrer allen Klassen zuzuweisen.
-   **Passwort:** Darf leer bleiben. In diesem Fall wird ein sicheres Passwort generiert.
-   **Mailsystem- und Festplattenquota:** kann in MB angegeben werden. Gibt man keinen Wert an, werden die in den Systemvariablen FILE\_QUOTA, FILE\_TEACHER\_QUOTA, MAIL\_QUOTA und MAIL\_TEACHER\_QUOTA gesetzten Werte verwendet.

<gallery heights=250px widths=325px> Cocpit-Benutzer-Liste.jpeg|Liste  
der Benutzer Cocpit-Benuzter-Liste-MD.jpeg|Liste der Benutzer Mobiles  
Gerät Benutzer-Anlegen.png|Benutzer anlegen </gallery>

> Sie können auch selber eine **UID** für den neuen Benutzer angeben. Bitte beachten Sie dabei, dass die UID aus Kleinbuchstaben bestehen muss, keine Sonderzeichen oder Leerstellen enthalten darf und im System eindeutig sein muss.

Für jeden neuen Benutzer wird ein eigenes Homeverzeichnis angelegt. Die Lehrer bekommen ihre Verzeichnisse unterhalb des Verzeichnisses /home/teachers/ und die Schüler unterhalb des Verzeichnisses /home/students. „Verwaltungsnutzer“ bekommen ihre Verzeichnisse unterhalb des Verzeichnisses /home/administration.

Es wird kein Windows-Profil-Verzeichnis für die neuen Benutzer angelegt. Diese werden bei der ersten Abmeldung an von einem Windows-Client erstellt. Die Windowsprofile liegen im /home/profiles/ und haben das Format <uid>.<Windowsversion>.

## Benutzer importieren

Da es sehr mühsam wäre, die Schüler bzw. die Lehrer jedes Jahr von Hand  
einzutragen, bietet der CRANIX die Möglichkeit, die Liste  
der Schüler (und Lehrer) aus einer Datei zu importieren.

Die Datei sollte dazu folgende Voraussetzungen erfüllen:

-   Muss UTF-8 kodiert sein.
-   Die Trennzeichen zwischen den einzelnen Feldern können Doppelpunkt, Semikolon, oder Komma sein. Andere Zeichen wie zB. Leerzeichen und Tabulator sind nicht erlaubt. Wichtig ist natürlich: die Trennzeichen dürfen sich nicht im Verlauf der Datei ändern. So reicht die bei vielen Schulverwaltungsprogrammen existierende Export-Funktion in eine CSV oder Textdatei meist vollkommen aus.
-   Eine Importdatei darf nur Benutzer der gleichen Rollen enthalten, man kann auf einmal nur Schüler oder nur Lehrer importieren.

![](Benutzer-Importieren.png)

|rahmenlos|zentriert

### Importparameter

Beim Start des Imports kann, bzw. muss man verschiedene Parameter  
angeben:

**Rolle:** Die Rolle der zu importierenden Benutzer.

```plaintext
Kommandozeilenparameter: --role [administration,students,sysadmins,teachers,templates]
```

**Sprache:** legt die Sprache fest, in der die Kopfzeile der Importdatei geschrieben ist.

```plaintext
Kommandozeilenparameter: --lang [DE,EN] 
```

**Identifizierungsmerkmal:** legt fest wie die Benutzer identifiziert werden. Dafür gibt es 3 Möglichkeiten: Durch Nachname, Vorname und Geburtstag oder durch das Benutzerkürzel, womit man sich am CRANIX anmeldet oder durch einen externen Identifikator.

```plaintext
Kommandozeilenparameter: --identifier [sn-gn-bd,uid,uuid]
```

> *WICHTIG* hat man **Benutzerkürzel** oder **Kennung (uuid)** gewählt, muss dieser auch in der Importdatei vorhanden sein

**Als Test ausführen:**  
Es werden keine Änderungen durchgeführt, es  
wird nur dargestellt, was bei einem Import gemacht werden würde.  
Welche Benutzer werden neu angelegt, welche gelöscht, welchen Klassen  
werden die Benutzer zugeordnet.

```plaintext
Kommandozeilenparameter: --test. Dieser Parameter hat keinen Wert.
```

**Passwort:**  
Hier kann man ein Passwort für die Benutzer festlegen. Dieses wird in erster Linie nur für die neu angelegten Benutzer verwendet.

```plaintext
Kommandozeilenparameter: --password PASSWORD
```

**Passwort bei erster Anmeldung ändern:**  
Benutzer werden bei ihrer  
ersten Anmeldung an einem Windows-Gerät aufgefordert, ihr Passwort zu  
ändern. Wurde **Passwort zurücksetzen** aktiviert, bezieht sich diese  
Einstellung auch auf vorhandene  
Benutzer.

```plaintext
Kommandozeilenparameter: --mustChange Dieser Parameter hat keinen Wert.
```

**Importdatei enthält alle Benutzer.:**  
Dieser Parameter hat nur dann  
einen Einfluss auf den Import, wenn Schüler importiert werden. Ist  
dieser Parameter gesetzt, werden Schüler, die nicht in der Liste  
enthalten sind, gelöscht.

```plaintext
Kommandozeilenparameter: --full Dieser Parameter hat keinen Wert.
```

**Importdatei enthält alle Klassen. Andere werden gelöscht.**  
Dieser Parameter hat nur dann einen Einfluss auf den Import, wenn Schüler  importiert werden. Wird dieser Parameter angegeben wird, werden die Klassen, die in der Importdatei nicht vorkommen, aus dem System gelöscht.

```plaintext
Kommandozeilenparameter: --allClasses Dieser Parameter hat keinen Wert.
```

**Klassenverzeichnisse leeren:**  
Nach dem Import werden alle Klassenverzeichnisse gelöscht und neu angelegt.

```plaintext
Kommandozeilenparameter: --cleanClassDirs Dieser Parameter hat keinen Wert.
```

**Passwörter für alte Benutzer auch setzen.:**  
Wird dieser Parameter aktiviert, werden auch den vorhandenen Benutzern neue Passwörter zugewiesen. Wurde im Feld **Passwort** eingegeben, wird dieses  verwendet, sonst bekommt jeder Benutzer ein zufällig generiertes Passwort. Sie haben auch die Möglichkeit das Passwort in der Importdatei festzulegen.

```plaintext
Kommandozeilenparameter: --resetPassword Dieser Parameter hat keinen Wert.
```

**Geburtsdatum an das Passwort anhängen.:**  
Ist dieser Schalter aktiv bekommen neue Benutzer, oder wenn **Passwort zurücksetzten:** aktiv ist, alle Benutzer das eigene Geburtsdatum in Form JJJJ-MM-DD als Passwort gesetzt. Wird im Feld **Passwort** auch etwas eingetragen, wird das vor das Geburtsdatum gesetzt.

```plaintext
Kommandozeilenparameter: --appendBirthdayToPassword Dieser Parameter hat keinen Wert.
```

**Klassennamen an das Passwort anhängen.:**  
Ist dieser Schalter aktiv bekommen neue Benutzer, oder wenn **Passwort zurücksetzten:** aktiv ist, alle Benutzer den eigenen Klassennamen als Passwort gesetzt. Wird im Feld **Passwort** auch etwas eingetragen, wird das vor den Klassennamen gesetzt. Diese Parameter kann auch mit **Geburtsdatum an das Passwort anhängen.** kombiniert werden.

```plaintext
Kommandozeilenparameter: --appendClassToPassword Dieser Parameter hat keinen Wert.
```

### Format der Import CSV-Datei

In der ersten Zeile werden die Spalten und das Trennzeichen der Datei definiert. Zur Zeit sind folgende Schlüsselwörter erlaubt, Pflichtfelder sind als solche vermerkt:

| Feldname | Pflichtfeld/Bedeutung | Syntax |
| --- | --- | --- |
| NACHNAME | Pflichtfeld | Keine Begrenzung. Lediglich das Trennzeichen darf nicht vorkommen. |
| VORNAME | Pflichtfeld | Keine Begrenzung. Lediglich das Trennzeichen darf nicht vorkommen. |
| GEBURTSTAG | Haben Sie als **Identifizierungsmerkmal** "Name + Geburtstag" gewählt ist dies ein Pflichtfeld. | Folgende Datumformate werden erkannt: JJJJ-MM-TT, TT-MM-JJJJ, JJJJ:MM:TT, TT:MM:JJJJ, [JJJJ.MM.TT](http://JJJJ.MM.TT), TT.MM.JJJJ, JJJJMMTT |
| KLASSE | Neue Klasse(n) des Benutzers. Aus den aktuellen Klassen wird der Benutzer entfernt. | Wird der Benutzer mehreren Klassen zugeordnet, müssen diese durch Leerzeichen getrennt werden. |
| PASSWORT | Hier können Sie das Passwort eines jeden Benutzers optional, individuell bestimmen. | Siehe <a href="Benutzerverwaltung#Passwortsicherheit\_.28Password\_Policy.29" |
| class="wikilink" title="Passwortsicherheit">Passwortsicherheit |     |     |
| BENUTZERNAME | Sie können individuelle User-IDs eingeben. Nutzen Sie diese Möglichkeit, wenn Ihnen die vom CRANIX automatisch generierten User-IDs (Anmeldenamen) nicht gefallen. Haben Sie als **Identifizierungsmerkmal** "Benutzername" gewählt ist dieser ein Pflichtfeld. | Dar nur ASCII-Buchstaben, Ziffern, Punkt und Bindestriche enthalten. Darf jedoch nicht mit Punkt oder Bindestrich anfangen. |
| KENNUNG | In diesem Feld kann ein externer Identifikator, zB. Personalnummer verwendet werden. Haben Sie als **Identifizierungsmerkmal** "Kennung (UUID)" gewählt ist dies ein Pflichtfeld. | Keine Begrenzung. Lediglich das Trennzeichen darf nicht vorkommen. |
| GRUPPE | Wird der Benutzer mehreren Gruppen zugeordnet, müssen diese durch Leerzeichen getrennt werden. |     |
| MAIL-QUOTA | Mailsystemquota in MB |     |
| FESTPLATTENQUOTA | Filesystemquota in MB |     |

Die mit *Pflichtfeld* gekennzeichneten Felder sind obligatorisch.  
Verwenden Sie als Namen für die Spalten die exakte Schreibweise wie oben  
in den Tabellen. Andernfalls kann der CRANIX die Spalten nicht richtig  
erkennen. Groß-/Kleinschreibung ist jedoch irrelevant.

#### Abarbeiten der Importdatei

Es bestehen drei Möglichkeiten, wie mit Benutzerdaten nach dem Einlesen  
der Benutzerdatei weiter verfahren wird.

**1\. Neuer Benutzer**

Wird ein Benutzer in der Datenbank nicht gefunden, handelt es sich um einen neuen Benutzer. In diesem Fall wird für diesen Benutzer ein neuer eindeutiger Login Name, wie unter *Neu Anlegen einzelner Benutzer* beschrieben, ermittelt und dieser in die Datenbank aufgenommen. Falls vorhanden, wird das Feld PASSWORT wie folgt ausgewertet:

-   ‘text’ => ‘text’ wird als Passwort gesetzt.
-   ‘\*’ => Sollten Sie in ein und derselben Textdatei einigen Nutzern ein fest definiertes Passwort über ‘text’ zuweisen, für andere jedoch ein zufälliges Passwort generieren lassen wollen, so fügen Sie bei diesen Nutzern den ‘\*’ ein.
-   ‘kein Inhalt’ => Sollten anderen Nutzern ein Passwort über die beiden oben angegebenen Möglichkeiten (fest oder zufällig) zugewiesen worden sein, bei einem (oder mehreren) Nutzern in derselben Datei, aber kein Wert im Passwortfeld enthalten sein, so bekommen diese Nutzer kein Passwort, d.h. sie können sich ohne Passwort am System anmelden.

Ist das Feld PASSWORT in der Datei nicht vorhanden, wird ein zufälliges Passwort zugewiesen.

**2\. Vorhandene Benutzer ändern**

Steht ein Benutzer sowohl in der Datenbank als auch in der Benutzerliste, handelt es sich um einen alten Benutzer.

Bei vorhandenen Benutzern wird das Feld PASSWORT nicht ausgewertet. Die Benutzer werden lediglich aus der alten Klasse aus- und in die neue Klasse eingetragen.

**3\. Benutzer löschen**

> *Hinweis*: Diese Funktion funktioniert nur, wenn sie eine vollständige Liste der Schüler einlesen.

-   Steht ein Benutzer in der Datenbank, jedoch nicht in der Benutzerliste, bedeutet das, dass dieser Benutzer die Schule verlassen hat.
-   Also werden seine Daten aus der Datenbank gelöscht, sein Heimatverzeichnis wird in ein Archiv zusammengefasst und unter   **/home/archiv/<benutzername>-<datum>.tgz** gespeichert.

**Ergebnis**
Nach dem Abarbeiten der eingelesenen Datei wird die neue, aktuelle Benutzerliste pro Klasse in der Datei **/home/groups/SYSADMINS/userimports/DATUM.UHRZEIT/userlist.KLASSE.txt** im Gruppenverzeichnis der Systemadministratoren gespeichert. In dieser Datei stehen die Passwörter im Klartext und muss dementsprechend mit Vorsicht behandelt werden.

Hier ist eine Beispieldatei für das erste Laden des Systems mit  
unterschiedlichen Passwort-Vergaben:

```plaintext{.line-numbers}
GEBURTSTAG:NACHNAME:VORNAME:PASSWORT:KLASSE 
11.10.1986:Klein:Aladar:12345:9A 
4.08.1986:Micuc:Emil::9A
09.11.1986:Groß:Evelyn:*:9A 
17.04.1986:Müller:Helmuth:*:9A 10A: 
29.9.1987:Klein:Aladar:*:10A
```

Die resultierenden Dateien:

-   /home/groups/SYSADMINS/userimports/<datum>.<uhrzeit>/userlist.9A.txt
-   /home/groups/SYSADMINS/userimports/<datum>.<uhrzeit>/userlist.10A.txt

## Passwortsicherheit

Die Sicherheit der Passwörter wird im CRANIX auf zwei Ebenen  
sichergestellt. Zunächst wird jedes Passwort beim Anlegen von Benutzern  
bzw. beim Ändern der Passwörter durch Benutzter auf bestimmte  
Sicherheitsmerkmale kontrolliert. Weiterhin kann man festlegen wie lange  
ein Passwort gültig ist, also nach wie vielen Tagen Benutzer ihre  
Passwörter ändern müssen bzw. wie mit fehlerhaften Anmeldeversuchen  
umgegangen werden soll.

### Überprüfung von Passwörtern

Beim Anlegen neuer Benutzer bzw. beim Ändern der Passwörter von  
vorhandenen Benutzern wird die Qualität des Passwortes überprüft. Um die  
Komplexität der Passwörter besser kontrollieren bzw. beschreiben zu  
können, liefert der CRANIX ein eigenes Script für diesen Zweck:  
**/usr/share/cranix/tools/check\_password\_complexity.sh** Dieses prüft ob  
das Passwort folgende Voraussetzungen erfüllt:

Technische Voraussetzungen:

-   Minimale Passwortlänge ist eingehalten. Die minimale Passwortlänge  
    wird durch die globale Variable MINIMAL\_PASSWORD\_LENGTH festgelegt.
-   Maximale Passwortlänge ist eingehalten. Die maximale Passwortlänge  
    wird durch die globale Variable MAXIMAL\_PASSWORD\_LENGTH festgelegt.
-   Das Passwort darf kein '&' und '§'-Zeichen enthalten. Das kommt aus  
    einer Restriktion von Office365 von Microsoft.
-   Das Passwort darf nicht mit Bindestrich '-' beginnen.

Sicherheitsrelevante Voraussetzungen:

-   Das Passwort muss mindestens einen Großbuchstaben enthalten.
-   Das Passwort muss mindestens einen Kleinbuchstaben enthalten.
-   Das Passwort muss mindestens eine Ziffer enthalten.
-   Das Passwort muss die Prüfung durch cracklib-check bestehen.

Dieses Programm liest das Passwort aus dem STDIN. Dieses Script wurde in  
**/etc/samba/smb.conf** als *check password script* für Samba auch als  
Überprüfungsskript gesetzt. Wer eine eigene Passwortüberprüfung  
erstellen möchte kann ein Script namens  
**/usr/share/cranix/tools/custom\_check\_password\_complexity.sh**  
erstellen. Dieses Script muss folgende Bedingungen erfüllen:

-   muss ausführbar sein.
-   muss das zu evaluierende Passwort aus der Standardeingabe lesen.
-   muss beim Erfolg mit einem Exitwert von 0 sonst ein Wert größer als 0  
    aufhören.
-   muss beim fehlerhaften Passwort den Grund auf die Standardausgabe  
    liefern.

Bitte beachten Sie, dass dieses Script nur die Sicherheitsrelevante  
Überprüfung ersetzt. Die technische Überprüfung bleibt nach wie vor  
erhalten.

<div class="warningbox">Mit der globalen Variable  
CHECK\_PASSWORD\_QUALITY kann man die Überprüfung der  
sicherheitsrelevanten Voraussetzungen ausschalten. Die technische  
Voraussetzungen müssen jedoch immer erfüllt werden. Das gilt auch, wenn  
Lehrer die Passwörter von Schüler zurücksetzen. Zwar wird in diesem Fall  
automatisch CHECK\_PASSWORD\_QUALITY auf no gesetzt, die technische  
Voraussetzungen müssen trotzdem erfüllt werden.</div>

### SAMBA Passwortrichtlinien (Password Policies)

Sie können die folgenden Passworteinstellungen mit CRANIX-4  
konfigurieren:

-   Wird die Komplexität des Passworts überprüft?
-   Minimale Passwortlänge. Dies kann über die globale Variable MINIMAL\_PASSWORD\_LENGTH gesetzt werden.
-   Maximale Passwortlänge. Das ist keine Standard Samba/Windows Passworteinstellung. In der Praxis hat es sich jedoch als sinnvoll erwiesen, die Länge der Passwörter zu begrenzen. Dies kann über die globale Variable MAXIMAL\_PASSWORD\_LENGTH eingestellt werden.
-   Mindestalter des Passworts
-   Maximales Passwortalter
-   Die Anzahl ungültiger Anmeldeversuche, die vor dem Sperren des Kontos zulässig sind.
-   Die Zeitspanne, in der ein Konto gesperrt wird, nachdem das Limit für fehlerhafte Anmeldeversuche überschritten wurde.

Grundsätzlich können diese Werte von der Kommandozeile mit samba-tool Befehl abgefragt bzw. gesetzt werden:

**Abfrage der aktuellen Werte:**

`samba-tool domain passwordsettings show`  
`Password complexity: on`  
`Store plaintext passwords: off`  
`Password history length: 24`  
`Minimum password length: 7`  
`Minimum password age (days): 1`  
`Maximum password age (days): 365`  
`Account lockout duration (mins): 30`  
`Account lockout threshold (attempts): 0`  
`Reset account lockout after (mins): 30`

> WICHTIG das sind die Standardwerten\`

**Auflisten der Befehlsoptionen**

`samba-tool domain passwordsettings set -h`

Bei der Installation wird für die Passwörter ein Jahr als Gültigkeit  
gesetzt. Mit folgendem Befehl werden alle Benutzer dazu gezwungen, die  
Passwörter halbjährlich zu ändern:

`samba-tool domain passwordsettings set --max-pwd-age=182`

Mit dem Wert **0** für **\--max-pwd-age** erreicht man, dass die  
Passwörter nie ihre Gültigkeit verlieren. Möchte man, dass einzelne  
Benutzer ihre Passwörter nie ändern müssen, muss man folgenden Befehl  
verwenden:

`samba-tool user  setexpiry --noexpiry <uid>`

## Benutzer über cranix-api verwalten

Man kann von der Kommandozeile über die cranix-api die Benutzer auch verwalten.

### Benutzer auflisten

`crx_api.sh GET users/all listet alle Benutzer in JSON List auf.`  
`crx_api.sh GET users/byRole/{role} listet alle Benutzer einer Rolle in JSON List auf.`  
`crx_api_text.sh GET users/byUid/{uid}/{parameter} Mit diesem Call kann man die Parameter eines Benutzers abfragen.`  
`crx_api_text.sh GET text/{userName}/classes Listet alle Klassen auf in der der Benutzer Mitglied ist`  
`crx_api_text.sh GET text/{userName}/grousp Listet alle Gruppen auf in der der Benutzer Mitglied ist`

### Benutzer erstellen

Zuerst muss man eine Datei mit einem JSON-Hash der Benutzerdaten erstellen. Diese muss mindestens die obligatorische Felder erhalten:

> `{`  
> `   "sn" : "Mouse",`  
> `   "givenName" : "Micky",`  
> `   "birthDay" : "2007.04-17",`  
> `   "role" : "students"`  
> `}`

Dieser muss in einer Datei gespeichert werden und kann nun mit folgendem Call den Benutzer erstellen:

> ` crx_api_post_file.sh users/add <Pfad zur JSON-Hash-File>`

### Benutzer löschen

Man kann einen Benutzer anhand seiner uid oder Datenbank-ID löschen:

`crx_api.sh DELETE users/text/{uid}`  
`crx_api.sh DELETE users/{Datenbank Id}`

### Gruppenmitgliedschaft von Benutzer ändern

`crx_api_text.sh PUT users/text/{userName}/groups/{groupName}`  
`crx_api_text.sh DELETE users/text/{userName}/groups/{groupName}`  
`crx_api_text.sh PUT users/text/{userName}/allClassess`  
`crx_api.sh PUT users/allTeachersInAllClasses`