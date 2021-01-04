---
title: Problembehandlung bei der Meldung „Keine berechtigten Abonnements“ im Azure-Portal
description: In diesem Artikel erfahren Sie, wie Sie die Fehlermeldung „Keine berechtigten Abonnements“ im Azure-Portal beheben, wenn Sie versuchen, eine Reservierung zu erwerben.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 12/15/2020
ms.openlocfilehash: ad85bd278b5dff1532f218acc0b8e88515d96070
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97561204"
---
# <a name="troubleshoot-no-eligible-subscriptions"></a>Problembehandlung bei der Meldung „Keine berechtigten Abonnements“

In diesem Artikel erfahren Sie, wie Sie die Fehlermeldung *Keine berechtigten Abonnements* im Azure-Portal beheben, wenn Sie versuchen, eine Reservierung zu erwerben.

## <a name="symptoms"></a>Symptome

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu **Reservierungen**.
1. Wählen Sie **Hinzufügen** aus, und wählen Sie dann einen Dienst aus.
1. Folgende Fehlermeldung wird angezeigt:
   ```
    No eligible subscriptions
    
    You do not have any eligible subscriptions to purchase reservations. To purchase a reservation, you should have owner or reservation purchaser permission on at least one subscription of the following type: Pay-as-you-go, CSP, Microsoft Enterprise or Microsoft Customer Agreement.
    ```
1. Erweitern Sie im Bereich **Select the product you want to purchase** (Zu erwerbendes Produkt auswählen) die Liste **Abrechnungsabonnement**, um den Grund zu erfahren, warum ein bestimmtes Abonnement nicht für den Kauf einer reservierten Instanz berechtigt ist. Die folgende Abbildung enthält Beispiele dafür, warum eine Reservierung nicht erworben werden kann.  
    :::image type="content" source="./media/troubleshoot-no-eligible-subscriptions/select-product-to-purchase.png" alt-text="Beispiel für den Grund, dass eine Reservierung nicht erworben werden kann" lightbox="./media/troubleshoot-no-eligible-subscriptions/select-product-to-purchase.png" :::

## <a name="cause"></a>Ursache

Um eine reservierte Azure-Instanz zu erwerben, müssen Sie über mindestens ein Abonnement verfügen, das die folgenden Anforderungen erfüllt:

- Das Abonnement muss einem unterstützten Angebotstyp entsprechen. Folgende Angebotstypen werden unterstützt: Nutzungsbasierte Bezahlung, Cloud Solution Provider (CSP), Microsoft Azure Enterprise oder Microsoft-Kundenvereinbarung
- Sie müssen ein Besitzer oder Reservierungskäufer für das Abonnement sein.

Wenn Sie kein Abonnement besitzen, das die Anforderungen erfüllt, erhalten Sie den Fehler `No eligible subscriptions`.

### <a name="cause-1"></a>Ursache 1

Das Abonnement muss einem unterstützten Angebotstyp entsprechen. Folgende Angebotstypen werden unterstützt: Nutzungsbasierte Bezahlung, CSP, Microsoft Azure Enterprise oder Microsoft-Kundenvereinbarung Ihr Abonnementtyp wird nicht unterstützt. Wenn Sie ein Abonnement mit einem Angebotstyp auswählen, der keine Reservierungen unterstützt, wird der folgende Fehler angezeigt.

```
Subscription not eligible for purchase

This subscription is not eligible for reservation benefit an cannot be used to purchase a reservation.
```

:::image type="content" source="./media/troubleshoot-no-eligible-subscriptions/subscription-not-eligible.png" alt-text="Beispiel für ein Abonnement, für das die Fehlermeldung „Abonnement nicht für Erwerb berechtigt“ angezeigt wird" :::

### <a name="cause-2"></a>Ursache 2

Sie müssen ein Besitzer oder Reservierungskäufer für das Abonnement sein. Wenn Sie nicht über ausreichende Berechtigungen verfügen, wird der folgende Fehler angezeigt.

```
You do not have owner or reservation purchaser access on the subscription

You can only purchase reservations using subscriptions on which you have owner or reservation purchaser access.
```

## <a name="solution"></a>Lösung

- Wenn Ihr aktuelles Angebot keine Reservierungen unterstützt, müssen Sie ein neues Azure-Abonnement erstellen.
- Wenn Sie keinen Zugriff auf eine vorhandene Reservierung haben, können Sie sich vom aktuellen Besitzer Zugriff darauf gewähren lassen.

### <a name="solution-1"></a>Lösung 1

Um eine Reservierung zu erwerben, müssen Sie ein neues Azure-Abonnement erstellen, das Reservierungen unterstützt.

- Wenn Sie über eine kostenlose Azure-Testversion verfügen, können Sie [ein Upgrade für Ihr Abonnement](../manage/upgrade-azure-subscription.md) durchführen.
- Sie können ein neues Azure-Abonnement mit [Sätzen für die nutzungsbasierte Bezahlung](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) erstellen.
- Registrieren Sie sich für eine [Microsoft-Kundenvereinbarung](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/), und erstellen Sie ein neues Abonnement.
- Erwerben Sie ein neues Abonnement bei einem [CSP](https://www.microsoft.com/solution-providers/home), und erstellen Sie ein neues Abonnement.

### <a name="solution-2"></a>Lösung 2

Um Besitzerzugriff auf eine Reservierung zu erhalten, benötigen Sie Zugriff auf eine der folgenden Komponenten:

- Den Reservierungsauftrag, mit dem die Reservierung erworben wurde
- Die Reservierung selbst

Der aktuelle Besitzer des Reservierungsauftrags oder der Reservierung kann den Zugriff mithilfe der folgenden Schritte an Sie delegieren.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie **Alle Dienste** > **Reservierungen** aus, um Reservierungen aufzulisten, auf die Sie Zugriff haben.
1. Wählen Sie die Reservierung aus, für die Sie den Zugriff an andere Benutzer delegieren möchten.
1. Wählen Sie die Option **Zugriffssteuerung (IAM)** aus.
1. Wählen Sie **Rollenzuweisung hinzufügen** > **Rolle** > **Besitzer** aus. Oder wählen Sie eine andere Rolle aus, wenn Sie eingeschränkten Zugriff erteilen möchten.
1. Geben Sie die E-Mail-Adresse des Benutzers ein, den Sie als Besitzer hinzufügen möchten.
1. Wählen Sie den Benutzer und dann **Speichern** aus.

Weitere Informationen finden Sie unter [Hinzufügen oder Ändern von Benutzern, die eine Reservierung verwalten können](manage-reserved-vm-instance.md#who-can-manage-a-reservation-by-default).

## <a name="next-steps"></a>Nächste Schritte

- Wenn Ihnen der Zugriff von einem Reservierungsbesitzer gewährt werden muss, informieren Sie sich unter [Hinzufügen oder Ändern von Benutzern, die eine Reservierung verwalten können](manage-reserved-vm-instance.md#who-can-manage-a-reservation-by-default).
