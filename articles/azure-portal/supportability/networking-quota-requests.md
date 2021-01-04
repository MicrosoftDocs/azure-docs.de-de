---
title: Erhöhung des Grenzwerts für Netzwerke
description: Erhöhung des Grenzwerts für Netzwerke
author: anavinahar
ms.author: anavin
ms.date: 01/23/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: e945dec2ce8514c3e4f1edecdecd13c5c43f7c75
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745450"
---
# <a name="networking-limit-increase"></a>Erhöhung des Grenzwerts für Netzwerke

Verwenden Sie das [Azure-Portal](https://portal.azure.com), um Ihr Netzwerkkontingent zu erhöhen.

Um Ihre aktuelle Netzwerkauslastung und Kontingente im Azure-Portal anzuzeigen, öffnen Sie Ihr Abonnement, und wählen Sie dann **Nutzung + Kontingente** aus. Sie können auch die folgenden Optionen verwenden, um Ihre Netzwerkauslastung und -limits anzuzeigen.

* [Auslastungs-CLI](/cli/azure/network#az-network-list-usages)
* [PowerShell](/powershell/module/azurerm.network/get-azurermnetworkusage)
* [Die Netzwerkauslastungs-API](/rest/api/virtualnetwork/virtualnetworks/listusage)

Sie können eine Erhöhung anfordern, indem Sie **Hilfe und Support** verwenden oder über **Nutzung + Kontingente** im Portal.

> [!Note]
> Um die Standardgröße der **Präfixe für öffentliche IP-Adressen** zu ändern, wählen Sie in der Dropdownliste **Min Public IP InterNetwork Prefix Length** aus.

## <a name="request-networking-quota-increase-at-subscription-level-using-help--support"></a>Anfordern einer Netzwerkkontingenterhöhung auf Abonnementebene mithilfe von „Hilfe und Support“

Führen Sie unten stehenden Anleitungen aus, um eine Supportanfrage zu erstellen, indem Sie **Hilfe und Support** im Azure-Portal verwenden.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und wählen Sie dann im Azure-Portal-Menü **Hilfe und Support** aus, oder suchen Sie nach **Hilfe und Support**, und wählen Sie dies dann aus.

    ![Hilfe und Support](./media/networking-quota-request/help-plus-support.png)

1. Wählen Sie **Neue Supportanfrage** aus.

    ![Neue Supportanfrage](./media/networking-quota-request/new-support-request.png)

1. Wählen Sie als **Problemtyp** den Eintrag **Grenzwerte für Dienste und Abonnements (Kontingente)** aus.

    ![Auswählen von Abonnementlimits im Dropdown „Problemtyp“](./media/networking-quota-request/select-quota-issue-type.png)

1. Wählen Sie das Abonnement aus, für das ein höheres Kontingent benötigt wird.

    ![Auswählen des Abonnements (neue Supportanfrage)](./media/networking-quota-request/select-subscription-support-request.png)

1. Wählen Sie unter **Kontingenttyp** den Eintrag **Netzwerk** aus. Klicken Sie auf **Weiter: Lösungen**.

    ![Auswählen des Kontingenttyps](./media/networking-quota-request/select-quota-type-network.png)

1. Wählen Sie in **PROBLEMDETAILS** den Eintrag **Details angeben** aus, und tragen Sie zusätzliche Informationen ein, die bei der Verarbeitung Ihrer Anfrage helfen können.

    ![Angeben von Details](./media/networking-quota-request/provide-details-link.png)

1. Wählen Sie im Bereich **Kontingentdetails** ein Bereitstellungsmodell, einen Ort und die Ressourcen aus, die in Ihre Anfrage aufgenommen werden sollen.

    ![Kontingentdetails (Bereitstellungsmodell)](./media/networking-quota-request/quota-details-network.png)

1. Geben Sie die gewünschten Grenzwerte für das Abonnement ein. Um eine Zeile zu entfernen, deaktivieren Sie die Ressource im Menü **Ressourcen**, oder wählen Sie das Symbol „x“ zum Verwerfen aus. Nachdem Sie für jede Ressource ein Kontingent eingegeben haben, wählen **Speichern und fortfahren** aus, um die Erstellung der Supportanfrage fortzusetzen.

    ![Neue Grenzwerte](./media/networking-quota-request/network-new-limits.png)

## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quotas"></a>Anfordern einer Netzwerkkontingenterhöhung auf Abonnementebene mithilfe von „Nutzung + Kontingente“

Befolgen Sie diese Anleitungen, um eine Supportanfrage zu erstellen, indem Sie **Nutzung + Kontingente** im Azure-Portal verwenden.

1. Suchen Sie unter https://portal.azure.com nach **Abonnements**, und wählen Sie diese aus.

    ![Abonnements](./media/networking-quota-request/search-for-suscriptions.png)

1. Wählen Sie das Abonnement aus, für das ein höheres Kontingent benötigt wird.

    ![Auswählen des Abonnements](./media/networking-quota-request/select-subscription-change-quota.png)

1. Wählen Sie **Nutzung + Kontingente** aus.

    ![Auswählen von „Nutzung + Kontingente“](./media/networking-quota-request/select-usage-plus-quotas.png)

1. Klicken Sie in der Ecke oben rechts auf **Erhöhung anfordern**.

    ![Anfordern einer Erhöhung](./media/networking-quota-request/request-increase-from-subscription.png)

1. Führen Sie die Schritte, beginnend mit Schritt 3unter [Anfordern einer Netzwerkkontingenterhöhung auf Abonnementebene](#request-networking-quota-increase-at-subscription-level-using-help--support) aus.

## <a name="about-networking-limits"></a>Informationen zu Netzwerkgrenzwerten

Weitere Informationen zu Netzwerkgrenzwerten finden Sie im Abschnitt [Netzwerk](../../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) der Seite „Grenzwerte“ oder dem Abschnitt zu häufig gestellten Fragen zu Netzwerkgrenzwerten.
