---
title: Netzwerkverwaltung
description: 
published: true
date: 2026-06-02T07:48:38.750Z
tags: räume, hardwarekonfiguration, software, image, clonetool, clonen
editor: markdown
dateCreated: 2026-06-02T07:12:02.035Z
---

## Grundlagen der Netzwerkverwaltung 
Unabhängig von dem Betriebsystem des Klientrechners müssen diese am CRANIX angemeldet werden, damit die Geräte Räumen zugeordnet und in die DNS– und DHCP–Dienste eingetragen werden. Diese Anmeldung bzw. Verwaltung der Workstations kann man leicht mit dem Webbrowser erledigen.

Die Netzwerkverwaltung erfolgt anhand folgender Objekte:
- Gerätekonfigurationen
- Räume
- Geräte
- DNS-Einträge
Alle Geräte werden anhand ihrer MAC-Adressen identifiziert. Besitz ein Gerät, zB. ein Laptop, sowohl eine Ethernet- als auch eine WLAN-Karte, können beide am CRANIX-Server registriert werden. Dadurch wird sichergestellt, dass ein Gerät immer auf die selbe Weise behandelt wird, unabhängig davon, wie es mit dem Netzwerk verbunden ist.

Für WLAN-Geräte wurden sog. private WLAN-Adressen eingeführt. Dieses Feature soll angeblich "die Privatsphäre weiter schützen". Das heißt, dass ein Gerät sich bei jedem WLAN mit einer anderen zufälligen MAC-Adressen meldet. Dieses Verhalten kann zu vielen Problemen führen, deshalb muss dieses Feature bei jedem Gerät abgeschaltet sein:
 [https://support.apple.com/de-de/HT211227 Apple]
 [https://www.heise.de/news/iOS-14-Private-WLAN-Adressen-koennen-fuer-Probleme-sorgen-4907542.html Heise]

## Hardwarekonfigurationen 
Die am CRANIX registrierten Geräte erhalten sog. Gerätekonfigurationen. Die Gerätekonfigurationen legen fest, wie die Geräte mit dem CRANIX verwendet werden können oder welche Funktion die Geräte im Netz haben. Ein Gerätekonfiguration wird mit folgenden Parametern erstellt.

{|class="wikitable" style="text-align: lef;"
! Parametername !! Beschreibung !! Syntax
|-
| name || Der Name der Gerätekonfiguration || Max 32 Zeichen
|-
| description || Ausführliche Beschreibung der Gerätekonfiguration || Max 64 Zeichen, kann leer sein
|-
| deviceType || Die Art der Geräte || Nur vorhandene Werte können verwendet werden
|}

Im nächsten Abschnitt sind die vordefinierten Gerätetypen erläutert:

* '''FatClient''' Normale stationäre oder mobile Rechner. Nur für Rechner die in solchen Gerätekonfigurationen sind, wird eine SALT-Konfiguration und ein Workstationsbenutzer erstellt. Darüber hinaus können nur solche Rechner geklont werden.
* '''BYOD''' Private Geräte von den Benutzern.
* '''Printer''' Netzwerkdrucker.
* '''Server''' Zusätzliche Server im Netz.
* '''Switch''' Switche im Netz.
* '''ThinClient''' Thinclients im Netz.
* '''cloneProxy''' Für das Klonen an WLAN-Geräten

Den FatClient-Gerätekonfigurationen werden durch das Klonen, während des Erstellens des Masterimages, Partitionen zugewiesen. Die Partitionen haben folgende Parameter
{|class="wikitable" style="text-align: lef;"
! Parametername !! Beschreibung !! Syntax !! Bemerkung
|-
| name || Der Kerneldevicename der Partition || Wird vom CloneTool ermittelt. (sda1, sda2 ...)
|-
| description || Ausführliche Beschreibung der Partition || Max 64 Zeichen. Boot, System ...
|-
| OS || Operationssystem auf der Partition || Nur vorhandene Werte können verwendet werden: Win10, Win11, Linux, Data
|-
| joinType || Windows Domainjoin || Nur vorhandene Werte können verwendet werden: no, Domain || Wird nur bei OS==Win10, Win11 verwendet
|-
| tool || Das verwendete Imagingtool || Nur vorhandene Werte können verwendet werden: partclone, Zpartclone, partimage, dd, dd_rescue
|-
| format || Filesystem auf der Partition || Nur vorhandene Werte können verwendet werden: msdos, vfat, ntfs, ext2, ext3, swap, clone, no || Wird nur bei OS==Data verwendet
|}


Beim CRANIX erfolgt die automatische Umbenennung von Rechnern und ggf. die Domänenaufnahme über SALT. Das heißt auch, dass nur Rechner mit einer Gerätekonfigurationen FatClient umbenannt oder in die Domäne aufgenommen werden.
Ob eine Domänenaufnahme erfolgt, hängt davon ab, ob es in der Gerätekonfiguration des Rechners eine Partition mit joinType=Domain existiert.

Der CRANIX liefert einige vordefinierte Rechnerkonfigurationen:
*'''Win10-64-Domain''' Rechner die vom CRANIX per SaltStack verwaltet und in die AD-Domäne aufgenommen werden.
*'''Win10-64-no-Domain''' Rechner die vom CRANIX per SaltStack verwaltet werden, aber nicht AD-Domänen-Mitglied sind.
*'''Server''' Nicht durch CRANIX verwaltete eigenständige Server. Sie können trotzdem Domänenmitglied sein, müssen jedoch manuell aufgenommen werden.
*'''BYOD''' '''B'''ring '''Y'''our '''O'''wn '''D'''evice Private Geräte. Diese werden auch nicht vom CRANIX per SaltStack verwaltet.
*'''cloneProxy''' Für das Klonen an WLAN Geräten

Wenn Sie das CloneTool von CRANIX nicht benutzen wollen, nehmen Sie Geräte, die in die Domäne aufgenommen werden müssen, in die vordefinierte Gerätekonfiguration Win10-64-Domain auf.

## Räume ##

Räume können über die Adminoberfläche unter Netzwerk -> Räume verwaltet und erstellt werden.
Beim Anlegen können/müssen folgende Angaben gemacht werden:
 
{|class="wikitable" style="text-align: lef;"
! Parametername !! Syntax !! Verfügbare Werte !! obligatorisch !! änderbar !! Verweis
|-
| Name || max 32 Zeichen. Nur DNS-konforme Zeichen || alles außer vorhandene Namen || Ja || Nein || [https://support.microsoft.com/de-de/help/909264/naming-conventions-in-active-directory-for-computers-domains-sites-and Namenskonventionen]
|-
| Beschreibung || max. 64 Zeichen. || alles außer vorhandene Namen || Ja || Ja  
|-
| Raumtyp || ||  ClassRoom, ComputerRoom, Library, Laboratory, TechnicalRoom || Ja || Ja || Hat nur informelle Bedeutung
|-
| HWConfig || || Müssen vorher definiert sein || Ja || Ja || Default in diesem Raum. Wichtig für die Erstellung von Softwaresets.
|-
| Raumkontrolle || || no, inRoom, allTeachers, sysadminsOnly || Ja || Ja
|-
| Anzahl der Geräte || || 4,8,16,32,64,128,512,1024,2048,4096 || Ja || Nein
|-
| Reihen || || 1-30 || Ja || Ja
|-
| Plätze || || 1-30 || Ja || Ja
|-
| Netzwerk || || Ist vordefiniert || Ja || Nein
|}

### Raumkontrolle ###
Zur Zeit gibt es folgende Einstellungen für die Raumkontrolle:
* '''inRoom'''  Räume mit "inRoom"-Kontrolle dürfen nur aus dem Raum selbst kontrolliert werden. 
* '''no''' In diesen Räumen gibt es keine Möglichkeit für die Raumkontrolle. Geräte aus solchen Räumen haben immer Zugriff auf alle Dienste des Servers. Für diese Räume kann man keine Raumzugriffregel erstellen. Allerdings kann man für diese Räume Firewallregel setzen, um den Zugriff auf das Internet aus diesen Räumen zu steuern.
* '''allTeachers''' Diese Räume können durch Lehrer kontrolliert werden, wenn sie im selben Raum oder in einem Raum mit Raumkontrolle '''no''' sind.
* '''sysadminsOnly''' Diese Räume können nur durch Systemadministratoren kontrolliert werden.

Systemadministratoren können für Räume mit Raumkontrolle '''inRoom''', '''allTeachers''' und '''sysadminsOnly''' einen Zugriffszeitplan erstellen. Dh. zu bestimmten Zeitpunkten wird die Firewall in einen definierten Zustand gesetzt oder bestimmte Aktionen an den Clients werden ausgeführt.

### Raumaktionen ###
Über das Action-Icon können folgende Aktionen für alle Geräte in den ausgewählten Räumen ausgeführt werden:
* '''Einschalten''' Funktioniert nur mit Geräten, bei denen in BIOS/Firmware WOL erlaubt ist.
* '''Ausschalten''' Funktioniert nur mit Geräten, auf denen '''cranix-client''' installiert ist. 
* '''neu starten''' Funktioniert nur mit Geräten, auf denen '''cranix-client''' installiert ist.
* '''Klonen starten''' PXE-Bootkonfiguration für die FatClient-Geräte werden geschrieben. 
* '''Klonen anhalten''' PXE-Bootkonfiguration für die FatClient-Geräte werden gelöscht.
* '''öffnen''' D.h. Bildschirm und Tastatur wieder freigeben.
* '''schließen''' Bildschirm und Tastatur sperren.
* '''abmelden''' Angemeldete Benutzer abmelden.
* '''löschen''' Raum und alle Geräte im Raum werden gelöscht.

## Geräte ##
Klickt man unter '''Netzwerk''' -> '''Räume'''  auf den Namen eines Raumes erhält man eine Liste mit den, in diesem Raum registrierten, Geräten. Man kann alle oder einzelne Geräte markieren und unter '''Aktionen''' folgende Funktionen mit den gewählten Rechnern ausführen:
* Eine CSV-Liste der gewählten Geräte herunterladen.
* Die gewählten Geräte löschen.
* Für die gewählten Geräte die Hardwarekonfiguration des Raumes setzen.
Bei einzelnen Geräten kann man durch das Klicken der Icons in der Zeile des Gerätes folgende Aktionen ausführen:
* Das Gerät über WOL einschalten
* Das Gerät bearbeiten. Das benötigt man meistens, wenn die Netzwerkkarte eines Rechners ausgetauscht werden muss. In diesem Fall reicht es hier nur die MAC-Adresse der neuen Karte einzutragen. Weiterhin kann man die Serial- oder Inventarnummer sowie die Platzierung des Rechners im Raum anpassen.
* Den Rechner als Klonemaster markieren. Da in einer Hardwarekonfiguration nur ein Gerät als Klonemaster markiert sein darf, wird der aktuelle Klonemaster abgewählt.
* DHCP-Parameter setzten. Man kann hier dem Rechner individuelle DHCP-Parameter setzten. '''Wichtig: Setzt man hier einen Parameter falsch, führt das ggf. dazu, dass der DHCP-Server nicht startet. Bitte diese Funktion nur dann benutzen, wenn Sie 100%-ig wissen, was Sie tun.'''
* Das Gerät löschen

### Geräte manuell registrieren ###
Klickt man unter '''Netzwerk''' -> '''Räume''' beim entsprechenden Raum auf das '''+''' Zeichen, kann ein Gerät dem Raum hinzugefügt werden. 

Wird die Registrierung von einem nicht registrierten Rechner ausgeführt, erkennt der Server die '''MAC-Adresse''' des Clients und trägt diese in das entsprechende Feld ein. Ist das nicht der Fall muss die '''MAC-Adresse''' manuell eingetragen werden. Das Feld '''MAC-Adresse''' ist ein Textfeld. Es können mehrere MAC-Adressen eingetragen werden. In diesem Fall werden der MAC-Adressen der Reihe nach die nächste freie IP-Adresse im Raum zugewiesen.

Man muss eine freie '''IP-Adresse wählen'''. Dadurch wird auch der dazugehörige vordefinierte Name gesetzt. Man kann den vordefinierten Namen auch ändern, man muss jedoch sowohl die DNS als auch die Microsoft Rechnernamenskonventionen einhalten: [https://support.microsoft.com/de-de/help/909264/naming-conventions-in-active-directory-for-computers-domains-sites-and Namenskonventionen in Active Directory für Computer] '''Wichtig''' Wurden mehrere MAC-Adressen eingetragen, darf der vordefinierte Name nicht geändert werden.

Standardmäßig wird als '''Hardwarekonfiguration''' die des Raumes gesetzt, diese kann jedoch geändert werden.

Hat der Rechner eine WLAN-Karte die auch benutzt wird, muss deren MAC-Adresse in das Feld '''WLAN-MAC-Adresse''' eingetragen werden. Wurde eine '''WLAN-MAC-Adresse''' eingetragen, kann auch nur eine '''MAC-Adresse''' angegeben werden.

Die Felder '''Seriennummer''' und '''Inventarnummer''' können bei Bedarf ausgefüllt werden.

### Geräte importieren ###
Geräte können von der Kommandozeile als Benutzer '''root''' mit folgenden Befehl aus einer CSV-Datei importiert werden:
 crx_api_upload_file.sh devices/import <Dateiname>
Die Datei hat folgendes Format:

* In der ersten Zeile muss ein Header stehen.
* Feldseparator ist ";" (Semikolon).
* Groß/Klein-Schreibung ist irrelevant.
* Reihenfolge ist irrelevant.
* Folgende Felder müssen vorhanden sein:
:* room -> hier muss der Name des Raumes stehen in dem das Gerät aufgenommen werden muss. Der Raum muss schon existieren.
:* mac MAC-Adresse des Gerätes.
Weitere mögliche Felder.:
:* ip
:* name
:* hwconf   -> hier muss der Name der Gerätkonfiguration stehen. Die Gerätkonfiguration muss schon existieren.
:* row
:* place
:* wlanmac
:* wlanip
:* serial
:* inventary
:* owner -> hier muss der Benutzername (uid) des Eigentümers stehen

Hier ist eine ganz einfache Beispieldatei:
  room;mac
  edv1;AA:BB:CC:DD:EE:01
  edv1;AA:BB:CC:DD:EE:02
  edv1;AA:BB:CC:DD:EE:03
  edv1;AA:BB:CC:DD:EE:04

### Hardwarekonfigurationen von Geräten ändern ###

#Wenn nötig neue Hardwarekonfigurationen anlegen
#Geräte -> Gerät suchen -> bearbeiten -> Hardwarekonfigurationen wählen

Will man mehrere Rechner in einem Raum umstellen, kann man wie folgt vorgehen:
#Wenn nötig neue Hardwarekonfigurationen anlegen
#Räume -> Raum suchen -> Bearbeiten -> Hardwarekonfigurationen wählen
#Räume -> Geräte -> zu ändernde Geräte wählen -> globale Aktion -> Hardwarekonfiguration des Raumes setzten

===Salt-Minion Konfiguration manuell bearbeiten===
#Dienst salt-minion auf dem betroffenen Client anhalten.
#c:\salt\conf\minion anpassen (id: und ggf master:). Wichtig ist, dass die id: eines Clients der FQHN also Rechnername.DNS-Domainname ist. 
#Alle Dateien in C:\salt\conf\pki\mimion\  löschen.
#Auf dem Server mit dem Befehl '''salt-key -d "minion.name"''' den Schlüssel des Minions löschen, wenn dieser vorhanden ist.
#Dienst salt-minion auf dem Client starten.

## CloneTool ##
  
Der CRANIX verfügt über ein eingebautes Werkzeug zum Klonen von PCs. Dieses Werkzeug unterstützt auch NTFS Partitionen und das Klonen von mehreren Festplatten und Partitionen. 
  
  
> WICHTIG Da die Verwendung von Masterrechner zu mehr Problemen geführt hat, als er hätte vermeiden können, gibt es ab '''CRANIX 4-3''' keine Masterrechner mehr. Das heißt: von allen Rechner können Images gezogen werden.
{.is-info}

Die so erstellte Partitionimages und die Partitionierung kann nun auf Rechner mit der selben Hardwarekonfiguration übertragen werden. In folgenden Schritten muss ein Rechner als Masterrechner vorbereitet werden:

### Windowsrechner für Klonen vorbereiten ###
- Den lokalen Administrator aktivieren! Öffnen Sie dazu einen Terminal (Eingabeaufforderung) mit der Option "Als Administrator ausführen" und führen Sie folgenden Befehl aus: 
  ```plaintext 
  		net user administrator * /active:yes
  ```
  
> Melden Sie sich als lokaler Administrator an! Die Verwaltung der Clients funktioniert nur dann, wenn Sie die folgenden Schritte als Benutzer **Administrator** ausführen.
>{.is-warning}


- Starten Sie die Kommandozeile oder PowerShell (WIN+X) oder über Start->Ausführen "CMD" mit STRG+UMSCHALT+ENTER.
	- Hybernatemodus abschalten: <code>powercfg /h off</code>
	- Bitlocker deaktivieren: <code>manage-bde -off c:</code>
	- Rechner am CRANIX registrieren: http://admin. Das machen Sie in der Administrationsoberfläche über '''Räume''' oder '''Geräte'''.
- Alle Updates installieren.
- Die [ClientSetup_py3.exe](https://repo.cephalix.eu/Downloads/ClientSetup_py3.exe) herunterladen. 
- Rechner vom Netzwerk trennen, sonst wird der Rechner in die Domäne aufgenommen. Vor dem Clonen darf der Rechner nicht in der Domäne sein.
- ClientSetup.exe auf dem Rechner installieren:
  - ClientSetup im silent mode installieren:  
  ```plaintext 
  ClientSetup_py3.exe /i /passive MinionName=hostname.dns-domainname
  ```
  - Alternativ können Sie den Installer per Doppelklick starten und den Minionnamen ins Feld '''Minionname''' eintragen. Der auf dem CRANIX-Server eingetragene Minionname des FQHN des Rechners. Also ''hostname''.''dns-domainname''.
  
>   WICHTIG rechner- drucker und domainnamen IMMER klein schreiben!!!
{.is-warning}
  
- Rechner auschalten. 

- Rechner mit Netzwerk verbinden.
- Neustart über das Netzwerk ins CloneTool.
- Starten Sie nun '''Rechner klonen'''.
- Als erstes müssen Sie die zu klonenden Partitionen auswählen
- Geben Sie den zu klonenden Partitionen eine Beschreibung.
- Anschließend müssen zu den Partitionen verschiedene Angaben gemacht werden:
- Betriebssystem: Win10, WinBoot, Linux, Data
- Bei Win10 müssen Sie daneben angeben, ob der Rechner in die Domäne aufgenommen werden muss oder nicht.
- Anschließend muss ein Imagingtool ausgewählt werden. '''Zpartclone''' bietet den besten Durchsatz und Komprimierung. Welches Tool mit Ihrer Hardware bzw. Netzwerk am besten zusammenarbeitet müssen Sie selber ermitteln. '''dd''' und '''dd_rescue''' erstellen eine 1 zu 1 Kopie der Partitionen. '''dd_rescue''' kann auch beschädigte Partitionen lesen.
*Wenn alle Partitionen geklont wurden, können Sie mit der Übertragung der Images auf den anderen Rechnern anfangen.

### Rechner wiederherstellen ###
Es gibt mehrere Möglichkeiten einen Rechner über das CloneTool wiederherstellen.
#### Manuelle Wiederherstellung ####
- Rechner über Netzwerk starten.
- CloneTool auswählen.
- Melden Sie sich als Administrator an.
- Seit CRANIX-4-1 können Rechner direkt im CloneTool registriert werden. Merkt das CloneTool, das Sie sich an einem nicht registrierten Rechner angemeldet haben, werden Sie zur Registrierung weitergeleitet. Bitte beachten Sie, dass Sie jedoch in diesem Fall nur den vom CRANIX generierten Rechnernamen verwenden können. Möchten Sie einen eigenen Namen für den Rechner verwenden, müssen Sie die MAC-Adresse des Rechners vor dem Start des CloneTools am Server registrieren.
- Wählen Sie '''Restore'''
- Die Festplatte des Rechners wird partitioniert und die Partitionen mit dem Image bespielt.

#### Automatische Wiederherstellung ####
Über die Adminoberfläche kann für die Rechner eine Bootkonfiguration erstellt werden. Dadurch starten die Rechner beim nächsten Neustart automatisch in das CloneTool und starten das '''Restore'''. Anschließend starten die Rechner neu im installierten Betriebssystem. Diese Bootkonfigurationen können Sie unter '''Räume''' oder '''Geräte''' erstellen. Klicken Sie auf &#8942; neben dem Raum oder Gerät den oder das Sie wiederherstellen wollen und wählen Sie '''Klonen starten''' aus dem Kontextmenü. Alternativ können Sie mehrere Rechner bzw. Räume auswählen. Klicken Sie anschließend oben Rechts auf &#8942; und wählen Sie '''Klonen starten''' aus dem Kontextmenü. Da in diesem Fall das Klonen auf jedem Rechner separat gestartet wird, können auf einmal mehrere Rechner aus verschiedenen Gerätekonfigurationen wiederhergestellt werden.
Tritt ein Fehler während des Klonvorgangs auf, können die erstellten Bootkonfigurationen gelöscht werden, damit die Rechner nicht wieder in das CloneTool starten. Das macht man wieder im Kontextmenü mit dem Menüpunkt '''Klonen anhalten'''.

#### Multicast Klonen ####
Wenn Ihr Netzwerk das anbietet, können Sie mehrere Rechner aus der selben Gerätekonfiguration über UDP-Multicast klonen. Der Vorteil dieses Verfahrens ist, dass das Image nur einmal an mehrere Geräte gesendet wird. Das heißt für die Geschwindigkeit des Klonens spielt es überhaupt keine Rolle wie viel Rechner Sie auf einmal klonen wollen. Der Nachteil von Multicast Klonen ist jedoch, dass die Switche dementsprechend konfiguriert werden müssen. Weiterhin genügt nur eine fehlerhafte Komponente (Netzwerkkarte, Netzwerkkabel, Switchport ...) und der ganze Klonvorgang wird ausgebremst. Multicast Klonen funktioniert nur über automatische Wiederherstellung. Bei Multicast Klonen ist es sehr wichtig, dass die zu wiederherstellenden Rechner die selbe Gerätekonfiguration haben, deshalb wird Multicast Klonen über den Menüpunkt '''Gerätekonfiugrationen''' in folgenden Schritten gestartet:
1. **Gerätekonfiugrationen**
1. Betroffene Gerätekonfiugration bearbeiten.
1. Untere Menütab '''Mitglieder''' wählen
1. Nun werden die Geräte raumweise gruppiert aufgelistet. Sie können oben rechts die Gruppierung ändern.
1. Die zu klonenden Rechner auswählen.
1. Rechts oben mit dem grünen Ikon '''Multicast Klonen starten''' werden die benötigte Bootkonfigurationen erstellt.
1. Rechts oben mit dem roten Icon '''Multicast Klonen anhalten''' können die Bootkonfigurationen bei Bedarf gelöscht werden.

### Klonen von Laptops ###
Laptops kann man genau so wie andere Rechner über ihren Ethernet-Anschluss geklont werden. Damit die Laptops auch dann identisch behandelt werden, wenn sie über WLAN mit dem CRANIX-Server verbunden sind, müssen ihre WLAN-Netzwerkkarten auch mit der permanenten MAC-Adresse registriert werden. Es ist sehr wichtig, dass die WLAN-Karte so konfiguriert ist, dass sie im CRANIX-WLAN-Netz immer die gleiche MAC-Adresse verwendet.  

### Klonen von Tablets über die **cloneProxy** ###
Tablets haben nur einen WLAN-Anschluss, deshalb können diese nicht per PXE-Boot in das CloneTool gestartet werden. Allerdings kann man dieses Problem mit Hilfe eines USB-Ethernet-Adapters umgehen. Besorgt man für jedes Tablet einen separaten Adapter, kann man Tablets wie Laptops im Netz behandeln. Allerdings ist es sehr unwirtschaftlich für jedes Tablet einen separaten Adapter zu kaufen.
Weiterhin ist Chaos vorprogrammiert, da die Adapter nicht vertauscht werden dürfen, da ansonsten die Ethernet- und WLAN-Karten Zuordnung nicht mehr passt. Das bedeutet ein Rechner heißt anders, wenn er per USB-Ethernet-Adapter geklont wird, als wenn er per WLAN im Netz benutzt wird.

Um dieses Problem zu umgehen, wurde im CRANIX-4-3 eine neue Gerätekonfiguration "'''cloneProxy'''" eingeführt. Diese Gerätekonfiguration verweist auf keine Hardware, sondern weist nur das CloneTool darauf hin, dass Geräte statt der Ethernet-MAC-Adresse mit der WLAN-Karten-MAC-Adresse identifiziert werden sollen.

Eine oder mehrere USB-Ethernet-Adapter müssen in dieser Gerätekonfiguration registriert werden. Dabei spielt es keine Rolle in welchem Raum die Adapter eingetragen werden. Sie können es am einfachsten in '''SERVER_NET''' machen. Vergessen Sie nicht bei der Registration einen eindeutigen Namen und die Gerätekonfiguration '''cloneProxy''' zu zuweisen. Alternativ können Sie einen Raum für die USB-Ethernet-Adapter mit entsprechenden anzahl von Geräten und der Gerätekonfiguration '''cloneProxy''' erstellen. Dieses Vorgehen hat den Vorteil, dass Sie die USB-Ethernet-Adapter nicht im Voraus registrieren müssen. Sie können es nach dem Anmelden in CloneTool machen. Auch die MAC-Adresse der WLAN-Karte muss im Voraus registriert werden, das können Sie auch im CloneTool machen. Ein Raum mit einer entsprechenden richtigen Gerätekonfiguration muss lediglich vorher für die Tablets erstellt werden. Hier sin die Schritte zum Klonen eines Tablets ohne diese vorher zu registrieren.

1. Raum 'usb-adapter' mit Gerätekonfiguration '''cloneProxy''' für die entsprechenden Anzahl von USB-Ethernet-Adapter anlegen.
1. Eine neue Gerätekonfiguration für die Tablets erstellen.
1. Einen Raum oder mehrere Räume für die Tablets anlegen.
1. Tablet für das Klonen, wie vorher beschrieben, vorbereiten.
1. Tablet über den USB-Ethernet-Adapter über PXE-Boot in CloneTool starten.
1. Als Administrator in CloneTool anmleden.
1. USB-Ethernet-Adapter in Raum 'usb-adapter' registrieren
1. Tablet in einem, für die Tablets angelegten Raum registrieren.
1. Rechner klonen. 
  
> '''WICHTIG''' Der Klonvorgang läuft über den USB-Ethernet-Adapter ab. Dieser darf erst nach dem Neustart des Gerätes entfernt werden.
{.is-warning}


Bei der Wiederherstellung muss das Tablet auch über ein USB-Ethernet-Adapter gestartet werden. Bitte beachten Sie, das je nach dem ob Sie das Klonen mit einem nicht registrierten USB-Ethernet-Adapter und/oder WLAN-Karte machen, müssen Sie das Tablet zwei, ein oder kein mal registrieren. Bei der Registrierung der WLAN-Karte steht oben ein Hinweis darauf. Bitte bei der Registrierung im CloneTool unbedingt alle Hinweise mitlesen!

#### Surface klonen ####
Surface ist ein Tablet von Microsoft und sollte im Prinzip genau so wie andere Tablets mit Hilfe eines USB-Ethernet-Adapters geklont werden können. Allerdings gibt es 2 Probleme. 

  - [Laut](https://docs.microsoft.com/de-de/surface/ethernet-adapters-and-surface-device-deployment) unterstützt das Surface zwar auch USB-Ethernet-Adapter von Drittanbieter. Allerdings ist ein PXE-Boot nur mit originalem Microsoft Produkten möglich.
 - Wir haben das PXE-Boot mit einem Surface Pro mit Surface USB 3,0-Gigabit-Ethernet-Adapter (USB-A) getestet. PXE-Start funktionierte zwar, auch die UFEI-Startdatei grub.efi wurde geladen, aber danach war Schluss. Der Bootprozess ging nicht weiter. Auch die Umstellung einige Firmware Parameter haben nicht geholfen.
Deshalb haben wir einen anderen Weg gesucht und gefunden. Die [CRANIX-4-3-x86_64.iso](https://repo.cephalix.eu/Downloads/CRANIX-4-3-x86_64.iso) beinhaltet den Menüpunkt '''CloneTool'''. Dieser wurde genau für Geräte die nicht über PXE starten können entwickelt. Diese ISO muss auf ein USB-C-Stick geschrieben werden und das Surface muss so eingestellt werden, dass dieses vom USB starten kann. Damit das Surface von USB starten und geklont werden kann müssen Sie folgende Schritte ausführen:
<gallery heights=200px widths=260px>
Surface_firmware_secure_boot.png|Secure Boot
Surface_firmware_tpm.png|TPM Einstellungen
Surface_firmware_bootorder.png|Bootreihenfolge
Surface_firmware_reboot.png|Reboot
CRANIX-DVD-Boot.png|Bootmenü
</gallery>

- BitLocker im Windows deaktivieren: https://www.wintotal.de/tipp/bitlocker-deaktivieren/
- Die üblichen Einstellungen vornehmen: 
	1. Den lokalen Administrator aktivieren!
	1. net user administrator * /active:yes
	1. Melden Sie sich als lokaler Administrator an!
	1. Hybernatemodus abschalten: powercfg /h off
- Tablet herunterfahren.
- USB-C-Stick mit dem CRANIX und einen USB-A-Ethernet-Adapter anstecken.
- Tablet so einschalten, dass die Firmware (Bios) gestartet wird:
	- Halten Sie die Lauter-Taste gedrückt.
	- Drücken Sie die Ein/Aus-Taste, und lassen Sie sie wieder los.
- Nun müssen Sie in der Firmware folgende Einstellungen unternehmen:
	- Security -> Secure boot -> Change Configuration -> none
	- Security -> Trusted Plattform -> off
	- Boot order -> USB an erste Stelle setzten.
	- Die Firmware verlassen.
- Anschließend startet Surface vom USB-Stick und Sie können das CloneTool aus dem Bootmenü auswählen.

### Über WLAN klonen ###
Das geht doch gar nicht. In erster Linie ist das wirklich unmöglich, da man zur Zeit noch nicht über WLAN PXE booten kann. Man kann jedoch das CloneTool auch über das CRANIX-Installationsmedium starten und so das Klonen über den WLAN-Adapter durchführen. Das setzt jedoch eine SEHR stabile WLAN-Infrastruktur voraus.

- Erstellen Sie ein [[Installation#Installationsmedium_erstellen|CRANIX-Installationsmedium]].
- Booten Sie das Gerät über das CRANIX-Installationsmedium und starten Sie das Bootmenü CloneTool.
- Das Bootsystem erkennt und aktiviert die WLAN-Karte des Gerätes, wenn dieses nicht mit einer Netzwerkkabel mit dem Netzwerk verbunden ist.
- Das Bootsystem fragt nach der SSID des WLANs und nach den Zugangsdaten.
- Anschließend gelangt man zum CloneTool, als wäre man über Ethernet verbunden und kann das CloneTool, wie gewohnt benutzen.
- Bevor Sie sich am CloneTool anmelden und das Klonen starten, müssen Sie das CRANIX-Installationsmedium entfernen, wenn Sie über einen USB-Stick gebootet haben, sonst wird dieser auch als Festplatte erkannt.

## Drucker ## 
Der CRANIX bietet die Möglichkeit Netzwerkdrucker im System zu registrieren: die Drucker können Räumen bzw. Rechnern als Standard oder als sonstiger Drucker zugewiesen werden. Weiterhin kann der CRANIX für Windows-Clients die Drucktertreiber bereitstellen. Im CRANIX können die Drucker direkt über die Adminoberfläche registriert und bearbeitet werden. Das geschieht unter dem Menü '''Geräte''' ->  '''Drucker'''. Ein direkter Zugriff auf das Druckersystem CUPS ist nur direkt auf dem CRANIX-Server selbst unter der URL https://localhost:631 möglich. Allerdings sollte das im normal Fall nicht erforderlich sein.
Bitte beachten Sie den Unterschied zwischen Drucker und Druckerwarteschlange. Unter Drucker verstehen wir ein Druckergerät. Unter Druckerwarteschlange verstehen wir die Freigabe, unter der man von einem Client auf einen Drucker erreichen und Druckaufträge senden kann. Zu einem physikalischen Druckergerät können mehrere Druckerwarteschlangen existieren. Das kann zB. erforderlich sein, wenn man für einen Farbdrucker die Möglichkeit schwarz-weiß zu drucken auch anbieten möchte. Oder wenn ein Drucker auf verschiedene Medien (A3/A4) drucken kann. In solchen Fällen kann man für den selben Drucker mehrere Druckerwarteschlangen mit verschiedenen Einstellungen definieren.
Im ersten Reiter des Menüs '''Drucker''' erhält man eine Liste der vorhanden Druckerwarteschlangen mit ihrem aktuellen Status. Hier können folgende Aktionen durchgeführt werden:
- Klickt man auf den Namen der Druckerwarteschlange kann man diese bearbeiten.
- Bereitstellung der Druckertreiber für Windows-Clients aktivieren und deaktivieren.
- Druckerwarteschlange deaktivieren. Will man zB. wegen Wartungsarbeiten den Zugriff auf eine Druckerwarteschlange sperren, kann man die Annahme von Druckaufträgen deaktivieren.
- Druckerwarteschlange zurücksetzten.
- Druckerwarteschlange löschen. Löscht man eine Druckerwarteschlange wird das Druckergerät nur dann gelöscht, wenn nur eine Druckerwarteschlange zu diesem Gerät existiert.

### Drucker anlegen ###
Zum Anlegen eines Druckergerätes werden folgende Parameter benötigt:
-  Name
-  MAC-Adresse
-  Raum in dem das Gerät registriert wird. Bitte beachten Sie, dass es hier nicht um den Raum geht, in dem die Druckerwarteschlange(n) des Gerätes bereitgestellt werden soll! Es empfiehlt sich die Drucker im SERVER_NET oder in einem, separat für die Drucker angelegtem Raum zu registrieren.
-  Druckertreiber. Für das setzen der Druckertreiber gibt es 2 Möglichkeiten. Entweder lädt man direkt eine PPD-Druckerbeschreibungsdatei hoch oder man wählt den Hersteller und das Model des Druckers. Taucht der zu installierende Drucker in der Liste nicht auf kann man in den meisten Fällen einen generischen Druckertreiber wählen. Wählen Sie dazu beim Hersteller '''Generic''' und als Model die Druckersprache. Für den meisten S/W Drucker eignet sich ''Generic PCL 5e Printer'' und für die Farbdrucker ''Generic PCL 6/PCL XL Printer''

Beim Anlegen eines Druckers werden folgende Aktionen ausgeführt:
-  Ein Gerät wird mit der Gerätekonfiguration <b>Printer</b> im gewählten Raum mit der gegebenen MAC-Adresse angelegt
-  Eine Druckerwarteschlange wird mit dem Namen des Druckergeräts in CUPS angelegt.
-  Die Druckerwarteschlange in CUPS wird aktiviert.
-  Die Verteilung von Druckertreiber für Windows-Clients wird aktiviert.
Die Druckerwarteschlange wird mit folgenden Standarparameter konfiguriert:
-  Papiergröße A4
-  Fehlerbehandlung: Druckerauftrag löschen
-  Druckerwarteschlange ist aktiv und nimmt Aufträge an.

>  <b>Wichtig</b> Damit die Verteilung der Windows-Druckertreiber funktioniert muss 
>  ein [[Anpassungsmöglichkeiten#Ein_Gruppenrichtlinienobjekt_erstellen|Gruppenrichtlinienobjekt erstellt]]  werden.
{.is-info}


### Druckerwarteschlange anlegen ###
Im dritten Reiter des Menüs '''Drucker''' kann man weitere Druckerwarteschlange zu den vorhandenen Druckergeräten anlegen. Anstatt MAC-Adresse und Raum muss man hier ein vorhandenes Druckergerät auswählen. Sonst gilt das Gleiche wie beim Anlegen eines Druckers. In diesem Fall wird nur eine Druckerwarteschlange in CUPS und SAMBA angelegt.

### Druckerwarteschlange zu Räumen oder Rechner zuweisen ###
Da es in einem Netzwerk mehrere Druckerwarteschlangen gibt und nicht alle von überall benutzt werden sollten, kann man die Druckerwarteschlangen Räumen oder Geräten zuweisen. Dies geschieht unter dem Menüpunkt ''Netzwerk -> Räumen''. Hier klickt man auf den Namen des Raumes. Will man für einen Raum den Standarddrucker oder die Sonstigen Drucker festlegen muss man auf den zweiten Reiter ''Drucker'' klicken. Nun gelten diese Einstellungen für alle Windows-Clients in diesem Raum. Will man für einen Client spezielle Einstellungen festlegen, muss man auf Details/Bearbeiten beim Gerät drücken. Im unterem Feld kann man den Standard- oder die Sonstigen- Drucker festlegen.

Die angelegten Druckerwarteschlangen können jedoch auch direkt von den Clients benutzt werden. 
Auf Windows-Clients ist es möglich unter der URL '''\\admin\<Druckerwarteschlangename>''' verbunden werden. Wurde die Treiberverteilung aktiviert, wird beim ersten Verbindungsaufbau der Druckertreiber auf Windows-Clients aktiviert.
Um die Drucker von Linux- oder MAC-Clients nutzen zu können muss man die Globale Variable CUPS_SERVER folgendermaßen setzen:
  CUPS_SERVER=admin

### Druckertreiber auf dem Server hinterlegen ###
Man kann beim Einrichten eines Druckers eine eigene ppd-Datei hochladen. Diese ppd-Datei wird jedoch nur für den eingerichteten Drucker hinterlegt. Will man einen Druckertreiber für später angelegten Drucker auch bereitstellen, muss man folgende Schritte ausführen:
# Sich als '''root''' anmelden.
# Druckerttreiberdatei packen: gzip <Ppd-Datei>.ppd
# Gepackte Datei nach /usr/share/cups bewegen: mv <Ppd-Datei>.ppd /usr/share/cups
# Datei dem System bekannt geben: /usr/share/cranix/tools/CreatePrinterPpd.pl

### Treiberloses Drucken mit Windows-Client über IPP ab Version 10 ###
Hier ist ein kompakter, praxisnaher Artikel für dein Setup mit CUPS und Windows 11:

#### 🖨️ Drucken per IPP: CUPS einrichten & Windows 11 anbinden ####

'''🔧 1. CUPS-Server für IPP konfigurieren'''

Damit Clients per **IPP (Internet Printing Protocol)** drucken können, muss CUPS Netzwerkzugriffe erlauben und Drucker freigeben. Dafür muss man die Datei <code>/etc/cups/cupsd.conf</code> anpassen.

'''🔓 Zugriff erlauben'''
Stelle sicher, dass CUPS auf allen Interfaces lauscht:

 Listen 0.0.0.0:631
oder
 Port 631

'''🌐 Zugriff im Netzwerk erlauben'''

 <Location />
  Allow @LOCAL
 </Location>
 <Location /admin>
  Allow @LOCAL
 </Location>
 <Location /printers>
   Allow @LOCAL
 </Location>

👉 Optional (offener, unsicherer):
 Allow all

'''📢 Drucker freigeben'''

In derselben Datei:
 Browsing On
 BrowseLocalProtocols dnssd
 DefaultShared Yes

'''🔄 CUPS neu starten'''

 systemctl restart cups

''🔎 2. IPP-URL des Druckers''

CUPS stellt Drucker typischerweise unter folgender URL bereit:

 http://<server>:631/printers/<druckername>

'''Auf einem CRANIX-Server:'''

  http://printserver:631/printers/hp-laserjet

Test im Browser:

 http://printserver:631/printers

#### 💻 3. Windows 11: IPP-Drucker per Kommandozeile hinzufügen ####

'''⚙️ Feature aktivieren (falls nötig)'''

 dism /online /Enable-Feature /FeatureName:Printing-Foundation-InternetPrinting-Client

oder
 powershell
   Enable-WindowsOptionalFeature -Online -FeatureName "Printing-Foundation-InternetPrinting-Client"

'''🚀 Drucker hinzufügen (PowerShell)'''

 powershell
   Add-Printer -Name "hp-laserjet" `
    -DriverName "Microsoft IPP Class Driver" `
    -PortName "http://printserver:631/printers/hp-laserjet"
👉 Das ist der wichtigste Schritt.


'''🔐 Optional: IPPS (verschlüsselt)'''

 powershell
   Add-Printer -Name "CUPS_Drucker" `
   -DriverName "Microsoft IPP Class Driver" `
   -PortName "https://printserver:631/printers/hp-laserjet"

⚠️ Bei selbstsignierten Zertifikaten kann es zu Fehlern kommen.

'''🧪 4. Verbindung testen'''

 powershell
  Test-NetConnection printserver -Port 631

#### 💻 4. Windows 11: IPP-Drucker manuell hinzufügen ####
'''Methode 1:''' Automatisch hinzufügen (empfohlen)

1. Öffne '''Einstellungen'''
1. Gehe zu '''Bluetooth & Geräte → Drucker & Scanner'''
1. Klicke auf '''„Gerät hinzufügen“'''
1. Windows sucht nach verfügbaren Druckern
1. Wenn dein IPP-Drucker erscheint → '''Hinzufügen'''

👉 Funktioniert gut, wenn der Drucker im selben Netzwerk ist und IPP korrekt annonciert (z. B. via Bonjour/mDNS). Bei CRANIX-Server sollte das der Fall sein.

  
### Methode 2: Manuell über IPP-URL hinzufügen

Wenn der Drucker nicht automatisch gefunden wird:

1. Öffne '''Einstellungen → Drucker & Scanner'''
1. Klicke auf '''„Gerät hinzufügen“'''
1. Scrolle runter → '''„Der gewünschte Drucker ist nicht aufgeführt“''' Wähle: '''„Einen Drucker über eine TCP/IP-Adresse oder einen Hostnamen hinzufügen“'''
1. Bei Gerätetyp: '''„Web Services Device“** oder **„TCP/IP-Gerät“'''
1. URL des Druckers eingeben, z. B.:  ''ipp://printserver/ipp/lz-dr1'' oder ''http://printserver:631/ipp/lz-dr1''
1. Treiber auswählen (oder automatisch erkennen lassen)

### Methode 3: Über Systemsteuerung (klassisch) ###

1. Öffne '''Systemsteuerung → Geräte und Drucker'''
1. Klick auf '''„Drucker hinzufügen“'''
1. → '''„Der gewünschte Drucker ist nicht aufgeführt“'''
1. → '''„Freigegebenen Drucker über Namen auswählen“'''
1. IPP-URL eingeben (wie oben)


'''Treiber:'''
Oft funktioniert '''Microsoft IPP Class Driver''' automatisch

'''Typische IPP-URL-Formate:'''
ipp://hostname/ipp/druckername

http://hostname:631/ipp/druckername

ipp://IP-Adresse/ipp/druckername

Port 631 ist Standard für IPP.

## Softwareverteilung ##
Die Softwareverteilung erfolgt durch Installationssets. Die Installationssets verbinden Softwarepakete mit Installationszielen.  Installationsziele können Räume, Rechnerkonfigurationen oder einzelne Rechner sein.
#Als erstes müssen die zu installierende Softwarepakete heruntergeladen werden: ''Administration'' -> ''Software'' -> ''Pakete herunterladen''. Abwarten bis die Pakete geladen sind.
#Anschließend kann man unter ''Installationsset erstellen'' aus den heruntergeladenen Softwarepaketen und aus den Installationszielen Installationssets erstellen.
#Zum Schluss wird für jeden Rechner durch die Auswertung der Installationssets je ein Salt-State-File erstellt: "/srv/salt/crx_device_<Gerätename>.sls". Es ist durchaus möglich, dass ein Gerät seine Softwarepakete aus verschiedenen Installationssets erhält.
Wichtig ist zu wissen, dass nur FatClients in die Softwareverteilung einbezogen werden. Also müssen die Rechner einer Hardwarekonfiguration zugewiesen sein, in der der Gerätetyp als FatClient definiert wurde.

## WLAN ##
Die Dienste des CRANIX-Servers können selbstverständlich auch über WLAN erreicht werden. Der Server bietet verschiedene Möglichkeiten WLAN Geräte ins Netz einzubinden. Voraussetzung ist immer eine gut funktionierende WLAN-Infrastruktur. Ist der Einsatz von mehreren Accesspoints erforderlich, müssen diese unbedingt über Hardware- oder Softwarecontroller (besser Hardware) zentral verwaltetet (managed) werden.
Wir können Sie natürlich bei, Planung, Beschaffung so wie Aufbau und Inbetriebnahme des WLAN-Netzwerks unterstützen, bzw. diese Schritte nach Ihren Anforderungen/Vorstellungen ausführen. 

### WPA2-PSK ###
Will man den WLAN-Zugang über WPA2-PSK (pre-shared key) bereitstellen müssen am CRANIX-Server keine weitere Einstellungen vorgenommen werden. Die Geräte können, wie Geräte mit Ethernetadaptern registriert werden. In diesem Fall können private Geräte nicht automatisch zu Benutzern zugeordnet werden, deshalb empfehlen wir für die Verwaltung von privaten Geräten (BYOD) den Einsatz des Radiusprotokolls (i.F). 

### WPA2-Enterprise Radius ###
Der CRANIX-Server bietet auch einen Radiusserver für die personalisierte Anmeldung im WLAN an. Wenn Sie den Radiusserver benutzen wollen müssen Sie das Paket cranix-radius als Benutzer root auf dem Server installieren:
  zypper install cranix-radius
Nach der Installation müssen noch folgende Konfigurationsschritte durchgeführt werden:
'''Setzen des Radius-Secrets''' Diese liegt am Ende der Datei '''/etc/raddb/clients.conf'''. Nach der Installation des Paketes wird ein Client-Eintrag '''server-net''' in dieser Datei erstell, der den Zugriff für Accesspoints aus dem Servernetz mit dem Passwort '''testing123-1''' festlegt. Sie sollten dieses Passwort unbedingt ändern und den Radiusdients neu starten. Anschließend müssen Sie alle Accespoints im Raum SERVER_NET registrieren und die Radiuseinstellungen setzen. Diese sind wie folgt:
 DNS-Name des Radiusservers: admin
 Radius-Secret: was Sie gesetzt haben
 Port: 1812
 Accounting Port: 1813 
Wenn erforderlich, können Sie für die Access-Points einen eigenen Raum anlegen um diesen zB. gesonderten Internetzugriff zu gewähren. In diesem Fall müssen Sie den Client-Eintrag '''server-net''' in der Datei '''/etc/raddb/clients.conf''' anpassen oder einen neuen Eintrag erstellen. Legen Sie diesen Raum mit Raumkontrolle '''no''' an, damit Sie für diesen Raum feste Firewallregeln setzen können.

Nun ist das WLAN-Netz einsatzbereit und jeder Benutzer kann sich mit Benutzername und Passwort anmelden. Nach der Anmeldung bekommen die nicht registrierten Geräten eine IP-Adresse aus dem ANON_DHCP-Raum, wenn dort noch genug IP-Adressen zur Verfügung stehen. Registrierte Geräte, bekommen die ihnen bei der Registrierung zugewiesene IP-Adressen.

In erster Linie können nur Systemadministratoren Geräte an CRANIX-Server anmelden. Das ist natürlich sehr viel Arbeit, die vor allem in Schulen nicht zu bewältigen ist. Um diese Arbeit zu erleichtern wurden die sog. AdHocLan Räume eingeführt.

## AdHocLan ##
Mit dem AdHocLan Modul ist es möglich, dass die Benutzer ihre privaten Geräten im Intranet selbst registrieren. Dabei spielt es keine Rolle ob es sich um WLAN oder normalen Netzwerkanschluss handelt. Der Vorteil von dieser Methode ist, dass der Systemadministrator sich nicht um die Registrierung der einzelnen Geräte (i.d.R. BYOD) kümmern muss. Die Benutzer können ihre eigenen Geräte selber verwalten: Löschen bzw. die MAC-Adresse ändern. Weiterhin werden beim Löschen eines Benutzers auch seine Geräte gelöscht.
Um AdHocLan nutzen zu können muss ein Systemadministrator AdHocLan-Räume erstellen und diese Benutzergruppen zuweisen. Beim Anlegen eines AdHocLan Raumes müssen folgende Parameter angegeben werden:
*'''Name''' Der Name des Raumes
*'''Beschreibung''' Eine kurze Beschreibung des Raumes
*'''Gerätekonfiguration''' Hier sollte für privaten Geräten BYOD stehen.
*'''Gerät pro Benutzer''' Wie viele Geräte einzelne Benutzer in diesem Raum registrieren dürfen.
*'''Anzahl der Geräte''' Max. Anzahl an Geräten, die registriert werden können pro Raum. Diese Zahl sollte Größer sein als "'''Gerät pro Benutzer'''" * "'''Anzahl der Benutzer in der Gruppe'''".
*'''Raumkontrolle''' Wie soll der Zugang im Raum kontrolliert werden.
*'''Gruppen''' Hier muss man die Gruppen auswählen, deren Mitglieder in diesem Raum Geräte registrieren dürfen.
*'''Nur für Schüler''' Diese sollten dann auf '''yes''' gesetzt werden, wenn Sie den Zugriff für Schüler über solche Gruppen ermöglichen möchten, in denen auch Lehrer Mitglieder sind.

Steht einem Benutzer mindestens ein AdHocLan-Raum für die Registrierung von privaten Geräten zur Verfügung, kann der Benutzer über die Administrationsoberfläche unten Profile -> Geräte, private Geräte registrieren, löschen oder ändern.

Nach der Installation des Paketes cranix-radius stehen zwei weitere Systemvariablen zur Verfügung, womit die automatische Registrierung der WLAN-Geräte gesteuert wird.
* '''RADIUS_REGISTER_DEVICE''' Ist diese Variable auf "yes" gesetzt, werden neue Geräte bei der Anmeldung an WLAN automatisch registriert, wenn dem angemeldeten Benutzer zu Registrierung ein AdHocLan zur Verfügung steht.
* '''FORCE_REGISTER_DEVICE''' Diese Variable steuert was mit einem Gerät passiert, das nach der Anmeldung an WLAN nicht automatisch registriert werden kann. Das kann der Fall sein, wenn ein Benutzer zu keinem AdHocRaum zugewiesen ist, oder wenn dieser schon die maximalen Anzahl der Geräten registriert hat. Ist diese Variable auf "yes" gesetzt, bekommen solche Geräte nach der Anmeldung an WLAN keine IP-Adresse und können überhaupt nicht benutzt werden. Anderenfalls bekommen sie eine IP-Adresse aus dem ANON_DHCP-Raum und der Benutzer kann über die Administrationsoberfläche seine alten Geräte löschen, um das neue registrieren zu können.
* '''AUTO_UPDATE_MAC_ADDRESS''' Ist diese Variable auf "yes" gesetzt, kann ein Benutzter auch dann ein neues Gerät registrieren, wenn er eigentlich die Zahl '''Gerät pro Benutzer''' überschritten hat. In diesem Fall wird die MAC-Adresse des zuletzt registrierten Gerätes durch die neue MAC-Adresse ersetzt. Dies ist nötig, da iOS und Android Geräte ihre MAC-Adresse von Zeit zu Zeit ändern. Dieses Verhalten kann man zwar ändern, das schaffen allerdings nicht alle Benutzer.

'''WICHTIG''' Ist ein Benutzer in der Gruppe Systemadministratoren, funktioniert für ihn die automatische Registrierung von privaten Geräten nicht. Der Grund dafür ist, dass Systemadministratoren sehr oft testen, melden sich an fremden Geräten ins WLAN ein usw... Im Laufe ihrer Arbeit, würden sie Unmengen an Geräten besitzen, die gar nicht ihnen gehören. Systemadministratoren müssen ihre privaten Geräte manuell über die Administrationsoberfläche registrieren.

====AdHocLan Raum für Lehrer====
In einer Schule gibt es 120 Lehrer. Jeder darf 4 Geräte registrieren. Das ergibt maximal 480 Geräte. Deshalb muss man einen Raum mit folgenden Parameter anlegen:
*'''Name''' lehrer-lan
*'''Beschreibung''' Raum für private Geräte der Lehrkraft
*'''Gerät pro Benutzer''' 4
*'''Anzahl der Geräte''' 512
*'''Raumkontrolle''' no
Anlegen. Raum Bearbeiten und die Gruppe teachers (Lehrer) dem Raum zuweisen.
Anschließend muss man in der Firewall die entsprechenden Ausgangsregel dem Raum zuweisen. Möchten man dem Raum direkten Internetzugang gewähren, muss folgende ''ausgehende Firewallregel'' erstellt werden:
*'''Art''' Room
*'''Quelle''' Raum auswählen
*'''Protokoll''' all
*'''Ziel''' 0/0
Will man nur Web-Zugang erlauben, müssen 2 ''ausgehende Firewallregeln'' erstellt werden:
Regel für http-Zugriffe:
*'''Art''' Room
*'''Quelle''' Raum auswählen
*'''Protokoll''' TCP
*'''Port''' 80
*'''Ziel''' 0/0
Regel für https-Zugriffe:
*'''Art''' Room
*'''Quelle''' Raum auswählen
*'''Protokoll''' TCP
*'''Port''' 443
*'''Ziel''' 0/0

Selbstverständlich ist es möglich, dass die Geräte aus AdHocLan-Räumen den Schulproxy nutzen. Dazu muss in der Proxy-Konfiguration des Gerätes/Browsers einer der folgenden Einstellungen gemacht werden:

#Automatische Internet-Proxyerkennung
#Konfigurationsadresse/ Adresse des Proxykonfigurationsscriptes http://admin/proxy.pac
#Proxy-Server: ''proxy'' Proxy-Server-Port: 8080

Bitte beachten Sie, dass ein Zugriff von Mailclients auf externe Mailserver nur mit direktem Internetzugang möglich ist.

====AdHocLan Raum für Schüler====

Für die Schüler kann man analog, wie bei den Lehrern, einen großen AdHocLan Raum anlegen. Dieser Raum darf natürlich nicht kontrollierbar sein. Wäre eine dynamische Raumkontrolle für die Lehrer möglich, würde das immer alle Schüler betreffen. Der Zugriff ins Internet muss über feste Firewallregeln oder über den Proxy geregelt werden. In bestimmten Schulen kann jedoch diese Lösung ausreichen.

Der CRANIX-Server bietet jedoch die Möglichkeit, die privaten Geräten der Schüler klassenweise in virtuellen Räumen zu ordnen. Bei Import der Schüler werden die eigenen Geräte in die neuen Klassenräume umgezogen. Ob und wie die AdHocLan-Klassenräume erstellt werden, wird durch folgende globalen Variablen geregelt:

* '''CRANIX_MAINTAIN_ADHOC_ROOM_FOR_CLASSES''' schaltet diese Funktion ein. 
* '''CRANIX_CLASS_ADHOC_DEVICE_PRO_USER''' gibt an, wie viele Rechner ein Benutzer registrieren darf.
* '''CRANIX_CLASS_ADHOC_DEVICE_COUNT''' gibt an, wie viele Geräte in einem Raum registriert werden können. Diese Zahl muss eine Potenz von 2 sein. Erlaubte Werte sind also 8,16,32,64,128. Diese Zahl ergibt sich aus der Maximalen Klassengröße und dem Wert von '''CRANIX_CLASS_ADHOC_DEVICE_PRO_USER'''.  Sind maximal 25 Schüler in einer Klasse und jeder Schüler darf 2 Geräte registrieren, muss als '''CRANIX_CLASS_ADHOC_DEVICE_COUNT''' als 64 gewählt werden.
* '''CRANIX_CLASS_ADHOC_NET''' Hat der CRANIX mehrere interne Netze, muss man hier angeben, welches Netz für die Klassen-AdHoc-Räume benutzt werden soll. Ist diese Variable nicht gesetzt, wird das Netz '''CRANIX_NETWORK/CRANIX_NETMASK''' verwendet.

Hat man diese Werte gesetzt, muss noch das Script '''/usr/share/cranix/tools/handle_class_adhoc_rooms.py''' als Benutzer '''root''' auf dem CRANIX ausgeführt werden. Damit werden die AdHocLan-Klassenräume für alle vorhandenen Klassen angelegt. Die Räume werden mit Raumkontrolle '''allTeachers''' erstellt, damit jeder Lehrer jeden Klassenraum kontrollieren kann.

==Gefilterter Internetzugang==
Vor allem in Schulen ist es unerlässlich, dass man den Internetzugang kontrolliert indem bestimmte Internetseiten nicht erreichbar gemacht werden. Dafür bietet der CRANIX-Server 2 Möglichkeiten: Proxy-Filterung und DNS-Filterung.

===Proxy-Filterung===
Nach der Installation des CRANIX-Servers wird der Proxy-Server squid, mit dem Contentfilter squidGuard installiert. Dieser Lösung bietet einen gefilterten, protokollierten Internetzugang. Der Contentfilter squidGard ordnet mehrer Millionen Internetseiten in Kategorien. Über die Administrationsoberfläche von CRANIX können Systemadministratoren einen Basisfilter zusammen stellen. Je nach Benutzerrolle können verschiedene Kategorien erlaubt und verboten sein. Dabei spielen folgende Kategorien besondere Rolle:
* '''Eigene White-Liste''' Die von squidGuard gelieferte Listen können nicht geändert werden. Möchte man den Zugriff auf eine Domäne erlauben, die jedoch in eine gesperrte Liste eingeordnet wurde, muss man diese hier eintragen. Der Zugriff auf die Domänen in der eigenen White-Liste ist für jede Benutzerrolle immer erlaubt.
* '''Eigene Black-Liste''' Die hier eingetragene Domänen sind für alle Benutzerrollen gesperrt.
* '''CEPHALIX-Liste''' Das ist eine White-Liste, die für alle Benutzerrollen erlaubt ist. Diese Liste kann jedoch nur von einem zentralen CEPHALIX-Server verwaltet werden. Der lokale Systemadministrator kann diese Liste nicht bearbeiten.
* '''in-addr''' Mit dieser Kategorie kann der Zugriff direkt auf IP-Adressen in der URL erlaubt oder verboten werden.
* '''all''' Das ist immer die letzte Kategorie. Ist dieser erlaubt, sind alle Zugriff die nicht explizit durch Kategorien verboten sind erlaubt. Ist diese Kategorie für eine Benutzerrolle verboten, haben die betroffene Benutzer nur Zugriff auf die explizit durch Kategorien erlaubten Seiten Zugriff.
Die Proxy-Filterung hat folgende Vorteile:
* Hohe Sicherheit, da nur http und https Zugriffe ins Internet erlaubt sind. (Nichtmal Pingen geht :-)
* Alle Zugriffe werden protokolliert.
* Man kann für die verschiedene Benutzerrollen verschiedene Filter erstellen.
Die Proxy-Filterung hat folgende Nachteile:
* Greift man auf verschlüsselte gesperrte Seiten, wird man nicht auf eine Blockseite weitergeleitet sondern erhält man ein Timeout. Das kann zu Verwirrungen führen.
* Verschiedene Video-Dienste sind durch Proxy nicht erreichbar. Deshalb muss man für diese Dienste extra Firewall- und Proxy-Pac-Regeln erstellen. Im Falle von Teams von Microsoft sind das bis zu Hundert Regel.
* MDM-Dienste benötigen auch direkten ungefilterten Internetzugang.

====Proxy-Konfiguration auf den Clients====
Der DHCP-Server vom CRANIX veröffentlicht im Netz über ein DHCP-Option, dass es im Netz einen Proxy-Server gibt, der über ein Proxy-Konfigurationsdatei benutzt werden sollt. Diese Datei ist über die URL '''http://admin/proxy.pac''' erreichbar.
Weiterhin ist auch der A-Record für wpad im DNS-Server von CRANIX gesetzt. Dadurch ist die selbige Konfigurationsdatei auch über die URL '''http://wpad.<Domainname>/wpad.dat''' erreichbar.
Beide Veröffentlichungsmethoden sind standardisiert und kennen die meisten moderne Clients. Das heißt prinzipiell ist es nicht erforderlich an den Clients die Proxy-Konfiguration einzustellen, da die diese automatisch erkennen können. Sollte das doch nicht der Fall sein, gibt es je nach Gerät bzw. Betriebssystem folgende Möglichkeiten:
* '''iOS''' Einstellungen -> WLAN -> beim betroffenen WLAN-SID -> Proxy konfigurieren -> Automatisch -> URL: http://admin/proxy.pac
* '''Win10'''

===DNS-Filterung===
Durch die Installation des Paketes '''cranix-unbound''' wird die DNS-Filterung eingeschaltet. Für diesen Dienst kann man eine Black-Liste von Domänen erstellen, die nicht erreichbar sein dürfen. Diese Domänen werden auf die Proxy-IP-Adresse des CRANIX-Servers aufgelöst und werden dadurch die Zugriffe auf diese Domänen auf eine Sperrseite weitergeleitet. Grundsätzlich ist in allen Räumen das direkte Internet eingeschaltet. D.h. man hat von allen Räumen vollen Zugriff auf alle IP-Adressen via alle Protokolle und Ports im Internet.

Während der Installation des Paketes '''cranix-unbound''' wird für jeden Raum, der mit einer dynamische Zugangskontrolle ( Kontrolle im Raum ist nicht ''no'' ) konfiguriert ist, eine Standardzugangsregel gesetzt, die alle Zugriffe inkl. direktes Internet erlaubt. Für Räume die mit Raumkontrolle ''no'' angelegt worden sind, muss man manuell Firewallregel anlegen. Alternativ können Sie vor der Installation in solchen Räumen die Raumkontrolle aud ''sysadminsOnly'' setzen. Weiterhin wir die automatische Proxykofigurationsdatei so umgeschrieben, das für alle Rechner mit allen Zielen direktes Internet eingestellt ist. Das erspart die erneute Konfiguration der vorhandenen Clients.
In Räumen mit dynamischer Zugangskontrolle können Lehrer den Internetzugang bei Bedarf sperren.

Die Black-Liste der gesperrten Domänen wird anhand der Proxy-Kategorien und der eigenen Black-, White- und CRANIX-Listen erstellt. Der grundsätzliche Unterschied zum Proxy-Zugang ist, dass bei der DNS-Filterung alles erlaubt ist was nicht verboten wurde. Deshalb gibt es bei DNS-Filterung keine positiv-Listen. Die Konfiguration der DNS-Filterung kann man über die Administrationsoberfläche als Systemadministrator '''Sicherheit''' -> '''DNS-Filterung''' erledigen. Das Paket ''cranix-unbound'' liefert eine Vorkonfiguration, die die Suchmaschinen bzw. YouTube zu '''SafeSearch''' Modus zwingt. Vor allem bei YouTube hat das zu Problem geführt. Deshalb haben wird das '''SafeSeraach'''-Modus konfigurierbar gemacht. Unter Sicherheit -> DNS-Filter gibt es nun einen zusätzlichen Reiter SafeSearch aktivieren. Hier kann man das SafeSearch-Modus für die verschiedene Dienste einzeln ein- bzw. ausschalten.

<div class=warningbox>'''SafeSearch''' Ist für ein Dienst (Ding, YouTube, Google) das SafeSearch-Modus aktiviert, hat nur der Dienstanbieter Einfluss darauf, was in ihren Diensten erreichbar oder gesperrt ist. Sie selber kategorisieren die Inhalte und man kann von außen das nicht beeinflussen. Sind Ihre Meinung nach zu viele Inhalte zBp. über YouTube nicht erreichbar, ist die Einzige Möglichkeit SafeSearch für YouTube zu deaktivieren. Man muss jedoch beachten, dass in diesem Fall alle Inhalten auf YouTube (auch Gewalt, Pornografie) erreichbar sind.</div>
 

'''WICHTIG''' Man sollte beachten, dass das Neustarten des Dienstes etwas 10 Sekunden lang dauert. Während dieser Zeit ist keine Namensauflösung im Netz möglich. Deshalb sollte man Änderungen nicht im laufenden Betrieb unternehmen. Die Konfigurationsseite ist so gestaltet, dass man erst alle Änderungen durchführen und anschließend den Dienst neu starten muss.

'''WICHTIG''' Der DNS-Filter ist ein separater Dienst, der auf die IP-Adresse des Proxy-Servers auf dem UDP-Port 53 lauscht. Deshalb muss die IP-Adresse des Proxy-Servers als <code>dns forwarder</code> in der Samba-Konfiguration in <code>[global]</code>-Sektion eingetragen werden. Das geschieht automatisch bei der Installation des Paketes '''cranix-unbound'''. Der Unbound-Server wiederum nutzt den Server 8.8.8.8 bzw. den Server, der vor der Installation in der Samba-Konfiguration als "dns forwarder" eingetragen war, als DNS-Forwader. Wenn Sie diese Einstellung ändern müssen, ändern Sie den Eintrag <code>forward-zone</code> in der Datei <code>/etc/unbound/conf.d/cranix.conf</code>.

Da bei DNS-Filterung grundsätzlich alle Internetzugriffe erlaubt sind, ist die Protokollierung der Zugriffe sehr wichtig. Dafür wurde ein Dienst entwickelt, der alle Internetzugriff in die Datei '''/var/log/cranix-internet-access.log''' schreibt. Diese hat folgendes Format:
  Zeitpunkt;Benutzer;IP des Clients;IP des Ziels;IP-Protokoll;Port auf dem Zielrechner
Um die Datenmenge in vernünftigen Rahmen zu halten werden pro Minute nur ein Zugriff mit den selben Parametern protokolliert. Weiterhin wird bei der Installation ein Logrotate-Konfiguration ( ''/etc/logrotate.d/crx-fw-watcher'' ) mit folgenden Parameter hinterlegt:
* Maximales Alter der Logdateien: 180 Tage
* Maximale Anzahl der Logdateien: 20
* Maximale Größe der Logdateien: 4MiB 
Vorteile der DNS-Filterung:
* Alle Dienste in Internet sind nun schmerzfrei erreichbar.
* Die Clients benötigen keine besondere Konfiguration. ''Plug and Play'' ist wirklich möglich.
Nachteile der DNS-Filterung:
* Alles ist erlaubt, was nicht verboten ist.
* Positivlisten können nicht verwendet werden.

==Fehlersuche==
Leider gehört die Suche nach Fehler im Netzwerk zum Alltag jeder Systemadministrator. Hier möchten wir einige Fehler Ihrer Symptomen und Behebung und wie man diese Probleme in der Zukunft vermeiden kann, beschreiben.

===Schleife im Netzwerk===
Das ist einer der häufigsten Probleme im Netzwerken. Eine Schleife im Netzwerk entsteht dadurch, dass zwei Ports im Netzwerk direkt miteinander verbunden werden. Dabei spielt es keine Rolle, ob sich diese Ports auf dem selben Switch befinden oder nicht.

Das wichtigste Symptomen einer Netzwerkschleife ist, dass an sich alles mehr oder weniger funktioniert, allerdings dauert alles wesentlich länger. Man kann sogar den Server erreichen, man kann sich evtl anmelden und surfen. Aber die Anmeldung dauert bei allen Benutzer mehrere Minuten und das Öffnen von Webseiten geht auch nicht zügig. Oft landet man in einem Time-Out.

Man kann anhand der Logs des DHCPD-Servers erkennen, ob es sich eine Schleife im Netzwerk befindet. Normaler weise sendet jeder Rechner 2 DHCP-Anfragen wenn dieser gestartet wird: Eine beim PXE-Boot und eine beim Starten des Betriebssystems, wenn die Netzwerkkarte aktiviert wird.

===Überprüfung der Konnektivität eines Windows-Rechners===
Dass ein Windows-Rechner läuft und man sich am Rechner anmelden kann, heißt noch lange nicht, dass dieser mit dem Netzwerk verbunden ist. Hat man sich einmal an einem Windows-Arbeitsplatz erfolgreich angemeldet, werden seine Anmeldedaten lokal gespeichert. Deshalb kann man sich später an diesem Rechner auch dann anmelden, wenn der CRANIX-Server nicht erreichbar ist.
