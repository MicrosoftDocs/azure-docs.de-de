---
title: Erstellen, Anzeigen und Verwalten von Metrikwarnungen mit Azure Monitor
description: Erfahren Sie, wie Sie mit dem Azure-Portal oder der CLI Metrikwarnungsregeln erstellen, anzeigen und verwalten können.
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 08/11/2020
ms.subservice: alerts
ms.openlocfilehash: 8b1015573e4c01f01d82c7c152b66db80bef0aee
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342160"
---
# <a name="create-view-and-manage-metric-alerts-using-azure-monitor"></a>Erstellen, Anzeigen und Verwalten von Metrikwarnungen mit Azure Monitor

Metrikwarnungen in Azure Monitor bieten eine Möglichkeit, benachrichtigt zu werden, wenn eine Ihrer Metriken einen Schwellenwert überschreitet. Die Metrikwarnungen funktionieren mit einer Reihe von mehrdimensionalen Plattformmetriken, benutzerdefinierten Metriken sowie Standard- und benutzerdefinierten Metriken von Application Insights. In diesem Artikel wird beschrieben, wie Sie Metrikwarnungsregeln über das Azure-Portal und die Azure CLI erstellen, anzeigen und verwalten können. Sie können Metrikwarnungsregeln auch mithilfe von Azure Resource Manager-Vorlagen erstellen, die in [einem separaten Artikel](alerts-metric-create-templates.md) beschrieben sind.

Sie können weitere Informationen zur Funktionsweise von Metrikwarnungen in der [Übersicht zu Metrikwarnungen](alerts-metric-overview.md) erhalten.

## <a name="create-with-azure-portal"></a>Erstellen mit dem Azure-Portal

Im folgenden Verfahren wird beschrieben, wie Sie eine Metrikwarnungsregel im Azure-Portal erstellen:

