---
title: Auswählen des richtigen Bereitstellungstyps – Azure Database for MySQL
description: In diesem Artikel wird beschrieben, welche Faktoren vor der Bereitstellung von Azure Database for MySQL als IaaS (Infrastructure-as-a-Service) oder PaaS (Platform-as-a-Service) zu berücksichtigen sind.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: 000de084cf9375347704cc4d3905ca36bdd77ff8
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92926188"
---
# <a name="choose-the-right-mysql-server-option-in-azure"></a>Auswählen der richtigen MySQL Server-Option in Azure

Mit Azure können Ihre MySQL-Serverworkloads auf einer gehosteten IaaS-VM (Infrastructure-as-a-Service) oder als gehostete PaaS-Instanz (Platform-as-a-Service) ausgeführt werden. PaaS stellt mehrere Bereitstellungsoptionen und Dienstebenen für jede Bereitstellungsoption zur Verfügung. Bei der Auswahl zwischen IaaS und PaaS müssen Sie entscheiden, ob Sie Ihre Datenbank verwalten, Patches anwenden und Sicherungen erstellen oder diese Vorgänge an Azure delegieren möchten.

Berücksichtigen Sie bei der Entscheidung die beiden folgenden Optionen:

- **Azure Database for MySQL** : Diese Option ist eine vollständig verwaltete MySQL-Datenbank-Engine, die auf der stabilen Version der MySQL Community Edition basiert. Diese relationale DBaaS-Lösung (Database-as-a-Service), die auf der Azure-Cloudplattform gehostet wird, fällt in die Branchenkategorie PaaS.

  Mit einer verwalteten Instanz von MySQL in Azure können Sie die integrierten Features, d. h. automatisiertes Patchen, Hochverfügbarkeit, automatisierte Sicherungen, elastische Skalierung, Sicherheit auf Unternehmensniveau, Compliance und Governance, Überwachung und Warnungen, nutzen, für die ansonsten eine umfassende Konfiguration erforderlich ist, wenn MySQL Server lokal oder auf einem virtuellen Azure-Computer ausgeführt wird. Wenn Sie mit MySQL als Dienst arbeiten, bezahlen Sie nur für das, was Sie nutzen. Darüber hinaus haben Sie dank Optionen zum Hoch- und Aufskalieren ohne Unterbrechung mehr Kontrolle.
  
  [Azure Database for MySQL](overview.md), ein Dienst, der auf MySQL Community Edition basiert, ist in zwei Bereitstellungsmodi verfügbar:

  - Bei [Azure Database for MySQL Single Server](single-server-overview.md) handelt es sich um einen vollständig verwalteten Datenbankdienst mit minimalen Anforderungen für die Anpassung der Datenbank. Die Single Server-Plattform ist für die Verarbeitung der meisten Datenbankverwaltungsfunktionen (z. B. Patching, Sicherungen, Hochverfügbarkeit und Sicherheit) mit minimaler Benutzerkonfiguration und -steuerung konzipiert. Die Architektur ist für eine Verfügbarkeit von 99,99 Prozent in einer einzelnen Verfügbarkeitszone optimiert. Single Server-Instanzen eignen sich am besten für cloudnative Anwendungen, da sie für das automatisierte Patchen ohne genauen Patchzeitplan und ohne benutzerdefinierte MySQL-Konfigurationseinstellungen konzipiert sind.

  - [Flexible Server (Vorschau)](flexible-server/overview.md) ist ein vollständig verwalteter Datenbankdienst, der eine differenziertere Steuerung und mehr Flexibilität in Bezug auf Datenbank-Verwaltungsfunktionen und -Konfigurationseinstellungen bietet. Im Allgemeinen bietet der Dienst im Vergleich zur Single Server-Bereitstellung basierend auf den Benutzeranforderungen mehr Flexibilität und Möglichkeiten zur Anpassung der Serverkonfigurationen. Mit der flexiblen Serverarchitektur können Benutzer Hochverfügbarkeit in einer einzelnen Verfügbarkeitszone sowie in mehreren Verfügbarkeitszonen auswählen. Flexible Server-Instanzen bieten außerdem bessere Steuerelemente für die Kostenoptimierung mit der Möglichkeit, den Server und burstfähige SKUs zu starten/anzuhalten. Dies eignet sich hervorragend für Workloads, die nicht durchgehend die gesamte Computekapazität benötigen.

  Flexible Server-Instanzen eignen sich am besten für Folgendes:

  - Anwendungsentwicklung, bei der eine bessere Kontrolle und bessere Anpassungsmöglichkeiten für die MySQL-Engine erforderlich sind
  - Zonenredundante Hochverfügbarkeit
  - Verwaltete Wartungsfenster

