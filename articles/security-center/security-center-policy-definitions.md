---
title: In Azure Security Center überwachte Azure Policy-Definitionen | Microsoft-Dokumentation
description: In Azure Security Center überwachte Azure Policy-Definitionen
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: c89cb1aa-74e8-4ed1-980a-02a7a25c1a2f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/19/2019
ms.author: monhaber
ms.openlocfilehash: 25ed9cb624474d5da56d385f4e9c155918ec8eab
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428335"
---
# <a name="azure-security-policies-monitored-by-security-center"></a>In Security Center überwachte Azure-Sicherheitsrichtlinien
Dieser Artikel enthält eine Liste mit Azure Policy-Definitionen, die Sie in Azure Security Center überwachen können. Weitere Informationen zu Sicherheitsrichtlinien finden Sie unter [Arbeiten mit Sicherheitsrichtlinien](tutorial-security-policy.md).

## <a name="available-security-policies"></a>Verfügbare Sicherheitsrichtlinien

Informationen zu den integrierten Richtlinien, die von Security Center überwacht werden, finden Sie in der folgenden Tabelle:

| Richtlinie | Zweck der Richtlinie |
| --- | --- |
|Diagnoseprotokolle in Virtual Machine Scale Sets sollten aktiviert werden.|Wir empfehlen Ihnen, Protokolle zu aktivieren, damit nach einem Incident oder einer Kompromittierung die Aktivitäten im Rahmen einer Untersuchung nachvollzogen werden können.|
|Alle Autorisierungsregeln außer RootManageSharedAccessKey sollten aus dem Event Hub-Namespace entfernt werden.|Azure Event Hubs-Clients dürfen keine Zugriffsrichtlinie auf Namespace-Ebene verwenden, die Zugriff auf alle Warteschlangen und Themen in einem Namespace gewährt. Um die Anforderungen des Sicherheitsmodells der geringsten Rechte zu erfüllen, müssen Sie Zugriffsrichtlinien auf Entitätsebene erstellen, damit nur der jeweiligen Entität Zugriff auf Warteschlangen und Themen gewährt wird.|
|Autorisierungsregeln für die Event Hub-Entität sollten definiert werden.|Hiermit wird das Vorhandensein von Autorisierungsregeln für Event Hubs-Entitäten überwacht, um Zugriff mit den geringsten Rechten zu gewähren.|
|Der Zugriff auf Speicherkonten mit Konfiguration für Firewall und virtuelle Netzwerke sollte eingeschränkt werden.|Überwachen Sie uneingeschränkten Netzwerkzugriff in den Firewalleinstellungen Ihres Speicherkontos. Konfigurieren Sie die Netzwerkregeln so, dass nur Anwendungen aus zulässigen Netzwerken auf ein Speicherkonto zugreifen können. Um Verbindungen von bestimmten Internetclients oder lokalen Clients zuzulassen, können Sie Zugriff für Datenverkehr aus bestimmten virtuellen Azure-Netzwerken oder an IP-Adressbereiche im öffentlichen Internet gewähren.|
|Verwendung benutzerdefinierter RBAC-Regeln überwachen|Hiermit werden integrierte Rollen (z. B. „Benutzer“, „Mitwirkender“ und „Leser“) anstelle von benutzerdefinierten RBAC-Rollen (rollenbasierte Zugriffssteuerung) überwacht, da diese fehleranfällig sind. Die Verwendung benutzerdefinierter Rollen wird als Ausnahme betrachtet und erfordert eine strenge Überprüfung und Bedrohungsmodellierung.|
|Diagnoseprotokolle in Azure Stream Analytics sollten aktiviert werden.|Hierbei wird die Aktivierung von Protokollen überwacht, die dann bis zu einem Jahr lang aufbewahrt werden. Auf diese Weise können im Rahmen von Untersuchungen die Aktivitäten nachvollzogen werden, wenn ein Sicherheitsincident eintritt oder Ihr Netzwerk kompromittiert wird.|
|Sichere Übertragung in Speicherkonten sollte aktiviert werden.|Hiermit werden die Anforderungen sicherer Übertragungen in Ihr Speicherkonto überwacht. Sichere Übertragung ist eine Option, die erzwingt, dass Ihr Storage-Konto nur Anforderungen von sicheren Verbindungen (HTTPS) akzeptiert. Durch die Verwendung von HTTPS wird die Authentifizierung zwischen dem Server und dem Dienst sichergestellt. Darüber hinaus werden Daten während der Übertragung vor Angriffen auf Netzwerkebene geschützt, z. B. Man-in-the-Middle-Angriffe, Abhörangriffe und Session Hijacking.|
|Azure AD-Administrator für SQL Server sollte bereitgestellt werden.|Hiermit wird die Bereitstellung eines Azure AD-Administrators (Azure Active Directory) für die SQL Server-Instanz überwacht, um die Azure AD-Authentifizierung zu aktivieren. Die Azure AD-Authentifizierung unterstützt die vereinfachte Verwaltung von Berechtigungen und eine zentralisierte Identitätsverwaltung von Datenbankbenutzern und anderen Microsoft-Diensten.|
|Alle Autorisierungsregeln außer RootManageSharedAccessKey sollten aus dem Service Bus-Namespace entfernt werden.|Azure Service Bus-Clients dürfen keine Zugriffsrichtlinie auf Namespace-Ebene verwenden, die Zugriff auf alle Warteschlangen und Themen in einem Namespace bereitstellt. Um die Anforderungen des Sicherheitsmodells der geringsten Rechte zu erfüllen, erstellen Sie Zugriffsrichtlinien auf Entitätsebene, damit nur der jeweiligen Entität Zugriff auf Warteschlangen und Themen gewährt wird.|
|Diagnoseprotokolle in Service Bus sollten aktiviert sein.|Überwachen Sie die Aktivierung von Protokollen, und bewahren Sie sie bis zu einem Jahr lang auf. Auf diese Weise können im Rahmen von Untersuchungen die Aktivitäten nachvollzogen werden, wenn ein Sicherheitsincident eintritt oder Ihr Netzwerk kompromittiert wird.|
|Die ClusterProtectionLevel-Eigenschaft in Service Fabric sollte auf „EncryptAndSign“ festgelegt werden.|Service Fabric bietet drei Schutzebenen für die Kommunikation zwischen zwei Knoten, bei der ein primäres Clusterzertifikat verwendet wird: „None“, „Sign“ und „EncryptAndSign“. Legen Sie die Schutzebene fest, um sicherzustellen, dass alle zwischen zwei Knoten übertragenen Nachrichten verschlüsselt und digital signiert werden.|
|Die Clientauthentifizierung sollte Azure Active Directory verwenden.|Hierbei wird die Verwendung der Clientauthentifizierung nur über Azure AD in Service Fabric überwacht.|
|Diagnoseprotokolle in Search-Diensten sollten aktiviert sein.|Hierbei wird die Aktivierung von Protokollen überwacht, die dann bis zu einem Jahr lang aufbewahrt werden. Auf diese Weise können im Rahmen von Untersuchungen die Aktivitäten nachvollzogen werden, wenn ein Sicherheitsincident eintritt oder Ihr Netzwerk kompromittiert wird.|
|Nur sichere Verbindungen mit Ihrer Redis Cache-Instanz sollten aktiviert werden|Hierbei wird die ausschließliche Aktivierung von Verbindungen über SSL mit Azure Cache for Redis überwacht. Durch die Verwendung von sicheren Verbindungen wird die Authentifizierung zwischen dem Server und dem Dienst sichergestellt. Darüber hinaus werden Daten während der Übertragung vor Angriffen auf Netzwerkebene geschützt, z. B. Man-in-the-Middle-Angriffe, Abhörangriffe und Session Hijacking.|
|Diagnoseprotokolle in Logic Apps sollten aktiviert sein.|Hierbei wird die Aktivierung von Protokollen überwacht, die dann bis zu einem Jahr lang aufbewahrt werden. Auf diese Weise können im Rahmen von Untersuchungen die Aktivitäten nachvollzogen werden, wenn ein Sicherheitsincident eintritt oder Ihr Netzwerk kompromittiert wird.|
|Diagnoseprotokolle in Key Vault sollten aktiviert sein.|Hierbei wird die Aktivierung von Protokollen überwacht, die dann bis zu einem Jahr lang aufbewahrt werden. Auf diese Weise können im Rahmen von Untersuchungen die Aktivitäten nachvollzogen werden, wenn ein Sicherheitsincident eintritt oder Ihr Netzwerk kompromittiert wird.|
|Diagnoseprotokolle in Event Hub sollten aktiviert sein.|Hierbei wird die Aktivierung von Protokollen überwacht, die dann bis zu einem Jahr lang aufbewahrt werden. Auf diese Weise können im Rahmen von Untersuchungen die Aktivitäten nachvollzogen werden, wenn ein Sicherheitsincident eintritt oder Ihr Netzwerk kompromittiert wird.|
|Diagnoseprotokolle in Azure Data Lake Store sollten aktiviert werden.|Hierbei wird die Aktivierung von Protokollen überwacht, die dann bis zu einem Jahr lang aufbewahrt werden. Auf diese Weise können im Rahmen von Untersuchungen die Aktivitäten nachvollzogen werden, wenn ein Sicherheitsincident eintritt oder Ihr Netzwerk kompromittiert wird.|
|Diagnoseprotokolle in Data Lake Analytics sollten aktiviert werden.|Hierbei wird die Aktivierung von Protokollen überwacht, die dann bis zu einem Jahr lang aufbewahrt werden. Auf diese Weise können im Rahmen von Untersuchungen die Aktivitäten nachvollzogen werden, wenn ein Sicherheitsincident eintritt oder Ihr Netzwerk kompromittiert wird.|
|Speicherkonten sollten zu neuen AzureRM-Ressourcen migriert werden.|Verwenden Sie Azure Resource Manager für Ihre Speicherkonten, um Sicherheitsverbesserungen zu erzielen. Das umfasst: <br>- Höhere Sicherheit bei der Zugriffssteuerung (RBAC)<br>- Bessere Überwachung<br>- Auf Azure Resource Manager basierende Bereitstellung und Governance<br>- Zugriff auf verwaltete Identitäten<br>- Zugriff auf Azure Key Vault für Geheimnisse<br>- Azure AD-basierte Authentifizierung<br>- Unterstützung von Tags und Ressourcengruppen zur Vereinfachung der Sicherheitsverwaltung|
|Virtuelle Computer sollten zu neuen AzureRM-Ressourcen migriert werden.|Verwenden Sie Azure Resource Manager für Ihre virtuellen Computer, um Sicherheitsverbesserungen zu erzielen.  Das umfasst: <br>- Höhere Sicherheit bei der Zugriffssteuerung (RBAC)<br>- Bessere Überwachung<br>- Auf Azure Resource Manager basierende Bereitstellung und Governance<br>- Zugriff auf verwaltete Identitäten<br>- Zugriff auf Azure Key Vault für Geheimnisse<br>- Azure AD-basierte Authentifizierung<br>- Unterstützung von Tags und Ressourcengruppen zur Vereinfachung der Sicherheitsverwaltung|
|Metrikwarnungsregeln sollten in Batch-Konten konfiguriert werden.|Hiermit wird die Konfiguration von Metrikwarnungsregeln für Azure Batch-Konten überwacht, um die erforderliche Metrik zu aktivieren.|
|Diagnoseprotokolle in Batch-Konten sollten aktiviert sein.|Hierbei wird die Aktivierung von Protokollen überwacht, die dann bis zu einem Jahr lang aufbewahrt werden. Auf diese Weise können im Rahmen von Untersuchungen die Aktivitäten nachvollzogen werden, wenn ein Sicherheitsincident eintritt oder Ihr Netzwerk kompromittiert wird.|
|In Automation-Kontovariablen sollte eine Verschlüsselung aktiviert sein.|Es ist wichtig, die Verschlüsselung für Variablenassets von Azure Automation-Konten zu aktivieren, wenn Sie vertrauliche Daten speichern.|
|Diagnoseprotokolle in App Services sollten aktiviert sein.|Hiermit wird die Aktivierung von Diagnoseprotokollen für die App überwacht. Auf diese Weise können im Rahmen von Untersuchungen die Aktivitäten nachvollzogen werden, wenn ein Sicherheitsincident eintritt oder Ihr Netzwerk kompromittiert wird.|
|Transparent Data Encryption für SQL-Datenbanken sollte aktiviert werden.|Hiermit wird der Transparent Data Encryption-Status für SQL-Datenbanken überwacht.|
|SQL Server-Überwachung sollte aktiviert werden.|Hierbei wird das Vorhandensein der SQL-Überwachung auf Serverebene überwacht.|
|\[Vorschauversion]: Nicht verschlüsselte SQL-Datenbank in Azure Security Center überwachen|Azure Security Center überwacht unverschlüsselte SQL Server-Instanzen oder -Datenbanken (wie empfohlen).|
|\[Vorschauversion]: Nicht überwachte SQL-Datenbank in Azure Security Center überwachen|Azure Security Center überwacht SQL Server-Instanzen und -Datenbanken, für die keine SQL-Überwachung aktiviert ist (wie empfohlen).|
|\[Vorschauversion]: Systemupdates sollten auf Ihren Computern installiert sein.|Azure Security Center überwacht fehlende Updates des Sicherheitssystems auf Ihren Servern (wie empfohlen).|
|\[Vorschauversion]: Fehlende Blobverschlüsselung für Speicherkonten überwachen|Hierbei werden Speicherkonten überwacht, für die keine Blobverschlüsselung verwendet wird. Dies gilt nur für Microsoft Storage-Ressourcentypen und nicht für Speicher von anderen Anbietern. Azure Security Center überwacht den möglichen Just-In-Time-Netzwerkzugriff (wie empfohlen).|
|\[Vorschauversion]: Die Just-In-Time-Netzwerkzugriffssteuerung sollte auf virtuelle Computer angewendet werden.|Azure Security Center überwacht den möglichen Just-In-Time-Netzwerkzugriff (wie empfohlen).|
|\[Vorschauversion]: Die adaptive Anwendungssteuerung sollte auf virtuellen Computern aktiviert werden.|Azure Security Center überwacht eine mögliche Konfiguration der Anwendungswhitelist.|
|\[Vorschauversion]: Fehlende Netzwerksicherheitsgruppen für virtuelle Computer sollten konfiguriert werden.|Azure Security Center überwacht Netzwerksicherheitsgruppen, die über zu tolerante Regeln verfügen (wie empfohlen).|
|\[Vorschauversion]: Sicherheitsrisiken in der Sicherheitskonfiguration für Ihre Computer sollten beseitigt werden.|Azure Security Center überwacht Server, die nicht die Anforderungen der konfigurierten Baseline erfüllen (wie empfohlen).| 
|\[Vorschauversion]: Endpoint Protection sollte auf virtuellen Computern installiert sein.|Azure Security Center überwacht Server, die nicht über einen installierten Microsoft System Center Endpoint Protection-Agent verfügen (wie empfohlen).|
|\[Vorschauversion]: Die Datenträgerverschlüsselung sollte auf virtuelle Computer angewendet werden.|Azure Security Center überwacht virtuelle Computer, für die keine Datenträgerverschlüsselung aktiviert ist (wie empfohlen).|
|\[Vorschauversion]: Sicherheitsrisiken sollten durch eine Lösung zur Sicherheitsrisikobewertung beseitigt werden.|Hiermit werden Sicherheitsrisiken überwacht, die von der Lösung zur Sicherheitsrisikobewertung erkannt werden, sowie virtuelle Computer, die nicht über eine Lösung zur Sicherheitsrisikobewertung in Azure Security Center verfügen (wie empfohlen).|
|\[Vorschauversion]: Nicht geschützte Webanwendung in Azure Security Center überwachen|Azure Security Center überwacht Webanwendungen, die nicht über einen Web Application Firewall-Schutz verfügen (wie empfohlen).|
|\[Vorschauversion]: Die Endpoint Protection-Lösung sollte auf virtuellen Computern installiert sein.|Azure Security Center überwacht Netzwerkendpunkte, die nicht über einen Firewallschutz der nächsten Generation verfügen (wie empfohlen).|
|\[Vorschauversion]: Sicherheitsrisiken in SQL-Datenbanken sollten beseitigt werden.|Hierbei werden Überprüfungsergebnisse der Sicherheitsrisikobewertung überwacht und Empfehlungen zum Beheben von Sicherheitsrisiken in der Datenbank bereitgestellt.|
|\[Vorschauversion]: Maximal 3 Besitzer sollten für Ihr Abonnement festgelegt sein.|Wir empfehlen Ihnen, maximal drei Abonnementbesitzer festzulegen, um die Möglichkeit einer Sicherheitsverletzung durch einen kompromittierten Besitzer zu verringern.|
|\[Vorschauversion]: Ihrem Abonnement sollte mehr als ein Besitzer zugewiesen sein.|Wir empfehlen Ihnen, mehr als einen Abonnementbesitzer festzulegen, um die Redundanz beim Administratorzugriff sicherzustellen.|
|\[Vorschauversion]: MFA sollte für Konten mit Besitzerberechtigungen in Ihrem Abonnement aktiviert sein. |MFA (Multi-Factor Authentication) muss für alle Abonnementkonten mit Besitzerberechtigungen aktiviert werden, um eine Sicherheitsverletzung für Konten oder Ressourcen zu verhindern.|
|\[Vorschauversion]: MFA sollte für Ihre Abonnementkonten mit Schreibberechtigungen aktiviert sein.|Multi-Factor Authentication muss für alle Abonnementkonten mit Schreibberechtigungen aktiviert werden, um eine Sicherheitsverletzung für Konten oder Ressourcen zu verhindern.|
|\[Vorschauversion]: MFA sollte für Ihre Abonnementkonten mit Leseberechtigungen aktiviert sein.|Multi-Factor Authentication muss für alle Abonnementkonten mit Leseberechtigungen aktiviert werden, um eine Sicherheitsverletzung für Konten oder Ressourcen zu verhindern.|
|\[Vorschauversion]: Veraltete Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.|Veraltete Konten mit Besitzerberechtigungen müssen aus Ihrem Abonnement entfernt werden. Für veraltete Konten wurde die Anmeldung blockiert.|
|\[Vorschauversion]: Veraltete Konten sollten aus Ihrem Abonnement entfernt werden.|Veraltete Konten sollten aus Ihren Abonnements entfernt werden. Für veraltete Konten wurde die Anmeldung blockiert.|
|\[Vorschauversion]: Externe Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.|Externe Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden, um den Zugriff über diese Berechtigungen zu verhindern.|
|\[Vorschauversion]: Externe Konten mit Schreibberechtigungen sollten aus Ihrem Abonnement entfernt werden.|Externe Konten mit Schreibberechtigungen sollten aus Ihrem Abonnement entfernt werden, um einen nicht überwachten Zugriff zu verhindern.|
|\[Vorschauversion]: Externe Konten mit Leseberechtigungen sollten aus Ihrem Abonnement entfernt werden.|Externe Konten mit Leseberechtigungen sollten aus Ihrem Abonnement entfernt werden, um einen nicht überwachten Zugriff zu verhindern.|




## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie erfahren, wie Sie Sicherheitsrichtlinien in Security Center konfigurieren können. Weitere Informationen zu Security Center finden Sie in den folgenden Artikeln.

* [Planungs- und Betriebshandbuch für Azure Security Center](security-center-planning-and-operations-guide.md): Hier erfahren Sie, wie Sie Entwurfsaspekte in Azure Security Center planen und verstehen.
* [Überwachen der Sicherheitsintegrität in Azure Security Center](security-center-monitoring.md): Hier erfahren Sie, wie Sie die Integrität Ihrer Azure-Ressourcen überwachen.
* [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center](security-center-managing-and-responding-alerts.md): Hier erfahren Sie, wie Sie Sicherheitswarnungen verwalten und darauf reagieren.
* [Überwachen von Partnerlösungen mit Azure Security Center](security-center-partner-solutions.md): Hier erfahren Sie, wie Sie den Integritätsstatus Ihrer Partnerlösungen überwachen.
* [Azure Security Center – häufig gestellte Fragen:](security-center-faq.md) Hier finden Sie häufig gestellte Fragen zur Verwendung des Diensts.
* [Azure Security-Blog](https://blogs.msdn.com/b/azuresecurity/): Hier finden Sie Blogbeiträge zur Sicherheit und Compliance von Azure.

Weitere Informationen zu Azure Policy finden Sie unter [Was ist Azure Policy?](../governance/policy/overview.md).
