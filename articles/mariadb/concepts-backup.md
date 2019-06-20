---
title: Sichern und Wiederherstellen in Azure Database for MariaDB
description: Erfahren Sie mehr über das automatische Sichern und Wiederherstellen Ihres Azure Database for MariaDB-Servers.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: d6141c3184c8915c36f22d010db39aef2460dd1c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60483050"
---
# <a name="backup-and-restore-in-azure-database-for-mariadb"></a>Sichern und Wiederherstellen in Azure Database for MariaDB

Azure Database for MariaDB erstellt automatisch Sicherungen und speichert diese in einem lokal redundanten oder georedundanten Speicher, der von einem Benutzer konfiguriert wurde. Sicherungen können verwendet werden, um für Ihren Server den Stand zu einem bestimmten Zeitpunkt wiederherzustellen. Sicherungen und Wiederherstellungen sind wesentliche Bestandteile jeder Strategie für Geschäftskontinuität, da Ihre Daten so vor versehentlichen Beschädigungen und Löschungen geschützt werden.

## <a name="backups"></a>Backups

Azure Database for MariaDB erstellt vollständige, differenzielle und Transaktionsprotokollsicherungen. Dank dieser Sicherungen können Sie für einen Server den Stand zu einem beliebigen Zeitpunkt wiederherstellen, der innerhalb Ihres konfigurierten Aufbewahrungszeitraums für Sicherungen liegt. Die Standardaufbewahrungsdauer für Sicherungen beträgt sieben Tage. Optional können Sie einen Zeitraum von bis zu 35 Tagen festlegen. Zur Verschlüsselung aller Sicherungen wird die AES-Verschlüsselung mit 256 Bit verwendet.

### <a name="backup-frequency"></a>Sicherungshäufigkeit

Normalerweise werden vollständige Sicherungen einmal pro Woche, differenzielle Sicherungen zweimal pro Tag und Transaktionsprotokollsicherungen alle fünf Minuten durchgeführt. Die erste vollständige Sicherung wird unmittelbar nach der Erstellung des Servers eingeplant. Für einen großen wiederhergestellten Server kann die erste Sicherung länger dauern. Der früheste Zeitpunkt, der für einen neuen Server wiederhergestellt werden kann, ist der Zeitpunkt, zu dem die erste vollständige Sicherung erstellt wurde.

### <a name="backup-redundancy-options"></a>Optionen für Sicherungsredundanz

Mit Azure Database for MariaDB können Sie in den Tarifen „Allgemein“ und „Arbeitsspeicheroptimiert“ flexibel zwischen lokal redundantem und georedundantem Sicherungsspeicher wählen. Wenn die Sicherungen in einem georedundanten Sicherungsspeicher gespeichert werden, werden sie nicht nur in der Region vorgehalten, in der Ihr Server gehostet wird. Sie werden außerdem in einem [gekoppelten Datencenter](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) repliziert. Dies erhöht den Schutz und ermöglicht in einem Notfall die Wiederherstellung Ihres Servers in einer anderen Region. Im Tarif „Basic“ ist nur lokal redundanter Sicherungsspeicher verfügbar.

> [!IMPORTANT]
> Das Konfigurieren von lokal redundantem oder georedundantem Speicher für die Sicherung ist nur während der Erstellung des Servers zulässig. Nachdem der Server bereitgestellt wurde, können Sie die Option für die Sicherungsspeicherredundanz nicht mehr ändern.

### <a name="backup-storage-cost"></a>Kosten für Sicherungsspeicher

Azure Database for MariaDB stellt bis zu 100 % Ihres bereitgestellten Serverspeichers ohne zusätzliche Kosten als Sicherungsspeicher zur Verfügung. Dies ist normalerweise für die Aufbewahrung von Sicherungen über einen Zeitraum von sieben Tagen geeignet. Wenn zusätzlicher Sicherungsspeicher verwendet wird, wird dies in GB pro Monat berechnet.

Beispiel: Wenn Sie einen Server mit 250 GB bereitgestellt haben, verfügen Sie ohne zusätzliche Kosten über 250 GB an Sicherungsspeicher. Für Speicher, der über 250 GB hinausgeht, werden Kosten berechnet.