- **MySQL auf Azure-VMs** : Diese Option fällt in die Branchenkategorie IaaS. Mit diesem Dienst können Sie MySQL Server auf einem verwalteten virtuellen Computer auf der Azure-Cloudplattform ausführen. Alle aktuellen Versionen und Editionen von MySQL können auf diesem virtuellen Computer installiert werden.

## <a name="comparing-the-mysql-deployment-options-in-azure"></a>Vergleich der MySQL-Bereitstellungsoptionen in Azure

Die Hauptunterschiede zwischen diesen Optionen werden in der folgenden Tabelle aufgeführt:

| attribute          | Azure Database for MySQL<br/>Einzelner Server |Azure Database for MySQL<br/>Flexible Server  |MySQL auf Azure-VMs                      |
|:-------------------|:-------------------------------------------|:---------------------------------------------|:---------------------------------------|
| Unterstützte MySQL-Versionen | 5.6, 5.7 & 8.0| 5.7 | Beliebige Version|
| Compute-Skalierung | Unterstützt (die Skalierung vom und zum Tarif Basic wird nicht unterstützt)| Unterstützt | Unterstützt|
| Speichergröße | 5 GiB bis 16 TiB| 5 GiB bis 16 TiB | 32 GiB bis 32.767 GiB|
| Onlinespeicherskalierung | Unterstützt| Unterstützt| Nicht unterstützt|
| Automatische Speicherskalierung | Unterstützt| In der Vorschauversion nicht unterstützt| Nicht unterstützt|
| Netzwerkverbindung | – Öffentliche Endpunkte mit Serverfirewall<br/> – Privater Zugriff mit Private Link-Unterstützung|– Öffentliche Endpunkte mit Serverfirewall<br/> – Privater Zugriff mit Virtual Network-Integration.| – Öffentliche Endpunkte mit Serverfirewall<br/> – Privater Zugriff mit Private Link-Unterstützung|
| Vereinbarung zum Servicelevel (SLA) | SLA mit 99,99 %Verfügbarkeit |Keine SLA in der Vorschauversion| 99,99 % bei Verwendung von Verfügbarkeitszonen|
| Betriebssystempatching| Automatic  | Automatisch mit benutzerdefinierter Wartungsfenstersteuerung | Von Endbenutzern verwaltet |
| MySQL-Patching     | Automatic  | Automatisch mit benutzerdefinierter Wartungsfenstersteuerung | Von Endbenutzern verwaltet |
| Hochverfügbarkeit | Integrierte Hochverfügbarkeit in einer einzelnen Verfügbarkeitszone| Integrierte Hochverfügbarkeit in Verfügbarkeitszonen und verfügbarkeitszonenübergreifend | Benutzerdefinierte Verwaltung, unter anderem mithilfe von Clustering und Replikation|
| Zonenredundanz | Nicht unterstützt | Unterstützt | Unterstützt|
| Hybridszenario | Unterstützt mit [Datenreplikation](./concepts-data-in-replication.md)| Nicht in der Vorschau verfügbar | Von Endbenutzern verwaltet |
| Lesereplikate | Unterstützt (bis zu 5 Replikate)| Unterstützt (bis zu 10 Replikate)| Von Endbenutzern verwaltet |
| Backup | Automatisiert mit Aufbewahrungsdauer von 7 bis 35 Tagen | Automatisiert mit Aufbewahrungsdauer von 1 bis 35 Tagen | Von Endbenutzern verwaltet |
| Überwachen von Datenbankvorgängen | Unterstützt | Unterstützt | Von Endbenutzern verwaltet |
| Notfallwiederherstellung | Unterstützt mit georedundantem Sicherungsspeicher und regionsübergreifenden Lesereplikaten | In der Vorschauversion nicht unterstützt| Benutzerdefinierte Verwaltung mit Replikationstechnologien |
| Statistik zur Abfrageleistung | Unterstützt | Nicht in der Vorschau verfügbar| Von Endbenutzern verwaltet |
| Reservierte Azure-VM-Instanzen (RIs): Preise | Unterstützt | Nicht in der Vorschau verfügbar | Unterstützt |
| Azure AD-Authentifizierung | Unterstützt | Nicht in der Vorschau verfügbar | Nicht unterstützt|
| Verschlüsselung ruhender Daten | Unterstützt mit kundenseitig verwalteten Schlüsseln | Unterstützt mit dienstseitig verwalteten Schlüsseln | Nicht unterstützt|
| SSL/TLS | Standardmäßig aktiviert mit Unterstützung für TLS 1.2, 1.1 und 1.0 | Erzwingung von TLS 1.2 | Unterstützung für TLS 1.2, 1.1 und 1.0 |
| Flottenverwaltung | Unterstützt mit der Azure CLI, PowerShell, REST und dem Azure Resource Manager | Unterstützt mit der Azure CLI, PowerShell, REST und Azure Resource Manager  | Unterstützt für VMs mit der Azure CLI, PowerShell, REST und Azure Resource Manager |

