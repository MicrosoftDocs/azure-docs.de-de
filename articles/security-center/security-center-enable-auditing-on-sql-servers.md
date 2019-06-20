---
title: Aktivieren der Überwachung und Bedrohungserkennung für SQL-Server in Azure Security Center | Microsoft-Dokumentation
description: In diesem Dokument wird erläutert, wie Sie die Azure Security Center-Empfehlung **Überwachung und Bedrohungserkennung für SQL-Server aktivieren** implementieren.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 042fca4d-7dab-4172-8614-e8c21ccb4960
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 5b41af83122d74fc766e6c5179d98803979269f7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60704655"
---
# <a name="enable-auditing-and-threat-detection-on-sql-servers-in-azure-security-center"></a>Aktivieren der Überwachung und Bedrohungserkennung für SQL-Server in Azure Security Center
Azure Security Center empfiehlt Ihnen, die Überwachung und Bedrohungserkennung für alle Datenbanken auf Ihren Azure SQL-Servern zu aktivieren, sofern noch nicht erfolgt. Die Überwachung und Bedrohungserkennung kann Ihnen dabei helfen, gesetzliche Bestimmungen einzuhalten, die Datenbankaktivität zu verstehen und Einblicke in Abweichungen und Anomalien zu erhalten, die auf geschäftliche Probleme oder mutmaßliche Sicherheitsverstöße hinweisen können.

Nachdem Sie die Überwachung aktiviert haben, können Sie Einstellungen für die Bedrohungserkennung und E-Mails zum Empfangen von Sicherheitswarnungen konfigurieren. Die Bedrohungserkennung erkennt anomale Datenbankaktivitäten, die auf potenzielle Sicherheitsrisiken für die Datenbank hindeuten. Dadurch können Sie potenzielle Bedrohungen erkennen und darauf reagieren, sobald sie auftreten.

Diese Empfehlung gilt für nur den Azure SQL-Dienst und nicht für auf Ihren virtuellen Computern in Azure Infrastructure Services (Azure IaaS) ausgeführte SQL-Server.

> [!NOTE]
> Der Dienst wird anhand einer Beispielbereitstellung vorgestellt.  Es ist keine schrittweise Anleitung.
>
>

## <a name="implement-the-recommendation"></a>Implementieren der Empfehlung
1. Wählen Sie auf dem Blatt **Empfehlungen** die Option **Aktivieren der Überwachung und Bedrohungserkennung in SQL-Servern** aus.  Daraufhin wird das Blatt **Aktivieren der Überwachung und Bedrohungserkennung in SQL-Servern** geöffnet.

   ![Überwachung für SQL-Server aktivieren][1]
2. Wählen Sie einen SQL-Server aus, auf dem Überwachung und Bedrohungserkennung aktiviert werden soll. Daraufhin wird das Blatt **Überwachung und Bedrohungserkennung** geöffnet.

3. Wählen Sie auf dem Blatt **Überwachung und Bedrohungserkennung** unter **Überwachung** die Option **EIN** aus.

   ![Aktivieren der Überwachungseinstellungen][2]
4. Führen Sie die Schritte in [Überwachen von SQL-Datenbank im Azure-Portal](../sql-database/sql-database-auditing-portal.md) aus, um den Speicher zu konfigurieren, in dem Ihre Überwachungsprotokolle gespeichert werden sollen. Das Speicherkonto des Abonnements für die Datensammlung ist das Standardspeicherkonto.
5. Führen Sie die Schritte in [Erste Schritte mit der Bedrohungserkennung von SQL-Datenbank](../sql-database/sql-database-threat-detection.md) aus, um die Bedrohungserkennung zu aktivieren und zu konfigurieren und die Liste der E-Mail-Empfänger einzurichten, die bei Erkennung ungewöhnlicher Aktivitäten Sicherheitswarnungen erhalten.

## <a name="see-also"></a>Weitere Informationen
In diesem Artikel wurde gezeigt, wie Sie die Security Center-Empfehlung „Überwachung und Bedrohungserkennung für SQL-Server aktivieren“ umsetzen. Weitere Informationen zum Schützen Ihrer SQL-Datenbank finden Sie unter:

* [Sichern der SQL-Datenbank](../sql-database/sql-database-security-overview.md)

Weitere Informationen zu Security Center finden Sie in den folgenden Quellen:

* [Festlegen von Sicherheitsrichtlinien in Azure Security Center:](tutorial-security-policy.md) Erfahren Sie, wie Sie Sicherheitsrichtlinien für Ihre Azure-Abonnements und -Ressourcengruppen konfigurieren.
* [Verwalten von Sicherheitsempfehlungen in Azure Security Center:](security-center-recommendations.md) Hier erfahren Sie, wie Empfehlungen Ihnen beim Schutz der Azure-Ressourcen helfen.
* [Überwachen der Sicherheitsintegrität in Azure Security Center:](security-center-monitoring.md) Erfahren Sie, wie Sie die Integrität Ihrer Azure-Ressourcen überwachen.
* [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center](security-center-managing-and-responding-alerts.md) : Hier erfahren Sie, wie Sie Sicherheitswarnungen verwalten und auf diese reagieren.
* [Überwachen von Partnerlösungen mit Azure Security Center:](security-center-partner-solutions.md) Erfahren Sie, wie der Integritätsstatus Ihrer Partnerlösungen überwacht wird.
* [Azure Security Center – Häufig gestellte Fragen:](security-center-faq.md) Hier finden Sie häufig gestellte Fragen zur Verwendung des Diensts.
* [Azure Security Blog](https://blogs.msdn.com/b/azuresecurity/) (Blog zur Azure-Sicherheit): Hier finden Sie Neuigkeiten und Informationen zur Azure-Sicherheit.

<!--Image references-->
[1]: ./media/security-center-enable-auditing-on-sql-server/enable-auditing-on-sql-servers.png
[2]: ./media/security-center-enable-auditing-on-sql-server/auditing-settings-blade.png
