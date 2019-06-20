---
title: Vertikales Skalieren von Azure-VM-Skalierungsgruppen | Microsoft Docs
description: Erfahren Sie, wie Sie einen virtuellen Computer als Reaktion auf die Überwachung von Warnungen mit Azure Automation vertikal skalieren.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 16b17421-6b8f-483e-8a84-26327c44e9d3
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 04/18/2019
ms.author: manayar
ms.openlocfilehash: 3846815dabdc9e351f3d8449feb88affb9c6efdb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60803451"
---
# <a name="vertical-autoscale-with-virtual-machine-scale-sets"></a>Vertikale automatische Skalierung mit VM-Skalierungsgruppen

In diesem Artikel wird beschrieben, wie Sie Azure [VM-Skalierungsgruppen](https://azure.microsoft.com/services/virtual-machine-scale-sets/) mit oder ohne erneute Bereitstellung vertikal skalieren können. Informationen zur vertikalen Skalierung von VMs, die sich nicht in Skalierungsgruppen befinden, finden Sie unter [Vertikales Skalieren von virtuellen Azure-Computern mit Azure Automation](../virtual-machines/windows/vertical-scaling-automation.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Beim vertikalen Skalieren, auch *zentrales Hochskalieren* und *zentrales Herunterskalieren* genannt, wird die Größe eines virtuellen Computers (VM) als Reaktion auf eine Workload vergrößert oder verringert. Vergleichen Sie dies mit dem [horizontalen Skalieren](virtual-machine-scale-sets-autoscale-overview.md), das auch als *horizontales Hochskalieren* und *horizontales Herunterskalieren* bezeichnet wird, bei dem die Anzahl der VMs je nach Workload geändert wird.

Bei der erneuten Bereitstellung wird ein vorhandener virtueller Computer entfernt und durch einen neuen ersetzt. Wenn Sie die Größe der VMs in einer VM-Skalierungsgruppe erhöhen oder verringern, ist es in einigen Fällen angebracht, die Größe von vorhandenen VMs zu ändern und Ihre Daten beizubehalten. In anderen Fällen müssen Sie neue VMs der neuen Größe bereitstellen. In diesem Dokument werden beide Fälle behandelt.

Die vertikale Skalierung kann in folgenden Fällen nützlich sein:

* Ein Dienst basierend auf virtuellen Computern wird zu wenig verwendet (beispielsweise am Wochenende). Das Reduzieren der Größe des virtuellen Computers kann die monatlichen Kosten reduzieren.
* Beim Erhöhen der Größe des virtuellen Computers, um den größeren Bedarf zu bewältigen, ohne zusätzliche virtuelle Computer zu erstellen.

Sie können die vertikale Skalierung so einrichten, dass sie auf Grundlage von metrikbasierten Warnungen aus Ihrer VM-Skalierungsgruppe ausgelöst wird. Bei Aktivierung der Warnung wird ein Webhook ausgelöst, der wiederum ein Runbook auslöst, das Ihre Skalierungsgruppe hoch- oder herunterskalieren kann. Die vertikale Skalierung kann wie folgt konfiguriert werden:

1. Erstellen Sie ein Azure Automation-Konto mit der Funktion „Ausführen als“.
2. Importieren Sie Azure Automation-Runbooks für die vertikale Skalierung für VM-Skalierungsgruppen in Ihr Abonnement.
3. Fügen Sie Ihrem Runbook einen Webhook hinzu.
4. Fügen Sie Ihrer VM-Skalierungsgruppe mithilfe einer Webhookbenachrichtigung eine Warnung hinzu.

> [!NOTE]
> Die Größen, auf die der virtuelle Computer skaliert werden kann, hängen einerseits von der Größe des ersten virtuellen Computers sowie von der Verfügbarkeit anderer Größen im Cluster ab, in dem der virtuelle Computer bereitgestellt wurde. In den veröffentlichten Automation-Runbooks, die in diesem Artikel verwendet werden, wird dies berücksichtigt und lediglich eine Skalierung innerhalb der nachstehenden VM-Größenpaare durchgeführt. Dies bedeutet, dass ein virtueller Standard_D1v2-Computer nicht plötzlich auf Standard_G5 hochskaliert oder auf Basic_A0 herunterskaliert werden kann. Ferner wird das Hoch- bzw. Herunterskalieren der Größe von eingeschränkten VMs nicht unterstützt. Sie können zwischen den folgenden Größenpaaren skalieren:
> 
> | VM-Größenpaare für die Skalierung |  |
> | --- | --- |
> | Basic_A0 |Basic_A4 |
> | Standard_A0 |Standard_A4 |
> | Standard_A5 |Standard_A7 |
> | Standard_A8 |Standard_A9 |
> | Standard_A10 |Standard_A11 |
> | Standard_A1_v2 |Standard_A8_v2 |
> | Standard_A2m_v2 |Standard_A8m_v2  |
> | Standard_B1s |Standard_B2s |
> | Standard_B1ms |Standard_B8ms |
> | Standard_D1 |Standard_D4 |
> | Standard_D11 |Standard_D14 |
> | Standard_DS1 |Standard_DS4 |
> | Standard_DS11 |Standard_DS14 |
> | Standard_D1_v2 |Standard_D5_v2 |
> | Standard_D11_v2 |Standard_D14_v2 |
> | Standard_DS1_v2 |Standard_DS5_v2 |
> | Standard_DS11_v2 |Standard_DS14_v2 |
> | Standard_D2_v3 |Standard_D64_v3 |
> | Standard_D2s_v3 |Standard_D64s_v3 |
> | Standard_DC2s |Standard_DC4s |
> | Standard_E2_v3 |Standard_E64_v3 |
> | Standard_E2s_v3 |Standard_E64s_v3 |
> | Standard_F1 |Standard_F16 |
> | Standard_F1s |Standard_F16s |
> | Standard_F2sv2 |Standard_F72sv2 |
> | Standard_G1 |Standard_G5 |
> | Standard_GS1 |Standard_GS5 |
> | Standard_H8 |Standard_H16 |
> | Standard_H8m |Standard_H16m |
> | Standard_L4s |Standard_L32s |
> | Standard_L8s_v2 |Standard_L80s_v2 |
> | Standard_M8ms  |Standard_M128ms |
> | Standard_M32ls  |Standard_M64ls |
> | Standard_M64s  |Standard_M128s |
> | Standard_M64  |Standard_M128 |
> | Standard_M64m  |Standard_M128m |
> | Standard_NC6 |Standard_NC24 |
> | Standard_NC6s_v2 |Standard_NC24s_v2 |
> | Standard_NC6s_v3 |Standard_NC24s_v3 |
> | Standard_ND6s |Standard_ND24s |
> | Standard_NV6 |Standard_NV24 |
> | Standard_NV6s_v2 |Standard_NV24s_v2 |
> 
> 

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>Erstellen eines Azure Automation-Kontos mit der Funktion „Ausführen als“
Als Erstes müssen Sie lediglich ein Azure Automation-Konto erstellen, in dem die Runbooks gehostet werden. So können die Instanzen der VM-Skalierungsgruppen skaliert werden. Vor kurzem wurde in [Azure Automation](https://azure.microsoft.com/services/automation/) das Feature „Als Konto ausführen“ eingeführt, wodurch der Dienstprinzipal für die automatische Ausführung der Runbooks im Auftrag des Benutzers eingerichtet wird. Weitere Informationen finden Sie unter

* [Authentifizieren von Runbooks mit der Azure-Option „Ausführendes Konto“](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Importieren der Azure Automation-Runbooks für die vertikale Skalierung in Ihr Abonnement

Die Runbooks, die zur vertikalen Skalierung Ihrer VM-Skalierungsgruppen benötigt werden, sind bereits im Azure Automation-Runbookkatalog veröffentlicht. Befolgen Sie die Schritte in diesem Artikel, um sie in Ihr Abonnement zu importieren:

* [Runbook und Modulkataloge für Azure Automation](../automation/automation-runbook-gallery.md)

Wählen Sie die Option zum Durchsuchen des Katalogs im Menü „Runbooks“ aus:

![Zu importierende Runbooks][runbooks]

Es werden die zu importierenden Runbooks angezeigt: Wählen Sie das Runbook aus, basierend darauf, ob Sie eine vertikale Skalierung mit oder ohne erneute Bereitstellung durchführen möchten:

![Runbooks-Katalog][gallery]

## <a name="add-a-webhook-to-your-runbook"></a>Hinzufügen eines Webhooks zu Ihrem Runbook

Nachdem Sie die Runbooks importiert haben, fügen Sie einen Webhook zum Runbook hinzu, damit es über eine Warnung von einer VM-Skalierungsgruppe ausgelöst werden kann. Ausführliche Informationen zum Erstellen eines Webhooks für Ihr Runbook finden Sie in dem folgenden Artikel:

* [Azure Automation-Webhooks](../automation/automation-webhooks.md)

> [!NOTE]
> Kopieren Sie den Webhook-URI, bevor Sie das Dialogfeld für Webhooks schließen, da sie diese Adresse im nächsten Abschnitt benötigen.
> 
> 

## <a name="add-an-alert-to-your-virtual-machine-scale-set"></a>Hinzufügen einer Warnung zu Ihrer VM-Skalierungsgruppe

Im Folgenden finden Sie ein PowerShell-Skript, das zeigt, wie Sie einer VM-Skalierungsgruppe eine Warnung hinzufügen können. Der folgende Artikel enthält Informationen zum Abrufen des Namens der Metrik, um die Warnung auszugeben: [Allgemeine Metriken für die automatische Skalierung in Azure Monitor](../azure-monitor/platform/autoscale-common-metrics.md).

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail user@contoso.com
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri <uri-of-the-webhook>
$threshold = <value-of-the-threshold>
$rg = <resource-group-name>
$id = <resource-id-to-add-the-alert-to>
$location = <location-of-the-resource>
$alertName = <name-of-the-resource>
$metricName = <metric-to-fire-the-alert-on>
$timeWindow = <time-window-in-hh:mm:ss-format>
$condition = <condition-for-the-threshold> # Other valid values are LessThanOrEqual, GreaterThan, GreaterThanOrEqual
$description = <description-for-the-alert>

Add-AzMetricAlertRule  -Name  $alertName `
                            -Location  $location `
                            -ResourceGroup $rg `
                            -TargetResourceId $id `
                            -MetricName $metricName `
                            -Operator  $condition `
                            -Threshold $threshold `
                            -WindowSize  $timeWindow `
                            -TimeAggregationOperator Average `
                            -Actions $actionEmail, $actionWebhook `
                            -Description $description
```

> [!NOTE]
> Es wird empfohlen, ein angemessenes Zeitfenster für die Warnung zu konfigurieren, um das allzu häufige Auslösen einer vertikale Skalierung und damit verbundener Dienstunterbrechungen zu vermeiden. Ziehen Sie ein Fenster von mindestens 20 bis 30 Minuten in Betracht. Ziehen Sie die horizontale Skalierung in Betracht, wenn Sie Unterbrechungen vermeiden müssen.
> 
> 

Weitere Informationen zum Erstellen von Warnungen finden Sie in den folgenden Artikeln:

* [Azure Monitor – PowerShell-Schnellstartbeispiele](../azure-monitor/platform/powershell-quickstart-samples.md)
* [Azure Monitor – Schnellstartbeispiele für die plattformübergreifende CLI](../azure-monitor/platform/cli-samples.md)

## <a name="summary"></a>Zusammenfassung

In diesem Artikel wurden einige einfache Beispiele der vertikalen Skalierung gezeigt. Mit diesen Bausteinen – Automation-Konto, Runbooks, Webhooks, Warnungen – können Sie eine Vielzahl von Ereignissen mit einem benutzerdefinierten Satz von Aktionen verbinden.

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png
