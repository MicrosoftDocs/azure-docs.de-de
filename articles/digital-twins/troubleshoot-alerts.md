---
title: Einrichten von Warnungen
titleSuffix: Azure Digital Twins
description: Es wird beschrieben, wie Sie Warnungen in Azure Digital Twins-Metriken aktivieren.
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 057a1b08095a71e2fca6119f5b63bfdc787eea88
ms.sourcegitcommit: 9706bee6962f673f14c2dc9366fde59012549649
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94616601"
---
# <a name="troubleshooting-azure-digital-twins-alerts"></a>Problembehandlung von Azure Digital Twins: Alerts

Azure Digital Twins sammelt [Metriken](troubleshoot-metrics.md) für Ihre Dienstinstanz, die Informationen zum Zustand Ihrer Ressourcen bereitstellen. Mit diesen Metriken können Sie die allgemeine Integrität des Azure Digital Twins-Diensts und der damit verbundenen Ressourcen bewerten.

**Warnungen** informieren Sie proaktiv, wenn wichtige Bedingungen in Ihren Metrikdaten gefunden werden. Sie ermöglichen es Ihnen, Probleme zu identifizieren und zu beheben, bevor die Benutzer Ihres Systems sie bemerken. Weitere Informationen zu Warnungen finden Sie unter [*Überblick über Warnungen in Microsoft Azure*](../azure-monitor/platform/alerts-overview.md).

## <a name="turn-on-alerts"></a>Aktivieren von Warnungen

Hier ist beschrieben, wie Sie Warnungen für Ihre Azure Digital Twins-Instanz aktivieren:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrer Azure Digital Twins-Instanz. Sie finden sie, indem Sie ihren Namen auf der Suchleiste des Portals eingeben. 

2. Wählen Sie im Menü die Option **Warnungen** und dann **+ Neue Warnungsregel** aus.

3. Auf der Seite *Warnungsregel erstellen*, die als Nächstes angezeigt wird, können Sie die Anweisungen befolgen, um Bedingungen, die auszulösenden Aktionen und die Warnungsdetails zu definieren.     
    * Die Details unter **Bereich** sollten zusammen mit den Details für Ihre Instanz automatisch eingefügt werden.
    * Sie definieren die Details für **Bedingung** und **Aktionsgruppe**, um Warnungstrigger und die zugehörigen Antworten anzupassen.
    * Geben Sie im Abschnitt **Details zur Warnungsregel** einen Namen und optional eine Beschreibung für Ihre Regel ein. Sie können das Kontrollkästchen _Warnungsregel bei Erstellung aktivieren_ aktivieren, wenn die Warnung aktiviert werden soll, sobald sie erstellt wird.
        - Hier wählen Sie auch eine _Ressourcengruppe_ und einen _Schweregrad_ aus.

4. Wählen Sie die Schaltfläche _Warnungsregel erstellen_ aus, um die Warnungsregel zu erstellen.

:::image type="content" source="media/troubleshoot-alerts/create-alert-rule.png" alt-text="Screenshot: Seite „Warnungsregel erstellen“ mit Abschnitten für Bereich, Bedingung, Aktionsgruppe und Details zur Warnungsregel" lightbox="media/troubleshoot-alerts/create-alert-rule.png":::

Eine Anleitung zum Ausfüllen dieser Felder finden Sie unter [*Überblick über Warnungen in Microsoft Azure*](../azure-monitor/platform/alerts-overview.md). Unten sind einige Beispiele dafür angegeben, welche Schritte für Azure Digital Twins ausgeführt werden müssen.

### <a name="select-conditions"></a>Auswählen von Bedingungen

Hier ist ein Auszug aus dem Prozess *Bedingung auswählen* angegeben, um zu veranschaulichen, welche Arten von Warnungssignalen für Azure Digital Twins verfügbar sind. Auf dieser Seite können Sie nach dem Typ des Signals filtern und das gewünschte Signal in einer Liste auswählen.

:::image type="content" source="media/troubleshoot-alerts/configure-signal-logic.png" alt-text="Screenshot: Erste Seite „Signallogik konfigurieren“. Das Feld „Signaltyp“ ist hervorgehoben, über das Metriken und Aktivitätsprotokolle ausgewählt werden können, und unterhalb davon können Sie eine Liste mit Metriken auswählen.":::

Nachdem Sie ein Signal ausgewählt haben, werden Sie aufgefordert, die Logik der Warnung zu konfigurieren. Sie können nach einer Dimension filtern und einen Schwellenwert für Ihre Warnung sowie die Häufigkeit der Überprüfungen für die Bedingung festlegen. Hier ist ein Beispiel für die Einrichtung einer Warnung für den Fall angegeben, dass der Durchschnittswert für die Metrik „Routingfehlerrate“ über 5 % steigt.

:::image type="content" source="media/troubleshoot-alerts/configure-signal-logic-2.png" alt-text="Screenshot: Zweite Seite „Signallogik konfigurieren“. Auf der Seite wird der Metrikverlauf angezeigt, sie verfügt über einen Bereich zum Filtern nach einer Dimension, z. B. Event Grid-Vorgänge, und es ist ein Abschnitt zum Definieren der Warnungslogik vorhanden (Beispiel: „Durchschnittswert größer als 5“).":::

### <a name="verify-success"></a>Überprüfen des erfolgreichen Abschlusses

Nach dem Einrichten von Warnungen werden diese auf der Seite *Warnungen* für Ihre Instanz angezeigt.
 
:::image type="content" source="media/troubleshoot-alerts/alerts-post.png" alt-text="Screenshot: Seite „Warnungen“ und Schaltfläche zum Hinzufügen. Eine Warnung ist konfiguriert." lightbox="media/troubleshoot-alerts/alerts-post.png":::

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu Warnungen mit Azure Monitor finden Sie unter [*Überblick über Warnungen in Microsoft Azure*](../azure-monitor/platform/alerts-overview.md).
* Informationen zu den Azure Digital Twins-Metriken finden Sie unter [*Problembehandlung: Anzeigen von Metriken mit Azure Monitor*](troubleshoot-metrics.md).
* Informationen zur Aktivierung der Diagnoseprotokollierung für Ihre Metriken finden Sie unter [*Problembehandlung: Einrichten der Diagnose*](troubleshoot-diagnostics.md).
