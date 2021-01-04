---
title: 'Benachrichtigung über geplante Wartung: Azure Database for MariaDB'
description: In diesem Artikel wird das Feature für Benachrichtigungen über geplante Wartungsmaßnahmen in Azure Database for MariaDB beschrieben.
author: ambhatna
ms.author: ambhatna
ms.service: mariadb
ms.topic: conceptual
ms.date: 10/21/2020
ms.openlocfilehash: c290236dfe7e88999847f8cb0d66b2d3c868c1ab
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96453299"
---
# <a name="planned-maintenance-notification-in-azure-database-for-mariadb"></a>Benachrichtigung über geplante Wartung in Azure Database for MariaDB

Erfahren Sie, wie Sie sich auf die geplante Wartung für Ihre Azure Database for MariaDB-Instanz vorbereiten.

## <a name="what-is-a-planned-maintenance"></a>Was ist eine geplante Wartung?

Der Azure Database for MariaDB-Dienst führt ein automatisiertes Patchen für die zugrunde liegende Hardware, das Betriebssystem und die Datenbank-Engine durch. Der Patch umfasst neue Dienstfeatures, Verbesserungen bei der Sicherheit und Softwareupdates. Bei der MariaDB-Engine werden Upgrades von Nebenversionen automatisch durchgeführt und sind im Patchzyklus enthalten. Für das Patchen sind keine Benutzeraktionen oder Konfigurationseinstellungen erforderlich. Der Patch wird ausgiebig getestet und mithilfe von sicheren Bereitstellungsmethoden eingeführt.

Eine geplante Wartung ist ein Wartungsfenster, in dem diese Dienstupdates auf Servern in einer bestimmten Azure-Region bereitgestellt werden. Während einer geplanten Wartung wird ein Benachrichtigungsereignis erstellt, um Kunden zu informieren, wann das Dienstupdate in der Azure-Region bereitgestellt wird, in der ihre Server gehostet werden. Zwischen zwei geplanten Wartungen liegen immer mindestens 30 Tage. Sie werden 72 Stunden im Voraus über das nächste Wartungsfenster informiert.

## <a name="planned-maintenance---duration-and-customer-impact"></a>Geplante Wartung: Dauer und Beeinträchtigung für Kunden

Eine geplante Wartung für eine bestimmte Azure-Region dauert in der Regel 15 Stunden. Das Fenster umfasst auch Pufferzeit zum Ausführen eines Rollbackplans, falls dies erforderlich wird. Während einer geplanten Wartung können Datenbankserver neu gestartet oder Failover ausgeführt werden. Dies kann dazu führen, dass die Datenbankserver kurzzeitig nicht für Endbenutzer verfügbar sind. Azure Database for MariaDB-Server werden in Containern ausgeführt, sodass Neustarts von Datenbankservern meist schnell abgeschlossen sind – in der Regel innerhalb von 60 bis 120 Sekunden. Das gesamte geplante Wartungsereignis, einschließlich der einzelnen Serverneustarts, wird vom Technikerteam sorgfältig überwacht. Die Zeit für Serverfailover hängt von der Wiederherstellungszeit der Datenbank ab. Es kann daher länger dauern, die Datenbank wieder online zu schalten, wenn während des Failovers auf dem Server extrem viele Transaktionsaktivitäten auftreten. Um längere Neustarts zu vermeiden, empfiehlt es sich, zeitintensive Transaktionen (Massenladen) während geplanter Wartungsereignisse zu vermeiden.

Zusammenfassend gilt: Auch wenn das geplante Wartungsereignis 15 Stunden dauert, sind die einzelnen Server in der Regel je nach den Transaktionsaktivitäten auf dem Server nur etwa 60 Sekunden lang beeinträchtigt. 72 Stunden vor dem Start der geplanten Wartung wird eine Benachrichtigung gesendet. Eine weitere Benachrichtigung erfolgt, wenn die Wartung für eine bestimmte Region ausgeführt wird.

## <a name="how-can-i-get-notified-of-planned-maintenance"></a>Wie werde ich über eine geplante Wartung benachrichtigt?

Sie können das Benachrichtigungsfeature zu geplanten Wartungen nutzen, um Warnungen zu anstehenden geplanten Wartungsereignissen zu erhalten. Sie erhalten 72 Stunden vor dem Start der geplanten Wartung eine Benachrichtigung. Eine weitere Benachrichtigung erfolgt, wenn die Wartung für eine bestimmte Region ausgeführt wird.

### <a name="planned-maintenance-notification"></a>Benachrichtigungen zu geplanten Wartungen

> [!IMPORTANT]
> Benachrichtigungen für geplante Wartungen sind derzeit in allen Regionen **mit Ausnahme von** „USA, Westen-Mitte“ als Vorschau verfügbar.

Mit **Benachrichtigungen zu geplanten Wartungen** können Sie Warnungen zu einem anstehenden geplanten Wartungsereignis für Azure Database for MariaDB erhalten. Diese Benachrichtigungen sind in die geplante Wartung von [Service Health](../service-health/overview.md) integriert, sodass Sie alle geplanten Wartungsarbeiten für Ihre Abonnements an zentraler Stelle anzeigen können. Außerdem ist es hilfreich, die Benachrichtigungen an die richtigen Zielgruppen für verschiedene Ressourcengruppen zu richten, da möglicherweise unterschiedliche Ansprechpartner für verschiedene Ressourcen zuständig sind. Sie erhalten die Benachrichtigung über die anstehende Wartung 72 Stunden vor dem Ereignis.

