---
title: Löschen einer Zahlungsmethode für die Azure-Abrechnung
description: Es wird beschrieben, wie Sie eine von einem Azure-Abonnement verwendete Zahlungsmethode löschen.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/07/2020
ms.author: banders
ms.openlocfilehash: 15d6c7731b541de638ceaf7828a7ce962cbf154a
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2020
ms.locfileid: "91827541"
---
# <a name="delete-an-azure-billing-payment-method"></a>Löschen einer Zahlungsmethode für die Azure-Abrechnung

Dieses Dokument enthält eine Anleitung zum Löschen einer Zahlungsmethode, z. B. einer Kreditkarte, aus unterschiedlichen Azure-Abonnements. Sie können eine Zahlungsmethode für Folgendes löschen:

- Microsoft-Kundenvereinbarung (Microsoft Customer Agreement, MCA)
- Microsoft-Onlineabonnementprogramm (Microsoft Online Services Program, MOSP), auch als „nutzungsbasierte Bezahlung“ bezeichnet

Unabhängig davon, welchen Typ von Azure-Abonnement Sie nutzen, müssen Sie zuerst die Kündigung durchführen, bevor Sie die zugeordnete Zahlungsmethode löschen können.

Das Entfernen einer Zahlungsmethode für andere Azure-Abonnementtypen, z. B. Microsoft Partner-Vereinbarung und Enterprise Agreement, wird nicht unterstützt.

## <a name="delete-an-mca-payment-method"></a>Löschen einer Zahlungsmethode für eine Microsoft-Kundenvereinbarung (MCA)

Nur der Benutzer, der das Konto vom Typ „Microsoft-Kundenvereinbarung“ erstellt hat, kann eine Zahlungsmethode löschen.

Führen Sie die folgenden Schritte aus, um eine Zahlungsmethode für eine Microsoft-Kundenvereinbarung zu löschen.

1. Melden Sie sich unter https://portal.azure.com/ beim Azure-Portal an.
1. Navigieren Sie zu **Kostenverwaltung und Abrechnung**.
1. Wählen Sie bei Bedarf einen Abrechnungsbereich aus.
1. Wählen Sie in der Menüliste auf der linken Seite unter **Abrechnung** die Option **Abrechnungsprofile** aus.  
    :::image type="content" source="./media/delete-azure-payment-method/billing-profiles.png" alt-text="Beispielscreenshot: Abrechnungsprofile im Azure-Portal" lightbox="./media/delete-azure-payment-method/billing-profiles.png" :::
1. Wählen Sie in der Liste mit den Abrechnungsprofilen das Profil aus, für das die Zahlungsmethode verwendet wird.  
    :::image type="content" source="./media/delete-azure-payment-method/select-billing-profile.png" alt-text="Beispielabbildung: Liste mit Abrechnungsprofilen" :::
1. Wählen Sie in der Menüliste auf der linken Seite unter **Einstellungen** die Option **Zahlungsmethoden** aus.
1. Auf der Seite „Zahlungsmethoden“ für Ihr Abrechnungsprofil wird im Abschnitt **Ihre Kreditkarten** eine Tabelle mit Zahlungsmethoden angezeigt. Suchen Sie nach der Kreditkarte, die Sie löschen möchten, und wählen Sie dann die Auslassungszeichen ( **…** ) und die Option **Löschen** aus.  
    :::image type="content" source="./media/delete-azure-payment-method/delete-credit-card.png" alt-text="Beispiel: Option zum Löschen einer Kreditkarte" :::
1. Die Seite zum Löschen einer Zahlungsmethode wird angezeigt. Azure prüft, ob die Zahlungsmethode gerade verwendet wird.
    - Wenn die Zahlungsmethode nicht verwendet wird, ist die Option **Löschen** aktiviert. Wählen Sie diese Option aus, um die Kreditkarteninformationen zu löschen.
    - Falls die Zahlungsmethode verwendet wird, muss sie ersetzt oder getrennt werden. Lesen Sie sich die folgenden Abschnitte durch. Darin wird beschrieben, wie Sie die Zahlungsmethode **trennen**, die von Ihrem Abonnement verwendet wird.

