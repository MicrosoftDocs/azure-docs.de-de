---
title: 'Konzepte: rollenbasierte Zugriffssteuerung in vSphere (RBAC)'
description: Erfahren Sie mehr über die wichtigsten Funktionen der rollenbasierten Zugriffssteuerung in vSphere für Azure VMware Solution.
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 9d7c724fba194d86c2d73bb8323439de7c83c170
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537017"
---
# <a name="vsphere-role-based-access-control-vsphere-rbac-for-azure-vmware-solution"></a>Rollenbasierte Zugriffssteuerung in vSphere (RBAC) für Azure VMware Solution

In Azure VMware Solution verfügt vCenter über einen integrierten lokalen Benutzer namens „cloudadmin“, der der integrierten Rolle CloudAdmin zugewiesen ist. Der lokale cloudadmin-Benutzer wird zum Einrichten von Benutzern in AD verwendet. Im Allgemeinen erstellt und verwaltet die Rolle CloudAdmin Workloads in Ihrer privaten Cloud. In Azure VMware Solution verfügt die Rolle CloudAdmin über vCenter-Berechtigungen, die sich von anderen VMware-Cloudlösungen unterscheiden.     

> [!NOTE]
> Azure VMware Solution bietet derzeit in vCenter oder im Azure VMware Solution-Portal keine benutzerdefinierten Rollen. 

Bei einer lokalen Bereitstellung von vCenter und ESXi hat der Administrator Zugriff auf das vCenter-Konto administrator@vsphere.local. Außerdem können ihm zusätzliche Active Directory-Benutzer/-Gruppen (AD) zugewiesen werden. 

Bei einer Azure VMware Solution-Bereitstellung hat der Administrator keinen Zugriff auf das Benutzerkonto des Administrators. Er kann jedoch AD-Benutzer und -Gruppen der Rolle CloudAdmin für vCenter zuweisen.  

Der Benutzer der privaten Cloud hat keinen Zugriff auf bestimmte Verwaltungskomponenten, die von Microsoft unterstützt und verwaltet werden, und kann diese nicht konfigurieren. Dazu gehören z. B. Cluster, Hosts, Datenspeicher und verteilte virtuelle Switches.

## <a name="azure-vmware-solution-cloudadmin-role-on-vcenter"></a>Azure VMware Solution-Rolle CloudAdmin in vCenter

Sie können die Berechtigungen anzeigen, die der Azure VMware Solution-Rolle CloudAdmin in vCenter Ihrer privaten Azure VMware Solution-Cloud erteilt wurden.

1. Melden Sie sich beim vSphere-Client des SDDC an, und gehen Sie zu **Menü** > **Verwaltung**.
1. Wählen Sie unter **Zugriffssteuerung** die Option **Rollen** aus.
1. Wählen Sie in der Liste der Rollen den Eintrag **CloudAdmin** aus, und wählen Sie dann **Berechtigungen** aus. 

   :::image type="content" source="media/role-based-access-control-cloudadmin-privileges.png" alt-text="Anzeigen der Berechtigungen der CloudAdmin-Rolle im vSphere-Client":::

Die Azure VMware Solution-Rolle CloudAdmin verfügt über die folgenden Berechtigungen für vCenter. Eine ausführliche Erläuterung der einzelnen Berechtigungen finden Sie in der [VMware-Produktdokumentation](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-ED56F3C4-77D0-49E3-88B6-B99B8B437B62.html).