Es wird jeder Versuch unternommen, die **Benachrichtigung zur geplanten Wartung** für alle Ereignisse 72 Stunden im Voraus bereitzustellen. Im Fall von kritischen oder Sicherheitspatches können Benachrichtigungen jedoch zeitlich näher am Ereignis gesendet werden oder ganz entfallen.

Sie können die Benachrichtigung zur geplanten Wartung im Azure-Portal einsehen oder Warnungen konfigurieren, um eine Benachrichtigung zu erhalten. 

### <a name="check-planned-maintenance-notification-from-azure-portal"></a>Überprüfen von Benachrichtigungen über geplante Wartungen im Azure-Portal

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) die Option **Service Health** aus.
2. Wählen Sie die Registerkarte **Geplante Wartung** aus.
3. Wählen Sie das **Abonnement**, die **Region** und den **Dienst** aus, für die Sie die Benachrichtigung zu geplanten Wartungen überprüfen möchten. 
   
### <a name="to-receive-planned-maintenance-notification"></a>Empfangen von Benachrichtigungen zu geplanten Wartungen

1. Wählen Sie im [Portal](https://portal.azure.com) die Option **Dienstintegrität** aus.
2. Wählen Sie im Abschnitt **Warnungen** die Option **Integritätswarnungen** aus.
3. Wählen Sie **+ Service Health-Warnung hinzufügen** aus, und füllen Sie die Felder aus.
4. Füllen Sie die erforderlichen Felder aus. 
5. Wählen Sie den **Ereignistyp** aus. Wählen Sie **Geplante Wartung** oder **Alle auswählen**.
6. Legen Sie unter **Aktionsgruppen** fest, wie Sie die Warnung erhalten möchten (Empfangen einer E-Mail, Auslösen einer Logik-App usw.)  
7. Stellen Sie sicher, dass „Regel beim Erstellen aktivieren“ auf „Ja“ festgelegt ist.
8. Wählen Sie **Warnungsregel erstellen** aus, um die Warnung fertig zu stellen.

Eine ausführliche Beschreibung der Schritte zum Erstellen von **Service Health-Warnungen** finden Sie unter [Erstellen von Aktivitätsprotokollwarnungen zu Dienstbenachrichtigungen](../service-health/alerts-activity-log-service-notifications-portal.md).

## <a name="can-i-cancel-or-postpone-planned-maintenance"></a>Kann ich eine geplante Wartung abbrechen oder verschieben?

Wartungen sind erforderlich, um Ihren Server sicher, stabil und auf dem neuesten Stand zu halten. Geplante Wartungsereignisse können weder abgebrochen noch verschoben werden. Nachdem die Benachrichtigung an eine bestimmte Azure-Region gesendet wurde, kann der Zeitplan für das Patchen einzelner Server in dieser Region nicht mehr geändert werden. Der Patch wird für die gesamte Region gleichzeitig eingeführt. Der Azure Database for MariaDB-Dienst ist für cloudnative Anwendungen konzipiert, die keine differenzierte Steuerung oder Anpassung des Diensts erfordern.

## <a name="are-all-the-azure-regions-patched-at-the-same-time"></a>Werden alle Azure-Regionen gleichzeitig gepatcht?

Nein. Die verschiedenen Azure-Regionen werden in den jeweiligen Zeitfenstern für die Bereitstellung gepatcht. Das Zeitfenster für die Bereitstellung in einer bestimmten Azure-Region liegt in der Regel zwischen 17:00 Uhr und 8:00 Uhr Ortszeit am Folgetag. Geografisch gekoppelte Azure-Regionen werden an unterschiedlichen Tagen gepatcht. Um Hochverfügbarkeit und Geschäftskontinuität von Datenbankservern zu gewährleisten, wird die Verwendung [regionsübergreifender Lesereplikate](./concepts-read-replicas.md#cross-region-replication) empfohlen.

## <a name="retry-logic"></a>Wiederholungslogik

Bei einem vorübergehenden Fehler handelt es sich um einen Fehler, der sich von alleine löst. Während der Wartung können [vorübergehende Fehler](./concepts-connectivity.md#transient-errors) auftreten. Meist werden diese Ereignisse in weniger als 60 Sekunden automatisch vom System behoben. Vorübergehende Fehler sollten mit [Wiederholungslogik](./concepts-connectivity.md#handling-transient-errors) behandelt werden.


## <a name="next-steps"></a>Nächste Schritte

- Sollten Sie Fragen oder Vorschläge im Zusammenhang mit der Verwendung von Azure Database for MariaDB haben, können Sie unter der Adresse AskAzureDBforMariaDB@service.microsoft.com eine E-Mail an das zuständige Team senden.
- Anleitungen zum Erstellen einer Warnung zu einer Metrik finden Sie unter [Einrichten von Warnungen](howto-alert-metric.md).
- [Beheben von Verbindungsproblemen mit Azure Database for MariaDB](howto-troubleshoot-common-connection-issues.md)
- [Behandeln vorübergehender Konnektivitätsfehler für Azure Database for MariaDB](concepts-connectivity.md)