1. Klicken Sie im [Azure-Portal](https://portal.azure.com) auf **Überwachen** . Das Blatt „Überwachen“ konsolidiert alle Ihre Überwachungseinstellungen und Daten in einer Ansicht.

2. Klicken Sie auf **Warnungen** und dann auf **+ Neue Warnungsregel** .

    > [!TIP]
    > Die meisten Ressourcenblätter verfügen auch über **Warnungen** in ihrem Ressourcenmenü (unter **Überwachung** ). Sie können auch von dort aus Benachrichtigungen erstellen.

3. Klicken Sie auf **Ziel auswählen** und wählen Sie im geladenen Kontextbereich eine Zielressource aus, für die Sie eine Warnungsregel erstellen möchten. Verwenden Sie die Dropdownlisten **Abonnement** und **Ressourcentyp** , um die zu überwachende Ressource zu finden. Sie können auch die Suchleiste verwenden, um Ihre Ressource zu finden.

4. Wenn die ausgewählte Ressource über Metriken verfügt, für die Sie Warnungen erstellen können, enthält die Option **Verfügbare Signale** unten rechts entsprechende Metriken. Sie können die vollständige Liste der für Metrikwarnungen unterstützten Ressourcentypen in diesem [Artikel](./alerts-metric-near-real-time.md#metrics-and-dimensions-supported) einsehen.

5. Nachdem Sie eine Zielressource ausgewählt haben, klicken Sie auf **Bedingung hinzufügen** .

6. Es wird eine Liste der für die Ressource unterstützten Signale angezeigt. Wählen Sie die Metrik aus, für die Sie eine Warnung erstellen möchten.

7. Es wird ein Diagramm für die Metrik für die letzten sechs Stunden angezeigt. Verwenden Sie die Dropdownliste **Diagrammzeitraum** , um einen längeren Verlauf für die Metrik anzuzeigen.

8. Wenn die Metrik über Dimensionen verfügt, wird eine Dimensionstabelle angezeigt. Wählen Sie mindestens einen Wert pro Dimension aus.
    - Die angezeigten Dimensionswerte basieren auf Metrikdaten der letzten drei Tagen.
    - Wenn der gesuchte Dimensionswert nicht angezeigt wird, klicken Sie auf „+“, um einen benutzerdefinierten Wert hinzuzufügen.
    - Sie können für jede der Dimensionen auch * *\* auswählen* _ verwenden. Mithilfe von _*\* auswählen*_ wird die Auswahl auf alle aktuellen und zukünftigen Werte für eine Dimension skaliert.

    Die Metrikwarnungsregel wertet die Bedingung für alle ausgewählten Wertekombinationen aus. [Erfahren Sie mehr über die Funktionsweise von Warnungen für mehrdimensionale Metriken](alerts-metric-overview.md).

9. Wählen Sie den Typ für _ *Schwellenwert* *, den **Operator** und den **Aggregationstyp** aus. Dadurch wird die Logik bestimmt, die die Metrikwarnungsregel auswerten soll.
    - Wenn Sie einen Schwellenwert vom Typ **Statisch** verwenden, legen Sie auch einen **Schwellenwert** fest. Anhand des Metrikdiagramms können Sie einen angemessenen Schwellenwert ermitteln.
    - Wenn Sie einen Schwellenwert vom Typ **Dynamisch** verwenden, legen Sie auch die **Schwellenwertempfindlichkeit** fest. Das Metrikdiagramm zeigt die berechneten Schwellenwerte basierend auf aktuellen Daten an. [Erfahren Sie mehr über den Bedingungstyp „Dynamische Schwellenwerte“ und Empfindlichkeitsoptionen.](alerts-dynamic-thresholds.md)

10. Sie können die Bedingung optional noch verfeinern, indem Sie die **Aggregationsgranularität** und die **Häufigkeit der Auswertung** anpassen. 

11. Klicken Sie auf **Fertig** .

12. Optional können Sie weitere Kriterien hinzufügen, wenn Sie eine komplexe Warnungsregel überwachen möchten. Derzeit stehen Benutzern Warnungsregeln mit dynamischen Schwellenwertkriterien als einzigem Kriterium zur Verfügung.

13. Geben Sie **Warnungsdetails** wie **Warnungsregelname** , **Beschreibung** und **Schweregrad** ein.

14. Fügen Sie der Warnung eine Aktionsgruppe hinzu, indem Sie entweder eine bestehende Aktionsgruppe auswählen oder eine neue Aktionsgruppe erstellen.

15. Klicken Sie auf **Fertig** , um die Metrikwarnungsregel zu speichern.

> [!NOTE]
> Über das Portal erstellte Metrikwarnungsregeln werden in derselben Ressourcengruppe erstellt wie die Zielressource.

## <a name="view-and-manage-with-azure-portal"></a>Anzeigen und Verwalten mit dem Azure-Portal

Sie können Metrikwarnungsregeln anzeigen und verwalten, indem Sie das Blatt „Regeln verwalten“ unter „Warnungen“ verwenden. Das folgende Verfahren zeigt Ihnen, wie Sie Ihre Metrikwarnungsregeln anzeigen und eine davon bearbeiten können.

1. Navigieren Sie im Azure-Portal zu **Überwachen** .

2. Klicken Sie auf **Warnungen** und **Regeln verwalten** .

3. Auf dem Blatt **Regeln verwalten** können Sie Ihre gesamten Warnungsregeln abonnementübergreifend anzeigen. Sie können die Regeln mithilfe von **Ressourcengruppe** , **Ressourcentyp** und **Ressource** weiter filtern. Wenn Sie nur Metrikwarnungen anzeigen möchten, wählen Sie **Signaltyp** als Metrik aus.

    > [!TIP]
    > Auf dem Blatt **Regeln verwalten** können Sie mehrere Warnungsregeln auswählen und diese aktivieren/deaktivieren. Dies kann hilfreich sein, wenn bestimmte Zielressourcen gewartet werden müssen.

4. Klicken Sie auf den Namen der Metrikwarnungsregel, die Sie bearbeiten möchten.

5. Klicken Sie unter „Regel bearbeiten“ auf die **Warnungskriterien** , die Sie bearbeiten möchten. Sie können die Felder für Metrik, Schwellenwertbedingung und auch andere Felder bei Bedarf ändern.

    > [!NOTE]
    > Sie können **Zielressource** und **Warnungsregelname** nicht bearbeiten, nachdem die Metrikwarnung erstellt wurde.

6. Klicken Sie auf **Fertig** , um Ihre Änderungen zu speichern.


## <a name="with-azure-cli"></a>Mit der Azure-Befehlszeilenschnittstelle

In den vorangegangenen Abschnitten wurde beschrieben, wie Sie über das Azure-Portal Metrikwarnungsregeln erstellen, anzeigen und verwalten. In diesem Abschnitt wird beschrieben, wie Sie dasselbe mit der plattformübergreifenden [Azure-Befehlszeilenschnittstelle](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) (Azure CLI) erreichen können. Die schnellste Möglichkeit, mit der Verwendung der Azure CLI zu beginnen, ist über [Azure Cloud Shell](../../cloud-shell/overview.md?view=azure-cli-latest). In diesem Artikel verwendeten wir Cloud Shell.

1. Wechseln Sie zum Azure-Portal, und klicken Sie auf **Cloud Shell** .

2. An der Eingabeaufforderung können Sie Befehle mit der Option ``--help`` verwenden, um mehr über den Befehl und seine Verwendung zu erfahren. Der folgende Befehl zeigt Ihnen z. B. die Liste der verfügbaren Befehle zum Erstellen, Anzeigen und Verwalten von Metrikwarnungsmeldungen an.

    ```azurecli
    az monitor metrics alert --help
    ```

3. Sie können eine einfache Metrikwarnungsregel erstellen, die überwacht, ob der durchschnittliche Wert für „CPU in Prozent“ auf einer VM größer als 90% ist.

    ```azurecli
    az monitor metrics alert create -n {nameofthealert} -g {ResourceGroup} --scopes {VirtualMachineResourceID} --condition "avg Percentage CPU > 90" --description {descriptionofthealert}
    ```

4. Sie können alle Metrikwarnungsmeldungen in einer Ressourcengruppe mit dem folgenden Befehl anzeigen.

    ```azurecli
    az monitor metrics alert list  -g {ResourceGroup}
    ```

5. Sie können die Details einer bestimmten Metrikwarnungsregel über den Namen oder die Ressourcen-ID der Regel anzeigen.

    ```azurecli
    az monitor metrics alert show -g {ResourceGroup} -n {AlertRuleName}
    ```

    ```azurecli
    az monitor metrics alert show --ids {RuleResourceId}
    ```

6. Sie können eine Metrikwarnungsregel mit dem folgenden Befehl deaktivieren.

    ```azurecli
    az monitor metrics alert update -g {ResourceGroup} -n {AlertRuleName} --enabled false
    ```

7. Sie können eine Metrikwarnungsregel mit dem folgenden Befehl löschen.

    ```azurecli
    az monitor metrics alert delete -g {ResourceGroup} -n {AlertRuleName}
    ```

## <a name="with-powershell"></a>Mit PowerShell

In Metrikwarnungsregeln stehen folgende dedizierte PowerShell-Cmdlets zur Verfügung:

- [Add-AzMetricAlertRuleV2](/powershell/module/az.monitor/add-azmetricalertrulev2): Hiermit wird eine neue Metrikwarnungsregel erstellt oder eine vorhandene Metrikwarnungsregel aktualisiert.
- [Get-AzMetricAlertRuleV2](/powershell/module/az.monitor/get-azmetricalertrulev2): Dient zum Abrufen von Metrikwarnungsregeln.
- [Remove-AzMetricAlertRuleV2](/powershell/module/az.monitor/remove-azmetricalertrulev2): Dient zum Löschen einer Metrikwarnungsregel.

## <a name="with-rest-api"></a>Mit der REST-API

- [Erstellen oder aktualisieren:](/rest/api/monitor/metricalerts/createorupdate) Hiermit wird eine neue Metrikwarnungsregel erstellt oder eine vorhandene Metrikwarnungsregel aktualisiert.
- [Get](/rest/api/monitor/metricalerts/get) (Abrufen): Dient zum Abrufen einer bestimmten Metrikwarnungsregel.
- [List By Resource Group](/rest/api/monitor/metricalerts/listbyresourcegroup) (Nach Ressourcengruppe gruppieren): Hiermit wird eine Liste der Metrikwarnungsregeln in einer bestimmten Ressourcengruppe abgerufen.
- [List By Subscription](/rest/api/monitor/metricalerts/listbysubscription) (Nach Abonnement auflisten): Hiermit wird eine Liste der Metrikwarnungsregeln in einem bestimmten Abonnement abgerufen.
- [Update](/rest/api/monitor/metricalerts/update) (Aktualisieren): Dient zum Aktualisieren einer Metrikwarnungsregel.
- [Löschen:](/rest/api/monitor/metricalerts/delete) Dient zum Löschen einer Metrikwarnungsregel.

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen von Metrikwarnungen mithilfe von Azure Resource Manager-Vorlagen](./alerts-metric-create-templates.md)
- [Informationen zur Funktionsweise von Metrikwarnungen](alerts-metric-overview.md)
- [Informationen zur Funktionsweise von Metrikwarnungen mit Bedingung „Dynamische Schwellenwerte“](alerts-dynamic-thresholds.md)
- [Informationen zum Webhook-Schema für Metrikwarnungen](./alerts-metric-near-real-time.md#payload-schema)
- [Behandeln von Problemen mit Metrikwarnungen](alerts-troubleshoot-metric.md)
