---
title: Ändern der Kontaktinformationen für ein Azure-Abrechnungskonto
description: In diesem Artikel wird beschrieben, wie Sie die Kontaktinformationen Ihres Azure-Abrechnungskontos ändern.
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/26/2020
ms.author: banders
ms.custom: contperf-fy21q2
ms.openlocfilehash: 776c207387ee55b998615131baf77e6c13655b49
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033936"
---
# <a name="change-contact-information-for-an-azure-billing-account"></a>Ändern der Kontaktinformationen für ein Azure-Abrechnungskonto

Dieser Artikel unterstützt Sie beim Aktualisieren der Kontaktinformationen für ein *Abrechnungskonto* im Azure-Portal. Die Anweisungen zum Aktualisieren der Kontaktinformationen variieren je nach Abrechnungskontotyp. Weitere Informationen zu Abrechnungskonten und zur Ermittlung der Art Ihres Abrechnungskontos finden Sie unter [Anzeigen Ihrer Abrechnungskonten im Azure-Portal](view-all-accounts.md). Ein Azure-Abrechnungskonto ist ein anderes Konto als das Azure-Benutzerkonto und das [Microsoft-Konto](https://account.microsoft.com/).

Wenn Sie Ihre Azure Active Directory-Benutzerprofilinformationen aktualisieren möchten, kann nur ein Benutzeradministrator die Änderungen vornehmen. Wenn Sie nicht über die Benutzeradministratorrolle verfügen, wenden Sie sich an den Benutzeradministrator. Weitere Informationen zum Ändern des Profils eines Benutzers finden Sie unter [Hinzufügen oder Aktualisieren von Benutzerprofilinformationen mit Azure Active Directory](../../active-directory/fundamentals/active-directory-users-profile-azure-portal.md).

*Käuferadresse*: Bei der Käuferadresse handelt es sich um die Anschrift und die Kontaktinformationen der Organisation oder der Person, die für ein Abrechnungskonto verantwortlich ist. Sie wird auf allen für das Abrechnungskonto generierten Rechnungen angezeigt.

*Rechnungsadresse*: Bei der Rechnungsadresse handelt es sich um die Anschrift und die Kontaktinformationen der Organisation oder der Person, die für die für ein Abrechnungskonto generierten Rechnungen verantwortlich ist. Bei einem Abrechnungskonto für ein Microsoft-Onlineabonnementprogramm (Microsoft Online Service Program, MOSP) gibt es eine Rechnungsadresse, die auf allen für das Konto generierten Rechnungen angezeigt wird. Bei einem Abrechnungskonto für eine Microsoft-Kundenvereinbarung (Microsoft Customer Agreement, MCA) gibt es eine Rechnungsadresse für jedes Abrechnungsprofil, die auf der für das jeweilige Abrechnungsprofil generierten Rechnung angezeigt wird.

*Kontakt-E-Mail-Adresse für Dienst- und Marketing-E-Mails*: Sie können eine E-Mail-Adresse angeben, die sich von der E-Mail-Adresse unterscheidet, mit der Sie sich anmelden, um wichtige abrechnungs- und dienstbezogene Benachrichtigungen sowie Benachrichtigungen zu Empfehlungen für Ihr Azure-Konto zu erhalten. Dienstbenachrichtigungs-E-Mails, z. B. zu dringenden Sicherheitsproblemen, Preisänderungen oder Breaking Changes bei Diensten, die von Ihrem Konto genutzt werden, werden immer an Ihre Anmeldeadresse gesendet.

## <a name="update-an-mosp-billing-account-address"></a>Aktualisieren einer Adresse für ein MOSP-Abrechnungskonto

1. Melden Sie sich beim Azure-Portal mit der E-Mail-Adresse an, die über die Berechtigung „Kontoadministrator“ für das Konto verfügt.
1. Suchen Sie nach **Kostenverwaltung + Abrechnung**.  
    ![Screenshot: Suche im Azure-Portal nach „Kostenverwaltung + Abrechnung“](./media/change-azure-account-profile/search-cmb.png)
1. Wählen Sie auf der linken Seite die Option **Eigenschaften** aus.  
    ![Screenshot mit den Eigenschaften des MOSP-Abrechnungskontos](./media/change-azure-account-profile/update-contact-information-select-properties.png)
1. Wählen Sie **Rechnungsadresse aktualisieren** aus, um die Käuferadresse und die Rechnungsadresse zu aktualisieren. Geben Sie die neue Adresse ein, und wählen Sie dann **Speichern** aus.  
    ![Screenshot mit der Adressenaktualisierung für das MOSP-Abrechnungskonto](./media/change-azure-account-profile/update-contact-information-mosp.png)

## <a name="update-an-mca-billing-account-sold-to-address"></a>Aktualisieren einer Käuferadresse eines MCA-Abrechnungskontos

1. Melden Sie sich beim Azure-Portal mit der E-Mail-Adresse an, die über die Rolle „Besitzer“ oder „Mitwirkender“ für das Abrechnungskonto für eine Microsoft-Kundenvereinbarung verfügt.
1. Suchen Sie nach **Kostenverwaltung + Abrechnung**.  
    ![Screenshot: Suche im Azure-Portal](./media/change-azure-account-profile/search-cmb.png)
1. Wählen Sie auf der linken Seite die Option **Eigenschaften** aus, und wählen Sie dann **Käufer aktualisieren** aus.  
    ![Screenshot mit den Eigenschaften eines M C A-Abrechnungskontos, wo die Käuferadresse aktualisiert werden kann](./media/change-azure-account-profile/update-sold-to-list-properties-mca.png)
1. Geben Sie die neue Adresse ein, und wählen Sie **Speichern** aus.  
    ![Screenshot: Aktualisieren der Käuferadresse für ein M C A-Konto](./media/change-azure-account-profile/update-sold-to-save-mca.png)

    > [!IMPORTANT]
    > Bei einigen Konten ist eine zusätzliche Überprüfung erforderlich, bevor der Käufer aktualisiert werden kann. Wenn Ihr Konto manuell genehmigt werden muss, werden Sie aufgefordert, sich an den Azure-Support zu wenden.

## <a name="update-an-mca-billing-account-address"></a>Aktualisieren einer Adresse eines MCA-Abrechnungskontos

1. Melden Sie sich beim Azure-Portal mit der E-Mail-Adresse an, die über die Rolle „Besitzer“ oder „Mitwirkender“ für ein Abrechnungskonto oder ein Abrechnungsprofil für eine Microsoft-Kundenvereinbarung verfügt.
1. Suchen Sie nach **Kostenverwaltung + Abrechnung**.  
1. Wählen Sie auf der linken Seite die Option **Abrechnungsprofile** aus.
1. Wählen Sie ein Abrechnungsprofil aus, um die Rechnungsadresse zu aktualisieren.  
    ![Screenshot der Seite „Abrechnungsprofile“, auf der Sie ein Abrechnungsprofil auswählen](./media/change-azure-account-profile/update-bill-to-list-profiles-mca.png)
1. Wählen Sie auf der linken Seite die Option **Eigenschaften** aus.
1. Wählen Sie **Adresse aktualisieren** aus.  
    ![Screenshot: Aktualisieren der Adresse](./media/change-azure-account-profile/update-bill-to-list-properties-mca.png)
1. Geben Sie die neue Adresse ein, und wählen Sie dann **Speichern** aus.  
    ![Screenshot, in dem das Aktualisieren der Adresse gezeigt wird](./media/change-azure-account-profile/update-bill-to-save-mca.png)

## <a name="service-and-marketing-emails"></a>Dienst- und Marketing-E-Mails

Sie werden im Azure-Portal aufgefordert, Ihre E-Mail-Adresse alle 90 Tage zu überprüfen oder zu aktualisieren. Microsoft sendet an diese E-Mail-Adresse E-Mails mit auf das Azure-Konto bezogenen Informationen mit folgenden Inhalten:

- Dienstbenachrichtigungen
- Sicherheitswarnungen
- Abrechnung
- Support
- Marketingmitteilungen
- Best Practices-Empfehlungen basierend auf Ihrer Azure-Nutzung

Geben Sie die E-Mail-Adresse ein, an die Sie Mitteilungen zu Ihrem Konto erhalten möchten. Durch die Eingabe einer E-Mail-Adresse stimmen Sie dem Erhalt von Mitteilungen von Microsoft zu.

![Beispiel für die Aufforderung zum Aktualisieren Ihrer Kontaktdaten](./media/change-azure-account-profile/update-contact-information.png)

### <a name="change-your-contact-email-address"></a>Ändern Ihrer Kontakt-E-Mail-Adresse

Sie können Ihre Kontakt-E-Mail-Adresse mithilfe einer der folgenden Methoden ändern. Wenn Sie Ihre Kontakt-E-Mail-Adresse aktualisieren, wird dabei nicht die E-Mail-Adresse aktualisiert, mit der Sie sich anmelden.

1. Wenn Sie ein Kontoadministrator für ein MOSP-Konto sind, folgen Sie den Anweisungen unter [Aktualisieren einer Adresse für ein MOSP-Abrechnungskonto](#update-an-mosp-billing-account-address), und wählen Sie im letzten Schritt die Option **Kontaktinformationen aktualisieren** aus. Geben Sie anschließend die neue E-Mail-Adresse ein.
1. Wechseln Sie im Azure-Portal zum Bereich [Kontaktinformationen](https://portal.azure.com/#blade/HubsExtension/ContactInfoBlade), und geben Sie die neue E-Mail-Adresse ein. 
1. Wählen Sie im Azure-Portal das Symbol mit Ihren Initialen oder Ihrem Bild aus. Wählen Sie dann das Kontextmenü ( **...** ) aus. Wählen Sie als Nächstes im Menü die Option **Meine Kontaktinformationen** aus, und geben Sie die neue E-Mail-Adresse ein.

![Beispiel für die Aktualisierung einer E-Mail-Adresse in Azure](./media/change-azure-account-profile/azure-contact-information.png)

### <a name="opt-out-of-marketing-emails"></a>Deaktivieren von Marketing-E-Mails

So deaktivieren Sie den Erhalt von Marketing-E-Mails

1. Wechseln Sie zum [Anforderungsformular](https://account.microsoft.com/profile/permissions-link-request), um über Ihre Profil-E-Mail-Adresse eine Anforderung zu senden. Sie erhalten per E-Mail einen Link zum Aktualisieren Ihrer Einstellungen.
1. Wählen Sie den Link aus, um die Seite **Manage communication permissions** (Kommunikationsberechtigungen verwalten) zu öffnen. Auf dieser Seite werden die Arten von Marketingkommunikation angezeigt, die für die E-Mail-Adresse ausgewählt wurden. Deaktivieren Sie alle Optionen der Themen, die Sie nicht mehr erhalten möchten, und wählen Sie dann **Speichern** aus.  
    ![Beispiel der Seite zum Verwalten von Kommunikationsberechtigungen](./media/change-azure-account-profile/manage-communication-permissions.png)

Wenn Sie Marketinginformationen deaktivieren, erhalten Sie weiterhin auf Ihr Konto bezogene Dienstbenachrichtigungen.

## <a name="update-the-email-address-that-you-sign-in-with"></a>Aktualisieren der E-Mail-Adresse, mit der Sie sich anmelden

Sie können die für den Zugriff auf Ihr Konto verwendete E-Mail-Adresse nicht aktualisieren. Wenn Sie jedoch über ein Abrechnungskonto für ein MOSP verfügen, können Sie sich mit der neuen E-Mail-Adresse für ein weiteres Konto registrieren und den Besitz Ihrer Abonnements auf das nächste Konto übertragen. Wenn Sie über ein MCA-Abrechnungskonto verfügen, können Sie der neuen E-Mail-Adresse Berechtigungen für Ihr Konto erteilen.

## <a name="update-your-credit-card"></a>Aktualisieren Ihrer Kreditkartendetails

Informationen zum Aktualisieren Ihrer Kreditkartendetails finden Sie unter [Ändern der für die Zahlung eines Azure-Abonnements verwendeten Kreditkarte](change-credit-card.md).

## <a name="update-your-country-or-region"></a>Aktualisieren Ihres Lands oder Ihrer Region

Das Ändern des Lands oder der Region für ein vorhandenes Konto wird nicht unterstützt. Sie können jedoch ein neues Konto in einem anderen Land oder einer anderen Region erstellen und dann den Azure-Support bitten, Ihr Abonnement auf das neue Konto zu übertragen.

## <a name="change-the-subscription-name"></a>Ändern des Abonnementnamens

1. Melden Sie sich beim Azure-Portal an, und wählen Sie auf der linken Seite **Abonnement** und dann das Abonnement aus, das Sie umbenennen möchten.
1. Wählen Sie **Übersicht** und dann auf der Befehlsleiste **Umbenennen** aus.  
    ![Beispiel für das Umbenennen eines Azure-Abonnements](./media/change-azure-account-profile/rename-sub.png)
1. Wählen Sie nach dem Ändern des Namens die Option **Speichern** aus.

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nächste Schritte

- [Anzeigen Ihrer Abrechnungskonten](view-all-accounts.md)
