---
title: Erstellen, Anzeigen und Verwalten von klassischen Metrikwarnungen mit Azure Monitor
description: Erfahren Sie, wie Sie mit dem Azure-Portal, der CLI oder PowerShell klassische Metrikwarnungsregeln erstellen, anzeigen und verwalten können.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: snmuvva
ms.openlocfilehash: 4a225dbc8e84d65a6ea25f63627599e5bb7d2ced
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66129752"
---
# <a name="create-view-and-manage-classic-metric-alerts-using-azure-monitor"></a>Erstellen, Anzeigen und Verwalten von klassischen Metrikwarnungen mit Azure Monitor

Klassische Metrikwarnungen in Azure Monitor bieten eine Möglichkeit, benachrichtigt zu werden, wenn eine Ihrer Metriken einen Schwellenwert überschreitet. Klassische Metrikwarnungen sind eine ältere Funktionalität, die es ermöglicht, nur bei nichtdimensionalen Metriken zu warnen. Es gibt eine neuere Funktionalität namens Metrikwarnungen, die die Funktionalität gegenüber klassischen Metrikwarnungen verbessert hat. Weitere Informationen zur Funktionsweise von neuen Metrikwarnungen finden Sie in der [Übersicht zu Metrikwarnungen](../../azure-monitor/platform/alerts-metric-overview.md). In diesem Artikel wird beschrieben, wie Sie klassische Metrikwarnungsregeln über das Azure-Portal, die Azure CLI und PowerShell erstellen, anzeigen und verwalten können.

## <a name="with-azure-portal"></a>Mit dem Azure-Portal