### <a name="detach-payment-method-used-by-an-mca-billing-profile"></a>Trennen der von einem MCA-Abrechnungsprofil verwendeten Zahlungsmethode

Wenn Ihre Zahlungsmethode von einem MCA-Abrechnungsprofil verwendet wird, wird eine Meldung wie im folgenden Beispiel angezeigt.

:::image type="content" source="./media/delete-azure-payment-method/payment-method-in-use-microsoft-customer-agreement.png" alt-text="Beispielabbildung: Zahlungsmethode wird von einer Microsoft-Kundenvereinbarung verwendet" :::

Die in einer Liste aufgeführten Bedingungen müssen erfüllt sein, um eine Zahlungsmethode trennen zu können. Falls einige Bedingungen nicht erfüllt sind, wird eine Anleitung dazu angezeigt, welche Schritte Sie für die Erfüllung ausführen müssen. Darüber hinaus wird ein Link angezeigt, der zu dem Ort führt, an dem Sie die Bedingung erfüllen können.

Nachdem alle Bedingungen erfüllt wurden, können Sie die Zahlungsmethode vom Abrechnungsprofil trennen.

> [!NOTE]
> Nach dem Trennen der Standardzahlungsmethode wird das Abrechnungsprofil in den Status _Inaktiv_ versetzt. Alle Elemente, die bei diesem Prozess gelöscht werden, können nicht wiederhergestellt werden. Nachdem ein Abrechnungsprofil auf „Inaktiv“ festgelegt wurde, müssen Sie sich für ein neues Azure-Abonnement registrieren, um neue Ressourcen zu erstellen.

#### <a name="to-detach-a-payment-method"></a>Trennen einer Zahlungsmethode

1. Wählen Sie im Bereich „Delete a payment method“ (Zahlungsmethode löschen) den Link **Detach the current payment method** (Aktuelle Zahlungsmethode trennen) aus.
1. Wählen Sie **Trennen** aus, wenn alle Bedingungen erfüllt sind. Fahren Sie andernfalls mit dem nächsten Schritt fort.
1. Wenn die Option „Trennen“ nicht verfügbar ist, wird eine Liste mit Bedingungen angezeigt. Führen Sie die aufgeführten Aktionen durch. Wählen Sie den Link aus, der im Bereich „Detach the default payment method“ (Standardzahlungsmethode trennen) angezeigt wird. Hier ist ein Beispiel für eine Korrekturmaßnahme angegeben, in dem die durchzuführenden Aktionen beschrieben werden.  
    :::image type="content" source="./media/delete-azure-payment-method/azure-subscriptions.png" alt-text="Beispiel: Erforderliche Korrekturmaßnahme zum Trennen einer Zahlungsmethode für Microsoft-Kundenvereinbarung" :::
1. Wenn Sie den Link für die Korrekturmaßnahme auswählen, werden Sie auf die Azure-Seite umgeleitet, auf der Sie die Aktion durchführen können. Führen Sie die erforderliche Korrekturmaßnahme durch.
1. Führen Sie auch alle anderen erforderlichen Korrekturmaßnahmen durch.
1. Navigieren Sie zurück zu **Kostenverwaltung + Abrechnung** > **Abrechnungsprofile** > **Zahlungsmethoden**. Wählen Sie die Option **Trennen** aus. Wählen Sie unten auf der Seite „Detach the default payment method“ (Standardzahlungsmethode trennen) die Option **Trennen** aus.

