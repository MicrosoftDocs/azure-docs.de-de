---
title: Übertragen des Abrechnungsbesitzes eines Azure-Abonnements
description: Beschreibt das Übertragen des Abrechnungsbesitzes eines Azure-Abonnements an ein anderes Konto
keywords: Azure-Abonnement übertragen, Azure Übertragung Abonnement, Verschieben Azure-Abonnement in anderes Konto, Azure Änderung Abonnementsbesitzer, Übertragen Azure-Abonnement an anderes Konto, Azure Abrechnungsbesitz übertragen
author: bandersmsft
ms.reviewer: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/11/2020
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: d37225f988cc906bdd92467afcf3f286220eebda
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97027865"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>Übertragen des Abrechnungsbesitzes eines Azure-Abonnements an ein anderes Konto

In diesem Artikel werden die Schritte zum Übertragen des Abrechnungsbesitzes eines Azure-Abonnements an ein anderes Konto beschrieben. Bevor Sie den Abrechnungsbesitz eines Abonnements übertragen, lesen Sie [Informationen zum Übertragen des Abrechnungsbesitzes für ein Azure-Abonnement](../understand/subscription-transfer.md).

Wenn Sie den Abrechnungsbesitz behalten, den Typ des Abonnements jedoch ändern möchten, finden Sie Informationen hierzu unter [Ändern Ihres Azure-Abonnements in ein anderes Angebot](switch-azure-offer.md). Informationen zur Steuerung, wer auf Ressourcen im Abonnement zugreifen kann, finden Sie unter [Integrierte Azure-Rollen](../../role-based-access-control/built-in-roles.md).

