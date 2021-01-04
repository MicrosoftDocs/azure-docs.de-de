---
title: 'Löschen einer Azure AD-Organisation (Mandant): Azure Active Directory | Microsoft-Dokumentation'
description: In diesem Artikel wird erläutert, wie Sie eine Azure AD-Organisation (Mandant) zum Löschen vorbereiten, einschließlich Self-Service-Organisationen.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: addimitu
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2edc6fb98359c5360836bc369e5ae1928464df92
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861029"
---
# <a name="delete-a-tenant-in-azure-active-directory"></a>Löschen eines Mandanten in Azure Active Directory

Beim Löschen einer Azure AD-Organisation (Mandant) werden auch alle in der Organisation enthaltenen Ressourcen gelöscht. Bereiten Sie Ihre Organisation vor, indem Sie die ihr zugeordneten Ressourcen minimieren, bevor Sie sie löschen. Nur ein globaler Administrator von Azure Active Directory (Azure AD) kann eine Azure AD-Organisation im Portal löschen.

## <a name="prepare-the-organization"></a>Vorbereiten der Organisation

Sie können eine Organisation in Azure AD erst löschen, nachdem sie mehrere Prüfungen bestanden hat. Diese Prüfungen verringern das Risiko, dass das Löschen einer Azure AD-Organisation den Benutzerzugriff beeinträchtigt, z. B. die Möglichkeit, sich bei Microsoft 365 anzumelden oder auf Ressourcen in Azure zuzugreifen. Wenn beispielsweise die einem Abonnement zugeordnete Organisation versehentlich gelöscht wird, können Benutzer auf die Azure-Ressourcen für dieses Abonnement nicht mehr zugreifen. Die folgenden Bedingungen werden überprüft:

* Es darf keine Benutzer in der Azure AD-Organisation (Mandant) geben, außer einem globalen Administrator, der die Organisation löschen soll. Alle anderen Benutzer müssen gelöscht werden, bevor die Organisation gelöscht werden kann. Wenn Benutzer aus der lokalen Umgebung synchronisiert werden, muss die Synchronisierung zuerst deaktiviert werden, und die Benutzer müssen in der Cloudorganisation über das Azure-Portal oder mithilfe von Azure PowerShell-Cmdlets gelöscht werden.
* Es dürfen keine Anwendungen in der Organisation vorhanden sein. Alle Anwendungen müssen entfernt werden, bevor die Organisation gelöscht werden kann.
* Mit der Organisation dürfen keine Anbieter für mehrstufige Authentifizierung verknüpft sein.
* Der Organisation dürfen keine Abonnements für Microsoft Online Services (z. B. Microsoft Azure, Microsoft 365 oder Azure AD Premium) zugeordnet sein. Falls für Sie in Azure beispielsweise eine Azure AD-Standardorganisation erstellt wurde, dürfen Sie diese Organisation nicht löschen, wenn sie vom Azure-Abonnement noch für die Authentifizierung benötigt wird. Analog dazu dürfen Sie auch keine Organisation löschen, wenn ein anderer Benutzer dieser ein Abonnement zugeordnet hat.

## <a name="delete-the-organization"></a>Löschen der Organisation

