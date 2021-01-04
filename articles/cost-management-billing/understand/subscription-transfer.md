---
title: Informationen zum Übertragen des Abrechnungsbesitzes für ein Azure-Abonnement
description: In diesem Artikel werden Informationen vermittelt, die Sie benötigen, bevor Sie den Abrechnungsbesitz eines Azure-Abonnements an ein anderes Konto übertragen.
keywords: Azure-Abonnement übertragen, Azure Übertragung Abonnement, Verschieben Azure-Abonnement in anderes Konto, Azure Änderung Abonnementsbesitzer, Übertragen Azure-Abonnement an anderes Konto, Azure Abrechnungsbesitz übertragen
author: bandersmsft
ms.reviewer: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: 163bd7dbdac4de7b7d9c1657ac3debf45382a27a
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97027270"
---
# <a name="about-transferring-billing-ownership-for-an-azure-subscription"></a>Informationen zum Übertragen des Abrechnungsbesitzes für ein Azure-Abonnement

Dieser Artikel vermittelt Kenntnisse, die Sie benötigen, bevor Sie den Abrechnungsbesitz eines Azure-Abonnements an ein anderes Konto übertragen. 

Wenn Sie Ihre Organisation verlassen oder Ihr Abonnement einem anderen Konto in Rechnung gestellt werden soll, möchten Sie möglicherweise den Abrechnungsbesitz Ihres Azure-Abonnements übertragen. Die Übertragung des Abrechnungsbesitzes an ein anderes Konto ermöglicht es den Administratoren des neuen Kontos, Rechnungsaufgaben durchzuführen. Sie können die Zahlungsmethode ändern, Gebühren anzeigen und das Abonnement kündigen.

Wenn Sie den Abrechnungsbesitz behalten, aber den Typ des Abonnements ändern möchten, finden Sie Informationen hierzu unter [Umstellen Ihres Azure-Abonnements auf ein anderes Angebot](../manage/switch-azure-offer.md). Informationen zur Steuerung, wer auf Ressourcen im Abonnement zugreifen kann, finden Sie unter [Integrierte Azure-Rollen](../../role-based-access-control/built-in-roles.md).

Wenn Sie ein Kunde mit Enterprise Agreement (EA) sind, können Administratoren in Ihrem Unternehmen den Abrechnungsbesitz Ihrer Abonnements zwischen den Konten übertragen.

Nur der Abrechnungsadministrator eines Kontos kann den Besitz eines Abonnements übertragen.

## <a name="determine-account-billing-administrator"></a>Ermitteln des Abrechnungsadministrators

<a name="whoisaa"></a>