1. Suchen Sie im [Portal](https://portal.azure.com/) die Ressource, die Sie überwachen möchten, und wählen Sie sie aus.

2. Wählen Sie im Abschnitt **ÜBERWACHUNG** die Option **Warnungen (klassisch)** aus. Text und Symbol können je nach Ressource geringfügig variieren. Wenn Sie **Warnungen (klassisch)** hier nicht finden, befindet sich die Option möglicherweise unter **Warnungen** oder **Warnungsregeln**.

    ![Überwachung](media/alerts-classic-portal/AlertRulesButton.png)

3. Wählen Sie den Befehl **Metrikwarnung hinzufügen (klassisch)** aus, und füllen Sie die Felder aus.

    ![Warnung hinzufügen](media/alerts-classic-portal/AddAlertOnlyParamsPage.png)

4. Geben Sie Ihrer Warnungsregel einen **Namen**. Wählen Sie dann eine **Beschreibung** aus, die auch in Benachrichtigungs-E-Mails angezeigt wird.

5. Wählen Sie die **Metrik** aus, die Sie überwachen möchten. Wählen Sie dann eine **Bedingung** und einen **Schwellenwert** für die Metrik aus. Wählen Sie auch den **Zeitraum** der Metrikregel aus, der erfüllt sein muss, ehe die Warnung ausgelöst wird. Wenn Sie z.B. den Zeitraum „In den letzten 5 Minuten“ auswählen und die Warnung nach einer CPU-Auslastung von über 80% sucht, wird die Warnung ausgelöst, wenn die CPU-Auslastung 5 Minuten durchgängig über 80% lag. Nach Auslösen des ersten Triggers erfolgt ein erneutes Auslösen, wenn die CPU-Auslastung 5 Minuten unter 80% bleibt. Die Messung der CPU-Metrik erfolgt minütlich.

6. Wählen Sie **E-Mail-Besitzer...** aus, wenn Sie möchten, dass Administratoren und Co-Administratoren E-Mail-Benachrichtigungen empfangen, wenn die Warnung ausgelöst wird.

7. Wenn Sie möchten, dass bei Auslösen der Warnung Benachrichtigungen an weitere E-Mail-Adressen gesendet werden, fügen Sie diese dem Feld **Weitere Administrator-E-Mail(s)** hinzu. Trennen Sie mehrere E-Mail-Nachrichten in folgendem Format durch Semikolons: *email\@contoso.com;email2\@contoso.com*

8. Fügen Sie einen gültigen URI in das Feld **Webhook** ein, wenn dieser bei Auslösen der Warnung aufgerufen werden soll.

9. Wenn Sie Azure Automation verwenden, können Sie ein Runbook auswählen, das ausgeführt werden soll, sobald die Warnung ausgelöst wird.

10. Wählen Sie **OK** , um die Warnung zu erstellen.

Innerhalb weniger Minuten wird die Warnung aktiv und wie oben beschrieben ausgelöst.

Nachdem Sie eine Warnung erstellt haben, können Sie sie auswählen und eine der folgenden Aufgaben ausführen:

* Anzeigen eines Diagramms, das den Schwellenwert der Metrik und die tatsächlichen Werte vom Vortag zeigt.
* bearbeiten oder löschen.
* sie **deaktivieren** oder **aktivieren**, wenn Sie den Empfang von Benachrichtigungen zu dieser Warnung vorübergehend beenden oder fortsetzen möchten.

## <a name="with-azure-cli"></a>Mit der Azure-Befehlszeilenschnittstelle

In den vorangegangenen Abschnitten wurde beschrieben, wie Sie über das Azure-Portal Metrikwarnungsregeln erstellen, anzeigen und verwalten. In diesem Abschnitt wird beschrieben, wie Sie dasselbe mit der plattformübergreifenden [Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) (Azure CLI) erreichen können. Die schnellste Möglichkeit, mit der Verwendung der Azure CLI zu beginnen, ist über [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest).

### <a name="get-all-classic-metric-alert-rules-in-a-resource-group"></a>Abrufen aller klassischen Metrikwarnungsregeln in einer Ressourcengruppe

```azurecli
az monitor alert list --resource-group <group name>
```

### <a name="see-details-of-a-particular-classic-metric-alert-rule"></a>Anzeigen von Details zu einer bestimmten klassischen Metrikwarnungsregel

```azurecli
az monitor alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-classic-metric-alert-rule"></a>Erstellen einer klassischen Metrikwarnungsregel

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-a-classic-metric-alert-rule"></a>Löschen einer klassischen Metrikwarnungsregel

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="with-powershell"></a>Mit PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

In diesem Abschnitt wird gezeigt, wie Sie mit PowerShell-Befehlen klassische Metrikwarnungen erstellen, anzeigen und verwalten können. Die Beispiele im Artikel veranschaulichen, wie Sie Azure Monitor-Cmdlets für klassische Metrikwarnungen verwenden können.

1. Sofern dies noch nicht geschehen ist, richten Sie PowerShell auf Ihrem Computer ein. Weitere Informationen finden Sie unter [Vorgehensweise zum Installieren und Konfigurieren von PowerShell](/powershell/azure/overview). Die vollständige Liste der PowerShell-Cmdlets für Azure Monitor finden Sie unter [Azure Monitor-Cmdlets (Insights)](https://docs.microsoft.com/powershell/module/az.applicationinsights).

2. Melden sich zuerst bei Ihrem Azure-Abonnement an.

    ```powershell
    Connect-AzAccount
    ```

3. Es wird ein Anmeldebildschirm angezeigt. Nach der Anmeldung werden Ihr Konto, die Mandanten-ID und die ID des Standardabonnements angezeigt. Alle Azure-Cmdlets werden im Kontext des Standardabonnements verwendet. Verwenden Sie den folgenden Befehl, um die Liste der Abonnements anzuzeigen, auf die Sie zugreifen können:

    ```powershell
    Get-AzSubscription
    ```

4. Verwenden Sie den folgenden Befehl, um den Arbeitskontext in ein anderes Abonnement zu ändern:

    ```powershell
    Set-AzContext -SubscriptionId <subscriptionid>
    ```

5. Sie können alle klassischen Metrikwarnungsregeln für eine Ressourcengruppe abrufen:

    ```powershell
    Get-AzAlertRule -ResourceGroup montest
    ```

6. Sie können Details einer klassischen Metrikwarnungsregeln anzeigen.

    ```powershell
    Get-AzAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
    ```

7. Sie können alle für eine Zielressource festgelegten Warnungsregeln abrufen. Beispiel: Alle Warnungsregeln, die für einen virtuellen Computer festgelegt wurden.

    ```powershell
    Get-AzAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
    ```

8. Klassische Warnungsregeln können nicht mehr über PowerShell erstellt werden. Zum Erstellen einer Warnungsregel müssen Sie den neuen Befehl [Add-AzMetricAlertRule](/powershell/module/az.monitor/add-azmetricalertrule) verwenden.

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen einer klassischen Metrikwarnung anhand einer Resource Manager-Vorlage](../../azure-monitor/platform/alerts-enable-template.md)
- [Benachrichtigung eines nicht unter Azure ausgeführten Systems durch eine klassische Metrikwarnung mithilfe eines Webhooks](../../azure-monitor/platform/alerts-webhooks.md)
