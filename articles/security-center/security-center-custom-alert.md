---
title: Benutzerdefinierte Warnungsregeln in Azure Security Center | Microsoft-Dokumentation
description: In diesem Dokument erfahren Sie, wie Sie benutzerdefinierte Warnungsregeln in Azure Security Center erstellen.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f335d8c4-0234-4304-b386-6f1ecda07833
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2018
ms.author: rkarlin
ms.openlocfilehash: a5deee4209001d8c2212033c2d547d7c4199bd25
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65192621"
---
# <a name="custom-alert-rules-in-azure-security-center-preview"></a>Benutzerdefinierte Warnungsregeln in Azure Security Center (Vorschauversion)
In diesem Dokument erfahren Sie, wie Sie benutzerdefinierte Warnungsregeln in Azure Security Center erstellen.

> [!NOTE]
> Benutzerdefinierte Warnungen werden am 30. Juni 2019 eingestellt.

## <a name="retirement-of-custom-alert-rules-in-azure-security-center"></a>Einstellung von benutzerdefinierten Warnungsregeln in Azure Security Center

Die Funktion der benutzerdefinierten Warnungen wird am 30. Juni 2019 aufgrund der Außerbetriebnahme der zugrunde liegenden Infrastruktur eingestellt. Im Zeitraum bis zur Einstellung können Benutzer vorhandene benutzerdefinierte Warnungsregeln bearbeiten, aber keine neuen Regeln mehr hinzufügen.
Den Benutzern wird empfohlen, eine der folgenden Aktionen auszuführen:
- Integrieren von [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) mit einem Mausklick, um vorhandene Warnungen automatisch zu migrieren und neue Warnungen zu erstellen
- Neuerstellen von Warnungen mit Azure Monitor-Protokollwarnungen
                                     
