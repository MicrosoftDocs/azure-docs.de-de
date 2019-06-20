---
title: Schützen Ihres Azure SQL-Diensts und Ihrer Daten in Azure Security Center | Microsoft Docs
description: Dieses Dokument behandelt Empfehlungen in Azure Security Center, die zum Schutz Ihrer Daten und des Azure SQL-Diensts sowie zur Einhaltung von Sicherheitsrichtlinien beitragen.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/27/2019
ms.author: monhaber
ms.openlocfilehash: bbba5f380fddb4fdec43a7414e59778135c4e0ef
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428297"
---
# <a name="protecting-azure-sql-service-and-data-in-azure-security-center"></a>Schützen des Azure SQL-Diensts und Ihrer Daten in Azure Security Center
Azure Security Center analysiert den Sicherheitsstatus Ihrer Azure-Ressourcen. Werden potenzielle Sicherheitslücken erkannt, erstellt Security Center Empfehlungen, die Sie beim Konfigurieren der erforderlichen Steuerelemente unterstützen.  Die Empfehlungen gelten für folgende Azure-Ressourcentypen: virtuelle Computer (Virtual Machines, VMs), Netzwerk, SQL, Daten und Anwendungen.


### <a name="monitor-data-security"></a>Überwachen der Datensicherheit

Wenn Sie im Abschnitt **Prävention** auf **Datensicherheit** klicken, wird **Datenressourcen** mit Empfehlungen für SQL und Speicher geöffnet. Außerdem werden [Empfehlungen](security-center-sql-service-recommendations.md) zum allgemeinen Integritätsstatus der Datenbank abgegeben. Weitere Informationen zur Speicherverschlüsselung finden Sie unter [Enable encryption for Azure storage account in Azure Security Center](security-center-enable-encryption-for-storage-account.md) (Aktivieren der Verschlüsselung für Azure-Speicherkonten in Azure Security Center).

![Datenressourcen](./media/security-center-monitoring/security-center-monitoring-fig13-newUI-2017.png)

Sie können unter **SQL Recommendations** (SQL-Empfehlungen) auf eine Empfehlung klicken und ausführlichere Informationen zu weiteren Aktionen erhalten, um ein Problem zu beheben. Das folgende Beispiel zeigt die Erweiterung der Empfehlung **Database Auditing & Threat detection on SQL databases** (Datenbanküberwachung und Bedrohungserkennung bei SQL-Datenbanken).

![Details zu einer SQL-Empfehlung](./media/security-center-monitoring/security-center-monitoring-fig14-ga-new.png)

Unter **Überwachung und Bedrohungserkennung für SQL-Datenbanken aktivieren** werden folgende Informationen angezeigt:

* Liste der SQL-Datenbanken
* Server, auf dem sich eine Datenbank befindet
* Informationen dazu, ob die Einstellung vom Server geerbt wurde oder in der Datenbank eindeutig ist
* Aktueller Status
* Schweregrad des Problems

Wenn Sie auf die Datenbank klicken, um die Empfehlung zu implementieren, erscheint **Überwachung und Bedrohungserkennung**, wie im folgenden Screenshot zu sehen:

![Überwachung und Bedrohungserkennung](./media/security-center-monitoring/security-center-monitoring-fig15-ga.png)

Um die Überwachung zu aktivieren, wählen Sie für **Überwachung** die Option **EIN** aus.

## <a name="data-and-storage-recommendations"></a>Empfehlungen für Daten und Speicher