Wenn Sie ein Kunde mit Enterprise Agreement (EA) sind, können Administratoren in Ihrem Unternehmen den Abrechnungsbesitz Ihrer Abonnements zwischen den Konten übertragen. Weitere Informationen finden Sie unter [Ändern des Besitzes von Azure-Abonnements oder -Konten](ea-portal-administration.md#change-azure-subscription-or-account-ownership).

Nur der Abrechnungsadministrator eines Kontos kann den Besitz eines Abonnements übertragen.

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>Übertragen des Abrechnungsbesitzes eines Azure-Abonnements

1. Melden Sie sich im [Azure-Portal](https://portal.azure.com) als Administrator für das Abrechnungskonto des Abonnements an, das Sie übertragen möchten. Wenn Sie sich nicht sicher sind, ob Sie über Administratorrechte verfügen, oder wenn Sie ermitteln möchten, wer Administrator ist, finden Sie entsprechende Informationen unter [Ermitteln des Abrechnungsadministrators](../understand/subscription-transfer.md#whoisaa).
1. Suchen Sie nach **Kostenverwaltung + Abrechnung**.  
   ![Screenshot, der die Suche im Azure-Portal zeigt](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)
1. Wählen Sie links die Option **Abonnements**. Je nach Zugriff müssen Sie möglicherweise einen Abrechnungsbereich auswählen. Wählen Sie dann **Abonnements** oder **Azure-Abonnements** aus.
1. Wählen Sie für das Abonnement, das Sie übertragen möchten, **Abrechnungsbesitz übertragen** aus.  
   ![Auswählen des zu übertragenden Abonnements](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)
1. Geben Sie die E-Mail-Adresse eines Benutzer ein, der ein Abrechnungsadministrator für das Konto ist, das der neue Besitzer des Abonnements sein soll.
1. Wenn Sie Ihr Abonnement an ein Konto in einem anderen Azure AD-Mandanten übertragen, wählen Sie aus, ob das Abonnement in den Mandanten des neuen Kontos verschoben werden soll. Weitere Informationen finden Sie unter [Übertragen eines Abonnements an ein Konto in einem anderen Azure AD-Mandanten](#transfer-a-subscription-to-another-azure-ad-tenant-account).
    > [!IMPORTANT]
    > Wenn Sie das Abonnement in den Azure AD-Mandanten des neuen Kontos verschieben, werden alle [Azure-Rollenzuweisungen](../../role-based-access-control/role-assignments-portal.md) zum Zugreifen auf Ressourcen im Abonnement dauerhaft entfernt. Nur der Benutzer des neuen Kontos, der Ihre Übertragungsanforderung angenommen hat, hat Zugriff auf die Verwaltung von Ressourcen im Abonnement. Alternativ können Sie die Option **Azure AD-Mandant des Abonnements** deaktivieren, um den Abrechnungsbesitz zu übertragen, ohne das Abonnement in den Mandanten des neuen Kontos zu verschieben. Wenn Sie dies tun, werden vorhandene Azure-Rollenzuweisungen zum Zugreifen auf Azure-Ressourcen beibehalten.  
    ![Seite zum Senden der Übertragungsanforderung](./media/billing-subscription-transfer/billing-send-transfer-request.png)
1. Wählen Sie **Übertragungsanforderung senden** aus.
1. Der Benutzer erhält eine E-Mail mit der Aufforderung, Ihre Übertragungsanforderung zu prüfen.  
   ![An den Empfänger gesendete E-Mail zur Abonnementübertragung](./media/billing-subscription-transfer/billing-receiver-email.png)
1. Um die Übertragungsanforderung zu genehmigen, muss der Benutzer auf den Link in der E-Mail klicken und den Anweisungen folgen. Anschließend muss der Benutzer eine Zahlungsmethode für das Abonnement auswählen. Sollte der Benutzer über kein Azure-Konto verfügen, muss er sich für ein neues Konto registrieren.  
   ![Erste Webseite für Abonnementübertragung](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)
   ![Zweite Webseite für Abonnementübertragung](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)
   ![Dritte Webseite für Abonnementübertragung](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)
1. Erfolg! Das Abonnement ist jetzt übertragen.

## <a name="transfer-a-subscription-to-another-azure-ad-tenant-account"></a>Übertragen eines Abonnements an ein anderes Azure AD-Mandantenkonto

Wenn Sie sich für Azure registrieren, wird ein Azure Active Directory (AD)-Mandant für Sie erstellt. Der Mandant stellt Ihr Konto dar. Sie verwenden den Mandanten zum Verwalten des Zugriffs auf Ihre Abonnements und Ressourcen.

Wenn Sie ein neues Abonnement erstellen, wird es vom Azure AD-Mandanten Ihres Kontos gehostet. Wenn Sie anderen Benutzern Zugriff auf Ihr Abonnement oder dessen Ressourcen gewähren möchten, müssen Sie diese dazu einladen, Ihrem Mandanten beizutreten. Dadurch können Sie den Zugriff auf Ihre Abonnements und Ressourcen besser steuern.

Wenn Sie den Abrechnungsbesitz Ihres Abonnements an ein Konto in einem anderen Azure AD-Mandanten übertragen, können Sie das Abonnement in den Mandanten des neuen Kontos verschieben. In diesem Fall verlieren alle Benutzer, Gruppen oder Dienstprinzipale, die über [Azure-Rollenzuweisungen](../../role-based-access-control/role-assignments-portal.md) zum Verwalten von Abonnements und deren Ressourcen verfügt haben, ihren Zugriff. Nur der Benutzer des neuen Kontos, der Ihre Übertragungsanforderung angenommen hat, hat Zugriff auf die Verwaltung der Ressourcen. Wenn der neue Besitzer den Benutzern, die den Zugriff verloren haben, erneut Zugriff gewähren möchte, muss er diese Benutzer manuell dem Abonnement hinzufügen. Weitere Informationen finden Sie unter [Übertragen eines Azure-Abonnements in ein anderes Azure AD-Verzeichnis](../../role-based-access-control/transfer-subscription.md).

## <a name="transfer-visual-studio-and-partner-network-subscriptions"></a>Übertragen von Visual Studio- und Partner Network-Abonnements

Mit Abonnements für Visual Studio und Microsoft Partner Network sind monatliche Azure-Gutschriften verbunden. Wenn Sie derartige Abonnements übertragen, ist Ihr Guthaben im Zielabrechnungskonto nicht verfügbar. Für das Abonnement wird das Guthaben des Ziel-Abrechnungskontos verwendet. Beispiel: Bob überträgt am 9. September ein Visual Studio Enterprise-Abonnement an das Konto von Jane, die die Übertragung annimmt. Nach der Übertragung wird für das Abonnement die Gutschrift für Janes Konto verwendet. Das Guthaben wird jeweils am 9. des Monats zurückgesetzt.

## <a name="next-steps-after-accepting-billing-ownership"></a>Nächste Schritte nach dem Übernehmen des Abrechnungsbesitzes

Wenn Sie den Abrechnungsbesitz für ein Azure-Abonnement angenommen haben, empfiehlt es sich, die folgenden weiteren Schritte zu prüfen:

1. Überprüfen und aktualisieren Sie die Einstellungen für Dienstadministratoren, Co-Admins und Azure-Rollenzuweisungen. Weitere Informationen finden Sie unter [Hinzufügen oder Ändern von Azure-Abonnementadministratoren](add-change-subscription-administrator.md) und [Hinzufügen oder Entfernen von Azure-Rollenzuweisungen über das Azure-Portal](../../role-based-access-control/role-assignments-portal.md).
1. Aktualisieren Sie die Anmeldeinformationen für die Dienste dieses Abonnements, darunter:
   1. Verwaltungszertifikate, die dem Benutzer Administratorrechte auf Abonnementressourcen gewähren. Weitere Informationen finden Sie unter [Erstellen und Hochladen eines Verwaltungszertifikats für Azure](../../cloud-services/cloud-services-certs-create.md).
   1. Tastenkombinationen für Dienste wie Storage. Weitere Informationen finden Sie unter [Informationen zu Azure-Speicherkonten](../../storage/common/storage-account-create.md).
   1. Anmeldeinformationen für den Remotezugriff für Dienste wie Azure Virtual Machines.
1. Wenn Sie mit einem Partner arbeiten, sollten Sie die Partner-ID im Abonnement aktualisieren. Sie können die Partner-ID im [Azure-Portal](https://portal.azure.com) aktualisieren. Weitere Informationen finden Sie unter [Verknüpfen einer Partner-ID mit Ihren Azure-Konten](link-partner-id.md).

## <a name="troubleshooting"></a>Problembehandlung

Verwenden Sie die folgenden Informationen zur Problembehandlung, wenn beim Übertragen von Abonnements Probleme auftreten.

### <a name="the-transfer-subscription-option-is-unavailable"></a>Die Option „Abonnement übertragen“ ist nicht verfügbar

<a name="no-button"></a> 

Die Self-Service-Abonnementübertragung ist für Ihr Abrechnungskonto nicht verfügbar. Derzeit wird die Übertragung des Abrechnungsbesitzes von Abonnements in Enterprise Agreement (EA)-Konten im Azure-Portal nicht unterstützt. Darüber hinaus wird die Übertragung des Abrechnungsbesitzes für Konten mit Microsoft-Kundenvereinbarung, die in Zusammenarbeit mit einem Microsoft-Vertreter erstellt werden, nicht unterstützt.

###  <a name="not-all-subscription-types-can-transfer"></a>Nicht alle Abonnementtypen können übertragen werden

Nicht alle Abonnementtypen unterstützen die Übertragung des Abrechnungsbesitzes. Unter [Unterstützte Abonnementtypen](../understand/subscription-transfer.md#supported-subscription-types) sind die Abonnementtypen aufgelistet, die eine Übertragung unterstützen.

###  <a name="access-denied-error-shown-when-trying-to-transfer-subscription-billing-ownership"></a>Verweigern des Zugriffs beim Versuch, den Abrechnungsbesitz des Abonnements zu übertragen

Dieser Fehler wird angezeigt, wenn Sie versuchen, ein Abonnement für einen Microsoft Azure-Plan zu übertragen und nicht über die erforderliche Berechtigung verfügen. Um das Abonnement für einen Microsoft Azure-Plan zu übertragen, müssen Sie im Rechnungsabschnitt entweder Besitzer oder Mitwirkender sein, dem das Abonnement in Rechnung gestellt wird. Weitere Informationen finden Sie unter [Verwalten von Abonnements für den Rechnungsabschnitt](../manage/understand-mca-roles.md#manage-subscriptions-for-invoice-section).

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nächste Schritte

- Überprüfen und aktualisieren Sie die Einstellungen für Dienstadministratoren, Co-Admins und Azure-Rollenzuweisungen. Weitere Informationen finden Sie unter [Hinzufügen oder Ändern von Azure-Abonnementadministratoren](add-change-subscription-administrator.md) und [Hinzufügen oder Entfernen von Azure-Rollenzuweisungen über das Azure-Portal](../../role-based-access-control/role-assignments-portal.md).