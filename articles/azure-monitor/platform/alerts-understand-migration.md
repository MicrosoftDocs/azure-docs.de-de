---
title: Funktionsweise des Tools für die freiwillige Migration von Azure Monitor-Warnungen
description: Enthält eine Beschreibung der Funktionsweise des Migrationstools für Warnungen sowie Informationen zur Problembehandlung.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: b5a13254fc9dfd58db83a1bc8b9dd071cfbbdab2
ms.sourcegitcommit: db3fe303b251c92e94072b160e546cec15361c2c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66015592"
---
# <a name="understand-how-the-migration-tool-works"></a>Funktionsweise des Migrationstools

Wie [bereits angekündigt](monitoring-classic-retirement.md) werden klassische Warnungen in Azure Monitor im September 2019 (ursprünglich Juli 2019) eingestellt. Im Azure-Portal steht ein Migrationstool für Kunden bereit, die klassische Warnungsregeln verwenden und die Migration selbst auslösen möchten.

In diesem Artikel wird erläutert, wie das Tool für die freiwillige Migration funktioniert. Außerdem werden Abhilfemaßnahmen für einige häufige Probleme beschrieben.

> [!NOTE]
> Aufgrund einer Verzögerung beim Rollout des Migrationstools wurde der Deaktivierungstermin vom ursprünglich angekündigten Datum, dem 30. Juni 2019, auf den [31. August 2019 verschoben](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/).

## <a name="which-classic-alert-rules-can-be-migrated"></a>Welche klassischen Warnungsregeln können migriert werden?

Zwar können mit dem Tool fast alle klassischen Warnungsregeln migriert werden, doch gibt es einige Ausnahmen. Die folgenden Warnungsregeln können mit dem Tool nicht migriert werden (und werden auch während der automatischen Migration im September 2019 nicht migriert):

