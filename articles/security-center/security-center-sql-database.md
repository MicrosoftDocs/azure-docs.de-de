---
title: Azure Security Center und Azure SQL-Datenbank-Dienst | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie mithilfe von Security Center Ihre Datenbanken in Azure SQL-Datenbank schützen können.
services: sql-database
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f109adfd-daed-4257-9692-2042a1399480
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: rkarlin
ms.openlocfilehash: 0a889de79b6a5921007614dac8d610c1be0222d2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60704553"
---
# <a name="azure-security-center-and-azure-sql-database-service"></a>Azure Security Center und Azure SQL-Datenbank-Dienst
Mit [Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/) können Sie Bedrohungen verhindern, erkennen und bekämpfen. Es bietet integrierte Sicherheitsüberwachung und Richtlinienverwaltung für Ihre Azure-Abonnements, hilft bei der Erkennung von Bedrohungen, die andernfalls möglicherweise unbemerkt bleiben, und kann gemeinsam mit einem breiten Spektrum an Sicherheitslösungen verwendet werden.

In diesem Artikel erfahren Sie, wie Sie mithilfe von Security Center Ihre Datenbanken in Azure SQL-Datenbank schützen können.

## <a name="why-use-security-center"></a>Gründe für die Verwendung von Security Center
Mit Security Center können Sie die Daten in SQL-Datenbank schützen, indem Sie Einblick in die Sicherheit Ihrer Server und Datenbanken ermöglichen. Mit Security Center haben Sie folgende Möglichkeiten:

* Definieren von Richtlinien für die Verschlüsselung und Überwachung in SQL-Datenbank
* Überwachen der Sicherheit von SQL-Datenbank-Ressourcen in allen Abonnements
* Schnelles Erkennen und Beheben von Sicherheitsproblemen
* Integrieren von Warnungen aus der [Bedrohungserkennung von Azure SQL-Datenbank](../sql-database/sql-database-threat-detection.md)

Security Center ermöglicht nicht nur den Schutz Ihrer SQL-Datenbank-Ressourcen, sondern stellt auch Möglichkeiten für die Sicherheitsüberwachung und Verwaltung von virtuellen Azure-Computern, Clouddienste, App Services, virtuelle Netzwerke und vieles mehr bereit. Weitere Informationen zu Security Center finden Sie [hier](security-center-intro.md).