1. Melden Sie sich beim [Azure AD Admin Center](https://aad.portal.azure.com) mit einem Konto an, das über globale Administratorberechtigungen für Ihre Organisation verfügt.

2. Wählen Sie **Azure Active Directory** aus.

3. Wechseln Sie zu der Organisation, die Sie löschen möchten.
  
   ![Bestätigen der Organisation vor dem Löschen](./media/directory-delete-howto/delete-directory-command.png)

4. Wählen Sie **Mandant löschen** aus.
  
   ![Auswählen des Befehls zum Löschen der Organisation](./media/directory-delete-howto/delete-directory-list.png)

5. Wenn Ihre Organisation mindestens eine Prüfung nicht bestanden hat, erhalten Sie einen Link zu weiteren Informationen, wie die Prüfung bestanden werden kann. Sobald Sie alle Prüfungen bestanden haben, klicken Sie auf **Löschen**, um den Vorgang abzuschließen.

## <a name="if-you-cant-delete-the-organization"></a>Wenn Sie die Organisation nicht löschen können

Bei der Konfiguration Ihrer Azure AD-Organisation haben Sie möglicherweise auch lizenzbasierte Abonnements für Ihre Organisation wie Azure AD Premium P2, Microsoft 365 Business Standard oder Enterprise Mobility + Security E5 aktiviert. Um einen versehentlichen Datenverlust zu vermeiden, können Sie eine Organisation erst löschen, nachdem die Abonnements vollständig gelöscht wurden. Die Abonnements müssen den Status **Bereitstellung aufgehoben** haben, damit die Organisation gelöscht werden kann. Ein Abonnement mit dem Status **Abgelaufen** oder **Gekündigt** wechselt in den Zustand **Deaktiviert**. Die letzte Stufe ist der Status **Bereitstellung aufgehoben**.

Informationen dazu, was zu erwarten ist, wenn ein Microsoft 365-Testabonnement abläuft (ohne bezahlte Partner/CSP-, Enterprise Agreement- oder Volumenlizenzen), finden Sie in der folgenden Tabelle. Unter [Was geschieht mit meinen Daten und dem Zugriff darauf, wenn mein Microsoft 365 Business-Abonnement endet?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3) finden Sie weitere Informationen zur Datenaufbewahrung und zum Abonnementlebenszyklus von Microsoft 365. 

Abonnementzustand | Daten | Zugriff auf Daten
----- | ----- | -----
Aktiv (30 Tage für die Testversion) | Daten für alle zugänglich | Benutzer haben normalen Zugriff auf Microsoft 365-Dateien oder -Apps<br>Administratoren besitzen normalen Zugriff auf das Microsoft 365 Admin Center und -Ressourcen 
Abgelaufen (30 Tage) | Daten für alle zugänglich| Benutzer haben normalen Zugriff auf Microsoft 365-Dateien oder -Apps<br>Administratoren besitzen normalen Zugriff auf das Microsoft 365 Admin Center und -Ressourcen
Deaktiviert (30 Tage) | Daten nur für Administrator zugänglich | Benutzer können nicht auf Microsoft 365-Dateien oder -Apps zugreifen<br>Administratoren können auf das Microsoft 365 Admin Center zugreifen, aber keine Lizenzen zuweisen oder Benutzer aktualisieren
Bereitstellung aufgehoben (30 Tage nach Deaktivierung) | Daten gelöscht (werden automatisch gelöscht, wenn keine anderen Dienste verwendet werden) | Benutzer können nicht auf Microsoft 365-Dateien oder -Apps zugreifen<br>Administratoren können auf das Microsoft 365 Admin Center zugreifen, um andere Abonnements zu erwerben und zu verwalten

## <a name="delete-a-subscription"></a>Löschen eines Abonnements

Sie können ein Abonnement im Microsoft 365 Admin Center in den Status **Bereitstellung aufgehoben** versetzen, damit es innerhalb von drei Tagen gelöscht wird.

1. Melden Sie sich beim [Microsoft 365 Admin Center](https://admin.microsoft.com) mit einem Konto mit Rechten eines globalen Administrators in Ihrer Organisation an. Wenn Sie versuchen, die Organisation „Contoso“ zu löschen, die die anfängliche Standarddomäne „contoso.onmicrosoft.com“ verwendet, melden Sie sich mit einem Benutzerprinzipalnamen wie admin@contoso.onmicrosoft.com an.

2. Zeigen Sie eine Vorschau des neuen Microsoft 365 Admin Center an, indem Sie sicherstellen, dass der Schalter **Testen Sie das neue Admin Center** aktiviert ist.

   ![Vorschau der neuen Microsoft 365 Admin Center-Umgebung](./media/directory-delete-howto/preview-toggle.png)

3. Sobald das neue Admin Center aktiviert ist, müssen Sie ein Abonnement kündigen, bevor Sie es löschen können. Wählen Sie **Abrechnung** und dann **Produkte und Dienste**  aus, und wählen Sie dann **Abonnement kündigen** für das zu kündigende Abonnement aus. Sie werden auf eine Feedbackseite weitergeleitet.

   ![Auswahl des zu kündigenden Abonnements](./media/directory-delete-howto/cancel-choose-subscription.png)

4. Füllen Sie das Feedbackformular aus, und wählen Sie dann **Abonnement kündigen** aus, um das Abonnement zu kündigen.

   ![Befehl „Kündigen“ in der Abonnementvorschau](./media/directory-delete-howto/cancel-command.png)

5. Jetzt können Sie das Abonnement löschen. Wählen Sie **Löschen** für das Abonnement aus, das Sie löschen möchten. Wenn Sie das Abonnement auf der Seite **Produkte und Dienste** nicht finden können, stellen Sie sicher, dass der **Abonnementstatus** auf **Alle** eingestellt ist.

   ![Link „Löschen“ zum Löschen des Abonnements](./media/directory-delete-howto/delete-command.png)

6. Wählen Sie **Abonnement löschen** aus, um das Abonnement zu löschen und die Geschäftsbedingungen zu akzeptieren. Alle Daten werden innerhalb von drei Tagen dauerhaft gelöscht. Innerhalb der dreitägigen Frist können Sie das [Abonnement reaktivieren](/office365/admin/subscriptions-and-billing/reactivate-your-subscription), sollten Sie Ihre Meinung ändern.
  
   ![Lesen Sie sich die Geschäftsbedingungen sorgfältig durch.](./media/directory-delete-howto/delete-terms.png)

7. Der Abonnementstatus hat sich nun geändert, und das Abonnement ist zum Löschen markiert. Das Abonnement wechselt 72 Stunden später in den Zustand **Bereitstellung aufgehoben**.

8. Sobald Sie eine Organisation in Ihrem Verzeichnis gelöscht haben und 72 Stunden verstrichen sind, können Sie sich wieder in Azure AD Admin Center anmelden. Dort sollten keine Aktionen erforderlich sein und keine Abonnements das Löschen Ihrer Organisation blockieren. Sie sollten Ihre Azure AD-Organisation nun erfolgreich löschen können.
  
   ![Bildschirm mit bestandener Abonnementprüfung bei Löschung](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>Ich habe ein Testabonnement, das die Löschung blockiert

Es gibt [Produkte zur Self-Service-Registrierung](/office365/admin/misc/self-service-sign-up) wie Microsoft Power BI, Rights Management Services, Microsoft Power Apps oder Dynamics 365, für die sich einzelne Benutzer über Microsoft 365 registrieren können. Dadurch wird auch ein Gastbenutzer zur Authentifizierung in Ihrer Azure AD-Organisation erstellt. Um einen Datenverlust zu vermeiden, blockieren diese Produkte zur Self-Service-Registrierung Organisationslöschungen so lange, bis sie vollständig aus dem Verzeichnis gelöscht wurden. Sie können nur vom Azure AD-Administrator gelöscht werden – unabhängig davon, ob der Benutzer einzeln registriert oder ihm das Produkt zugewiesen wurde.

Es gibt zwei Typen von Produkten zur Self-Service-Registrierung aufgrund der Art, wie sie zugewiesen werden: 

* Zuweisung auf Organisationsebene: Ein Azure AD-Administrator weist das Produkt der gesamten Organisation zu, und ein Benutzer kann den Dienst bei dieser Zuweisung auf Organisationsebene selbst dann aktiv nutzen, wenn er nicht einzeln lizenziert wurde.
* Zuweisung auf Benutzerebene: Ein einzelner Benutzer weist das Produkt während einer Self-Service-Registrierung im Wesentlichen sich selbst (ohne einen Administrator) zu. Sobald die Organisation von einem Administrator verwaltet wird (siehe dazu [Administratorübernahme einer nicht verwalteten Organisation](domains-admin-takeover.md)), kann der Administrator das Produkt Benutzern (ohne Self-Service-Registrierung) direkt zuweisen.  

Wenn Sie mit dem Löschen des Produkts zur Self-Service-Registrierung beginnen, löscht diese Aktion die Daten dauerhaft und entfernt den gesamten Benutzerzugriff auf den Dienst. Dann wird jeder Benutzer blockiert, dem das Angebot einzeln oder auf Organisationsebene zugewiesen wurde, sodass er sich nicht mehr anmelden oder auf vorhandene Daten zugreifen kann. Wenn Sie bei dem Produkt zur Self-Service-Registrierung wie [Microsoft Power BI-Dashboards](/power-bi/service-export-to-pbix) oder [Rights Management Services-Richtlinienkonfiguration](/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy) einen Datenverlust verhindern möchten, sorgen Sie dafür, dass die Daten gesichert und an einem anderen Ort gespeichert werden.

Weitere Informationen zu den derzeit verfügbaren Produkten und Diensten zur Self-Service-Registrierung finden Sie unter [Verfügbare Self-Service-Programme](/office365/admin/misc/self-service-sign-up#available-self-service-programs).

Informationen dazu, was zu erwarten ist, wenn ein Microsoft 365-Testabonnement abläuft (ohne bezahlte Partner/CSP-, Enterprise Agreement- oder Volumenlizenzen), finden Sie in der folgenden Tabelle. Unter [Was geschieht mit meinen Daten und dem Zugriff darauf, wenn mein Microsoft 365 Business-Abonnement endet?](/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires) finden Sie weitere Informationen zur Datenaufbewahrung und zum Abonnementlebenszyklus von Microsoft 365.

Produktstatus | Daten | Zugriff auf Daten
------------- | ---- | --------------
Aktiv (30 Tage für die Testversion) | Daten für alle zugänglich | Benutzer haben normalen Zugriff auf Produkte, Dateien oder Apps zur Self-Service-Registrierung<br>Administratoren besitzen normalen Zugriff auf das Microsoft 365 Admin Center und -Ressourcen
Deleted | Daten gelöscht | Benutzer können auf Produkte, Dateien oder Apps zur Self-Service-Registrierung nicht zugreifen<br>Administratoren können auf das Microsoft 365 Admin Center zugreifen, um andere Abonnements zu erwerben und zu verwalten

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>Wie kann ich ein Produkt zur Self-Service-Registrierung im Azure-Portal löschen?

Sie können ein Produkt zur Self-Service-Registrierung wie Microsoft Power BI oder Azure Rights Management Services in den Status **Löschen** versetzen, damit es im Azure-Portal sofort gelöscht wird.

1. Melden Sie sich beim [Azure AD Admin Center](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) mit dem Konto eines globalen Administrators in der Organisation an. Wenn Sie versuchen, die Organisation „Contoso“ zu löschen, die die anfängliche Standarddomäne „contoso.onmicrosoft.com“ verwendet, registrieren Sie sich mit einem Benutzerprinzipalnamen wie admin@contoso.onmicrosoft.com.

2. Wählen Sie **Lizenzen** aus, und wählen Sie dann **Self-Service-Registrierungsprodukte** aus. Sie können alle Produkte zur Self-Service-Registrierung getrennt von den auf Arbeitsplätzen basierenden Abonnements anzeigen. Wählen Sie das Produkt aus, das Sie dauerhaft löschen möchten. Hier ist ein Beispiel in Microsoft Power BI:

    ![Screenshot der Seite „Lizenzen – Produkte zur Self-Service-Registrierung“](./media/directory-delete-howto/licenses-page.png)

3. Wählen Sie **Löschen** aus, um das Produkt zu löschen, und akzeptieren Sie die Bedingungen, dass die Daten sofort und unwiderruflich gelöscht werden. Durch diese Löschaktion werden alle Benutzer und der Organisationszugriff auf das Produkt entfernt. Klicken Sie auf „Ja“, damit der Löschvorgang fortgesetzt wird.  

    ![Screenshot der Seite „Lizenzen – Produkte zur Self-Service-Registrierung“ mit geöffnetem Fenster „Produkt zur Self-Service-Registrierung löschen“](./media/directory-delete-howto/delete-product.png)

4. Wenn Sie **Ja** auswählen, wird das Löschen des Produkts zur Self-Service-Registrierung gestartet. In einer Benachrichtigung werden Sie informiert, dass der Löschvorgang ausgeführt wird.  

    ![Screenshot der Seite „Lizenzen – Produkte zur Self-Service-Registrierung“ mit angezeigter Benachrichtigung „Löschvorgang wird ausgeführt“](./media/directory-delete-howto/progress-message.png)

5. Jetzt wurde der Status des Produkts zur Self-Service-Registrierung in **Gelöscht** geändert. Wenn Sie die Seite aktualisieren, sollte das Produkt auf der Seite **Self-Service-Registrierungsprodukte** nicht mehr angezeigt werden.  

    ![Screenshot der Seite „Lizenzen – Produkte zur Self-Service-Registrierung“ mit dem Bereich „Produkt zur Self-Service-Registrierung wurde gelöscht“ auf der rechten Seite](./media/directory-delete-howto/product-deleted.png)

6. Sobald Sie alle Produkte gelöscht haben, können Sie sich wieder im Azure AD Admin Center anmelden. Dort sollten keine Aktionen erforderlich sein und keine Produkte das Löschen Ihrer Organisation blockieren. Sie sollten Ihre Azure AD-Organisation nun erfolgreich löschen können.

    ![Der Benutzername wurde falsch eingegeben oder nicht gefunden](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>Nächste Schritte

[Dokumentation zu Azure Active Directory](../index.yml)
