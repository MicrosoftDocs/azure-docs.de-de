---
title: Grundlegendes zu Azure-Gebühren für externe Dienste | Microsoft-Dokumentation
description: Informieren Sie sich über die Gebührenabrechnung für externe Dienste in Azure (früher Marketplace).
services: ''
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
tags: billing
ms.assetid: 5e0e2a3c-d111-4054-8508-0c111c1b749b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ae9c2c975bf49725be1858ad02a1c4b90ef58a7f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60370579"
---
# <a name="understand-your-azure-billing-for-external-service-charges"></a>Grundlegendes zur Abrechnung von Azure-Gebühren für externe Dienste
Externe Dienste werden von Drittanbietern im Azure Marketplace veröffentlicht. Ein Beispiel für einen externen Dienst, den Sie in Azure erwerben können, der jedoch nicht von Microsoft veröffentlicht wird, ist SendGrid.

Beim Bereitstellen eines neuen externen Diensts oder einer neuen externen Ressource wird eine Warnung angezeigt:

![Warnung beim Erwerb von Marketplace-Angeboten](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

> [!NOTE]
> Externe Dienste werden von Drittanbietern veröffentlicht, einige Microsoft-Produkte sind jedoch ebenfalls als externe Dienste gekennzeichnet.
> 
> 

## <a name="how-external-services-are-billed"></a>So werden externe Dienste in Rechnung gestellt
- Externe Dienste werden separat in Rechnung gestellt. Sie werden als einzelne Bestellungen innerhalb Ihres Azure-Abonnements behandelt. Der Abrechnungszeitraum für die einzelnen Dienste wird beim Erwerb des Diensts festgelegt. Dieser Zeitraum darf nicht mit dem Abrechnungszeitraum des Abonnements verwechselt werden, das zum Kauf des externen Diensts verwendet wurde. Sie erhalten separate Rechnungen, und Ihre Kreditkarte wird separat belastet.
- Jeder externe Dienst verwendet ein eigenes Abrechnungsmodell. Für einige Dienste wird die nutzungsbasierte Bezahlung verwendet, anderen liegt ein monatliches Zahlungsmodell zugrunde. Für externe Azure-Dienste wird eine Kreditkarte benötigt, eine Zahlung per Rechnung ist nicht möglich.
- Die kostenlosen monatlichen Gutschriften können nicht für externe Dienste verwendet werden. Wenn Sie ein Azure-Abonnement mit [kostenlosen Gutschriften](https://azure.microsoft.com/pricing/spending-limits/) verwenden, können diese Gutschriften nicht zum Begleichen von Rechnungen genutzt werden, die Sie für externe Dienste erhalten. Nutzen Sie eine Kreditkarte, um externe Dienste zu erwerben.

## <a name="view-external-service-spending-and-history-in-the-azure-portal"></a>Anzeigen der Ausgaben und des Verlaufs für externe Dienste im Azure-Portal
Im [Azure-Portal](https://portal.azure.com/) kann eine Liste der externen Dienste angezeigt werden, die unter den einzelnen Abonnements erworben wurden: 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) als Kontoadministrator an.
2. Wählen Sie im Menü „Hub“ die Option **Abonnements**aus.
   
    ![„Abonnements“ im Menü „Hub“ auswählen](./media/billing-understand-your-azure-marketplace-charges/sub-button.png) 
3. Wählen Sie auf dem Blatt **Abonnements** das anzuzeigende Abonnement aus, und wählen Sie dann **Externe Dienste**.
   
    ![Auswählen eines Abonnements auf dem Blatt „Abrechnung“](./media/billing-understand-your-azure-marketplace-charges/select-sub-external-services.png)
4. In dieser Anzeige sollten die einzelnen externen Dienste, der Name des Herausgebers, die erworbene Dienstebene, der von Ihnen zugewiesene Ressourcenname und der aktuelle Bestellstatus angezeigt werden. Wählen Sie einen externen Dienst aus, um die Rechnungen anzuzeigen, die bereits erstellt wurden.
   
    ![Auswählen eines externen Diensts](./media/billing-understand-your-azure-marketplace-charges/external-service-blade2.png)
5. In dieser Ansicht können Sie die Rechnungsbeträge einschließlich Steuern anzeigen.
   
    ![Anzeigen des Abrechnungsverlaufs für externe Dienste](./media/billing-understand-your-azure-marketplace-charges/billing-overview-blade.png)

## <a name="view-external-service-spending-for-enterprise-agreement-ea-customers"></a>Anzeigen der Ausgaben für externe Dienste für Kunden mit Enterprise Agreement (EA)
EA-Kunden können im EA-Portal die Ausgaben für externe Dienste anzeigen und Berichte herunterladen. Informationen zum Einstieg finden Sie unter [Azure Marketplace for EA Customers](https://ea.azure.com/helpdocs/azureMarketplace) (Azure Marketplace für EA-Kunden).

## <a name="manage-payment-methods-for-external-service-orders"></a>Verwalten der Zahlungsmethoden für Bestellungen externer Dienste
Ihre Zahlungsmethoden für externe Dienste können über das [Kontocenter](https://account.windowsazure.com/) aktualisiert werden.

> [!NOTE]
> Wenn Sie Ihr Abonnement über ein Geschäfts- oder Schulkonto erworben haben, [kontaktieren Sie den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), um Ihre Zahlungsmethode zu ändern.
> 
> 

1. Melden Sie sich beim [Kontocenter](https://account.windowsazure.com/) an, und [navigieren Sie auf die Registerkarte **Marketplace**](https://account.windowsazure.com/Store).
   
    ![Auswählen von „Marketplace“ im Kontocenter](./media/billing-understand-your-azure-marketplace-charges/select-marketplace.png)
2. Auswählen des externen Diensts, der verwaltet werden soll
   
    ![Auswählen des externen Diensts, der verwaltet werden soll](./media/billing-understand-your-azure-marketplace-charges/select-ext-service.png)
3. Klicken Sie rechts auf der Seite auf **Zahlungsmethode ändern**. Sie gelangen in ein anderes Portal, um Ihre Zahlungsmethode zu verwalten.
   
    ![Bestellübersicht](./media/billing-understand-your-azure-marketplace-charges/change-payment.PNG)
4. Klicken Sie auf **Informationen bearbeiten**, und befolgen Sie die Anweisungen zum Aktualisieren Ihrer Zahlungsinformationen.
   
    ![Auswählen von „Informationen bearbeiten“](./media/billing-understand-your-azure-marketplace-charges/edit-info.png)

## <a name="cancel-an-external-service-order"></a>Kündigen eines externen Diensts
Wenn Sie einen externen Dienst kündigen möchten, löschen Sie die Ressource im [Azure-Portal](https://portal.azure.com).

![Löschen der Ressource](./media/billing-understand-your-azure-marketplace-charges/deleteMarketplaceOrder.PNG)

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).