## <a name="prerequisites"></a>Voraussetzungen
Für den Einstieg in Security Center benötigen Sie ein Microsoft Azure-Abonnement. Der Free-Tarif für Security Center wird mit Ihrem Abonnement aktiviert. Weitere Informationen zum Free- und Standard-Tarif von Security Center finden Sie unter [Security Center Preise](https://azure.microsoft.com/pricing/details/security-center/).

Security Center unterstützt rollenbasierten Zugriff. Weitere Informationen zur rollenbasierten Zugriffssteuerung (RBAC) in Azure finden Sie unter [Rollenbasierte Zugriffssteuerung in Azure Active Directory](../role-based-access-control/role-assignments-portal.md). Im Artikel mit häufig gestellten Fragen zu Security Center finden Sie Informationen zur [Behandlung von Berechtigungen in Security Center](security-center-faq.md#permissions).

## <a name="access-security-center"></a>Zugreifen auf Security Center
Sie können über das [Azure-Portal](https://azure.microsoft.com/features/azure-portal/)auf Security Center zugreifen. [Melden Sie sich beim Portal](https://portal.azure.com/) an, und wählen Sie die Option **Security Center** aus.

![Option „Security Center“][1]

Das Blatt **Security Center** wird geöffnet.
![Blatt „Security Center“][2]

## <a name="set-security-policy"></a>Sicherheitsrichtlinie festlegen
In einer Sicherheitsrichtlinie werden die Sicherheitsmechanismen definiert, die für Ressourcen im angegebenen Abonnement oder in der angegebenen Ressourcengruppe zu empfehlen sind. In Security Center definieren Sie Richtlinien für Ihre Abonnements oder Ressourcengruppen gemäß den Sicherheitsanforderungen Ihres Unternehmens sowie gemäß der Art von Anwendungen oder der Vertraulichkeit der Daten in den einzelnen Abonnements.

Sie können eine Richtlinie zum Anzeigen von Empfehlungen für die SQL-Überwachung und die SQL Transparent Data Encryption (TDE) festlegen.

* Wenn Sie die **SQL-Überwachung und -Bedrohungserkennung** aktivieren, wird von Security Center empfohlen, die Überwachung des Zugriffs auf Azure-Datenbanken in Bezug auf Compliance, erweiterte Erkennung und Untersuchung zu aktivieren.
* Wenn Sie die **SQL Transparent Data Encryption** aktivieren, wird von Security Center empfohlen, die Verschlüsselung im Ruhezustand für Ihre Azure SQL-Datenbank, die zugehörigen Sicherungen und die Transaktionsprotokolldateien zu aktivieren.

Wählen Sie auf dem Blatt „Security Center“ die Kachel **Richtlinie** aus, um eine Sicherheitsrichtlinie festzulegen. Wählen Sie auf dem Blatt **Sicherheitsrichtlinie** das Abonnement aus, für das die Sicherheitsrichtlinie aktiviert werden soll. Wählen Sie **Präventionsrichtlinie** und dann **Ein** aus, um die Sicherheitsempfehlungen zu aktivieren, die Sie für dieses Abonnement verwenden möchten.
![Sicherheitsrichtlinie][3]

Weitere Informationen finden Sie unter [Festlegen von Sicherheitsrichtlinien](tutorial-security-policy.md).

## <a name="manage-security-recommendation"></a>Verwalten von Sicherheitsempfehlungen
Security Center analysiert in regelmäßigen Abständen den Sicherheitsstatus der Azure-Ressourcen. Wenn Security Center potenzielle Sicherheitsrisiken identifiziert, werden Empfehlungen erstellt. Entsprechend den Empfehlungen werden Sie durch den Prozess der Konfiguration des erforderlichen Sicherheitsmechanismus geführt.

Nach Einstellung einer Sicherheitsrichtlinie analysiert Security Center den Sicherheitsstatus Ihrer Ressourcen, um potenzielle Sicherheitsrisiken zu erkennen. Die Empfehlungen werden in einem Tabellenformat angezeigt, wobei jede Zeile eine bestimmte Empfehlung darstellt. Verwenden Sie die folgende Tabelle als Übersicht über die verfügbaren Empfehlungen für Azure SQL-Datenbank und um zu erfahren, was die einzelnen Empfehlungen bei Anwendung bewirken. Wenn Sie eine Empfehlung auswählen, gelangen Sie zu einem Artikel, in dem erläutert wird, wie die Empfehlung in Security Center implementiert wird.

| Empfehlung | BESCHREIBUNG |
| --- | --- |
| [Aktivieren der Überwachung und Bedrohungserkennung auf SQL-Servern](security-center-enable-auditing-on-sql-servers.md) |Empfiehlt die Überwachung und Bedrohungserkennung für SQL-Datenbank-Server zu aktivieren. (Nur SQL-Datenbank-Dienst. Bezieht sich nicht auf Microsoft SQL Server auf virtuellen Computern.) |
| [Aktivieren der Überwachung und Bedrohungserkennung auf SQL-Datenbanken](security-center-enable-auditing-on-sql-databases.md) |Empfiehlt, die Überwachung und Bedrohungserkennung für SQL-Datenbank Datenbanken zu aktivieren. (Nur SQL-Datenbank-Dienst. Bezieht sich nicht auf Microsoft SQL Server auf virtuellen Computern.) |
| [Aktivieren von Transparent Data Encryption](security-center-enable-transparent-data-encryption.md) |Empfiehlt die Verschlüsselung für SQL-Datenbanken zu aktivieren. (Nur SQL-Datenbank-Dienst.) |

Wenn Sie Empfehlungen für Ihre Azure-Ressourcen anzeigen möchten, wählen Sie auf dem Blatt „Security Center“ die Kachel **Empfehlungen** aus. Wählen Sie auf dem Blatt **Empfehlungen** eine Empfehlung aus, um die entsprechenden Details anzuzeigen. Lassen Sie uns in diesem Beispiel **Enable Auditing & Threat detection on SQL servers** (Überwachung und Bedrohungserkennung auf SQL-Servern aktivieren) auswählen.

![Empfehlungen][4]

Wie in der folgenden Abbildung dargestellt, werden in Security Center die SQL-Server angezeigt, für die keine Überwachung und Bedrohungserkennung aktiviert ist. Nachdem Sie die Überwachung aktiviert haben, können Sie Einstellungen für die Bedrohungserkennung und E-Mails zum Empfangen von Sicherheitswarnungen konfigurieren. Die Bedrohungserkennung warnt Sie, wenn anormale Datenbankaktivitäten erkannt werden, die auf potenzielle Sicherheitsrisiken für die Datenbank hindeuten. Die Warnungen werden im Security Center-Dashboard angezeigt.
![Überwachung und Bedrohungserkennung][5]

Führen Sie die Schritte in [Bedrohungserkennung bei SQL-Datenbank im Azure-Portal](../sql-database/sql-database-threat-detection-portal.md) aus, um die Bedrohungserkennung zu aktivieren und zu konfigurieren und die Liste der E-Mail-Empfänger einzurichten, die bei Erkennung ungewöhnlicher Aktivitäten Sicherheitswarnungen erhalten.

Weitere Informationen zu Empfehlungen finden Sie unter [Verwalten von Sicherheitsempfehlungen](security-center-recommendations.md).

## <a name="monitor-security-health"></a>Überwachen der Sicherheitsintegrität
Nachdem Sie die [Sicherheitsrichtlinien](tutorial-security-policy.md) für die Ressourcen des Abonnements aktiviert haben, analysiert Security Center die Sicherheit Ihrer Ressourcen, um mögliche Sicherheitsrisiken zu erkennen.  Der Sicherheitszustand Ihrer Ressourcen wird auf der Kachel **Sicherheitsintegrität der Ressource** angezeigt. Wenn Sie auf der Kachel **Sicherheitsintegrität der Ressource** auf **Daten** klicken, wird das Blatt **Datenressourcen** geöffnet. Es enthält SQL-Empfehlungen zu Problemen, die mit der Überwachung oder der nicht aktivierten Transparent Data Encryption zusammenhängen. Außerdem werden Empfehlungen zum allgemeinen Integritätsstatus der Datenbank angegeben.
![Sicherheitsintegrität von Ressourcen][6]

Weitere Informationen finden Sie unter [Überwachen der Sicherheitsintegrität](security-center-monitoring.md).

## <a name="manage-and-respond-to-security-alerts"></a>Verwalten von und Reagieren auf Sicherheitswarnungen
Security Center erfasst, analysiert und verknüpft Protokolldaten von [Azure SQL-Bedrohungserkennung](../sql-database/sql-database-threat-detection.md) sowie von anderen Azure-Ressourcen, um echte Bedrohungen zu erkennen und falsch positive Ergebnisse zu reduzieren. Eine Liste mit priorisierten Sicherheitswarnungen wird im Security Center zusammen mit den Informationen angezeigt, die Sie zum schnellen Untersuchen des Problems benötigen. Außerdem sind Empfehlungen zum Reagieren auf einen Angriff vorhanden.

Wählen Sie auf dem Blatt „Security Center“ die Kachel **Sicherheitswarnungen** aus, um Warnungen anzuzeigen. Wählen Sie auf dem Blatt **Sicherheitswarnungen** eine Sicherheitswarnung aus, um weitere Informationen zu den Ereignissen zu erhalten, die die Warnung ausgelöst haben, sowie ggf. zu den Schritten, die Sie als Reaktion auf den Angriff ausführen müssen. Lassen Sie uns in diesem Beispiel **Potential SQL injection** (Potenzielle Einschleusung von SQL-Befehlen) auswählen.
![Sicherheitswarnungen][7]

Wie in der folgenden Abbildung dargestellt, werden in Security Center zusätzliche Informationen dazu bereitgestellt, wodurch die Sicherheitswarnung ausgelöst wurde. Zudem finden Sie hier die Zielressource, ggf. die IP-Quelladresse sowie Empfehlungen zur Problembehebung.
![Potenzielle Einschleusung von SQL-Befehlen][8]

Weitere Informationen finden Sie unter [Verwalten von und Reagieren auf Sicherheitswarnungen](security-center-managing-and-responding-alerts.md).

## <a name="next-steps"></a>Nächste Schritte
* [Security Center – Häufig gestellte Fragen:](security-center-faq.md) Hier finden Sie häufig gestellte Fragen zur Verwendung des Diensts.
* [Planungs- und Betriebshandbuch für Security Center](security-center-planning-and-operations-guide.md) – Eine Reihe von Schritten und Aufgaben, mit denen Sie die Verwendung von Security Center auf Grundlage der Sicherheitsanforderungen und des Cloudverwaltungsmodells Ihres Unternehmens optimieren können.
* [Azure Security Center-Datensicherheit](security-center-data-security.md) – Erfahren Sie, wie Security Center Daten zu Ihren Azure-Ressourcen (z. B. Konfigurationsinformationen, Metadaten, Ereignisprotokolle, Absturzabbilddateien und mehr) erfasst und verarbeitet.
* [Behandeln von Sicherheitswarnungen](security-center-incident.md) – Erfahren Sie, wie Sie die Sicherheitswarnungsfunktionen in Security Center verwenden, um die Behandlung von Sicherheitsvorfällen zu unterstützen.

<!--Image references-->
[1]: ./media/security-center-sql-database/security-center.png
[2]: ./media/security-center-sql-database/security-center-blade.png
[3]: ./media/security-center-sql-database/security-policy.png
[4]: ./media/security-center-sql-database/recommendation.png
[5]: ./media/security-center-sql-database/turn-on-auditing.png
[6]: ./media/security-center-sql-database/monitor-health.png
[7]: ./media/security-center-sql-database/alert.png
[8]: ./media/security-center-sql-database/sql-injection.png
