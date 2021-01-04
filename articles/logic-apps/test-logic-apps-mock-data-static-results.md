---
title: Testen von Logik-Apps mit simulierten Daten
description: Einrichten von statischen Ergebnissen zum Testen von Logik-Apps mit Simulationsdaten ohne Auswirkungen auf die Produktionsumgebungen
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/13/2019
ms.openlocfilehash: 711d753203aeaeba50cea692053a37fcab2e9c7b
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "93027702"
---
# <a name="test-logic-apps-with-mock-data-by-setting-up-static-results"></a>Testen von Logik-Apps mit Simulationsdaten durch die Einrichtung von statischen Ergebnissen

Beim Testen von Logik-Apps kann es verschiedene Gründe dafür geben, warum möglicherweise noch keine Apps, Dienste und Systeme aufgerufen werden sollen. In einem solchen Szenario müssen Sie für gewöhnlich verschiedene Bedingungspfade ausführen, Fehler erzwingen, bestimmte Antwortnachrichten bereitstellen oder sogar versuchen, einige Schritte zu überspringen. Wenn Sie aber statische Ergebnisse für eine Aktion in Ihrer Logik-App einrichten, können Sie Simulationsdaten als Ausgabe für diese Aktion generieren. Wenn statische Ergebnisse für eine Aktion aktiviert werden, wird diese nicht ausgeführt. Stattdessen werden Simulationsdaten zurückgegeben.

Wenn Sie beispielsweise statische Ergebnisse für die Outlook 365-Aktion „E-Mail senden“ einrichten, gibt die Engine für Logik-Apps nur die Simulationsdaten zurück, die Sie als statische Ergebnisse angegeben haben. Dabei wird weder Outlook aufgerufen noch wird eine E-Mail gesendet.

## <a name="prerequisites"></a>Voraussetzungen

* ein Azure-Abonnement Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich <a href="https://azure.microsoft.com/free/" target="_blank">für ein kostenloses Azure-Konto registrieren</a>.