|Ressourcentyp|Sicherheitsbewertung|Empfehlung|BESCHREIBUNG|
|----|----|----|----|
|Speicherkonto|20|Sichere Übertragung in Speicherkonten sollte aktiviert werden.|Sichere Übertragung ist eine Option, die erzwingt, dass Ihr Storage-Konto nur Anforderungen von sicheren Verbindungen (HTTPS) akzeptiert. Durch die Verwendung von HTTPS wird eine Authentifizierung zwischen dem Server und dem Dienst sichergestellt, und die übertragenen Daten werden vor Angriffen auf Netzwerkebene geschützt, wie z.B. Man-in-the-Middle-Angriffe, Abhörangriffe und Session Hijacking.|
|Redis|20|Nur sichere Verbindungen mit Ihrer Redis Cache-Instanz sollten aktiviert werden|Aktivieren Sie nur Verbindungen über SSL mit Azure Cache for Redis. Durch die Verwendung sicheren Verbindungen wird eine Authentifizierung zwischen dem Server und dem Dienst sichergestellt, und die übertragenen Daten werden vor Angriffen auf Netzwerkebene geschützt, wie z.B. Man-in-the-Middle-Angriffe, Abhörangriffe und Session Hijacking.|
|SQL|15|Transparent Data Encryption für SQL-Datenbanken sollte aktiviert werden.|Aktivieren Sie die transparente Datenverschlüsselung, um ruhende Daten zu schützen und Konformitätsanforderungen zu erfüllen.|
|SQL|15|SQL Server-Überwachung sollte aktiviert werden.|Aktivieren Sie die Überwachung für Azure SQL-Server. (Nur Azure SQL-Dienst. Schließt nicht die Ausführung von SQL auf Ihren virtuellen Computern mit ein.)|
|Data Lake Analytics|5|Diagnoseprotokolle in Data Lake Analytics sollten aktiviert werden.|Aktivieren Sie Protokolle, und bewahren Sie sie bis zu ein Jahr lang auf. Auf diese Weise können Sie vergangene Aktivitäten nachvollziehen, wenn Sie Sicherheitsincidents untersuchen oder Ihr Netzwerk gefährdet ist. |
|Data Lake Store|5|Diagnoseprotokolle in Azure Data Lake Store sollten aktiviert werden.|Aktivieren Sie Protokolle, und bewahren Sie sie bis zu ein Jahr lang auf. Auf diese Weise können Sie vergangene Aktivitäten nachvollziehen, wenn Sie Sicherheitsincidents untersuchen oder Ihr Netzwerk gefährdet ist. |
|SQL|30|Sicherheitsrisiken in SQL-Datenbanken sollten beseitigt werden.|Die SQL-Sicherheitsrisikobewertung überprüft Ihre Datenbank auf Sicherheitsrisiken und zeigt Abweichungen von bewährten Methoden wie z. B. Fehlkonfigurationen, übermäßige Berechtigungen und ungeschützte vertrauliche Daten an. Durch das Beseitigen der Sicherheitsrisiken kann die Sicherheit Ihrer Datenbank deutlich verbessert werden.|
|SQL|20|Azure AD-Administrator für SQL Server bereitstellen|Stellen Sie einen Azure AD-Administrator für Ihren SQL-Server bereit, um Azure AD-Authentifizierung zu aktivieren. Die Azure AD-Authentifizierung ermöglicht eine vereinfachte Verwaltung von Berechtigungen und eine zentralisierte Identitätsverwaltung von Datenbankbenutzern und anderen Microsoft-Diensten.|
|Speicherkonto|15|Der Zugriff auf Speicherkonten mit Konfiguration für Firewall und virtuelle Netzwerke sollte eingeschränkt werden.|Überwachen Sie uneingeschränkten Netzwerkzugriff in den Firewalleinstellungen Ihres Speicherkontos. Konfigurieren Sie stattdessen Netzwerkregeln, sodass nur Anwendungen aus zulässigen Netzwerken auf ein Speicherkonto zugreifen können. Um Verbindungen von bestimmten Internetclients oder lokalen Clients zuzulassen, können Sie Zugriff für Datenverkehr aus bestimmten virtuellen Azure-Netzwerken oder an IP-Adressbereiche im öffentlichen Internet gewähren.|
|Speicherkonto|1|Speicherkonten sollten zu neuen Azure Resource Manager-Ressourcen migriert werden.|Verwenden Sie den neuen Azure Resource Manager v2 für Ihre Speicherkonten, um von den folgenden Sicherheitsverbesserungen zu profitieren: strengere Zugriffssteuerung (RBAC), bessere Überwachung, Resource Manager-basierte Bereitstellung und Governance, Zugriff auf verwaltete Identitäten, Zugriff auf Schlüsseltresore für Geheimnisse, Azure AD-basierte Authentifizierung sowie Unterstützung für Markierungen und Ressourcengruppen für eine einfachere Sicherheitsverwaltung.|

## <a name="see-also"></a>Weitere Informationen
Weitere Informationen zu Empfehlungen für andere Arten von Azure-Ressourcen finden Sie in den folgenden Themen:

* [Schützen Ihrer virtuellen Computer in Azure Security Center](security-center-virtual-machine-recommendations.md)
* [Schützen Ihrer Anwendungen in Azure Security Center](security-center-application-recommendations.md)
* [Schützen Ihres Netzwerks in Azure Security Center](security-center-network-recommendations.md)

Weitere Informationen zu Security Center finden Sie in den folgenden Quellen:

* [Festlegen von Sicherheitsrichtlinien in Azure Security Center:](tutorial-security-policy.md) Erfahren Sie, wie Sie Sicherheitsrichtlinien für Ihre Azure-Abonnements und -Ressourcengruppen konfigurieren.
* [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center:](security-center-managing-and-responding-alerts.md) Erfahren Sie, wie Sie Sicherheitswarnungen verwalten und darauf reagieren.
* [Azure Security Center – Häufig gestellte Fragen](security-center-faq.md) : Hier finden Sie häufig gestellte Fragen zur Verwendung des Diensts.