Wenn Sie Ihre vorhandenen Warnungen beibehalten und nach Azure Sentinel migrieren möchten, [starten Sie Azure Sentinel](https://portal.azure.com/#create/Microsoft.ASI/preview). Wählen Sie als ersten Schritt den Arbeitsbereich aus, in dem Ihre benutzerdefinierten Warnungen gespeichert sind. Wählen Sie dann das Menüelement „Analytics“ aus, damit Ihre Warnungen automatisch migriert werden.

> [!NOTE]
> Die Migration von benutzerdefinierten Warnungen nach Azure Sentinel ist eine einmalige Migration aller Ihrer benutzerdefinierten Warnungen im ausgewählten Arbeitsbereich. Nach Abschluss der Migration kann auf die benutzerdefinierten Warnungen für diesen ausgewählten Arbeitsbereich über Azure Security Center nicht mehr zugegriffen werden.
>
> Benutzerdefinierte Warnungen mithilfe von Abfragen des Typs [Search](https://docs.microsoft.com/azure/azure-monitor/log-query/search-queries)- oder [Union](https://docs-analytics-eus.azurewebsites.net/queryLanguage/query_language_unionoperator.html)-Anweisungen werden in Azure Sentinel nicht unterstützt und deshalb nicht migriert. Bitte bearbeiten Sie diese Warnungen, bevor Sie die Migration durchführen.

Wenn Sie Ihre Warnungen unter Verwendung von Azure Monitor-Protokollwarnungen neu erstellen möchten, lesen Sie den Artikel: [Erstellen, Anzeigen und Verwalten von Protokollwarnungen mithilfe von Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log). Er enthält Anleitungen zum Erstellen von Protokollwarnungen. Zum Anzeigen einer allgemeinen Übersicht über Protokollwarnungen in Azure Monitor klicken Sie [hier](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log).

## <a name="what-are-custom-alert-rules-in-security-center"></a>Was sind benutzerdefinierte Warnungsregeln in Security Center?

In Security Center steht eine Reihe vordefinierter [Sicherheitswarnungen](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) zur Verfügung, die ausgelöst werden, wenn eine Bedrohung oder eine verdächtige Aktivität erkannt wird. Manchmal empfiehlt sich allerdings die Erstellung einer benutzerdefinierten Warnung, um auf spezifische Anforderungen Ihrer Umgebung einzugehen.

Mithilfe benutzerdefinierter Warnungsregeln in Security Center können Sie neue Sicherheitswarnungen auf der Grundlage von Daten definieren, die bereits in Ihrer Umgebung gesammelt werden. Sie können Abfragen erstellen und die Ergebnisse dieser Abfragen als Kriterien für die benutzerdefinierte Regel verwenden, sodass die Regel ausgeführt wird, wenn die Kriterien erfüllt sind. Bei der Erstellung Ihrer benutzerdefinierten Abfragen können Sie Sicherheitsereignisse von Computern, Protokolle von Partnersicherheitslösungen oder über APIs erfasste Daten verwenden.

> [!NOTE]
> Benutzerdefinierte Warnungen werden im [Untersuchungsfeature](security-center-investigation.md) von Security Center nicht unterstützt.
>
>

## <a name="how-to-create-a-custom-alert-rule-in-security-center"></a>Erstellen einer benutzerdefinierten Warnungsregel in Security Center

Öffnen Sie zum Erstellen einer benutzerdefinierten Warnungsregel das Dashboard **Security Center**, und führen Sie die folgenden Schritte aus:

1.  Klicken Sie im linken Bereich unter **Erkennung** auf **Benutzerdefinierte Warnungsregeln** (Vorschauversion).
2.  Klicken Sie auf der Seite **Security Center – Benutzerdefinierte Warnungsregeln** (Vorschauversion) auf **Neue benutzerdefinierte Warnungsregel**.

    ![Benutzerdefinierte Warnung](./media/security-center-custom-alert/security-center-custom-alert-fig1.png)

3.  Die Seite „Benutzerdefinierte Warnungsregel erstellen“ wird angezeigt. Hier stehen folgende Optionen zur Verfügung:

    ![Erstellen](./media/security-center-custom-alert/security-center-custom-alert-fig2.png)

4.  Geben Sie im Feld **Name** den Namen für diese benutzerdefinierte Regel ein.
5.  Geben Sie im Feld **Beschreibung** eine kurze Beschreibung ein, die den Zweck der Regel deutlich macht.
6.  Wählen Sie im Feld **Schweregrad** den gewünschten Schweregrad (hoch, mittel, niedrig) aus.
7.  Wählen Sie im Feld **Abonnement** das Abonnement aus, für das die Regel gelten soll.
8.  Wählen Sie im Feld **Arbeitsbereich** den Arbeitsbereich aus, den Sie mit dieser Regel überwachen möchten, und wählen Sie im Feld **Suchabfrage** die Abfrage zum Abrufen der Ergebnisse aus.

    > [!NOTE]
    > Sie müssen in dem Arbeitsbereich, den Sie zum Speichern Ihrer benutzerdefinierten Warnung auswählen, über Schreibberechtigung verfügen.
    >
    >

    Das Ergebnis der Abfrage löst die Warnung aus. Wenn die eingegebene Abfrage gültig ist, erscheint in der rechten Ecke des Felds ein grünes Häkchen:

    ![Abfragen](./media/security-center-custom-alert/security-center-custom-alert-fig3.png)

10. Wählen Sie im Feld **Zeitraum** den Zeitraum aus, in dem die obige Abfrage ausgeführt wird. Das Suchergebnis am unteren Rand des Felds ändert sich abhängig vom gewählten Zeitraum.

    ![Zeitraum](./media/security-center-custom-alert/security-center-custom-alert-fig4.png)

11. Wählen Sie im Feld **Auswertung** die Frequenz aus, mit der diese Regel ausgewertet und ausgeführt werden soll.
12. Wählen Sie im Feld **Anzahl von Ergebnissen** den gewünschten Operator (größer als oder kleiner als) aus.
13. Geben Sie im Feld **Schwellenwert** eine Zahl als Referenz für den zuvor ausgewählten Operator ein.
14. Aktivieren Sie die Option **Warnungen unterdrücken**, wenn Sie eine Wartezeit bis zum Senden einer weiteren Warnung für diese Regel einrichten möchten.
15. Klicken Sie auf **OK**, um den Vorgang abzuschließen.

Die neu erstellte Warnungsregel wird in der Liste mit den benutzerdefinierten Warnungsregeln angezeigt. Sobald die Bedingungen für diese Regel erfüllt sind, wird eine neue Warnung ausgelöst und auf dem Dashboard **Sicherheitswarnungen** angezeigt.

![Warnung](./media/security-center-custom-alert/security-center-custom-alert-fig5.png)

Wie Sie sehen, sind die bei der Regelerstellung eingerichteten Parameter (Suchabfrage, Schwellenwert usw.) in der Warnung für diese benutzerdefinierte Regel enthalten.

## <a name="see-also"></a>Weitere Informationen
In diesem Dokument haben Sie erfahren, wie Sie eine benutzerdefinierte Warnungsregel in Azure Security Center erstellen. Weitere Informationen zu Azure Security Center finden Sie in den folgenden Quellen:

* [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Hier erfahren Sie, wie Sie Warnungen verwalten und auf Sicherheitsvorfälle in Security Center reagieren.
* [Überwachen der Sicherheitsintegrität in Azure Security Center](security-center-monitoring.md). Hier erfahren Sie, wie Sie die Integrität Ihrer Azure-Ressourcen überwachen.
* [Verstehen der Sicherheitswarnungen in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Hier finden Sie Informationen zu den unterschiedlichen Arten von Sicherheitswarnungen.
* [Azure Security Center – Handbuch zur Problembehandlung](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Hier erfahren Sie, wie Sie allgemeine Probleme in Security Center behandeln.
* [Azure Security Center – häufig gestellte Fragen](security-center-faq.md)festgelegt ist. Enthält häufig gestellte Fragen zur Verwendung des Diensts.
* [Azure Security-Blog](https://blogs.msdn.com/b/azuresecurity/). Hier finden Sie Blogbeiträge zur Sicherheit und Compliance von Azure.