> [!NOTE]
> - Nachdem Sie ein Abonnement gekündigt haben, kann es bis zu 90 Tage dauern, bis das Abonnement gelöscht wird. Gehen Sie wie folgt vor, um die Wartezeit zu verkürzen: Öffnen Sie eine Azure-Supportanfrage mit der Bitte zur sofortigen Löschung des Abonnements.
> - Sie können eine Zahlungsmethode nur löschen, nachdem alle vorherigen Gebühren für ein Abrechnungsprofil beglichen wurden. Wenn Sie sich innerhalb eines aktiven Abrechnungszeitraums befinden, müssen Sie bis zum Ende des Abrechnungszeitraums warten, bis Sie Ihre Zahlungsmethode löschen können. **Stellen Sie sicher, dass alle anderen Bedingungen für die Trennung erfüllt sind, während Sie auf das Ende Ihres Abrechnungszeitraums warten**.

## <a name="delete-a-mosp-payment-method"></a>Löschen einer MOSP-Zahlungsmethode

Sie müssen ein Kontoadministrator sein, um eine Zahlungsmethode löschen zu können.

Führen Sie die unten angegebenen Schritte aus, falls Ihre Zahlungsmethode von einem MOSP-Abonnement genutzt wird.

1. Melden Sie sich unter https://portal.azure.com/ beim Azure-Portal an.
1. Navigieren Sie zu **Kostenverwaltung und Abrechnung**.
1. Wählen Sie bei Bedarf einen Abrechnungsbereich aus.
1. Wählen Sie in der Menüliste auf der linken Seite unter **Abrechnung** die Option **Zahlungsmethoden** aus.
1. Wählen Sie im Bereich „Zahlungsmethoden“ die _Zeile_ aus, in der sich Ihre Zahlungsmethode befindet. Wählen Sie nicht den Link für die Zahlungsmethoden aus. Unter Umständen ist keine sichtbare Markierung vorhanden, dass Sie die Zahlungsmethode ausgewählt haben.
1. Klicken Sie auf **Löschen**.  
    :::image type="content" source="./media/delete-azure-payment-method/delete-mosp-payment-method.png" alt-text="Beispiel: Erforderliche Korrekturmaßnahme zum Trennen einer Zahlungsmethode für Microsoft-Onlineabonnementprogramm" :::
1. Wählen Sie im Bereich „Delete a payment method“ (Zahlungsmethode löschen) die Option **Löschen** aus, wenn alle Bedingungen erfüllt sind. Fahren Sie mit dem nächsten Schritt fort, falls die Option „Löschen“ nicht verfügbar ist.
1. Eine Liste mit Bedingungen wird angezeigt. Führen Sie die aufgeführten Aktionen durch. Wählen Sie den Link aus, der im Bereich „Delete a payment method“ (Zahlungsmethode löschen) angezeigt wird.  
    :::image type="content" source="./media/delete-azure-payment-method/payment-method-in-use-mosp.png" alt-text="Beispielabbildung: Zahlungsmethode wird von einem MOSP-Abonnement verwendet" :::
1. Wenn Sie den Link für die Korrekturmaßnahme auswählen, werden Sie auf die Azure-Seite umgeleitet, auf der Sie die Aktion durchführen können. Führen Sie die erforderliche Korrekturmaßnahme durch.
1. Führen Sie auch alle anderen erforderlichen Korrekturmaßnahmen durch.
1. Navigieren Sie zurück zu **Kostenverwaltung + Abrechnung** > **Abrechnungsprofile** > **Zahlungsmethoden**, und löschen Sie die Zahlungsmethode.

> [!NOTE]
> Nachdem Sie ein Abonnement gekündigt haben, kann es bis zu 90 Tage dauern, bis das Abonnement gelöscht wird. Gehen Sie wie folgt vor, um die Wartezeit zu verkürzen: Öffnen Sie eine Azure-Supportanfrage mit der Bitte zur sofortigen Löschung des Abonnements.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur Kündigung Ihres Azure-Abonnements finden Sie unter [Kündigen Ihres Azure-Abonnements](cancel-azure-subscription.md).
- Weitere Informationen zum Hinzufügen oder Aktualisieren einer Kreditkarte finden Sie unter [Hinzufügen, Aktualisieren oder Entfernen einer Kreditkarte für Azure](change-credit-card.md).