- Klassische Warnungsregeln für VM-Gastmetriken (sowohl Windows als auch Linux). Informationen finden Sie in der [Anleitung zum erneuten Erstellen solcher Warnungsregeln in neuen Metrikwarnungen](#guest-metrics-on-virtual-machines) weiter unten in diesem Artikel.
- Klassische Warnungsregeln für Metriken von klassischem Speicher. Informationen finden Sie in der [Anleitung zum Überwachen Ihrer klassischen Speicherkonten](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/).
- Klassische Warnungsregeln in einigen Speicherkontometriken. Informationen finden Sie unter den [Details](#storage-account-metrics) weiter unten in diesem Artikel.

Wenn Ihr Abonnement über klassische Regeln dieser Art verfügt, müssen Sie sie manuell migrieren. Da wir keine automatische Migration bereitstellen können, funktionieren alle vorhandenen klassischen Metrikwarnungen noch bis Juni 2020. Diese Verlängerung gibt Ihnen Zeit für die Umstellung auf neue Warnungen. Nach August 2019 können aber keine neuen klassischen Warnungen mehr erstellt werden.

### <a name="guest-metrics-on-virtual-machines"></a>Gastmetriken auf virtuellen Computern

Bevor Sie neue Metrikwarnungen für Gastmetriken erstellen können, müssen die Gastmetriken an den benutzerdefinierten Metrikspeicher von Azure Monitor gesendet werden. Befolgen Sie die nachstehenden Anweisungen, um die Azure Monitor-Senke in den Diagnoseeinstellungen zu aktivieren:

- [Aktivieren von Gastmetriken für Windows-VMs](collect-custom-metrics-guestos-resource-manager-vm.md)
- [Aktivieren von Gastmetriken für Linux-VMs](collect-custom-metrics-linux-telegraf.md)

Nachdem die oben genannten Schritte ausgeführt wurden, können Sie neue Metrikwarnungen für Gastmetriken erstellen. Und nachdem Sie neue Metrikwarnungen erstellt haben, können Sie klassische Warnungen löschen.

### <a name="storage-account-metrics"></a>Speicherkontometriken

Alle klassischen Warnungen in Speicherkonten können migriert werden, mit Ausnahme von Warnungen in den folgenden Metriken:

- PercentAuthorizationError
- PercentClientOtherError
- PercentNetworkError
- PercentServerOtherError
- PercentSuccess
- PercentThrottlingError
- PercentTimeoutError
- AnonymousThrottlingError
- SASThrottlingError

Klassische Warnungsregeln für Metriken vom Typ „Percent“ müssen basierend auf der [Zuordnung zwischen alten und neuen Speichermetriken](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics) migriert werden. Schwellenwerte müssen entsprechend geändert werden, da die neue verfügbare Metrik eine absolute Metrik ist.

Die klassischen Warnungsregeln für AnonymousThrottlingError und SASThrottlingError müssen in zwei neue Warnungen aufgeteilt werden, da es keine kombinierte Metrik gibt, die über die gleiche Funktionalität verfügt. Schwellenwerte müssen entsprechend angepasst werden.

## <a name="rollout-phases"></a>Rolloutphasen

Der Rollout des Migrationstools erfolgt für Kunden, die klassische Warnungsregeln verwenden, in mehreren Phasen. Abonnementbesitzer erhalten eine E-Mail, wenn das Abonnement für die Migration mit dem Tool bereit ist.

> [!NOTE]
> Da der Rollout des Tools in Phasen erfolgt, wird Ihnen in den frühen Phasen möglicherweise angezeigt, dass die meisten Ihrer Abonnements noch nicht für die Migration bereit sind.

Derzeit ist nur eine Teilmenge der Abonnements als migrationsbereit gekennzeichnet. Die Teilmenge umfasst die Abonnements, die über klassische Warnungsregeln nur für die folgenden Ressourcentypen verfügen. Unterstützung für weitere Ressourcentypen wird in den nächsten Phasen hinzugefügt.

- Microsoft.apimanagement/service
- Microsoft.batch/batchaccounts
- Microsoft.cache/redis
- Microsoft.datafactory/datafactories
- Microsoft.dbformysql/servers
- Microsoft.dbforpostgresql/servers
- Microsoft.eventhub/namespaces
- Microsoft.logic/workflows
- Microsoft.network/applicationgateways
- Microsoft.network/dnszones
- Microsoft.network/expressroutecircuits
- Microsoft.network/loadbalancers
- Microsoft.network/networkwatchers/connectionmonitors
- Microsoft.network/publicipaddresses
- Microsoft.network/trafficmanagerprofiles
- Microsoft.network/virtualnetworkgateways
- Microsoft.search/searchservices
- Microsoft.servicebus/namespaces
- Microsoft.streamanalytics/streamingjobs
- Microsoft.timeseriesinsights/environments
- Microsoft.web/hostingenvironments/workerpools
- Microsoft.web/serverfarms
- Microsoft.web/sites

## <a name="who-can-trigger-the-migration"></a>Wer kann die Migration auslösen?

Alle Benutzer, die auf Abonnementebene über die integrierte Rolle „Mitwirkender an der Überwachung“ verfügen, können die Migration auslösen. Benutzer, die über eine benutzerdefinierte Rolle mit den folgenden Berechtigungen verfügen, können die Migration ebenfalls auslösen:

- */Lesen
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*

## <a name="common-problems-and-remedies"></a>Häufige Probleme und Abhilfemaßnahmen

Nachdem Sie [die Migration ausgelöst haben](alerts-using-migration-tool.md), erhalten Sie E-Mails an die Adressen, die Sie für die Benachrichtigung über den Abschluss der Migration oder die erforderliche Ausführung einer Aktion angegeben haben. In diesem Abschnitt sind einige häufige Probleme und der Umgang damit beschrieben.

### <a name="validation-failed"></a>Fehler bei der Überprüfung

Aufgrund von einigen kürzlich durchgeführten Änderungen an den klassischen Warnungsregeln in Ihrem Abonnement kann das Abonnement nicht migriert werden. Dies ist ein vorübergehendes Problem. Sie können die Migration neu starten, nachdem der Migrationsstatus nach einigen Tagen wieder **Bereit für die Migration** lautet.

### <a name="policy-or-scope-lock-preventing-us-from-migrating-your-rules"></a>Richtlinien- oder Bereichssperre verhindert die Migration Ihrer Regeln

Im Rahmen der Migration werden neue Metrikwarnungen und neue Aktionsgruppen erstellt und anschließend klassische Warnungsregeln gelöscht. Es besteht aber entweder eine Richtlinien- oder Bereichssperre, die verhindert, dass wir Ressourcen erstellen können. Je nach Richtlinien- oder Bereichssperre können einige oder alle Regeln nicht migriert werden. Sie können dieses Problem beheben, indem Sie die Richtlinien- oder Bereichssperre vorübergehend aufheben und die Migration erneut auslösen.

## <a name="next-steps"></a>Nächste Schritte

- [How to use the migration tool](alerts-using-migration-tool.md) (Verwenden des Migrationstools)
- [Vorbereiten der Migration](alerts-prepare-migration.md)
