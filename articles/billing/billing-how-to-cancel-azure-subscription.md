---
title: Kündigen Ihres Azure-Abonnements | Microsoft Docs
description: Beschreibt, wie Sie Ihr Azure-Abonnement, etwa das Abonnement für die kostenlose Testversion, kündigen.
author: bandersmsft
manager: amberb
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: banders
ms.openlocfilehash: 8f4279d9ac085cdd1ded0dfdda4fad9d3fe12fb8
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66480221"
---
# <a name="cancel-your-subscription-for-azure"></a>Kündigen Ihres Abonnements für Azure

Ein Azure-Abonnement kann nur von einem Benutzer gekündigt werden, der innerhalb des Azure-Abonnements die Funktion eines [Kontoadministrators](billing-subscription-transfer.md#whoisaa) hat. Ein Azure-Abonnementadministrator kann auch [einen anderen Benutzer als Abonnementadministrator benennen](billing-add-change-azure-subscription-administrator.md#assign-a-user-as-an-administrator-of-a-subscription), damit dieser ein Abonnement kündigen kann. Nach der Kündigung Ihres Abonnements haben Sie keinen Zugriff auf Azure-Dienste und -Ressourcen mehr.

Vor der Kündigung Ihres Abonnements:

* Sichern Sie Ihre Daten. Wenn Sie Ihre Daten beispielsweise im Azure-Speicher oder SQL speichern, laden Sie eine Kopie herunter. Wenn Sie einen virtuellen Computer haben, speichern lokal ein Image davon.
* Beenden Sie Ihre Dienste. Wechseln Sie zur [Ressourcenseite im Verwaltungsportal](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources), und **beenden** Sie alle ausgeführten virtuellen Computer, Anwendungen oder anderen Dienste.
* Ziehen Sie die Migration Ihrer Daten in Betracht. Informationen finden Sie unter [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](../azure-resource-manager/resource-group-move-resources.md).
* Sie müssen alle Ressourcen und Ressourcengruppen löschen. Ein Abonnement kann erst gekündigt werden, nachdem diese gelöscht wurden. Jede Ressourcengruppe muss einzeln gelöscht werden. Beim Löschen von Ressourcengruppen müssen Sie den Löschvorgang bestätigen, indem Sie den Namen der Ressourcengruppe eingeben.
* Wenn Sie über benutzerdefinierte Rollen verfügen, die in `AssignableScopes` auf das Abonnement verweisen, sollten Sie die benutzerdefinierten Rollen aktualisieren, um das Abonnement zu entfernen. Wenn Sie versuchen, eine benutzerdefinierte Rolle zu aktualisieren, nachdem Sie ein Abonnement gekündigt haben, wird möglicherweise ein Fehler ausgegeben. Weitere Informationen finden Sie unter [Probleme mit benutzerdefinierten Rollen](../role-based-access-control/troubleshooting.md#problems-with-custom-roles) und [Benutzerdefinierte Rollen für Azure-Ressourcen](../role-based-access-control/custom-roles.md).

Wenn Sie einen kostenpflichtigen Azure-Supportplan kündigen, werden Ihnen die Gebühren für die restliche Laufzeit weiterhin in Rechnung gestellt. Weitere Informationen finden Sie unter [Azure-Supportpläne](https://azure.microsoft.com/support/plans/).

## <a name="cancel-subscription-using-the-azure-portal"></a>Kündigen eines Abonnements unter Verwendung des Azure-Portals

1. Wählen Sie auf der Seite [Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) im Azure-Portal Ihr Abonnement aus.
2. Wählen Sie das Abonnement aus, das Sie kündigen möchten.
3. Wählen Sie **Übersicht** und dann **Abonnement kündigen** aus.

    ![Screenshot mit der Schaltfläche zum Kündigen](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
3. Befolgen Sie die Anweisungen, und schließen Sie die Kündigung ab.

## <a name="what-happens-after-i-cancel-my-subscription"></a>Was geschieht, nachdem ich mein Abonnement gekündigt habe?

Nach Ihrer Kündigung wird die Abrechnung sofort beendet. Bis die Kündigung im Portal angezeigt wird, kann es allerdings bis zu zehn Minuten dauern. Wenn Sie das Abonnement mitten in einem Abrechnungszeitraum kündigen, senden wir die Schlussrechnung zum üblichen Rechnungsdatum nach Ende des Abrechnungszeitraums.

Nach der Kündigung werden Ihre Dienste deaktiviert. Dies bedeutet, dass die Zuordnung Ihrer virtuellen Computer aufgehoben wird, die vorübergehenden IP-Adressen freigegeben werden und der Speicher nur noch im Lesezugriff verfügbar ist.

Wir warten 90 Tage, bevor wir Ihre Daten endgültig löschen, für den Fall, dass Sie darauf zugreifen möchten oder Ihre Meinung ändern. Für die Aufbewahrung der Daten rechnen wir keine Gebühr ab. Weitere Informationen finden Sie in [Microsoft Trust Center – Wie wir Ihre Daten verwalten](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

## <a name="reactivate-subscription"></a>Erneutes Aktivieren von Abonnements

Wenn Sie Ihr Abonnement mit nutzungsbasierter Bezahlung versehentlich gekündigt haben, können Sie es [im Kontocenter erneut aktivieren](billing-subscription-become-disable.md).

Wenn es sich bei Ihrem Abonnement nicht um ein Abonnement mit nutzungsbasierter Bezahlung handelt, nehmen Sie bitte innerhalb von 90 Tagen nach der Kündigung Kontakt zum Support auf, um Ihr Abonnement erneut zu aktivieren.

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).
