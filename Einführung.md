---
title: Einführung
description: 
published: true
date: 2026-05-27T16:19:12.262Z
tags: 
editor: markdown
dateCreated: 2026-05-27T15:08:10.383Z
---


## Was ist der CRANIX Server
Der CRANIX Server ist ein zentraler Authentifizierung, Autorisierung, File, Drucker und Datenbankserver. Der aktuelle CRANIX Server 4.5 basiert auf der Distribution openSUSE Leap 15.6.

## Namenskonventionen
Leider behandeln nicht alle Betriebs- bzw. Filesysteme Groß-/Kleinschreibung gleich.
Um Probleme zu vermeiden haben wir folgende Namenskonvention festgelegt:

> Alle Objektnamen, ausgenommen GRUPPENNAMEN, werden ausschließlich mit kleinen Buchstaben geschrieben. GRUPPENNAMEN werden ausschließlich mit großen Buchstaben geschrieben.
{.is-info}



## Die Administrationsoberfläche
Die Administrationsoberfläche kann intern über die URL http://admin erreicht werden. Diese URL wird auf die sichere URL "https://admin.<Domainname>" weitergeleitet.

Nach erfolgreicher Anmeldung gelangt man in die Administrationsoberfläche (Cockpit) des CRANIX-Servers. Diese besteht aus vier Teilen:

- Links oben sieht man das CRANIX-Logo. Weiterhin wird hier angezeigt, ob man sich auf einen CRANIX- oder CEPHALIX-Server angemeldet hat. Auf einem mobilen Gerät bzw. bei einer schmaler Ansicht ist hier der Menü-Knopf. In der normalen Ansicht kann man mit einem Klick auf das CRANIX-Logo die Menüleiste einklappen und anschließend mit Doppelklick wieder ausklappen.
- In der Toolbar ist der Name des angemeldeten Benutzers und der Raum, in dem er sich angemeldet hat sichtbar. Auf der rechten Seite befinden sich, je nach Rollen und Rechten des angemeldeten Benutzers bis zu 3 Knöpfe:
    - **Alle Objekte vom Server holen** Daten werden neu geladen
    - **Benutzereinstellungen bearbeiten** Hier kann man die Sprache, das Erscheinungsbild und das Verhalten des Cockpits individuell ändern. Diese Einstellungen werden auf dem jeweiligen Gerät gespeichert. Also kann man sich verschiedene Einstellungen für Tablet, Handy und Rechner erstellen.
    - **Abmelden** 
- Auf der linken Seite ist die Menüleiste zu sehen. Welche Menüpunkte ein Benutzer zu sehen bekommt hängt von seinen Rechten ab. Diese wiederum hängen von der Rolle des Benutzers ab. Der aktive Menüpunkt ist farblich hinterlegt.
- In der Mitte wird der ausgewählte Menüpunkt dargestellt. Bei den meisten Menüpunkten ist der Aufbau folgender:
    - **Titel** Der ist vor allem bei schmalen Ansichten wichtig, da hier die Menüleiste nicht sichtbar ist.
    - **Suchfeld** In den meisten Menüpunkten werden viele Objekten (Benutzer, Rechner, Räume, Geräte ..) dargestellt. Im Suchfeld kann man nach Objekten in allen Felder auch die, die nicht sichtbar sind, suchen.
    - Rechts neben dem Suchfeld, bzw. auf Mobilen Geräten in einem "Floating Action Button" sind Knöpfe, mit denen verschiedene Aktionen ausgeführt werden können:
			- Mit dem Pluszeichen kann man ein neues Objekt anlegen.
			- Das 3-Punkte Menü ⋮ , kann nur auf gewählten Objekte angewendet werden.
  		- Mit dem ⋮⋮⋮ Zeichen kann man für jede Objekttabelle die darzustellenden Felder auswählen. Diese Einstellung wird auch dauerhaft auf dem jeweiligen Gerät gespeichert.
  
  ![cocpit_toolbar_basic.png](/cocpit_toolbar_basic.png)