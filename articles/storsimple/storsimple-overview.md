---
title: Lösungsübersicht zur StorSimple 8000-Serie | Microsoft-Dokumentation
description: Beschreibt die StorSimple-Speicherstaffelung, das Gerät, das virtuelle Gerät, Dienste und die Speicherverwaltung und erläutert wichtige in StorSimple verwendete Begriffe.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 7144d218-db21-4495-88fb-e3b24bbe45d1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/10/2017
ms.author: timlt
ms.openlocfilehash: f9c7a2df83e9d630ce2b4635a830fb941180c758
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021832"
---
# <a name="storsimple-8000-series-a-hybrid-cloud-storage-solution"></a>StorSimple 8000-Serie: eine Hybridcloud-Speicherlösung

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Übersicht
Willkommen bei Microsoft Azure StorSimple, einer integrierten Speicherlösung, die Speicheraufgaben zwischen lokalen Geräten und Microsoft Azure-Cloudspeicher verwaltet. StorSimple ist eine effiziente, kostengünstige und einfach zu verwaltende Storage Area Network (SAN)-Lösung, mit der viele der Probleme und Kosten vermieden werden, die mit der Datenspeicherung und dem Datenschutz im Unternehmen verbunden sind. Die Lösung verwendet das proprietäre Gerät der StorSimple 8000-Serie, bietet die Integration mit Clouddiensten und stellt einen Satz integrierter Verwaltungstools bereit, um eine transparente Ansicht aller Speicher im Unternehmen, einschließlich Cloudspeicher, zu ermöglichen. (Die auf der Microsoft Azure-Website veröffentlichten StorSimple-Bereitstellungsinformationen gelten nur für Geräte der StorSimple 8000-Serie. Wenn Sie ein Gerät der StorSimple 5000/7000-Serie verwenden, finden Sie entsprechende Informationen unter [StorSimple Help](http://onlinehelp.storsimple.com/)(in englischer Sprache).)

StorSimple verwendet zur Verwaltung von gespeicherten Daten auf verschiedenen Speichermedien eine [Speicherstaffelung](#automatic-storage-tiering) . Der aktuelle Arbeitssatz wird lokal auf Solid-State-Laufwerken (SSDs) gespeichert, weniger häufig verwendete Daten auf Festplattenlaufwerken (HDDs), und Archivdaten werden in die Cloud verschoben. Darüber hinaus verwendet StorSimple die Deduplizierung und Komprimierung, um den Speicherplatz zu reduzieren, den die Daten nutzen. Weitere Informationen finden Sie unter [Deduplizierung und Komprimierung](#deduplication-and-compression). Definitionen anderer wichtiger Begriffe und Konzepte, die in der Dokumentation zur StorSimple 8000-Serie verwendet werden, finden Sie unter [StorSimple-Terminologie](#storsimple-terminology) am Ende dieses Artikels.

Zusätzlich zur Speicherverwaltung können Sie mithilfe der StorSimple-Funktionen zum Schutz von Daten bedarfsgesteuerte und geplante Sicherungen erstellen und anschließend lokal oder in der Cloud speichern. Sicherungen erfolgen in Form von inkrementellen Momentaufnahmen, sodass sie schnell erstellt und wiederhergestellt werden können. Cloud-Momentaufnahmen können bei Wiederherstellungen im Notfall extrem wichtig sein, da sie sekundäre Speichersystemen (z. B. Backup auf Bandlaufwerken) ersetzen und es Ihnen ermöglichen, Daten bei Bedarf in Ihrem Rechenzentrum oder an anderen Standorten wiederherzustellen.

![Video-Symbol](./media/storsimple-overview/video_icon.png) Sehen Sie sich das Video mit einer kurzen Einführung in Microsoft Azure StorSimple an.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/StorSimple-Hybrid-Cloud-Storage-Solution/player]

## <a name="why-use-storsimple"></a>Gründe für die Verwendung von StorSimple
In der folgenden Tabelle werden einige der wichtigsten Vorteile von Microsoft Azure StorSimple beschrieben.

| Funktion | Vorteil |
| --- | --- |
| Transparente Integration |Verwendet das iSCSI-Protokoll, um Datenspeicher unsichtbar miteinander zu verknüpfen. Dies sorgt dafür, dass Daten, die in der Cloud, im Datencenter oder auf Remoteservern gespeichert sind, dem Anschein nach an einem zentralen Speicherort gespeichert werden. |
| Reduzierte Speicherkosten |Reserviert ausreichend lokalen oder Cloudspeicher, um aktuelle Anforderungen zu erfüllen, und erweitert Cloudspeicher nur bei Bedarf. Außerdem werden Speicheranforderungen und -kosten durch die Beseitigung redundanter Versionen der gleichen Daten (Deduplizierung) und eine Komprimierung noch weiter verringert. |
| Vereinfachte Speicherverwaltung |Stellt Systemverwaltungstools bereit, mit denen Sie lokal, auf einem Remoteserver und in der Cloud gespeicherte Daten konfigurieren und verwalten können. Darüber hinaus können Sie Backup- und Wiederherstellungsfunktionen über ein Microsoft Management Console-Snap-In verwalten.|
| Verbesserte Notfallwiederherstellung und Vorschrifteneinhaltung |Weist keine verlängerte Wiederherstellungszeit auf. Stattdessen stellt die Lösung Daten nach Bedarf wieder her. Dies bedeutet, dass normale Vorgänge mit minimaler Unterbrechung fortgesetzt werden können. Darüber hinaus können Sie Richtlinien zum Angeben von Sicherungszeitplänen und zur Aufbewahrung von Daten konfigurieren. |
| Datenmobilität |Auf in Microsoft Azure Cloud Services hochgeladene Daten kann für Wiederherstellungs- und Migrationszwecke von anderen Standorten aus zugegriffen werden. Darüber hinaus können Sie mithilfe von StorSimple auch StorSimple Cloud Appliances auf virtuellen Computern (VMs) konfigurieren, die in Microsoft Azure ausgeführt werden. Die virtuellen Computer nutzen dann für Test-oder Wiederherstellungszwecke virtuelle Geräte für den Zugriff auf gespeicherte Daten. |
| Geschäftskontinuität |Erlaubt Benutzern der StorSimple-Serien 5000–7000, ihre Daten zu einem Gerät der StorSimple 8000-Serie zu migrieren. |
| Verfügbarkeit im Azure-Portal für Behörden |StorSimple ist im Azure Government-Portal verfügbar. Weitere Informationen finden Sie unter [Bereitstellen lokaler StorSimple-Geräte im Government-Portal](storsimple-8000-deployment-walkthrough-gov-u2.md). |
| Datensicherheit und -verfügbarkeit |Die StorSimple 8000-Serie unterstützt neben lokal redundantem Speicher (LRS) und georedundantem Speicher (GRS) auch zonenredundanten Speicher (ZRS). Ausführliche Informationen über zonenredundanten Speicher (ZRS) finden Sie in [diesem Artikel über Redundanzoptionen von Azure Storage](../storage/common/storage-redundancy.md) . |
| Unterstützung für kritische Anwendungen |Mit StorSimple können Sie die entsprechenden Volumes als lokal identifizieren, womit wiederum sichergestellt wird, dass die für wichtige Anwendungen erforderlichen Daten nicht in die Cloud verschoben werden. Lokale Volumes werden nicht durch Cloudlatenzen oder Konnektivitätsprobleme beeinträchtigt. Weitere Informationen zu lokalen Volumes finden Sie unter [Verwalten von Volumes mithilfe des StorSimple-Geräte-Manager-Diensts](storsimple-8000-manage-volumes-u2.md). |
| Geringe Latenz und hohe Leistung |Sie können Cloud Appliances erstellen, die die Features von Azure Storage Premium für hohe Leistung und niedrige Latenzen nutzen. Weitere Informationen zu Premium-StorSimple Cloud Appliances finden Sie unter [Bereitstellen und Verwalten einer StorSimple Cloud Appliance in Azure](storsimple-8000-cloud-appliance-u2.md). |


## <a name="storsimple-components"></a>StorSimple-Komponenten
Die Microsoft Azure StorSimple-Lösung umfasst die folgenden Komponenten:

* **Microsoft Azure StorSimple-Gerät**: Ein lokales Hybridspeicherarray mit SSDs und HDDs sowie redundanten Controllern und automatischen Failoverfunktionen. Die Controller verwalten die Speicherstaffelung, wobei derzeit verwendete (d. h. aktiv genutzte) Daten in lokalem Speicher (auf dem Gerät oder lokalen Server) abgelegt werden, während weniger häufig verwendete Daten in die Cloud verschoben werden.
* **StorSimple Cloud Appliance:** Dies ist eine auch als virtuelles StorSimple-Gerät bezeichnete Softwareversion des StorSimple-Geräts, die die Architektur und die meisten Funktionen des physischen Hybridspeichergeräts repliziert. Die StorSimple Cloud Appliance wird auf einem einzelnen Knoten auf einem virtuellen Azure-Computer ausgeführt. Virtuelle Premium-Geräte, die Azure-Premium-Speicher nutzen, stehen in Update 2 und höher zur Verfügung.
* **StorSimple-Geräte-Manager-Dienst:** eine Erweiterung des Azure-Portals, mit der Sie ein StorSimple-Gerät oder eine StorSimple Cloud Appliance über eine gemeinsame Weboberfläche verwalten können. Sie können den StorSimple-Geräte-Manager-Dienst nutzen, um Dienste zu erstellen und zu verwalten, Geräte anzuzeigen und zu verwalten, Warnungen anzuzeigen, Volumes zu verwalten und Sicherungsrichtlinien und den Sicherungskatalog anzuzeigen und zu verwalten.
* **Windows PowerShell für StorSimple** – Eine Befehlszeilenschnittstelle, mit der Sie das StorSimple-Gerät verwalten können. Windows PowerShell für StorSimple bietet Features, mit denen Sie Ihr StorSimple-Gerät registrieren, die Netzwerkschnittstelle Ihres Geräts konfigurieren, bestimmte Arten von Updates installieren, Probleme Ihres Geräts durch Zugriff auf eine Supportsitzung beheben und den Gerätestatus ändern können. Sie können auf Windows PowerShell für StorSimple zugreifen, indem Sie eine Verbindung mit der seriellen Konsole herstellen oder Windows PowerShell-Remoting verwenden.
* **Azure PowerShell-Cmdlets für StorSimple** – eine Auflistung von Windows PowerShell-Cmdlets, die es Ihnen ermöglichen, Servicelevel- und Migrationsaufgaben über die Befehlszeile zu automatisieren. Weitere Informationen zu den Azure PowerShell-Cmdlets für StorSimple finden Sie unter [Cmdlet-Referenz](/powershell/module/servicemanagement/azure.service/?view=azuresmps-3.7.0#azure).
* **StorSimple Snapshot Manager** – Ein MMC-Snap-In, das Volumegruppen und den Volumeschattenkopie-Dienst von Windows verwendet, um anwendungskonsistente Backups zu generieren. Darüber hinaus können Sie den StorSimple-Momentaufnahmen-Manager zum Erstellen von Sicherungszeitplänen und Klonen oder Wiederherstellen von Volumes einsetzen.
* **StorSimple-Adapter für SharePoint** – Ein Tool, das den Microsoft Azure StorSimple-Speicher und den Datenschutz auf SharePoint Server-Farmen transparent erweitert, während der StorSimple-Speicher über das Portal der SharePoint-Zentraladministration angezeigt und verwaltet werden kann.

Das folgende Diagramm bietet eine allgemeine Übersicht über die Architektur und die Komponenten von Microsoft Azure StorSimple.

![StorSimple-Architektur](./media/storsimple-overview/overview-big-picture.png)

In den folgenden Abschnitten wird jede dieser Komponenten ausführlicher beschrieben. Es wird zudem erläutert, wie die Lösung Daten anordnet, Speicher zuweist sowie die Speicherverwaltung und den Schutz von Daten vereinfacht. Der letzte Abschnitt enthält Definitionen für einige wichtige Begriffe und Konzepte im Zusammenhang mit den StorSimple-Komponenten und deren Verwaltung.

## <a name="storsimple-device"></a>StorSimple-Gerät
Das Microsoft Azure StorSimple-Gerät ist ein lokales Hybridspeicherarray, das primären Speicher und iSCSI den Zugriff auf die darauf gespeicherten Daten ermöglicht. Es verwaltet die Kommunikation mit Cloudspeicher und gewährleistet die Sicherheit und Vertraulichkeit aller Daten, die in der Microsoft Azure StorSimple-Lösung gespeichert sind.

Zum StorSimple-Gerät gehören SSDs und HDDs sowie Unterstützung für Clustering und automatisches Failover. Das Gerät enthält einen gemeinsam genutzten Prozessor, gemeinsam genutzten Speicher sowie zwei gespiegelte Controller. Jeder Controller stellt Folgendes bereit:

* Verbindung mit einem Hostcomputer
* Bis zu sechs Netzwerkanschlüsse zum Herstellen einer Verbindung mit dem lokalen Netzwerk (LAN)
* Hardwareüberwachung
* NVRAM (Non-Volatile Random Access Memory), der Informationen speichert, selbst wenn die Stromzufuhr unterbrochen wird
* Clusterfähiges Aktualisieren für die Verwaltung von Softwareupdates auf Servern in einem Failovercluster, damit die Updates nur minimale oder keine Auswirkungen auf die Dienstverfügbarkeit haben
* Einen Clusterdienst, der wie ein Back-End-Cluster funktioniert und Hochverfügbarkeit bereitstellt sowie negative Auswirkungen verringert, die ggf. auftreten können, wenn ein HDD oder SSD ausfällt oder offline geschaltet wird

Es ist nur ein Controller gleichzeitig aktiv. Wenn der aktive Controller ausfällt, wird automatisch der zweite Controller aktiviert.

Weitere Informationen finden Sie unter [StorSimple-Hardwarekomponenten und Status](storsimple-8000-monitor-hardware-status.md).

## <a name="storsimple-cloud-appliance"></a>StorSimple Cloud Appliance
Sie können StorSimple zum Erstellen einer Cloud Appliance verwenden, die die Architektur und Funktionen des physischen Hybridspeichergeräts repliziert. Die StorSimple Cloud Appliance (auch als virtuelles StorSimple-Gerät bezeichnet) wird auf einem einzelnen Knoten eines virtuellen Azure-Computers ausgeführt. (Eine Cloud Appliance kann nur auf einem virtuellen Azure-Computer erstellt werden. Es kann nicht auf einem StorSimple-Gerät oder auf einem lokalen Server erstellt werden.)

Die Cloud Appliance weist folgende Merkmale auf:

* Es verhält sich wie ein physisches Gerät und bietet eine iSCSI-Schnittstelle mit virtuellen Computern in der Cloud.
* Sie können eine unbegrenzte Anzahl von Cloud Appliances in der Cloud erstellen und sie nach Bedarf aktivieren und deaktivieren.
* Mit dem virtuellen Gerät können lokale Umgebungen bei der Notfallwiederherstellung und in Entwicklungs- oder Testszenarios simuliert sowie der Abruf aus Sicherungen auf Elementebene unterstützt werden.

Die StorSimple Cloud Appliance steht in zwei Modellen zur Verfügung: dem 8010-Gerät (früher als Modell 1100 bekannt) und dem 8020-Gerät. Das 8010-Gerät bietet eine maximale Kapazität von 30 TB. Das 8020-Gerät nutzt Azure-Premium-Speicher und umfasst eine maximale Kapazität von 64 TB. (In lokalen Ebenen speichert der Azure-Premium-Speicher Daten auf SSDs. Beim Standardspeicher werden Daten auf HDDs gespeichert.) Beachten Sie, dass Sie zur Verwendung von Premium-Speicher ein Azure-Premium-Speicherkonto benötigen.

Weitere Informationen zu StorSimple Cloud Appliances finden Sie unter [Bereitstellen und Verwalten einer StorSimple Cloud Appliance in Azure](storsimple-8000-cloud-appliance-u2.md).

## <a name="storsimple-device-manager-service"></a>StorSimple-Geräte-Manager-Dienst
Microsoft Azure StorSimple stellt eine webbasierte Benutzeroberfläche (den StorSimple-Geräte-Manager-Dienst) zur Verfügung, die die zentrale Verwaltung von Rechenzentrum und Cloudspeicher ermöglicht. Mithilfe des StorSimple-Geräte-Manager-Diensts können Sie die folgenden Aufgaben ausführen:

* Konfigurieren von Systemeinstellungen für StorSimple-Geräte
* Konfigurieren und Verwalten von Sicherheitseinstellungen für StorSimple-Geräte
* Konfigurieren von Cloudanmeldeinformationen und -Eigenschaften
* Konfigurieren und Verwalten von Volumes auf einem Server
* Konfigurieren von Volumegruppen
* Sichern und Wiederherstellen von Daten
* Überwachen der Leistung
* Überprüfen von Systemeinstellungen und Bestimmen möglicher Probleme

Sie können den StorSimple-Geräte-Manager-Dienst zum Ausführen aller Verwaltungsaufgaben mit Ausnahme der Aufgaben verwenden, die einen Systemausfall mit sich bringen (z.B. die Ersteinrichtung oder die Installation von Updates).

Weitere Informationen finden Sie unter [Verwalten Ihres StorSimple-Geräts mithilfe des StorSimple-Geräte-Manager-Diensts](storsimple-8000-manager-service-administration.md).

## <a name="windows-powershell-for-storsimple"></a>Windows PowerShell für StorSimple
Windows PowerShell für StorSimple stellt eine Befehlszeilen-Schnittstelle zur Verfügung, die Sie zum Erstellen und Verwalten des Microsoft Azure StorSimple-Diensts sowie zum Einrichten und Überwachen von StorSimple-Geräten verwenden können. Es handelt sich um eine auf Windows PowerShell basierende Befehlszeilen-Schnittstelle, die dedizierte Cmdlets zum Verwalten Ihres StorSimple-Geräts umfasst. Mit den Funktionen von Windows PowerShell für StorSimple können Sie die folgenden Aufgaben ausführen:

* Registrieren eines Geräts
* Konfigurieren der Netzwerkschnittstelle auf einem Gerät
* Installieren bestimmter Typen von Updates
* Beheben von Geräteproblemen durch Zugreifen auf die Supportsitzung
* Ändern des Gerätezustands

Sie können auf Windows PowerShell für StorSimple über eine serielle Konsole (auf einem Hostcomputer, der direkt mit dem Gerät verbunden ist) oder remote mithilfe von Windows PowerShell-Remoting zugreifen. Beachten Sie, dass einige der Windows PowerShell für StorSimple-Aufgaben (z. B. die anfängliche Registrierung des Geräts) nur in der seriellen Konsole erfolgen können.

Weitere Informationen finden Sie unter [Verwenden von Windows PowerShell für StorSimple zum Verwalten eines StorSimple-Geräts](storsimple-8000-windows-powershell-administration.md).

## <a name="azure-powershell-storsimple-cmdlets"></a>Azure PowerShell-Cmdlets für StorSimple
Die Azure PowerShell-Cmdlets für StorSimple sind eine Sammlung von Windows PowerShell-Cmdlets, mit denen Sie Servicelevel- und Migrationsaufgaben über die Befehlszeile automatisieren können. Weitere Informationen zu den Azure PowerShell-Cmdlets für StorSimple finden Sie unter [Cmdlet-Referenz](/powershell/module/servicemanagement/azure.service/?view=azuresmps-3.7.0).

## <a name="storsimple-snapshot-manager"></a>StorSimple Snapshot Manager
StorSimple Snapshot Manager ist ein MMC-Snap-In (Microsoft Management Console), das Sie zum Erstellen konsistenter zeitpunktbezogener Sicherungskopien von lokalen und Clouddaten verwenden können. Das Snap-In wird auf einem auf Windows Server basierenden Host ausgeführt. Sie können StorSimple Snapshot Manager für die folgenden Aufgaben verwenden:

* Konfigurieren, Sichern und Löschen von Volumes
* Konfigurieren von Volumegruppen, um sicherzustellen, dass gesicherte Daten anwendungskonsistent sind
* Verwalten von Sicherungsrichtlinien, damit Daten gemäß einem vordefinierten Zeitplan gesichert und an einem festgelegten Speicherort (lokal oder in der Cloud) gespeichert werden
* Wiederherstellen von Volumes und einzelnen Dateien

Sicherungen werden als Momentaufnahmen erfasst, die nur die Änderungen seit der letzen Erstellung einer Momentaufnahme aufzeichnen und weit weniger Speicherplatz als vollständige Sicherungen erfordern. Sie können Sicherungspläne erstellen oder bei Bedarf sofortige Sicherungen vornehmen. Außerdem können Sie StorSimple Snapshot Manager zum Einrichten von Aufbewahrungsrichtlinien verwenden, die steuern, wie viele Momentaufnahmen gespeichert werden. Wenn Sie anschließend Daten aus einer Sicherung wiederherstellen müssen, ermöglicht StorSimple Snapshot Manager Ihnen die Auswahl aus in einem Katalog mit lokalen oder Cloudmomentaufnahmen. 

Wenn ein Notfall eintritt oder Daten aus anderen Gründen wiederhergestellt werden müssen, stellt StorSimple Snapshot Manager diese nach Bedarf inkrementell wieder her. Für die Datenwiederherstellung ist es nicht erforderlich, das gesamte System herunterzufahren, während eine Datei wiederhergestellt, Hardware ausgetauscht oder der Betrieb an einen anderen Standort verlagert wird.

Weitere Informationen finden Sie unter [Was ist der StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

## <a name="storsimple-adapter-for-sharepoint"></a>StorSimple-Adapter für SharePoint
Microsoft Azure StorSimple enthält den StorSimple-Adapter für SharePoint – eine optionale Komponente, die die Speicher- und Datenschutzfunktionen von StorSimple transparent auf SharePoint-Serverfarmen erweitert. Der Adapter arbeitet mit dem RBS-Anbieter (Remote Blob Storage) und der RBS-Funktion von SQL Server zusammen und ermöglicht das Verschieben von BLOBs auf einen Server, der durch das Microsoft Azure StorSimple-System gesichert wird. Microsoft Azure StorSimple speichert die BLOB-Daten dann basierend auf ihrer Verwendung lokal oder in der Cloud.

Der StorSimple-Adapter für SharePoint wird im Portal der SharePoint-Zentraladministration verwaltet. Aus diesem Grund bleibt die SharePoint-Verwaltung zentralisiert, und der gesamte Speicher scheint sich in der SharePoint-Farm zu befinden.

Weitere Informationen finden Sie unter [StorSimple-Adapter für SharePoint](storsimple-adapter-for-sharepoint.md). 

## <a name="storage-management-technologies"></a>Speicherverwaltungstechnologien
Zusätzlich zum dedizierten StorSimple-Gerät, virtuellen Gerät und anderen Komponenten verwendet Microsoft Azure StorSimple die folgenden Softwaretechnologien, um schnellen Zugriff auf Daten bereitzustellen und die Speicherbelegung zu verringern:

* [Automatische Speicherstaffelung](#automatic-storage-tiering) 
* [Schlanke Speicherzuweisung](#thin-provisioning) 
* [Deduplizierung und Komprimierung](#deduplication-and-compression) 

### <a name="automatic-storage-tiering"></a>Automatische Speicherstaffelung
Microsoft Azure StorSimple ordnet Daten automatisch in logischen Ebenen basierend auf aktueller Nutzung, Alter und Beziehung zu anderen Daten an. Daten, die am aktivsten sind, werden lokal gespeichert, während weniger aktive und inaktive Daten automatisch in die Cloud migriert werden. Die folgende Abbildung zeigt dieses Speicherkonzept.

![StorSimple-Speicherebenen](./media/storsimple-overview/hcs-data-services-storsimple-components-tiers.png)

Damit ein schneller Zugriff ermöglicht wird, speichert Azure StorSimple sehr aktive Daten auf SSDs im StorSimple-Gerät. Daten, die gelegentlich verwendet werden, werden auf HDDs im Gerät oder auf Servern im Datencenter gespeichert. Inaktive Daten, Sicherungsdaten und Daten, die für Archivierungs- oder Kompatibilitätszwecke verwaltet werden, werden in der Cloud gespeichert. 

> [!NOTE]
> In Update 2 oder höher können Sie ein Volume als lokal festlegen. In diesem Fall verbleiben die Daten auf dem lokalen Gerät und werden nicht in die Cloud ausgelagert. 


StorSimple passt Daten- und Speicherzuordnungen an und ordnet diese neu, wenn sich die Verwendungsmuster ändern. Im Lauf der Zeit können einige Informationen z. B. weniger aktiv werden. Wenn die betreffenden Informationen kontinuierlich weniger aktiv werden, werden sie von SSDs zu HDDs und dann zur Cloud migriert. Wenn diese Daten erneut aktiv werden, werden sie zurück in das Speichergerät migriert.

Die Speicherstaffelung erfolgt folgendermaßen:

1. Ein Systemadministrator richtet ein Microsoft Azure-Cloudspeicherkonto ein.
2. Der Administrator verwendet die serielle Konsole sowie den StorSimple-Geräte-Manager-Dienst (der im Azure-Portal ausgeführt wird) zum Konfigurieren des Geräts und des Dateiservers, wobei er Volumes und Richtlinien zur Datensicherheit erstellt. Lokale Maschinen (wie z. B. Dateiserver) verwenden die iSCSI-Schnittstelle (Internet Small Computer System Interface) auf dem StorSimple-Gerät.
3. StorSimple speichert zunächst Daten auf der schnellen SSD-Ebene des Geräts.
4. Wenn die SSD-Ebene sich der Kapazitätsgrenze nähert, dupliziert und komprimiert StorSimple die ältesten Datenblöcke und verschiebt sie auf die HDD-Ebene.
5. Wenn sich die HDD-Ebene der Kapazitätsgrenze nähert, verschlüsselt StorSimple die ältesten Datenblöcke und sendet diese sicher per HTTPS an das Microsoft Azure-Speicherkonto.
6. Microsoft Azure erstellt mehrere Replikate der Daten im Rechenzentrum und einem Remoterechenzentrum, um sicherzustellen, dass die Daten wiederhergestellt werden können, wenn ein Notfall eintritt.
7. Wenn der Dateiserver in der Cloud gespeicherte Daten anfordert, gibt StorSimple diese nahtlos zurück und speichert eine Kopie auf der SSD-Ebene des StorSimple-Geräts.

#### <a name="how-storsimple-manages-cloud-data"></a>Verwaltung von Clouddaten durch StorSimple

StorSimple dedupliziert Kundendaten über alle Momentaufnahmen und die primären Daten hinweg (Daten, die von Hosts geschrieben werden). Wenngleich die Deduplizierung für optimale Speichereffizienz sorgt, erschwert sie die Frage, was sich in der Cloud befindet. Die mehrstufigen primären Daten und die Momentaufnahmedaten überschneiden sich. Ein einzelner Datenblock in der Cloud kann gleichzeitig für mehrstufige primäre Daten verwendet und durch verschiedene Momentaufnahmen referenziert werden. Jede Cloudmomentaufnahme stellt sicher, dass eine Kopie aller Point-in-Time-Daten in der Cloud verbleibt, bis diese Momentaufnahme gelöscht wird.

Daten werden nur aus der Cloud gelöscht, wenn keine Verweise auf diese Daten vorliegen. Wenn beispielsweise eine Cloudmomentaufnahme aller Daten im StorSimple-Gerät erstellt wird und Sie dann einige primäre Daten löschen, ist die Abnahme der _primären Daten_ sofort erkennbar. Die _Clouddaten_, die sowohl die mehrstufigen Daten als auch die Sicherungen enthalten, bleiben dieselben. Dies liegt daran, dass über eine Momentaufnahme weiterhin auf die Clouddaten verwiesen wird. Nachdem die Cloudmomentaufnahme gelöscht wurde (sowie jede weitere Momentaufnahme, die auf dieselben Daten verweist), nimmt der Cloudverbrauch ab. Bevor Clouddaten entfernt werden, muss sichergestellt werden, dass keine Momentaufnahmen weiterhin auf diese Daten verweisen. Dieser Prozess wird als _Garbage Collection_ bezeichnet und ist ein Hintergrunddienst, der auf dem Gerät ausgeführt wird. Das Entfernen von Clouddaten erfolgt nicht sofort, weil der Garbage Collection-Dienst vor dem Löschen auf andere Verweise auf diese Daten überprüft. Die Geschwindigkeit der Garbage Collection hängt von der Gesamtzahl der Momentaufnahmen und der Gesamtmenge an Daten ab. Typischerweise werden die Clouddaten in weniger als einer Woche bereinigt.


### <a name="thin-provisioning"></a>Schlanke Speicherzuweisung
Die Bereitstellung nach Bedarf ist eine Virtualisierungstechnologie, bei der der verfügbare Speicher scheinbar die physischen Ressourcen überschreitet. Anstatt ausreichend Speicher im Voraus zu reservieren, verwendet StorSimple die schlanke Bereitstellung, um nur eben genug Speicher zum Erfüllen der aktuellen Anforderungen zuzuweisen. Die Elastizität von Cloudspeicher ermöglicht diesen Ansatz, weil StorSimple den Cloudspeicher vergrößern oder verkleinern kann, um sich ändernde Anforderungen zu erfüllen.

> [!NOTE]
> Lokale Volumes werden nicht mit schlanker Speicherzuweisung bereitgestellt. Der für ein rein lokales Volume reservierte Speicher wird in seiner Gesamtheit bereitgestellt, wenn das Volume erstellt wird.


### <a name="deduplication-and-compression"></a>Deduplizierung und Komprimierung
Microsoft Azure StorSimple arbeitet mit Deduplizierung und Datenkomprimierung, um Speicheranforderungen weiter zu verringern.

Durch Deduplizierung wird die Datenmenge verringert, indem Redundanz im gespeicherten Dataset beseitigt wird. Wenn sich Informationen ändern, ignoriert Azure StorSimple die unveränderten Daten und erfasst nur die Änderungen. Außerdem verringert StorSimple die Menge der gespeicherten Daten, indem nicht erforderliche Informationen bestimmt und entfernt werden. 

> [!NOTE]
> Daten auf lokalen Volumes werden nicht dedupliziert oder komprimiert. Sicherungen lokaler Volumes werden hingegen dedupliziert und komprimiert.


## <a name="storsimple-workload-summary"></a>StorSimple-Workload – Übersicht
In der folgenden Tabelle finden Sie eine Übersicht über die unterstützten StorSimple-Workloads.

| Szenario | Workload | Unterstützt | Beschränkungen | Version |
| --- | --- | --- | --- | --- |
| Zusammenarbeit |Dateifreigabe |Ja | |Alle Versionen |
| Zusammenarbeit |Verteilte Dateifreigabe |Ja | |Alle Versionen |
| Zusammenarbeit |SharePoint |Ja* |Nur mit lokalen Volumes unterstützt |Update 2 und höher |
| Archivierung |Einfache Dateiarchivierung |Ja | |Alle Versionen |
| Virtualisierung |Virtuelle Computer |Ja* |Nur mit lokalen Volumes unterstützt |Update 2 und höher |
| Datenbank |SQL |Ja* |Nur mit lokalen Volumes unterstützt |Update 2 und höher |
| Videoüberwachung |Videoüberwachung |Ja* |Unterstützt, wenn das StorSimple-Gerät nur für diese Workload verwendet wird |Update 2 und höher |
| Backup |Sicherung des Primärziels |Ja* |Unterstützt, wenn das StorSimple-Gerät nur für diese Workload verwendet wird |Update 3 und höher |
| Backup |Sicherung des Sekundärziels |Ja* |Unterstützt, wenn das StorSimple-Gerät nur für diese Workload verwendet wird |Update 3 und höher |

*Ja&#42; – Lösungsrichtlinien und -einschränkungen sollten angewendet werden.*

Die folgenden Workloads werden von Geräten der StorSimple 8000-Serie nicht unterstützt. Die Bereitstellung dieser Workloads auf einem StorSimple-Gerät führt zu einer nicht unterstützten Konfiguration.

* Medizinische Bilddaten
* Exchange
* VDI
* Oracle
* SAP
* Big Data
* Inhaltsverteilung
* Starten von SCSI

Nachfolgend finden Sie eine Liste der von StorSimple unterstützten Infrastrukturkomponenten.

| Szenario | Workload | Unterstützt | Beschränkungen | Version |
| --- | --- | --- | --- | --- |
| Allgemein |ExpressRoute |Ja | |Alle Versionen |
| Allgemein |DataCore FC |Ja* |Unterstützt mit DataCore SANsymphony |Alle Versionen |
| Allgemein |DFSR |Ja* |Nur mit lokalen Volumes unterstützt |Alle Versionen |
| Allgemein |Indizierung |Ja* |Bei mehrstufigen Volumes wird nur die Indizierung der Metadaten unterstützt (nicht der Daten).<br>Bei lokalen Volumes wird eine vollständige Indizierung unterstützt. |Alle Versionen |
| Allgemein |Virenschutz |Ja* |Bei mehrstufigen Volumes wird das Scannen nur beim Öffnen und Schließen unterstützt.<br> Bei lokalen Volumes wird ein vollständiger Scan unterstützt. |Alle Versionen |

*Ja&#42; – Lösungsrichtlinien und -einschränkungen sollten angewendet werden.*

Im Folgenden finden Sie eine Liste von Software, die mit StorSimple zum Erstellen von Lösungen verwendet wird.

| Workloadtyp | Mit StorSimple verwendete Software | Unterstützte Versionen|Link zur Lösungsanleitung| 
| --- | --- | --- | --- |
| Sicherungsziel |Veeam |Veeam v9 und höher |[StorSimple als Sicherungsziel mit Veeam](storsimple-configure-backup-target-veeam.md)|
| Sicherungsziel |Veritas Backup Exec |Backup Exec 16 und höher |[StorSimple als Sicherungsziel mit Backup Exec](storsimple-configure-backup-target-using-backup-exec.md)|
| Sicherungsziel |Veritas NetBackup |NetBackup 7.7.x und höher  |[StorSimple als Sicherungsziel mit NetBackup](storsimple-configure-backuptarget-netbackup.md)|
| Globale Dateifreigabe <br></br> Zusammenarbeit |Talon  |[StorSimple mit Talon](https://www.talonstorage.com/products/archive/fast-deployment-azure-storsimple) | |

## <a name="storsimple-terminology"></a>StorSimple-Terminologie
Vor dem Bereitstellen Ihrer Microsoft Azure StorSimple-Projektmappe, empfehlen wir, sich mit den folgenden Begriffen und Definitionen vertraut zu machen.

### <a name="key-terms-and-definitions"></a>Wichtige Begriffe und Definitionen
| Begriff (Akronym oder Abkürzung) | BESCHREIBUNG |
| --- | --- |
| Zugriffssteuerungsdatensätze (Access Control Record, ACR) |Ein Datensatz, der einem Volume auf dem Microsoft Azure StorSimple-Gerät zugeordnet ist, durch den festgelegt wird, welche Hosts eine Verbindung mit diesem herstellen können. Die Feststellung basiert auf den qualifizierten iSCSI-Namen (IQN) der Hosts (im ACR enthalten), die eine Verbindung mit dem StorSimple-Gerät herstellen. |
| AES-256 |Ein erweiterter 256-Bit-Verschlüsselungsalgorithmus (Advanced Encryption Standard, AES) zum Verschlüsseln von Daten, die aus der und in die Cloud verschoben werden. |
| Größe der Zuordnungseinheiten (Allocation Unit Size, AUS) |Die kleinste Menge an Speicherplatz, die zum Speichern einer Datei in Windows-Dateisystemen zugeordnet werden kann. Ist eine Dateigröße kein entsprechendes Vielfaches der Clustergröße, muss zusätzlicher Speicherplatz zum Speichern der Datei verwendet werden (bis zum nächsten Vielfachen der Clustergröße). Dies führt zu verlorenem Speicherplatz und zur Fragmentierung der Festplatte. <br>Die empfohlene AUS für Azure StorSimple-Volumes beträgt 64 KB, da diese Größe gut mit den Deduplizierungsalgorithmen funktioniert. |
| Automatische Speicherstaffelung |Automatisches Verschieben von weniger aktiven Daten von SSDs auf HDDs und anschließend auf eine Ebene in der Cloud, um den gesamten Speicher über eine zentrale Benutzeroberfläche verwalten zu können. |
| Sicherungskatalog |Eine Auflistung von Backups, in der Regel verknüpft durch den Anwendungstyp, der verwendet wurde. Diese Sammlung wird auf dem Blatt „Sicherungskatalog“ der Benutzeroberfläche des StorSimple-Geräte-Manager-Diensts angezeigt. |
| Sicherungskatalogdatei |Eine Datei mit einer Liste von verfügbaren Momentaufnahmen, die derzeit in der Sicherungsdatenbank von StorSimple Snapshot Manager gespeichert sind. |
| Sicherungsrichtlinie |Eine Auswahl von Volumes, Sicherungstypen und Zeitplänen, mit der Sie Sicherungen nach einem vordefinierten Zeitplan erstellen können. |
| BLOBs (Binary Large Objects) |Eine Sammlung von Binärdaten in einem Datenbankverwaltungssystem, die als eine Einheit gespeichert werden. BLOBs sind in der Regel Bilder, Audiodaten oder andere Multimediaobjekte. Es kann jedoch gelegentlich auch ausführbarer Binärcode in einem BLOB gespeichert werden. |
| CHAP (Challenge Handshake Authentication Protocol) |Ein Protokoll, das zum Authentifizieren des Peers einer Verbindung basierend auf dem Peer verwendet wird, der ein Kennwort oder einen geheimen Schlüssel freigibt. CHAP kann unidirektional oder bidirektional sein. Bei unidirektionalem CHAP authentifiziert das Ziel einen Initiator. Beim bidirektionalen CHAP ist es erforderlich, dass das Ziel den Initiator und der Initiator wiederum das Ziel authentifiziert. |
| Klon |Ein Duplikat eines Volumes. |
| Cloud als Ebene (CaaT, Cloud as a Tier) |Cloudspeicherung, die als Ebene innerhalb der Speicherarchitektur integriert ist, sodass alle Speicher als Teil eines Unternehmensspeichernetzwerks angezeigt werden. |
| Clouddienstanbieter (Cloud Service Provider, CSP) |Ein Anbieter von Cloudcomputingdiensten. |
| Cloudmomentaufnahme |Eine Kopie von in der Cloud gespeicherten Volumedaten zu einem bestimmten Zeitpunkt. Eine Cloudmomentaufnahme entspricht einer Momentaufnahme, die auf einem anderen, externen Speichersystem repliziert wird. Cloudmomentaufnahmen sind besonders nützlich für Notfallwiederherstellungszenarios. |
| Verschlüsselungsschlüssel für Cloudspeicher |Ein Kennwort oder ein Schlüssel, der von Ihrem StorSimple-Gerät verwendet wird, um auf die verschlüsselten Daten zuzugreifen, die vom Gerät in die Cloud gesendet werden. |
| Clusterfähiges Aktualisieren |Verwaltung von Softwareupdates auf Servern in einem Failovercluster, damit die Updates nur minimale oder keine Auswirkungen auf die Dienstverfügbarkeit haben. |
| DataPath |Eine Auflistung funktionaler Einheiten, die miteinander verbundene Datenverarbeitungsvorgänge ausführen. |
| Deaktivieren |Eine dauerhafte Aktion, die die Verbindung zwischen dem StorSimple-Gerät und dem zugeordneten Clouddienst unterbricht. Cloudmomentaufnahmen des Geräts bleiben nach diesem Prozess erhalten und können geklont oder zur Wiederherstellung im Notfall verwendet werden. |
| Datenträgerspiegelung |Replikation von logischen Datenträgern auf separaten Festplatten in Echtzeit, um kontinuierliche Verfügbarkeit sicherzustellen. |
| Dynamische Datenträgerspiegelung |Replikation logischer Datenträgervolumes auf dynamischen Datenträgern. |
| Dynamische Datenträger |Ein Datenträgervolumeformat, das Logical Disk Manager (LDM) zum Speichern und Verwalten von Daten auf mehreren physischen Datenträgern verwendet. Dynamische Datenträger können erweitert werden, um zusätzlichen Speicherplatz bereitzustellen. |
| EBOD-Gehäuse (Extended Bunch of Disks) |Ein sekundäres Gehäuse Ihres Microsoft Azure StorSimple-Geräts, das zusätzliche Festplattenlaufwerke für zusätzlichen Speicher enthält. |
| Normale Speicherzuweisung |Eine klassische Speicherbereitstellung, in der Speicherplatz auf Grundlage von erwarteten Anforderungen zugewiesen wird (in der Regel über dem aktuellen Bedarf). Siehe auch *Schlanke Speicherzuweisung*. |
| Festplattenlaufwerk (HDD) |Ein Laufwerk, das rotierende Scheiben zum Speichern von Daten nutzt. |
| Hybridcloudspeicher |Eine Speicherarchitektur, die lokale und externe Ressourcen, einschließlich Cloudspeicher verwendet. |
| iSCSI (Internet Small Computer System Interface) |Ein Speichernetzwerkstandard auf IP-Grundlage (Internetprotokoll) zum Verknüpfen von Datenspeichergeräten oder -funktionen. |
| iSCSI-Initiator |Eine Softwarekomponente, die einem Hostcomputer unter Windows die Verbindung mit einem externen iSCSI-basierten Speichernetzwerk ermöglicht. |
| IQN (iSCSI Qualified Name) |Ein eindeutiger Name, der ein iSCSI-Ziel oder einen iSCI-Initiator bezeichnet. |
| iSCSI-Ziel |Eine Softwarekomponente, die zentralisierte iSCSI-Datenträgersubsysteme in SANs bereitstellt. |
| Livearchivierung |Ein Speicheransatz für permanenten Zugriff auf Archivdaten (die Daten sind z. B. nicht außerhalb des Standorts auf Band gespeichert). Microsoft Azure StorSimple nutzt Livearchivierung. |
| Lokales Volume |Ein Volume, das sich auf dem Gerät befindet und niemals in die Cloud ausgelagert wird. |
| Lokale Momentaufnahme |Lokale Momentaufnahmen sind Zeitpunktkopien der Volumedaten, die auf dem Microsoft Azure StorSimple-Gerät gespeichert sind. |
| Microsoft Azure StorSimple |Eine leistungsstarke Lösung aus einem Rechenzentrums-Speichergerät und Software, die IT-Organisationen für Cloudspeicher nutzen, der wie Rechenzentrumsspeicher verwendet werden kann. StorSimple vereinfacht den Schutz von Daten und die Datenverwaltung bei gleichzeitigen Kosteneinsparungen. Die Lösung konsolidiert primären Speicher, Archiv, Backup und Notfallwiederherstellung durch nahtlose Integration in die Cloud. Durch die Kombination von SAN-Speicher und Clouddatenverwaltung auf einer Unternehmensplattform ermöglichen StorSimple-Geräte, hohe Geschwindigkeit, einfache Verwendung und Zuverlässigkeit für alle speicherbezogenen Anforderungen. |
| Stromversorgungs- und Kühleinheiten (Power and Cooling Modules, PCMs) |Hardwarekomponenten des StorSimple-Geräts bestehend aus Netzteilen und Lüfter. Das primäre Gehäuse des Geräts verfügt über zwei PCMs mit 764 W Leistung, während das EBOD-Gehäuse zwei PCMs mit 580 W Leistung besitzt. |
| Primäres Gehäuse |Hauptgehäuse des StorSimple-Geräts, das die Controller der Anwendungsplattform enthält. |
| RTO (Recovery Time Objective) |Die maximale Zeit, die die vollständige Wiederherstellung eines Geschäftsprozesses oder des Systems nach einem Notfall dauern sollte. |
| SAS (Serial Attached SCSI) |Ein Festplattenlaufwerkstyp (HDD). |
| Verschlüsselungsschlüssel für Dienstdaten |Ein Schlüssel, der jedem neuen StorSimple-Gerät zur Verfügung gestellt wird, das im StorSimple-Geräte-Manager-Dienst registriert wird. Zwischen dem StorSimple-Geräte-Manager-Dienst und dem Gerät übertragene Konfigurationsdaten werden mit einem öffentlichen Schlüssel verschlüsselt und können anschließend nur auf dem Gerät mit einem privaten Schlüssel entschlüsselt werden. Mit dem Verschlüsselungsschlüssel für Dienstdaten kann der Dienst diesen privaten Schlüssel zur Entschlüsselung abrufen. |
| Dienstregistrierungsschlüssel |Ein Schlüssel, der zur Registrierung des StorSimple-Geräts im StorSimple-Geräte-Manager-Dienst beiträgt, sodass es im Azure-Portal für weitere Verwaltungsvorgänge angezeigt wird. |
| SCSI (Small Computer System Interface) |Ein Standardsatz zur physischen Verbindung von Computern und zur Datenübertragung zwischen diesen. |
| Solid-State-Laufwerk (SSD) |Ein Datenträger, der keine beweglichen Teile enthält, wie z. B. ein Flashlaufwerk. |
| Speicherkonto |Ein Satz von Anmeldeinformationen, die mit Ihrem Speicherkonto bei einem Clouddienstanbieter verknüpft sind. |
| StorSimple-Adapter für SharePoint |Eine Microsoft Azure StorSimple-Komponente, die die Speicher- und Datenschutzfunktionen von StorSimple transparent auf SharePoint-Serverfarmen erweitert. |
| StorSimple-Geräte-Manager-Dienst |Eine Erweiterung des Azure-Portals, die die Verwaltung von lokalen und virtuellen Azure StorSimple-Geräten ermöglicht. |
| StorSimple Snapshot Manager |Ein Microsoft Management Console (MMC)-Snap-In zum Verwalten von Sicherung und Wiederherstellung in Microsoft Azure StorSimple. |
| Sicherung anlegen |Ein Feature, das dem Benutzer ermöglicht, ein interaktives Backup eines Volumes zu erstellen. Dies ist eine alternative Möglichkeit zum Erstellen eines manuellen Backups eines Volumes gegenüber einem automatisierten Backup mit einer definierten Richtlinie. |
| Schlanke Speicherzuweisung |Eine Methode zur Optimierung der Effizienz, mit der der verfügbare Speicherplatz in Speichersystemen genutzt wird. Bei der schlanken Speicherzuweisung wird der Speicher für mehrere Benutzer basierend auf den minimalen Speicherplatzanforderungen jedes Benutzers zu einem beliebigen Zeitpunkt zugeteilt. Siehe auch *Normale Speicherzuweisung*. |
| Staffelung |Anordnen von Daten in logischen Gruppierungen basierend auf aktueller Nutzung, Alter und Beziehung zu anderen Daten. StorSimple ordnet Daten automatisch in Ebenen an. |
| Volume |Logische Speicherbereiche, die in Form von Laufwerken dargestellt werden. StorSimple-Volumes entsprechen den vom Host bereitgestellten Volumes, einschließlich jener Volumes, die durch iSCSI und StorSimple-Geräte erkannt werden. |
| Volumecontainer |Eine Gruppierung von Volumes und den Einstellungen, die für diese gelten. Alle Volumes auf Ihrem StorSimple-Gerät sind in Volumecontainer unterteilt. Die Einstellungen für Volumecontainer umfassen Speicherkonten, Einstellungen für die Verschlüsselung von Daten, die mit zugeordneten Verschlüsselungsschlüsseln in die Cloud gesendet werden, und die für Cloudvorgänge verbrauchte Bandbreite. |
| Volumegruppe |In StorSimple Snapshot Manager ist eine Volumegruppe eine Sammlung von Volumes, die für die Backupverarbeitung konfiguriert sind. |
| Volumeschattenkopie-Dienst (VSS) |Ein Dienst des Windows Server-Betriebssystems, der Anwendungskonsistenz über die Kommunikation mit VSS-fähigen Anwendungen zur Koordination der Erstellung inkrementeller Momentaufnahmen gewährleistet. VSS stellt sicher, dass die Anwendungen vorübergehend inaktiv sind, wenn die Momentaufnahmen erstellt werden. |
| Windows PowerShell für StorSimple |Eine Windows PowerShell-basierte Befehlszeilenschnittstelle zur Verwendung und Verwaltung Ihres StorSimple-Geräts. Diese Schnittstelle verfügt unter Beibehaltung einiger grundlegender Funktionen von Windows PowerShell über zusätzliche dedizierte Cmdlets, die auf die Verwaltung von StorSimple-Geräten zugeschnitten sind. |

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur [StorSimple-Sicherheit](storsimple-8000-security.md).