| Berechtigung | BESCHREIBUNG |
| --------- | ----------- |
| **Warnungen** | Warnung bestätigen<br />Warnung erstellen<br />Warnungsaktion deaktivieren<br />Warnung ändern<br />Warnung entfernen<br />Warnungsstatus festlegen |
| **Berechtigungen** | Berechtigungen ändern |
| **Inhaltsbibliothek** | Bibliothekselement hinzufügen<br />Abonnement für eine veröffentlichte Bibliothek erstellen<br />Lokale Bibliothek erstellen<br />Abonnierte Bibliothek erstellen<br />Bibliothekselement löschen<br />Lokale Bibliothek löschen<br />Abonnierte Bibliothek löschen<br />Abonnement für eine veröffentlichte Bibliothek löschen<br />Herunterladen von Dateien<br />Bibliothekselemente entfernen<br />Abonnierte Bibliothek entfernen<br />Speicher importieren<br />Abonnementinformationen testen<br />Bibliothekselement für seine Abonnenten veröffentlichen<br />Bibliothek für ihre Abonnenten veröffentlichen<br />Speicher lesen<br />Bibliothekselement synchronisieren<br />Abonnierte Bibliothek synchronisieren<br />Typintrospektion<br />Konfigurationseinstellungen aktualisieren<br />Dateien aktualisieren<br />Bibliothek aktualisieren<br />Bibliothekselement aktualisieren<br />Lokale Bibliothek aktualisieren<br />Abonnierte Bibliothek aktualisieren<br />Abonnement einer veröffentlichten Bibliothek aktualisieren<br />Konfigurationseinstellungen anzeigen |
| **Kryptografische Vorgänge** | Direktzugriff |
| **Datenspeicher** | Speicherplatz zuweisen<br />Durchsuchen des Datenspeichers<br />Datenspeicher konfigurieren<br />Low-Level-Dateivorgänge<br />Dateien entfernen<br />Metadaten virtueller Computer aktualisieren |
| **Ordner** | Ordner erstellen<br />Ordner löschen<br />Ordner verschieben<br />Ordner umbenennen |
| **Global** | Task abbrechen<br />Globales Tag<br />Health<br />Ereignis protokollieren<br />Benutzerdefinierte Attribute definieren<br />Dienst-Manager<br />Benutzerdefiniertes Attribut festlegen<br />Systemtag |
| **Host** | vSphere-Replikation<br />&#160;&#160;&#160;&#160;Replikation verwalten |
| **vSphere-Tagging** | vSphere-Tag zuweisen und Zuweisung aufheben<br />vSphere-Tag erstellen<br />vSphere-Tagkategorie erstellen<br />vSphere-Tag löschen<br />vSphere-Tagkategorie löschen<br />vSphere-Tag bearbeiten<br />vSphere-Tagkategorie bearbeiten<br />Feld „UsedBy“ für Kategorie ändern<br />Feld „UsedBy“ für Tag ändern |
| **Network** | Netzwerk zuweisen |
| **Ressource** | Anwenden einer Empfehlung<br />vApp dem Ressourcenpool zuweisen<br />Virtuellen Computer dem Ressourcenpool zuweisen<br />Ressourcenpool erstellen<br />Ausgeschalteten virtuellen Computer migrieren<br />Eingeschalteten virtuellen Computer migrieren<br />Ressourcenpool ändern<br />Ressourcenpool entfernen<br />vMotion abfragen<br />Ressourcenpool entfernen<br />Ressourcenpool umbenennen |
| **Geplante Aufgabe** | Erstellen der Aufgabe<br />Aufgabe ändern<br />Aufgabe entfernen<br />Aufgabe ausführen |
| **Sitzungen** | `Message`<br />Sitzung überprüfen |
| **Profil** | Profilgesteuerte Speicheransicht |
| **Speicheransicht** | Sicht |
| **vApp** | Virtuellen Computer hinzufügen<br />Ressourcenpool zuweisen<br />vApp zuweisen<br />Klon<br />Erstellen<br />Löschen<br />Exportieren<br />Importieren<br />Move<br />Ausschalten<br />Einschalten<br />Umbenennen<br />Angehalten<br />Unregister<br />OVF-Umgebung anzeigen<br />vApp-Anwendungskonfiguration<br />vApp-Instanzkonfiguration<br />vApp-managedBy-Konfiguration<br />vApp-Ressourcenkonfiguration |
| **Virtueller Computer** | Konfiguration ändern<br />&#160;&#160;&#160;&#160;Leasedauer des Datenträgers abrufen<br />&#160;&#160;&#160;&#160;Vorhandenen Datenträger hinzufügen<br />&#160;&#160;&#160;&#160;Neuen Datenträger hinzufügen<br />&#160;&#160;&#160;&#160;Gerät hinzufügen oder entfernen<br />&#160;&#160;&#160;&#160;Erweiterte Konfiguration<br />&#160;&#160;&#160;&#160;CPU-Anzahl ändern<br />&#160;&#160;&#160;&#160;Arbeitsspeicher ändern<br />&#160;&#160;&#160;&#160;Einstellungen ändern<br />&#160;&#160;&#160;&#160;Platzierung der Auslagerungsdatei ändern<br />&#160;&#160;&#160;&#160;Ressource ändern<br />&#160;&#160;&#160;&#160;Host-USB-Gerät konfigurieren<br />&#160;&#160;&#160;&#160;Unformatiertes Gerät konfigurieren<br />&#160;&#160;&#160;&#160;ManagedBy konfigurieren<br />&#160;&#160;&#160;&#160;Verbindungseinstellungen anzeigen<br />&#160;&#160;&#160;&#160;Virtuellen Datenträger erweitern<br />&#160;&#160;&#160;&#160;Geräteeinstellungen ändern<br />&#160;&#160;&#160;&#160;Kompatibilität der Fehlertoleranz abfragen<br />&#160;&#160;&#160;&#160;Nicht im Besitz befindliche Dateien abfragen<br />&#160;&#160;&#160;&#160;Aus Pfaden neu laden<br />&#160;&#160;&#160;&#160;Datenträger entfernen<br />&#160;&#160;&#160;&#160;Umbenennen<br />&#160;&#160;&#160;&#160;Gastinformationen zurücksetzen<br />&#160;&#160;&#160;&#160;Anmerkung festlegen<br />&#160;&#160;&#160;&#160;Änderungsnachverfolgung für Datenträger ein-/ausschalten<br />&#160;&#160;&#160;&#160;Übergeordneten Fork ein-/ausschalten<br />&#160;&#160;&#160;&#160;VM-Kompatibilität aktualisieren<br />Inventar bearbeiten<br />&#160;&#160;&#160;&#160;Aus vorhandenem Element erstellen<br />&#160;&#160;&#160;&#160;Neues Element erstellen<br />&#160;&#160;&#160;&#160;Verschieben<br />&#160;&#160;&#160;&#160;Registrieren<br />&#160;&#160;&#160;&#160;Entfernen<br />&#160;&#160;&#160;&#160;Registrierung aufheben<br />Gastvorgänge<br />&#160;&#160;&#160;&#160;Gastvorgang Aliasänderung<br />&#160;&#160;&#160;&#160;Gastvorgang Aliasabfrage<br />&#160;&#160;&#160;&#160;Gastvorgang Änderungen<br />&#160;&#160;&#160;&#160;Gastvorgang Programmausführung<br />&#160;&#160;&#160;&#160;Gastvorgang Abfragen<br />Interaktion<br />&#160;&#160;&#160;&#160;Frage beantworten<br />&#160;&#160;&#160;&#160;Sicherungsvorgang für VM<br />&#160;&#160;&#160;&#160;CD-Medien konfigurieren<br />&#160;&#160;&#160;&#160;Disketten konfigurieren<br />&#160;&#160;&#160;&#160;Geräte verbinden<br />&#160;&#160;&#160;&#160;Konsoleninteraktion<br />&#160;&#160;&#160;&#160;Screenshot erstellen<br />&#160;&#160;&#160;&#160;Alle Datenträger defragmentieren<br />&#160;&#160;&#160;&#160;Drag & Drop<br />&#160;&#160;&#160;&#160;Gastbetriebssystemverwaltung durch VIX-API<br />&#160;&#160;&#160;&#160;USB-HID-Scancodes einfügen<br />&#160;&#160;&#160;&#160;VMware-Tools installieren<br />&#160;&#160;&#160;&#160;Anhalten oder Anhalten aufheben<br />&#160;&#160;&#160;&#160;Lösch- oder Verkleinerungsvorgänge ausführen<br />&#160;&#160;&#160;&#160;Ausschalten<br />&#160;&#160;&#160;&#160;Einschalten<br />&#160;&#160;&#160;&#160;Sitzung auf VM aufzeichnen<br />&#160;&#160;&#160;&#160;Sitzung auf VM wiedergeben<br />&#160;&#160;&#160;&#160;Anhalten<br />&#160;&#160;&#160;&#160;Fehlertoleranz anhalten<br />&#160;&#160;&#160;&#160;Failover testen<br />&#160;&#160;&#160;&#160;Neustart der sekundären VM testen<br />&#160;&#160;&#160;&#160;Fehlertoleranz deaktivieren<br />&#160;&#160;&#160;&#160;Fehlertoleranz aktivieren<br />Bereitstellung<br />&#160;&#160;&#160;&#160;Datenträgerzugriff zulassen<br />&#160;&#160;&#160;&#160;Dateizugriff zulassen<br />&#160;&#160;&#160;&#160;Schreibgeschützten Datenträgerzugriff zulassen<br />&#160;&#160;&#160;&#160;VM-Download zulassen<br />&#160;&#160;&#160;&#160;Vorlage klonen<br />&#160;&#160;&#160;&#160;VM klonen<br />&#160;&#160;&#160;&#160;Vorlage aus VM erstellen<br />&#160;&#160;&#160;&#160;Gast anpassen<br />&#160;&#160;&#160;&#160;Vorlage bereitstellen<br />&#160;&#160;&#160;&#160;Als Vorlage markieren<br />&#160;&#160;&#160;&#160;Anpassungsspezifikation ändern<br />&#160;&#160;&#160;&#160;Datenträger höherstufen<br />&#160;&#160;&#160;&#160;Anpassungsspezifikationen lesen<br />Dienstkonfiguration<br />&#160;&#160;&#160;&#160;Benachrichtigungen zulassen<br />&#160;&#160;&#160;&#160;Abrufen globaler Ereignisbenachrichtigungen zulassen<br />&#160;&#160;&#160;&#160;Dienstkonfiguration verwalten<br />&#160;&#160;&#160;&#160;Dienstkonfiguration ändern<br />&#160;&#160;&#160;&#160;Dienstkonfigurationen abfragen<br />&#160;&#160;&#160;&#160;Dienstkonfiguration lesen<br />Momentaufnahmenverwaltung<br />&#160;&#160;&#160;&#160;Momentaufnahme erstellen<br />&#160;&#160;&#160;&#160;Momentaufnahme entfernen<br />&#160;&#160;&#160;&#160;Momentaufnahme umbenennen<br />&#160;&#160;&#160;&#160;Momentaufnahme wiederherstellen<br />vSphere-Replikation<br />&#160;&#160;&#160;&#160;Replikation konfigurieren<br />&#160;&#160;&#160;&#160;Replikation verwalten<br />&#160;&#160;&#160;&#160;Replikation überwachen |
| **vService** | Abhängigkeit erstellen<br />Abhängigkeit löschen<br />Abhängigkeitskonfiguration neu konfigurieren<br />Abhängigkeit aktualisieren |



## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich mit den Grundlagen der rollenbasierten Zugriffssteuerung in vSphere für Azure VMware Solution vertraut gemacht haben, informieren Sie sich über die folgenden Themen:

- Details der einzelnen Berechtigungen in der [VMware-Produktdokumentation](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-ED56F3C4-77D0-49E3-88B6-B99B8B437B62.html)
- [Überwachen und Reparieren von privaten Clouds mit Azure VMware Solution](concepts-monitor-repair-private-cloud.md)
- [Aktivieren einer Azure VMware Solution-Ressource](enable-azure-vmware-solution.md)

<!-- LINKS - internal -->

<!-- LINKS - external-->
[VMware product documentation]: https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-ED56F3C4-77D0-49E3-88B6-B99B8B437B62.html

