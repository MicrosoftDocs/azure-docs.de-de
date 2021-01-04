---
title: Einrichten von Warnungen zur Verfügbarkeit mit Azure Application Insights | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Webtests in Application Insights einrichten. Erhalten Sie Benachrichtigungen, wenn eine Website nicht mehr zur Verfügung steht oder langsam reagiert.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/19/2019
ms.reviewer: sdash
ms.openlocfilehash: a0e8540d9488e9cc3756eb09c69349ac90d20b83
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93043389"
---
# <a name="availability-alerts"></a>Verfügbarkeitswarnungen

[Azure Application Insights](./app-insights-overview.md) sendet regelmäßig Webanforderungen von verschiedenen Punkten auf der ganzen Welt an Ihre Anwendung. Außerdem kann dieser Dienst Sie warnen, wenn Ihre Anwendung nicht oder zu langsam reagieren sollte.

## <a name="enable-alerts"></a>Aktivieren von Warnungen

Warnungen werden jetzt zwar standardmäßig automatisch aktiviert, aber Sie müssen zunächst einen Verfügbarkeitstest erstellen, damit diese vollständig konfiguriert werden können.

![Erstellen einer Erfahrung](./media/availability-alerts/create-test.png)

> [!NOTE]
>  Bei den [neuen einheitlichen Warnungen](../platform/alerts-overview.md) **müssen** der Schweregrad der Warnungsregel sowie die Benachrichtigungseinstellungen mit [Aktionsgruppen](../platform/action-groups.md) in der Warnungsumgebung konfiguriert werden. Ohne die folgenden Schritten erhalten Sie nur portalinterne Benachrichtigungen.

1. Nach dem Speichern des Verfügbarkeitstests, klicken Sie auf der Registerkarte „Details“ auf die Auslassungspunkte neben dem Test, den Sie gerade erstellt haben. Klicken Sie auf „Warnung bearbeiten“.

   ![Screenshot der im Menü ausgewählten Option „Warnung bearbeiten“](./media/availability-alerts/edit-alert.png)

2. Legen Sie den gewünschten Schweregrad, die Regelbeschreibung und vor allem die Aktionsgruppe fest, die über die Benachrichtigungseinstellungen verfügt, die Sie für diese Warnungsregel verwenden möchten.

   ![Screenshot mit der Seite „Regelverwaltung“, auf der die Regel bearbeitet werden kann](./media/availability-alerts/set-action-group.png)

> [!NOTE]
> Über diese Benutzeroberfläche erstellte Verfügbarkeitswarnungen sind zustandsbasiert. Dies bedeutet, dass bei Erfüllung der Warnungskriterien eine einzelne Warnung generiert wird, wenn die Website als nicht mehr verfügbar erkannt wird. Falls die Website bei der nächsten Auswertung der Warnungskriterien immer noch nicht verfügbar ist, wird keine neue Warnung generiert. Falls Ihre Website also eine Stunde lang ausgefallen war und Sie eine E-Mail-Benachrichtigung eingerichtet haben, erhalten Sie nur eine E-Mail nach dem Ausfall der Website und dann eine weitere E-Mail, wenn die Website wieder verfügbar ist. Sie erhalten keine fortlaufenden Benachrichtigungen mit dem Hinweis, dass die Website immer noch nicht verfügbar ist.

### <a name="alert-on-x-out-of-y-locations-reporting-failures"></a>Warnung bei X von Y Standorten, die Fehler melden

Die Warnungsregel „X von Y Standorten“ ist in der [neuen einheitlichen Warnungserfahrung](../platform/alerts-overview.md) standardmäßig aktiviert, wenn Sie einen neuen Verfügbarkeitstest erstellen. Sie können sich dagegen entscheiden, indem Sie die „klassische“ Option auswählen oder die Warnungsregel deaktivieren.

> [!NOTE]
> Konfigurieren Sie die Aktionsgruppen so, dass sie Benachrichtigungen empfangen, wenn die Warnung ausgelöst wird, indem Sie die oben beschriebenen Schritte ausführen. Ohne diese Schritte erhalten Sie nur portalinterne Benachrichtigungen, wenn die Regel ausgelöst wird.
>

### <a name="alert-on-availability-metrics"></a>Warnung bei Verfügbarkeitsmetriken

Mithilfe der [neuen einheitlichen Warnungen](../platform/alerts-overview.md) können Sie sowohl bei segmentierter aggregierter Verfügbarkeit als auch bei Testdauermetriken warnen:

1. Wählen Sie eine Application Insights-Ressource in der Metrikenerfahrung aus, und wählen Sie eine Verfügbarkeitsmetrik aus:

    ![Auswahl einer Verfügbarkeitsmetrik](./media/availability-alerts/select-metric.png)

2. Wenn Sie die Option „Warnungen“ über das Menü konfigurieren, gelangen Sie zur neuen Erfahrung, wo Sie bestimmte Tests oder Standorte auswählen können, für die Warnungsregeln eingerichtet werden sollen. Sie können hier außerdem die Aktionsgruppen für diese Warnungsregel konfigurieren.

### <a name="alert-on-custom-analytics-queries"></a>Warnung bei benutzerdefinierten Analyseabfragen

Mithilfe der [neuen einheitlichen Warnungen](../platform/alerts-overview.md) können Sie bei [benutzerdefinierten Protokollabfragen](../platform/alerts-unified-log.md) warnen. Mit benutzerdefinierten Abfragen können Sie bei jeder beliebigen Bedingung warnen, die Ihnen hilft, das zuverlässigste Signal für Verfügbarkeitsprobleme abzurufen. Dies ist auch anwendbar, wenn Sie benutzerdefinierte Verfügbarkeitsergebnisse mithilfe des TrackAvailability SDK senden.

> [!Tip]
> Die Metriken zu Verfügbarkeitsdaten umfassen alle benutzerdefinierten Verfügbarkeitsergebnisse, die Sie möglicherweise durch Aufrufen unseres TrackAvailability SDKs übermitteln. Sie können die Unterstützung für Warnungen bei Metriken verwenden, um bei benutzerdefinierten Verfügbarkeitsergebnissen zu warnen.
>

## <a name="automate-alerts"></a>Automatisieren von Warnungen

Informationen zur Automatisierung dieses Prozesses mit Azure Resource Manager-Vorlagen finden Sie in der Dokumentation zum [Erstellen einer Metrikwarnung mit Resource Manager-Vorlagen](../platform/alerts-metric-create-templates.md#template-for-an-availability-test-along-with-a-metric-alert).

## <a name="troubleshooting"></a>Problembehandlung

[Artikel zur Problembehandlung](troubleshoot-availability.md) (Dedicated)

## <a name="next-steps"></a>Nächste Schritte

* [Multi-step web tests (Mehrstufige Webtests)](availability-multistep.md)
* [Url ping web tests (URL-Pingtests)](monitor-web-app-availability.md)

