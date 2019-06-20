---
title: Ändern der Kreditkarte für Azure | Microsoft-Dokumentation
description: Enthält Informationen zum Ändern der für die Zahlung eines Azure-Abonnements verwendeten Kreditkarte.
services: ''
documentationcenter: ''
author: genlin
manager: jureid
editor: ''
tags: billing
ms.assetid: 15252ced-1841-4a66-ae79-2e58af1d3370
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 3/13/2019
ms.author: banders
ms.openlocfilehash: b910cb3061b1451ea80b9843e2aa4047a784548f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60918738"
---
# <a name="add-update-or-remove-a-credit-or-debit-card-for-azure"></a>Hinzufügen, Aktualisieren oder Entfernen einer Kredit- oder Debitkarte für Azure

Im Azure-Portal können Sie eine neue Kreditkarte hinzufügen, eine vorhandene Kreditkarte aktualisieren oder eine nicht verwendete Kreditkarte löschen. Sie können diese Änderungen nur als [Kontoadministrator](billing-subscription-transfer.md#whoisaa) vornehmen.

<!-- If your Angola, Belize, Algeria, Vietnam, or Virgin Islands. -->

**Sie möchten auf die Bezahlung per Rechnung umsteigen?** Informationen dazu finden Sie unter [Zahlen für Azure-Abonnements auf Rechnung](billing-how-to-pay-by-invoice.md).

<a id="addcard"></a>

## <a name="add-a-new-credit-or-debit-card-to-an-azure-subscription"></a>Hinzufügen einer neuen Kredit- oder Debitkarte zu einem Azure-Abonnement

1. Melden Sie sich als Kontoadministrator beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach **Kostenverwaltung + Abrechnung**.

    ![Screenshot: Suche](./media/billing-how-to-change-credit-card/search.png)

1. Wählen Sie das Abonnement aus, dem Sie die Kredit- oder Debitkarte hinzufügen möchten.
1. Wählen Sie die Option **Zahlungsmethoden**.

    ![Screenshot mit ausgewählter Option „Zahlungsmethoden verwalten“.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Wählen Sie oben links das Pluszeichen („+“), um eine Karte hinzuzufügen. Rechts wird ein Kreditkartenformular angezeigt.
1. Geben Sie die Kredit- oder Debitkartendetails ein.

    ![Screenshot: Hinzufügen einer neuen Karte](./media/billing-how-to-change-credit-card/sub-add-new-x.png)

1. Aktivieren Sie oberhalb des Formulars das Kontrollkästchen **Diese Zahlungsmethode als aktive Methode festlegen**, um diese Karte als Ihre aktive Zahlungsmethode festzulegen. Diese Karte wird zum aktiven Zahlungsmittel für alle Abonnements, indem dieselbe Karte wie für das ausgewählte Abonnement verwendet wird.

1. Klicken Sie auf **Weiter**.

Wenn nach dem Hinzufügen der Kreditkarte eine Fehlermeldung ausgegeben wird, befolgen Sie die Anleitung unter [Ablehnung der Kreditkarte bei der Azure-Registrierung](billing-credit-card-fails-during-azure-sign-up.md).

## <a name="update-existing-credit-or-debit-card"></a>Aktualisieren einer vorhandenen Kredit- oder Debitkarte

Wenn Ihre Kreditkarte verlängert wird und die Kreditkartennummer gleich bleibt, können Sie die bereits vorhandenen Kreditkartendetails (z. B. das Ablaufdatum) aktualisieren. Wenn sich die Kreditkartennummer aufgrund von Verlust, Diebstahl oder Ablauf ändert, führen Sie die Schritte im Abschnitt [Hinzufügen einer Kreditkarte als Zahlungsmethode](#addcard) aus. Die Kartenprüfnummer müssen Sie nicht aktualisieren.

1. Melden Sie sich als Kontoadministrator beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach **Kostenverwaltung + Abrechnung**.

    ![Screenshot: Suche](./media/billing-how-to-change-credit-card/search.png)

1. Wählen Sie die Option **Zahlungsmethoden**.

    ![Screenshot mit ausgewählter Option „Zahlungsmethoden verwalten“.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Klicken Sie auf die Kredit- oder Debitkarte, die Sie bearbeiten möchten. Rechts wird ein Kreditkartenformular angezeigt.

    ![Screenshot: Auswahl der Kredit- bzw. Debitkarte](./media/billing-how-to-change-credit-card/edit-card-x.png)

1. Aktualisieren Sie die Kredit- oder Debitkartendetails.
1. Wählen Sie **Speichern** aus.

## <a name="use-a-different-credit-card-for-the-azure-subscription"></a>Verwenden einer anderen Kreditkarte für das Azure-Abonnement

Falls mehr als eines Ihrer Abonnements über die gleiche aktive Zahlungsmethode verfügt, wird durch die Änderung der aktiven Zahlungsmethode für eines dieser Abonnements auch die aktive Zahlungsmethode für die anderen Abonnements aktualisiert.

1. Melden Sie sich als Kontoadministrator beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach **Kostenverwaltung + Abrechnung**.

    ![Screenshot: Suche](./media/billing-how-to-change-credit-card/search.png)

1. Wählen Sie das Abonnement aus, dem Sie die Kredit- oder Debitkarte hinzufügen möchten.
1. Wählen Sie die Option **Zahlungsmethoden**.

    ![Screenshot mit ausgewählter Option „Zahlungsmethoden verwalten“.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Aktivieren Sie das Kontrollkästchen neben der Karte, die Sie als aktive Zahlungsmethode festlegen möchten.
1. Klicken Sie auf **Als aktiv festlegen**.
    ![Screenshot: Ausgewählte Kredit- oder Debitkarte und Auswahl von „Als aktiv festlegen“](./media/billing-how-to-change-credit-card/sub-change-active-x.png)

## <a name="remove-a-credit-or-debit-card-from-the-account"></a>Entfernen einer Kredit- oder Debitkarte aus dem Konto

1. Melden Sie sich als Kontoadministrator beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie links auf der Seite die Option **Kostenverwaltung + Abrechnung**.

    ![Screenshot: Suche](./media/billing-how-to-change-credit-card/search.png)

1. Wählen Sie unter **Abrechnung** die Option **Zahlungsmethoden**.

    ![Screenshot mit ausgewählter Option „Zahlungsmethoden verwalten“.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Aktivieren Sie das Kontrollkästchen neben der Karte, die Sie entfernen möchten.
1. Klicken Sie auf **Löschen**.

Wenn Ihre Kreditkarte die aktive Zahlungsmethode für eines Ihrer Microsoft-Abonnements ist, können Sie sie nicht aus Ihrem Azure-Konto entfernen. Ändern Sie die aktive Zahlungsmethode für alle Abonnements, die mit dieser Kredit- oder Debitkarte verknüpft sind, und versuchen Sie es noch einmal.
<!-- # Add, update, or remove a credit or debit card for Azure

In the Account Center, you can add a new credit card, update an existing credit card, or delete a credit card that you don't use. You must be an [Account Administrator](billing-subscription-transfer.md#whoisaa) to make these changes.

**Want to switch to pay by invoice?** See [Pay for Azure subscriptions by invoice](billing-how-to-pay-by-invoice.md).
 
<a id="addcard"></a>

## Add a new credit or debit card

1. Sign in to the [Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select a subscription.
1. On the right side of the page, select **Manage payment methods**.

    ![Screenshot that shows Manage payment methods option selected.](./media/billing-how-to-change-credit-card/changesub_new.png)
1. Select “+” to add a card.

    ![Screenshot that shows the edit option next to the payment method.](./media/billing-how-to-change-credit-card/editcard_new.png)
1. Enter credit or debit card details.
1. Select **Save**. 

If you get an error after you add the credit card, see [Credit card declined at Azure sign-up](billing-credit-card-fails-during-azure-sign-up.md).

## Update existing credit or debit card

If your credit card gets renewed and the number remains the same, update the existing credit card details like the expiration date. If your credit card number changes because the card is lost, stolen, or expired, follow the steps in the [Add a credit card as a payment method](#addcard) section. You don't need to update the CVV.

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
1. Select **Manage payment methods**.
1. Select **Edit** next to the card you want to update.
1. Update the credit or debit card details.
1. Select **Save**.

## Use a different credit card for the Azure subscription

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
1. On the right side of the page, select **Manage payment methods**.
1. Click **Use Instead** next to the card that you want to use. This also updates any other subscriptions currently associated with this card. 

## Remove a credit or debit card from the account

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
3. On the right side of the page, select **Manage payment methods**.
4. Click **Delete** for the credit card that you want to delete.

If your credit card is associated with other active Microsoft subscriptions, you can't remove it from your Azure account. Remove the credit card from all active subscriptions that you have with Microsoft and try again. -->
## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>Das Abonnement ist deaktiviert. Warum kann ich jetzt meine Kreditkarte nicht entfernen?

Nach der Deaktivierung oder Kündigung Ihres Abonnements warten wir 90 Tage, bevor wir Ihr Abonnement endgültig löschen. Während der Aufbewahrungsdauer wird die Zahlungsmethode beibehalten, für den Fall, dass Sie das Abonnement reaktivieren möchten. Anschließend wird das Abonnement endgültig gelöscht.

Wenn Sie Ihre Kredit- oder Debitkarte vor Ablauf des Beibehaltungszeitraums von 90 Tagen entfernen möchten, [reaktivieren Sie Ihr Abonnement](billing-subscription-become-disable.md). Wenn die Reaktivierung nicht möglich ist, [wenden Sie sich an den Azure-Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>Warum wird beständig die Meldung „Ihre Anmeldesitzung ist abgelaufen. Klicken Sie hier, um sich wieder anzumelden.“ angezeigt?

Wenn diese Fehlermeldung weiterhin angezeigt wird, selbst wenn Sie sich bereits abgemeldet und dann erneut angemeldet haben, versuchen Sie es erneut mit einer privaten Browsersitzung.

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>Wie kann ich für jedes meiner Abonnements eine andere Karte verwenden?

Wenn für Ihre Abonnements bereits ein und dieselbe Karte verwendet wird, ist es leider nicht möglich, sie zu trennen, um verschiedene Karten zu verwenden. Wenn Sie sich jedoch für ein neues Abonnement registrieren, können Sie eine neue Zahlungsmethode für dieses Abonnement auswählen.

### <a name="how-do-i-make-payments"></a>Wie tätige ich Zahlungen?

Wenn Sie eine Kreditkarte oder Debitkarte als Zahlungsmethode eingerichtet haben, wird diese Karte automatisch nach jedem Abrechnungszeitraum belastet. Sie müssen nichts weiter tun.

Wenn Sie [auf Rechnung zahlen](billing-how-to-pay-by-invoice.md), senden Sie Ihre Zahlung an den Empfänger, der unten auf der Rechnung angegeben ist.

### <a name="how-do-i-make-a-one-time-payment"></a>Wie tätige ich eine einmalige Zahlung?

Leider unterstützt Azure derzeit keine einmaligen Zahlungen für Kredit- oder Debitkarten. 

### <a name="how-do-i-change-the-tax-id"></a>Wie ändere ich die Steuernummer?

Navigieren Sie zum Hinzufügen oder Aktualisieren der Steuernummer im [Azure-Kontocenter zu **Profil**](https://account.azure.com/Profile), und klicken Sie auf **Steuereintrag**. Diese Steuernummer wird auf Ihrer Rechnung angezeigt und für die Berechnung von Steuerbefreiungen verwendet.

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).