Weitere Informationen zu den Preisen für Sicherungsspeicher finden Sie in der [Preisübersicht für Azure Database for MariaDB](https://azure.microsoft.com/pricing/details/mariadb/).

## <a name="restore"></a>Restore

Wenn in Azure Database for MariaDB eine Wiederherstellung ausgeführt wird, wird aus den Sicherungen des ursprünglichen Servers ein neuer Server erstellt.

Es gibt zwei Arten der Wiederherstellung:

- Die **Point-in-Time-Wiederherstellung** ist für beide Sicherungsredundanzoptionen verfügbar. Es wird ein neuer Server in derselben Region wie der ursprüngliche Server erstellt.
- Die **Geowiederherstellung** ist nur verfügbar, wenn Sie Ihren Server für georedundanten Speicher konfiguriert haben. Hierbei können Sie den Server in einer anderen Region wiederherstellen.

Die geschätzte Wiederherstellungszeit hängt von verschiedenen Faktoren ab, z.B. der Datenbankgröße, Transaktionsprotokollgröße und Netzwerkbandbreite sowie der Gesamtzahl von Datenbanken, die gleichzeitig in derselben Region wiederhergestellt werden müssen. Die Wiederherstellungszeit beträgt für gewöhnlich weniger als 12 Stunden.

> [!IMPORTANT]
> Gelöschte Server **können nicht** wiederhergestellt werden. Wenn Sie den Server löschen, werden alle zum Server gehörigen Datenbanken ebenfalls gelöscht und können nicht wiederhergestellt werden. Um Serverressourcen nach der Bereitstellung vor versehentlichem Löschen oder unerwarteten Änderungen zu schützen, können Administratoren [Verwaltungssperren](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) nutzen.

### <a name="point-in-time-restore"></a>Point-in-Time-Wiederherstellung

Unabhängig von Ihrer gewählten Option für die Sicherungsredundanz können Sie eine Wiederherstellung für einen beliebigen Zeitpunkt durchführen, der innerhalb Ihres Aufbewahrungszeitraums für Sicherungen liegt. Ein neuer Server wird in derselben Azure-Region wie der ursprüngliche Server erstellt. Bei der Erstellung werden die Konfiguration für den Tarif, die Computegeneration, die Anzahl von V-Kernen, die Speichergröße, den Aufbewahrungszeitraum für Sicherungen und die Option für Sicherungsredundanz des ursprünglichen Servers verwendet.

Die Point-in-Time-Wiederherstellung ist für viele Szenarien hilfreich. Beispiele hierfür sind Fälle, in denen ein Benutzer versehentlich Daten löscht oder eine wichtige Tabelle oder Datenbank entfernt oder in denen eine Anwendung aufgrund eines Defekts fälschlicherweise Daten durch fehlerhafte Daten überschreibt.

Unter Umständen müssen Sie warten, bis die nächste Transaktionsprotokollsicherung erstellt wird, bevor Sie die Wiederherstellung für einen Zeitpunkt innerhalb der letzten fünf Minuten durchführen können.

### <a name="geo-restore"></a>Geografische Wiederherstellung

Sie können einen Server in einer anderen Azure-Region wiederherstellen, in der der Dienst verfügbar ist, wenn Sie Ihren Server für georedundante Sicherungen konfiguriert haben. Die Geowiederherstellung ist die Standardoption für die Wiederherstellung, wenn Ihr Server aufgrund eines Incidents in der Region, in der der Server gehostet wird, nicht verfügbar ist. Wenn Ihre Datenbankanwendung wegen eines umfangreichen Incidents in einer Region nicht mehr verfügbar ist, können Sie einen Server aus den georedundanten Sicherungen auf einem Server in einer beliebigen anderen Region wiederherstellen. Zwischen der Erstellung einer Sicherung und der Replikation in einer anderen Region kommt es zu einer Verzögerung. Diese Verzögerung kann bis zu einer Stunde betragen. Folglich kann bei einem Notfall ein Datenverlust von bis zu einer Stunde auftreten.

Während der Geowiederherstellung können folgende Serverkonfigurationen geändert werden: Computegeneration, virtueller Kern, Aufbewahrungszeitraum für die Sicherung und Sicherungsredundanzoptionen. Allerdings wird während der Geowiederherstellung nicht das Ändern des Tarifs („Basic“, „Allgemein“ oder „Arbeitsspeicheroptimiert“) oder der Speichergröße unterstützt.

### <a name="perform-post-restore-tasks"></a>Durchführen der Aufgaben nach der Wiederherstellung

Nach beiden Wiederherstellungsverfahren sollten Sie die folgenden Aufgaben durchführen, um Ihre Benutzer und Anwendungen wieder in den betriebsbereiten Zustand zu versetzen:

- Umleiten von Clients und Clientanwendungen an den neuen Server, wenn der neue Server den ursprünglichen Server ersetzen soll
- Sicherstellen, dass geeignete Firewallregeln auf Serverebene vorhanden sind, damit Benutzer eine Verbindung herstellen können
- Sicherstellen, dass geeignete Anmeldungen und Berechtigungen auf Datenbankebene vorhanden sind
- Konfigurieren der erforderlichen Warnungen

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur Geschäftskontinuität finden Sie in der  [Übersicht über die Geschäftskontinuität](concepts-business-continuity.md).
- Informationen zur Wiederherstellung des Zustands zu einem bestimmten Zeitpunkt über das Azure-Portal finden Sie unter  [Sichern und Wiederherstellen eines Servers in Azure Database for MySQL mit dem Azure-Portal](howto-restore-server-portal.md).
 
<!--
- To restore to a point in time using Azure CLI, see [restore database to a point in time using CLI](howto-restore-server-cli.md).-->
