---
title: Verwenden von Azure Automation zum vertikalen Skalieren von virtuellen Windows-Computern | Microsoft-Dokumentation
description: So skalieren Sie einen virtuellen Windows-Computer als Reaktion auf die Überwachung von Warnungen mit Azure Automation vertikal
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 4f964713-fb67-4bcc-8246-3431452ddf7d
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/18/2019
ms.author: kasing
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f46164d52d22b54c0c4f4d707e76be274ebaaf4b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080145"
---
# <a name="vertically-scale-windows-vms-with-azure-automation"></a>Vertikales Skalieren von Windows-VMs mit Azure Automation

Als vertikale Skalierung wird der Vorgang bezeichnet, die Ressourcen eines Computers als Reaktion auf die Workload zu erhöhen oder zu verringern. In Azure kann dies erreicht werden, indem die Größe des virtuellen Computers geändert wird. Dies kann in folgenden Szenarien hilfreich sein:

* Wenn der virtuelle Computer nicht häufig verwendet wird, können Sie ihn auf eine kleinere Größe skalieren, um die monatlichen Kosten zu senken.
* Wenn auf dem virtuellen Computer eine Lastspitze auftritt, kann seine Größe geändert werden, um die Kapazität zu erhöhen.

Nachfolgend werden die Schritte aufgelistet, die hierzu erforderlich sind:

1. Einrichten von Azure Automation für den Zugriff auf Ihre virtuellen Computer
2. Importieren der Azure Automation-Runbooks für die vertikale Skalierung in Ihr Abonnement
3. Hinzufügen eines Webhooks zu Ihrem Runbook
4. Hinzufügen einer Warnung zu Ihrem virtuellen Computer

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
> | Standard_E2v3 |Standard_E64v3 |
> | Standard_E2sv3 |Standard_E64sv3 |
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
> | Standard_NV12s_v3 |Standard_NV48s_v3 |
> 

## <a name="setup-azure-automation-to-access-your-virtual-machines"></a>Einrichten von Azure Automation für den Zugriff auf Ihre virtuellen Computer
Als Erstes müssen Sie ein Azure Automation-Konto erstellen, in dem die Runbooks gehostet werden, mit denen ein virtueller Computer skaliert wird. Vor Kurzem wurde für den Automation-Dienst die Funktion des ausführenden Kontos eingeführt, wodurch die Einrichtung des Dienstprinzipals für die automatische Ausführung der Runbooks im Auftrag des Benutzers stark vereinfacht wird. Weitere Informationen darüber finden Sie im unten stehenden Artikel:

* [Authentifizieren von Runbooks mit der Azure-Option „Ausführendes Konto“](../../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-the-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Importieren der Azure Automation-Runbooks für die vertikale Skalierung in Ihr Abonnement
Die Runbooks, die zur vertikalen Skalierung Ihres virtuellen Computers benötigt werden, sind bereits im Azure Automation-Runbookkatalog veröffentlicht. Sie müssen diese in Ihr Abonnement importieren. Informationen dazu, wie Sie Runbooks importieren, finden Sie im folgenden Artikel:

* [Runbook und Modulkataloge für Azure Automation](../../automation/automation-runbook-gallery.md)

Die nachfolgende Abbildung zeigt, welche Runbooks importiert werden müssen:

![Importieren von Runbooks](./media/vertical-scaling-automation/scale-runbooks.png)

## <a name="add-a-webhook-to-your-runbook"></a>Hinzufügen eines Webhooks zu Ihrem Runbook
Nachdem Sie die Runbooks importiert haben, müssen Sie einen Webhook zum Runbook hinzufügen, damit es über eine Warnung von einem virtuellen Computer ausgelöst werden kann. Ausführliche Informationen zum Erstellen eines Webhooks für Ihr Runbook finden Sie hier:

* [Azure Automation-Webhooks](../../automation/automation-webhooks.md)

Stellen Sie sicher, dass Sie den Webhook kopieren, bevor Sie das Dialogfeld für Webhooks schließen, da sie den Webhook im nächsten Abschnitt benötigen.

## <a name="add-an-alert-to-your-virtual-machine"></a>Hinzufügen einer Warnung zu Ihrem virtuellen Computer
1. Wählen Sie die VM-Einstellungen aus.
2. Wählen Sie „Warnungsregeln“ aus.
3. Wählen Sie „Warnung hinzufügen“ aus.
4. Wählen Sie die Metrik aus, bei der die Warnung ausgelöst wird.
5. Wählen Sie die Bedingung aus, bei deren Erfüllung die Warnung ausgelöst wird.
6. Wählen Sie einen Schwellenwert aus, bei dem die Bedingung aus Schritt 5 erfüllt sein soll.
7. Wählen Sie den Zeitraum aus, in dem der Überwachungsdienst auf die Bedingung und die Schwellenwerte aus den Schritten 5 und 6 prüft.
8. Fügen Sie den Webhook ein, den Sie im vorherigen Abschnitt kopiert haben.

![Hinzufügen einer Warnung zu VM 1](./media/vertical-scaling-automation/add-alert-webhook-1.png)

![Hinzufügen einer Warnung zu VM 2](./media/vertical-scaling-automation/add-alert-webhook-2.png)