## <a name="business-motivations-for-choosing-paas-or-iaas"></a>Geschäftsmotive für die Entscheidung für PaaS oder IaaS

Es gibt verschiedene Faktoren, die Ihre Entscheidung zwischen PaaS und IaaS zum Hosten Ihrer MySQL-Datenbanken beeinflussen.

### <a name="cost"></a>Kosten

Die Einsparung von Kosten ist oft der wichtigste Aspekt, der darüber entscheidet, was die beste Lösung für das Hosten Ihrer Datenbanken ist. Dies gilt unabhängig davon, ob Sie ein Startup-Unternehmen mit wenig liquiden Mitteln oder ein Team in einem etablierten Unternehmen sind, das unter engen Budgetbeschränkungen arbeitet. In diesem Abschnitt werden die Grundlagen zur Abrechnung und Lizenzierung in Azure in Bezug auf Azure Database for MySQL und MySQL auf Azure-VMs beschrieben.

#### <a name="billing"></a>Abrechnung

Azure Database for MySQL ist derzeit als Dienst in verschiedenen Tarifen mit unterschiedlichen Preisen für Ressourcen verfügbar. Alle Ressourcen werden auf Stundenbasis mit einer festen Gebühr abgerechnet. Die neuesten Informationen zu aktuell unterstützten Dienstebenen, Computegrößen und Speichermengen finden Sie auf der [Preisübersichtsseite](https://azure.microsoft.com/pricing/details/mysql/). Sie können Dienstebenen und Computegrößen entsprechend den unterschiedlichen Durchsatzanforderungen Ihrer Anwendung dynamisch anpassen. Ihnen wird der ausgehende Internetdatenverkehr basierend auf den üblichen [Datenübertragungsraten](https://azure.microsoft.com/pricing/details/data-transfers/) berechnet.

Mit Azure Database for MySQL wird die Datenbanksoftware automatisch von Microsoft konfiguriert, gepatcht und aktualisiert. Diese automatisierten Aktionen senken Ihre Verwaltungskosten. Außerdem bietet Azure Database for MySQL [automatisierte Sicherungen](./concepts-backup.md). Mithilfe dieser Funktionen können Sie erhebliche Kosteneinsparungen erzielen, vor allem bei einer großen Anzahl von Datenbanken. Im Gegensatz dazu können Sie mit MySQL auf Azure-VMs eine beliebige MySQL-Version auswählen und ausführen. Unabhängig davon, welche MySQL-Version Sie verwenden, zahlen Sie für Folgendes: die bereitgestellte VM, die Speicherkosten für die Daten, den Speicher für die Sicherungen, die Überwachungsdaten und die Protokolle sowie die Kosten für den jeweils verwendeten MySQL-Lizenztyp (sofern vorhanden).

Azure Database for MySQL bietet integrierte Hochverfügbarkeit für jede Art von Unterbrechung auf Knotenebene und bietet gleichzeitig eine SLA-Garantie von 99,99 % für den Dienst. Für Hochverfügbarkeit von Datenbanken auf VMs sollten Sie jedoch Hochverfügbarkeitsoptionen wie die [MySQL-Replikation](https://dev.mysql.com/doc/refman/8.0/en/replication.html) verwenden, die für MySQL-Datenbanken zur Verfügung stehen. Durch Verwendung einer unterstützten Hochverfügbarkeitsoption erhalten Sie keine zusätzliche SLA. Allerdings können Sie mit zusätzlichen Kosten und höherem Verwaltungsaufwand eine Datenbankverfügbarkeit von mehr als 99,99 % erzielen.

Weitere Informationen zu Preisen finden Sie in den folgenden Artikeln:

- [Azure Database for MySQL: Preise](https://azure.microsoft.com/pricing/details/mysql/)
- [Preise für virtuelle Computer](https://azure.microsoft.com/pricing/details/virtual-machines/)
- [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Verwaltung

Bei vielen Unternehmen geht es bei der Entscheidung für den Wechsel zu einem Clouddienst ebenso um eine Vereinfachung der Verwaltung wie um die Kosten.

Bei IaaS übernimmt Microsoft folgende Aufgaben:

- Verwaltet die zugrunde liegende Infrastruktur
- Automatisiertes Patchen der zugrunde liegenden Hardware und der Betriebssysteme
  
Bei PaaS übernimmt Microsoft folgende Aufgaben:

- Verwaltet die zugrunde liegende Infrastruktur
- Automatisiertes Patchen der zugrunde liegenden Hardware, der Betriebssysteme und des Datenbankmoduls
- Verwaltung der Hochverfügbarkeit der Datenbank
- Automatische Erstellung von Sicherungen und Replikation aller Daten, um eine Notfallwiederherstellung zu ermöglichen
- Verschlüsselung von ruhenden Daten und Daten in Bewegung als Standard
- Überwachung Ihres Servers und Bereitstellung von Features für Statistiken zur Abfrageleistung und Leistungsempfehlungen

In der folgenden Liste sind Verwaltungsaspekte der einzelnen Optionen beschrieben:

- Mit Azure Database for MySQL können Sie Ihre Datenbank weiterhin verwalten. Es ist aber keine Verwaltung von Datenbank-Engine, Betriebssystem oder Hardware mehr erforderlich. Hier sind einige Beispiele für Elemente angegeben, die Sie weiterhin verwalten können:

  - Datenbanken
  - Anmeldung
  - Indexoptimierung
  - Abfrageoptimierung
  - Überwachung
  - Sicherheit

  Darüber hinaus erfordert die Konfiguration der Hochverfügbarkeit für ein anderes Rechenzentrum nur noch einen minimalen bzw. gar keinen Konfigurations- oder Verwaltungsaufwand mehr.

- Mit MySQL auf virtuellen Azure-Computern haben Sie die uneingeschränkte Kontrolle über das Betriebssystem und die Konfiguration der MySQL-Serverinstanzen. Bei einer VM entscheiden Sie, wann ein Update oder Upgrade des Betriebssystems und der Datenbanksoftware durchgeführt werden soll und welche Patches angewendet werden sollen. Außerdem entscheiden Sie, wann zusätzliche Software, z. B. eine Antivirenanwendung, installiert werden soll. Durch einige bereitgestellte Automatisierungsfeatures lassen sich Patching, Sicherungen und Hochverfügbarkeit erheblich vereinfachen. Sie können die Größe der VM, die Anzahl von Datenträgern sowie deren Speicherkonfigurationen steuern. Weitere Informationen finden Sie unter [Größen virtueller Computer und Clouddienste für Azure](../virtual-machines/sizes.md).

### <a name="time-to-move-to-azure"></a>Zeit für einen Wechsel nach Azure

- Azure Database for MySQL ist die geeignete Lösung für cloudbasierte Anwendungen, wenn Entwicklerproduktivität und eine schnelle Markteinführung für neue Lösungen entscheidend sind. Aufgrund von programmgesteuerten Funktionen wie DBA ist der Dienst für Cloudarchitekten und Entwickler geeignet, da er den Verwaltungsaufwand für das zugrunde liegende Betriebssystem und die Datenbank verringert.

- Wenn Sie die Zeit und Kosten für den Erwerb neuer lokaler Hardware sparen möchten, ist MySQL auf Azure-VMs die richtige Lösung für Anwendungen, die eine differenzierte Steuerung und Anpassung der MySQL-Engine erfordern, die vom Dienst nicht unterstützt wird oder Zugriff auf das zugrunde liegende Betriebssystem erfordert. Diese Lösung eignet sich auch in Fällen, in denen Azure Database for MySQL nicht geeignet ist, für die Migration vorhandener lokaler Anwendungen und Datenbanken in unverändertem Zustand zu Azure.

Da keine Änderungen an der Darstellungs-, Anwendungs- und Datenschicht erforderlich sind, sparen Sie sich die Zeit und das Geld, die sonst für den Neuentwurf der vorhandenen Lösung nötig wären. Sie können sich stattdessen auf die Migration Ihrer gesamten Lösungen zu Azure konzentrieren und müssen lediglich einige Leistungsoptimierungen durchführen, die für die Azure-Plattform unter Umständen erforderlich sind.

## <a name="next-steps"></a>Nächste Schritte

- [Azure Database for MySQL: Preise](https://azure.microsoft.com/pricing/details/MySQL/)
- [Erstellen Sie Ihren ersten Server.](./quickstart-create-mysql-server-database-using-azure-portal.md)
