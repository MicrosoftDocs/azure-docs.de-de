---
title: Behandeln von Azure Stack Edge Pro-Bestellproblemen im Azure-Portal | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Azure Stack Edge Pro-Bestellprobleme behandelt werden.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 08/11/2020
ms.author: alkohli
ms.openlocfilehash: be7d477d08b025fd450bfa859e8eb677ba680143
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96446370"
---
# <a name="troubleshoot-your-azure-stack-edge-pro-ordering-issues"></a>Behandeln von Azure Stack Edge Pro-Bestellproblemen

In diesem Artikel wird beschrieben, wie Azure Stack Edge Pro-Bestellprobleme behandelt werden.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Behandeln von Bestellproblemen

## <a name="unsupported-subscription-or-region"></a>Das Abonnement oder die Region wird nicht unterstützt

**Fehlerbeschreibung**: Sie erhalten die folgende Fehlermeldung im Azure-Portal:

*Das ausgewählte Abonnement oder die ausgewählte Region wird nicht unterstützt. Wählen Sie ein anderes Abonnement oder eine andere Region aus.*

![Das Abonnement oder die Region wird nicht unterstützt](media/azure-stack-edge-troubleshoot-ordering/azure-stack-edge-troubleshoot-ordering-01.png)

**Vorgeschlagene Lösung**:  Stellen Sie sicher, dass Sie ein unterstütztes Abonnement verwendet haben, z. B. [Microsoft Enterprise Agreement (EA)](https://azure.microsoft.com/overview/sales-number/), [Cloud Solution Provider (CSP)](/partner-center/azure-plan-lp) oder [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/). Abonnements mit nutzungsbasierter Bezahlung werden nicht unterstützt. Weitere Informationen finden Sie unter [Ressourcenvoraussetzungen für Azure Stack Edge](azure-stack-edge-deploy-prep.md#prerequisites).

Microsoft ermöglicht auf Einzelfallbasis gegebenenfalls ein Upgrade des Abonnementtyps. Wenden Sie sich an den [Microsoft-Support](https://azure.microsoft.com/support/options/), um Ihre Anforderungen zu besprechen und diese Grenzwerte entsprechend anzupassen.

## <a name="selected-subscription-type-not-supported"></a>Der ausgewählte Abonnementtyp wird nicht unterstützt

**Fehler:** Sie verfügen über ein EA-, CSP- oder gesponsertes Abonnement und erhalten die folgende Fehlermeldung:

*Der ausgewählte Abonnementtyp wird nicht unterstützt. Stellen Sie sicher, dass Sie ein unterstütztes Abonnement verwenden. [Weitere Informationen](azure-stack-edge-deploy-prep.md#prerequisites). Wenn Sie einen unterstützten Abonnementtyp verwenden, stellen Sie sicher, dass der `Microsoft.DataBoxEdge`-Anbieter registriert ist. Informationen zur Registrierung finden Sie unter [Registrieren des Ressourcenanbieters](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)* .

**Vorgeschlagene Lösung**: Führen Sie diese Schritte aus, um Ihren Azure Stack Edge-Ressourcenanbieter zu registrieren:

1. Navigieren Sie im Azure-Portal zu **Start** > **Abonnements**.

2. Wählen Sie das Abonnement aus, das Sie zum Bestellen Ihres Geräts verwenden möchten.

3. Wählen Sie **Ressourcenanbieter** aus, und suchen Sie dann nach **Microsoft.DataBoxEdge**.

    ![Registrieren des Ressourcenanbieters](media/azure-stack-edge-troubleshoot-ordering/azure-stack-edge-troubleshoot-ordering-02.png)

Wenn Sie nicht über die Zugriffsberechtigungen eines Besitzers oder Mitwirkenden zum Registrieren des Ressourcenanbieters verfügen, erhalten Sie die folgende Fehlermeldung: *Das Abonnement &lt;Abonnementname&gt; ist nicht berechtigt, die folgenden Ressourcenanbieter zu registrieren: Microsoft.DataBoxEdge.*

Weitere Informationen finden Sie unter [Registrieren von Ressourcenanbietern](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers).

## <a name="microsoftdataboxedge-not-registered-for-subscription"></a>Microsoft.DataBoxEdge ist nicht für ein Abonnement registriert

**Fehler:** Sie wählen im Azure-Portal ein Abonnement zur Verwendung mit Azure Stack Edge Pro oder Data Box Gateway aus und erhalten den folgenden Fehler:

*Der Ressourcenanbieter „Microsoft.DataBoxEdge“ ist für das Abonnement &lt;Abonnementname&gt; nicht registriert, und Sie verfügen nicht über die Berechtigungen zum Registrieren eines Ressourcenanbieters für das Abonnement &lt;Abonnementname&gt;* .

**Vorgeschlagene Lösung**: Erhöhen Sie Ihre Zugriffsberechtigungen für das Abonnement, oder bitten Sie einen Benutzer mit Besitzer- oder Mitwirkenderzugriff, den Ressourcenanbieter zu registrieren.

## <a name="resource-disallowed-by-policy"></a>Die Ressource ist aufgrund einer Richtlinie unzulässig

**Fehler:** Sie versuchen, im Azure-Portal einen Ressourcenanbieter zu registrieren, und erhalten die folgende Fehlermeldung:

*Die Ressource &lt;Ressourcenname&gt; wurde durch eine Richtlinie abgelehnt. (Code: RequestDisallowedByPolicy). Initiative: Allgemein unerwünschte Ressourcentypen ablehnen. Richtlinie: Nicht zulässige Ressourcentypen.*

**Vorgeschlagene Lösung**: Dieser Fehler tritt aufgrund einer vorhandenen Azure-Richtlinie auf, durch die das Erstellen der Ressource blockiert wird. Azure-Richtlinien werden vom Systemadministrator einer Organisation festgelegt, um die Einhaltung von Vorgaben beim Verwenden oder Erstellen von Azure-Ressourcen sicherzustellen. Wenn das Erstellen einer Azure Stack Edge-Ressource durch eine solche Richtlinie blockiert wird, wenden Sie sich an Ihren Systemadministrator, damit Ihre Azure-Richtlinie angepasst wird.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen finden Sie unter [Behandeln von Problemen mit Azure Stack Edge Pro](azure-stack-edge-troubleshoot.md).