Der Abrechnungsadministrator ist die Person, die über Berechtigungen zum Verwalten der Abrechnung für ein Konto verfügt. Sie ist berechtigt, im [Azure-Portal](https://portal.azure.com) auf die Abrechnung zuzugreifen und verschiedene Abrechnungsaufgaben durchzuführen (Abonnements erstellen, Rechnungen anzeigen und bezahlen, Zahlungsmethoden aktualisieren und Ähnliches).

Um Konten zu identifizieren, für die Sie der Abrechnungsadministrator sind, besuchen Sie die Seite [„Kostenverwaltung + Abrechnung“ im Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview). Wählen Sie dann im linken Bereich die Option **Alle Abrechnungsbereiche** aus. Auf der Seite „Abonnements“ werden alle Abonnements aufgelistet, für die Sie als Abrechnungsadministrator fungieren.

Wenn Sie sich nicht sicher sind, wer der Kontoadministrator für ein Abonnement ist, besuchen Sie die [Seite „Abonnements“ im Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Wählen Sie dann das zu überprüfende Abonnement aus, und sehen Sie unter **Einstellungen** nach. Wählen Sie **Eigenschaften** aus, und der Kontoadministrator des Abonnements wird im Feld **Kontoadministrator** angezeigt.


## <a name="supported-subscription-types"></a>Unterstützte Abonnementtypen

Die Übertragung von Abonnements im Azure-Portal ist für die unten aufgeführten Abonnementtypen verfügbar. Für Abonnements vom Typ [Kostenlose Testversion](https://azure.microsoft.com/offers/ms-azr-0044p/) oder [Azure in Open (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/) wird derzeit keine Übertragung unterstützt. Wie Sie dies umgehen können, erfahren Sie unter [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md). Zum Übertragen anderer Abonnements wie Supportpläne [wenden Sie sich an den Azure-Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

- [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)<sup>1</sup>
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)  
- [Visual Studio Enterprise-Abonnenten (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/)
- [MSDN-Plattformen](https://azure.microsoft.com/offers/ms-azr-0062p/)  
- [Nutzungsbasierte Bezahlung](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Microsoft Azure Plan](https://azure.microsoft.com/offers/ms-azr-0017g/)<sup>2</sup>

<sup>1</sup> Über das EA-Portal.

<sup>2</sup> Wird nur für Konten unterstützt, die bei der Registrierung auf der Azure-Website erstellt werden.

## <a name="resources-transferred-with-subscriptions"></a>Mit Abonnements übertragene Ressourcen

Alle Ressourcen wie virtuelle Computer, Datenträger und Websites werden zum neuen Konto übertragen. Wenn Sie aber ein Abonnement an ein Konto in einem anderen Azure AD-Mandanten übertragen, werden [Administratorrollen](../manage/add-change-subscription-administrator.md) und [Azure-Rollenzuweisungen](../../role-based-access-control/role-assignments-portal.md) für das Abonnement nicht übertragen. Auch [App-Registrierungen](../../active-directory/develop/quickstart-register-app.md) und andere mandantenspezifische Dienste werden nicht mit dem Abonnement übertragen.

## <a name="transfer-account-ownership-to-another-countryregion"></a>Übertragen des Kontobesitzes in ein anderes Land bzw. in eine andere Region

Leider ist es nicht möglich, Abonnements mithilfe des Azure-Portals länder- oder regionsübergreifend zu übertragen. Sie können jedoch übertragen werden, wenn Sie eine Azure-Supportanfrage öffnen. Um eine Supportanfrage zu erstellen, [wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="transfer-a-subscription-from-one-account-to-another"></a>Übertragen eines Abonnements zwischen Konten

Wenn Sie ein Administrator von zwei Konten sind, können Sie ein Abonnement zwischen Ihren Konten übertragen. Da Ihre Konten theoretisch als Konten von zwei verschiedenen Benutzern gelten, können Sie Abonnements zwischen Ihren Konten übertragen.
Informationen über die erforderlichen Schritte zum Übertragen Ihres Abonnements finden Sie unter [Übertragen des Abrechnungsbesitzes eines Azure-Abonnements](../manage/billing-subscription-transfer.md).

## <a name="transferring-a-subscription-shouldnt-create-downtime"></a>Das Übertragen eines Abonnements sollte keine Ausfallzeiten verursachen

Wenn Sie ein Abonnement an ein Konto im gleichen Azure AD-Mandanten übertragen, hat dies keine Auswirkungen auf die Ressourcen, die unter dem Abonnement ausgeführt werden. In PowerShell gespeicherte Kontextinformationen werden jedoch nicht aktualisiert. Daher müssen Sie diese unter Umständen löschen oder die Einstellungen ändern. Wenn Sie das Abonnement an ein Konto in einem anderen Mandanten übertragen und das Abonnement in den Mandanten verschieben, verlieren alle Benutzer, Gruppen und Dienstprinzipale, die über [Azure-Rollenzuweisungen](../../role-based-access-control/role-assignments-portal.md) zum Zugreifen auf Ressourcen im Abonnement verfügt haben, ihren Zugriff. Dies kann zu Ausfallzeiten führen.

## <a name="new-account-usage-and-billing-history"></a>Nutzungs- und Abrechnungsverlauf des neuen Kontos

Die einzigen Informationen, die für Benutzer des neuen Kontos verfügbar sind, sind die Kosten für das Abonnement im letzten Monat. Der übrige Nutzungs- und Abrechnungsverlauf wird nicht zusammen mit dem Abonnement übertragen.

## <a name="manually-migrate-data-and-services"></a>Manuelles Migrieren von Daten und Diensten

Wenn Sie ein Abonnement übertragen, bleiben dessen Ressourcen in ihm erhalten. Wenn Sie ein Abonnement nicht übertragen können, können Sie seine Ressourcen manuell migrieren. Weitere Informationen finden Sie unter [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

## <a name="remaining-subscription-credits"></a>Verbleibendes Abonnementguthaben 

Wenn Sie über ein Visual Studio- oder Microsoft Partner Network-Abonnement verfügen, erhalten Sie monatliches Guthaben. Ihr Guthaben wird nicht mit dem Abonnement in das neue Konto übertragen. Der Benutzer, der die Übertragungsanforderung annimmt, muss eine Visual Studio-Lizenz haben, um die Übertragungsanforderung annehmen zu können. Für das Abonnement wird das Visual Studio-Guthaben verwendet, das für das Konto des Benutzers verfügbar ist. Weitere Informationen finden Sie unter [Übertragen von Visual Studio- und Partner Network-Abonnements](../manage/billing-subscription-transfer.md#transfer-visual-studio-and-partner-network-subscriptions).

## <a name="users-keep-access-to-transferred-resources"></a>Die Benutzer haben weiterhin Zugriff auf übertragene Ressourcen

Beachten Sie, dass der Zugriff der Benutzer auf Ressourcen in einem Abonnement erhalten bleibt, wenn der Besitz übertragen wird. Es kann aber sein, dass [Administratorrollen](../manage/add-change-subscription-administrator.md) und [Azure-Rollenzuweisungen](../../role-based-access-control/role-assignments-portal.md) entfernt werden. Der Zugriff geht verloren, wenn sich Ihr Konto in einem Azure AD-Mandanten befindet, bei dem es sich nicht um den Mandanten des Abonnements handelt, und der Benutzer, der die Übertragungsanforderung gesendet hat, das Abonnement in den Mandanten Ihres Kontos verschiebt. 

Sie können im Azure-Portal die Benutzer anzeigen, die über Azure-Rollenzuweisungen Zugriff auf Ressourcen im Abonnement haben. Navigieren Sie [im Azure-Portal zur Seite „Abonnements“](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Wählen Sie das Abonnement aus, das Sie überprüfen möchten, und wählen Sie dann **Zugriffssteuerung (IAM)** im linken Bereich aus. Wählen Sie anschließend oben auf der Seite **Rollenzuweisungen** aus. Auf der Seite „Rollenzuweisungen“ werden alle Benutzer mit Zugriff auf das Abonnement aufgelistet.

Auch wenn die [Azure-Rollenzuweisungen](../../role-based-access-control/role-assignments-portal.md) während der Übertragung entfernt werden, haben Benutzer im ursprünglichen Besitzerkonto unter Umständen über andere Sicherheitsverfahren weiterhin Zugriff auf das Abonnement. Hierzu zählen:

* Verwaltungszertifikate, die dem Benutzer Administratorrechte auf Abonnementressourcen gewähren. Weitere Informationen finden Sie unter [Erstellen und Hochladen eines Verwaltungszertifikats für Azure](../../cloud-services/cloud-services-certs-create.md).
* Tastenkombinationen für Dienste wie Storage. Weitere Informationen finden Sie unter [Informationen zu Azure-Speicherkonten](../../storage/common/storage-account-create.md).
* Anmeldeinformationen für den Remotezugriff für Dienste wie Azure Virtual Machines.

Der neue Besitzer sollte das Aktualisieren der dem Dienst zugeordneten Geheimnisse in Erwägung ziehen, wenn er den Zugriff auf Ressourcen einschränken muss. Die meisten Ressourcen können aktualisiert werden. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und wählen Sie dann im Menü „Hub“ **Alle Ressourcen** aus. Wählen Sie dann die Ressource aus. Wählen Sie anschließend auf der Ressourcenseite die Option **Einstellungen** aus. Hier können Sie die vorhandenen Schlüssel anzeigen und aktualisieren.

## <a name="you-pay-for-usage-when-you-receive-ownership"></a>Sie zahlen für die Nutzung, wenn der Besitz an Sie übertragen wurde

Ihr Konto ist verantwortlich für die Bezahlung jeglicher Nutzung, die ab dem Zeitpunkt der Übertragung gemeldet wird. Möglicherweise gibt es Nutzung, die vor der Übertragung stattgefunden hat, aber danach gemeldet wurde. Diese Nutzung ist in der Abrechnung für Ihr Konto enthalten.

## <a name="use-a-different-payment-method"></a>Verwenden einer anderen Zahlungsmethode

Bei der Annahme der Übertragungsanforderung können Sie eine vorhandene Zahlungsmethode auswählen, die mit Ihrem Konto verknüpft ist, oder eine neue Zahlungsmethode hinzufügen.

## <a name="transfer-enterprise-agreement-subscription-ownership"></a>Übertragen des Abonnementbesitzes mit einem Enterprise Agreement

Der Unternehmensadministrator kann den Kontobesitz für beliebige Konten aktualisieren, auch wenn der ursprüngliche Kontobesitzer nicht mehr der Organisation angehört. Weitere Informationen zum Übertragen von Konten mit Azure Enterprise Agreement finden Sie unter [Azure Enterprise-Übertragungen](../manage/ea-transfers.md).

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nächste Schritte

- [Übertragen des Abrechnungsbesitzes eines Azure-Abonnements](../manage/billing-subscription-transfer.md)