* Grundlegende Kenntnisse über die [Erstellung von Logik-Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Die Logik-App, für die Sie statische Ergebnisse einrichten möchten

<a name="set-up-static-results"></a>

## <a name="set-up-static-results"></a>Einrichten von statischen Ergebnissen

1. Öffnen Sie, falls noch nicht geschehen, Ihre Logik-App über das [Azure-Portal](https://portal.azure.com) im Designer für Logik-Apps.

1. Führen Sie für die Aktion, für die Sie statische Ergebnisse einrichten möchten, die folgenden Schritte aus: 

   1. Klicken Sie in der Aktion oben rechts zuerst auf die Auslassungspunkte ( *...* ) und dann auf **Statisches Ergebnis**. Z. B.:

      ![Klicken Sie auf „Statisches Ergebnis“ > „Statisches Ergebnis aktivieren“](./media/test-logic-apps-mock-data-static-results/select-static-result.png)

   1. Aktivieren Sie die Option **Statisches Ergebnis aktivieren**. Geben Sie für die erforderlichen Eigenschaften (mit einem * gekennzeichnet) die Simulationswerte ein, die Sie als Antwort auf die Aktion zurückgeben möchten.

      Die folgenden Eigenschaften sind beispielsweise für die HTTP-Aktion erforderlich:

      | Eigenschaft | BESCHREIBUNG |
      |----------|-------------|
      | **Status** | Der Status der Aktion, der zurückgegeben werden soll |
      | **Statuscode** | Der genaue Statuscode, der zurückgeben werden soll |
      | **Headers** | Der Headerinhalt, der zurückgegeben werden soll |
      |||

      ![Aktivieren Sie die Option „Statisches Ergebnis aktivieren“](./media/test-logic-apps-mock-data-static-results/enable-static-result.png)

      Wenn Sie die Simulationsdaten im JSON-Format (JavaScript Object Notation) eingeben, klicken Sie auf **Zum JSON-Modus wechseln** (![Auf „Zum JSON-Modus wechseln“ klicken](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)).

   1. Optionale Eigenschaften finden Sie in der Liste **Optionale Felder auswählen**. Öffnen Sie diese, und wählen Sie die Eigenschaften aus, die simuliert werde sollen.

      ![Optionale Eigenschaften auswählen](./media/test-logic-apps-mock-data-static-results/optional-properties.png)

1. Klicken Sie zum Speichern auf **Fertig**.

   Oben rechts in der Aktion wird dann in der Titelleiste ein Becherglas angezeigt (![Symbol für statische Ergebnisse](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)). Daran können Sie erkennen, dass Sie die statischen Ergebnisse aktiviert haben.

   ![Symbol, das angezeigt wird, wenn statische Ergebnisse aktiviert wurden](./media/test-logic-apps-mock-data-static-results/static-results-enabled.png)

   Informationen dazu, wie Sie frühere Ausführungen finden, bei denen Simulationsdaten verwendet werden, erhalten Sie weiter unten in diesem Artikel im Abschnitt [Suchen von Ausführungen mit statischen Ergebnissen](#find-runs-mock-data).

<a name="reuse-sample-outputs"></a>

## <a name="reuse-previous-outputs"></a>Frühere Ausgaben wiederverwenden

Wenn es von einer früheren Ausführung Ihrer Logik-App noch Ausgaben gibt, die Sie als Simulationsausgaben wiederverwenden können, können Sie diese kopieren und entsprechend einfügen.

1. Öffnen Sie, falls noch nicht geschehen, Ihre Logik-App über das [Azure-Portal](https://portal.azure.com) im Designer für Logik-Apps.

1. Klicken Sie im Hauptmenü Ihrer Logik-App auf **Übersicht**.

1. Wählen Sie im Abschnitt **Ausführungsverlauf** die gewünschte Ausführung der Logik-App aus.

1. Suchen Sie im Workflow der Logik-App die Aktion mit den gewünschten Ausgaben, und erweitern Sie diese.

1. Klicken Sie auf den Link **Unformatierte Ausgaben anzeigen**.

1. Kopieren Sie entweder das ganze JSON-Objekt oder den genauen Unterabschnitt, den Sie verwenden möchten, z. B. den Abschnitt „Ausgaben“ oder sogar nur den Abschnitt „Header“.

1. Führen Sie alle unter [Einrichten von statischen Ergebnissen](#set-up-static-results) beschriebenen notwendigen Schritte aus, um das Feld **Statisches Ergebnis** zu öffnen.

1. Wählen Sie anschließend eine der folgenden beiden Optionen aus **:**

   * Klicken Sie auf **Zum JSON-Modus wechseln** (![Auf „Zum JSON-Modus wechseln“ klicken](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)), um ein ganzes JSON-Objekt einzufügen:

     ![Auf „Zum JSON-Modus wechseln“ klicken, um ein ganzes Objekt einzufügen](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete.png)

   * Wenn Sie nur einen JSON-Abschnitt einfügen möchten, klicken Sie neben der Bezeichnung des entsprechenden Abschnitts auf **Zum JSON-Modus wechseln**. Z. B.:

     ![Auf „Zum JSON-Modus wechseln“ klicken, um Ausgaben einzufügen](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-outputs.png)

1. Fügen Sie das zuvor kopierte JSON-Objekt in den JSON-Editor ein.

   ![JSON-Modus](./media/test-logic-apps-mock-data-static-results/json-editing-mode.png)

1. Klicken Sie auf **Fertig** , wenn Sie fertig sind. Stattdessen können Sie auch auf **Editor-Modus wechseln** (Auf ![„Editor-Modus wechseln“](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png) klicken) klicken, um zum Designer zurückzukehren.

<a name="find-runs-mock-data"></a>

## <a name="find-runs-that-use-static-results"></a>Suchen von Ausführungen mit statischen Ergebnissen

Der Ausführungsverlauf Ihrer Logik-App ermittelt die Ausführungen, bei denen statische Ergebnisse von den Aktionen verwendet werden. Gehen Sie wie folgt vor, um diese Ausführungen zu finden:

1. Klicken Sie im Hauptmenü Ihrer Logik-App auf **Übersicht**. 

1. Suchen Sie auf der rechten Seite unter **Ausführungsverlauf** die Spalte **Statische Ergebnisse**. 

   Bei jeder Ausführung, die Aktionen mit Ergebnissen umfasst, ist die Spalte **Statische Ergebnisse** auf **Aktiviert** festgelegt. Z. B.:

   ![Ausführungsverlauf: Spalte „Statische Ergebnisse“](./media/test-logic-apps-mock-data-static-results/run-history.png)

1. Wenn Sie Aktionen anzeigen lassen möchten, die statische Ergebnisse verwenden, wählen Sie die gewünschte Ausführung aus, für die die Spalte **Statische Ergebnisse** auf **Aktiviert** festgelegt ist.

   Aktionen, die statische Ergebnisse verwenden, werden mit einem Becherglas gekennzeichnet (![Symbol für statische Ergebnisse](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)). Z. B.:

   ![Ausführungsverlauf: Aktionen, die statische Ergebnisse verwenden](./media/test-logic-apps-mock-data-static-results/static-results-enabled-run-details.png)

## <a name="disable-static-results"></a>Statische Ergebnisse deaktivieren

Wenn Sie die statischen Ergebnisse deaktivieren, werden die Werte Ihres letzten Setups nicht verworfen. Wenn Sie die statischen Ergebnisse das nächste Mal aktivieren, können Sie wieder auf die in der Vergangenheit verwendeten Werte zurückgreifen.

1. Suchen Sie die Aktion, für die Sie statische Ausgaben deaktivieren möchten. Klicken Sie oben rechts in der Aktion auf das Becherglas (![Symbol für statische Ergebnisse](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)).

   ![Screenshot: Eine HTTP-Aktion, bei der Sie das Becherglassymbol auswählen können](./media/test-logic-apps-mock-data-static-results/disable-static-results.png)

1. Klicken Sie auf **Statisches Ergebnis deaktivieren** > **Fertig**.

   ![Screenshot: Auswählbare Option „Statisches Ergebnis deaktivieren“](./media/test-logic-apps-mock-data-static-results/disable-static-results-button.png)

## <a name="reference"></a>Verweis

Weitere Informationen zu dieser Einstellung in Ihren zugrunde liegenden Workflowdefinitionen finden Sie unter [Statische Ergebnisse – Schemareferenz für Workflowdefinitionssprache](../logic-apps/logic-apps-workflow-definition-language.md#static-results) und [runtimeConfiguration.staticResult – Laufzeitkonfigurationseinstellungen](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-configuration-settings)

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu [Azure Logic Apps](../logic-apps/logic-apps-overview.md)