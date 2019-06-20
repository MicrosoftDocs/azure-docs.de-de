---
title: Löschen eines Azure AD-Verzeichnisses | Azure Active Directory | Microsoft-Dokumentation
description: In diesem Artikel wird erläutert, wie Sie ein Azure AD-Verzeichnis zum Löschen vorbereiten, einschließlich Self-Service-Verzeichnissen.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/15/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91ac6b4530414850c52605bac8cb701aa2b877d4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60473127"
---
# <a name="delete-a-directory-in-azure-active-directory"></a>Löschen eines Verzeichnisses in Azure Active Directory

Beim Löschen eines Azure AD-Verzeichnisses werden auch alle darin enthaltenen Ressourcen gelöscht. Bereiten Sie Ihre Organisation vor, indem Sie die ihr zugeordneten Ressourcen minimieren, bevor Sie sie löschen. Nur ein globaler Administrator von Azure Active Directory (Azure AD) kann ein Azure AD-Verzeichnis im Portal löschen.

## <a name="prepare-the-directory"></a>Vorbereiten des Verzeichnisses

Sie können ein Verzeichnis in Azure AD erst löschen, nachdem es mehrere Prüfungen bestanden hat. Diese Prüfungen verringern das Risiko, dass das Löschen eines Azure AD-Verzeichnisses den Benutzerzugriff beeinträchtigt, wie z.B. die Möglichkeit, sich bei Office 365 anzumelden oder auf Ressourcen in Azure zuzugreifen. Wenn beispielsweise das einem Abonnement zugeordnete Verzeichnis versehentlich gelöscht wird, können Benutzer auf die Azure-Ressourcen für dieses Abonnement nicht mehr zugreifen. Die folgenden Bedingungen werden überprüft:

* Es darf keine Benutzer im Verzeichnis geben, außer einem globalen Administrator, der das Verzeichnis löschen soll. Andere Benutzer müssen gelöscht werden, bevor das Verzeichnis gelöscht werden kann. Wenn Benutzer aus der lokalen Umgebung synchronisiert werden, muss die Synchronisierung zuerst deaktiviert werden, und die Benutzer müssen im Cloudverzeichnis über das Azure-Portal oder mithilfe von Azure PowerShell-Cmdlets gelöscht werden.
* Es können keine Anwendungen im Verzeichnis vorhanden sein. Alle Anwendungen müssen entfernt werden, bevor das Verzeichnis gelöscht werden kann.
* Mit dem Verzeichnis dürfen keine Anbieter einer mehrstufigen Authentifizierung verknüpft sein.
* Dem Verzeichnis können keine Abonnements für Microsoft Online Services, z.B. Microsoft Azure, Office 365 oder Azure AD Premium, zugeordnet werden. Wenn für Sie in Azure beispielsweise ein Standardverzeichnis erstellt wurde, können Sie dieses Verzeichnis nicht löschen, wenn es vom Azure-Abonnement noch für die Authentifizierung benötigt wird. Analog dazu können Sie auch kein Verzeichnis löschen, wenn ein anderer Benutzer dem Verzeichnis ein Abonnement zugeordnet hat.

## <a name="delete-the-directory"></a>Löschen des Verzeichnisses

1. Melden Sie sich beim [Azure AD Admin Center](https://aad.portal.azure.com) mit einem Konto an, das über globale Administratorberechtigungen für Ihre Organisation verfügt.

2. Wählen Sie **Azure Active Directory**.

3. Wechseln Sie zu dem Verzeichnis, das Sie löschen möchten.
  
   ![Bestätigen der Organisation vor dem Löschen](./media/directory-delete-howto/delete-directory-command.png)

4. Klicken Sie auf **Verzeichnis löschen**.
  
   ![Auswählen des Befehls zum Löschen der Organisation](./media/directory-delete-howto/delete-directory-list.png)

5. Wenn Ihr Verzeichnis eine oder mehrere Prüfungen nicht bestanden hat, erhalten Sie einen Link zu weiteren Informationen, wie die Prüfung bestanden werden kann. Sobald Sie alle Prüfungen bestanden haben, klicken Sie auf **Löschen**, um den Vorgang abzuschließen.

## <a name="if-you-cant-delete-the-directory"></a>Wenn Sie das Verzeichnis nicht löschen können

Bei der Konfiguration Ihres Azure AD-Verzeichnisses haben Sie möglicherweise auch lizenzbasierte Abonnements für Ihre Organisation wie Azure AD Premium P2, Office 365 Business Premium oder Enterprise Mobility + Security E5 aktiviert. Um einen versehentlichen Datenverlust zu vermeiden, können Sie ein Verzeichnis erst löschen, nachdem die Abonnements vollständig gelöscht wurden. Die Abonnements müssen den Status **Bereitstellung aufgehoben** haben, damit das Verzeichnis gelöscht werden kann. Ein Abonnement mit dem Status **Abgelaufen** oder **Gekündigt** wechselt in den Zustand **Deaktiviert**. Die letzte Stufe ist der Status **Bereitstellung aufgehoben**.

Informationen dazu, was zu erwarten ist, wenn ein Testabonnement für Office 365 ausläuft (ohne bezahlte Partner/CSP-, Enterprise Agreement- oder Volumenlizenzen), finden Sie in der folgenden Tabelle. Weitere Informationen zum Datenaufbewahrungs- und Abonnementlebenszyklus von Office 365 finden Sie unter [Was geschieht mit meinen Daten und dem Zugriff darauf, wenn mein Office 365 Business-Abonnement endet?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3). 

Abonnementzustand | Daten | Zugriff auf Daten
----- | ----- | -----
Aktiv (30 Tage für die Testversion) | Daten für alle zugänglich | Benutzer haben normalen Zugriff auf Office 365-Dateien oder -Apps<br>Administratoren besitzen normalen Zugriff auf das Microsoft 365 Admin Center und -Ressourcen 
Abgelaufen (30 Tage) | Daten für alle zugänglich| Benutzer haben normalen Zugriff auf Office 365-Dateien oder -Apps<br>Administratoren besitzen normalen Zugriff auf das Microsoft 365 Admin Center und -Ressourcen
Deaktiviert (30 Tage) | Daten nur für Administrator zugänglich | Benutzer können nicht auf Office 365-Dateien oder -Apps zugreifen<br>Administratoren können auf das Microsoft 365 Admin Center zugreifen, aber keine Lizenzen zuweisen oder Benutzer aktualisieren
Bereitstellung aufgehoben (30 Tage nach Deaktivierung) | Daten gelöscht (werden automatisch gelöscht, wenn keine anderen Dienste verwendet werden) | Benutzer können nicht auf Office 365-Dateien oder -Apps zugreifen<br>Administratoren können auf das Microsoft 365 Admin Center zugreifen, um andere Abonnements zu erwerben und zu verwalten

## <a name="delete-a-subscription"></a>Löschen eines Abonnements

Sie können ein Abonnement im Microsoft 365 Admin Center in den Status „Bereitstellung aufgehoben“ versetzen, damit es innerhalb von drei Tagen gelöscht wird.

1. Melden Sie sich beim [Microsoft 365 Admin Center](https://admin.microsoft.com) mit einem Konto mit Rechten eines globalen Administrators in Ihrer Organisation an. Wenn Sie versuchen, das Verzeichnis „Contoso“ zu löschen, das die anfängliche Standarddomäne „contoso.onmicrosoft.com“ hat, melden Sie sich mit einem Benutzerprinzipalnamen wie admin@contoso.onmicrosoft.com an.

2. Wählen Sie **Abrechnung**, anschließend **Abonnements** und dann das Abonnement aus, das Sie kündigen möchten. Nachdem Sie auf **Kündigen** geklickt haben, aktualisieren Sie die Seite.
  
   ![Link „Löschen“ zum Löschen des Abonnements](./media/directory-delete-howto/delete-command.png)
  
3. Klicken Sie auf **Löschen**, um das Abonnement zu löschen, und akzeptieren Sie die Geschäftsbedingungen. Alle Daten werden innerhalb von drei Tagen dauerhaft gelöscht. Sie können das Abonnement innerhalb der dreitägigen Frist reaktivieren, sollten Sie Ihre Meinung ändern.
  
   ![Lesen Sie sich die Geschäftsbedingungen sorgfältig durch.](./media/directory-delete-howto/delete-terms.png)

4. Nachdem sich der Abonnementzustand geändert hat, wird das Abonnement zum Löschen gekennzeichnet. Das Abonnement wechselt 72 Stunden später in den Zustand **Bereitstellung aufgehoben**.

5. Sobald Sie ein Abonnement in Ihrem Verzeichnis gelöscht haben und 72 Stunden verstrichen sind, können Sie sich wieder im Azure AD Admin Center anmelden. Dort sollten keine Aktionen erforderlich sein und keine Abonnements das Löschen Ihres Verzeichnisses blockieren. Sie sollten Ihr Azure AD-Verzeichnis nun erfolgreich löschen können.
  
   ![Bildschirm mit bestandener Abonnementprüfung bei Löschung](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>Ich habe ein Testabonnement, das die Löschung blockiert

Es gibt [Produkte zur Self-Service-Registrierung](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide) wie Microsoft Power BI, Rights Management Services, Microsoft Power Apps oder Dynamics 365, für die sich einzelne Benutzer über Office 365 registrieren können. Dadurch wird auch ein Gastbenutzer zur Authentifizierung in Ihrem Azure AD-Verzeichnis erstellt. Um einen Datenverlust zu vermeiden, blockieren diese Produkte zur Self-Service-Registrierung Verzeichnislöschungen so lange, bis sie vollständig aus dem Verzeichnis gelöscht wurden. Sie können nur vom Azure AD-Administrator gelöscht werden – unabhängig davon, ob der Benutzer einzeln registriert oder ihm das Produkt zugewiesen wurde.

Es gibt zwei Typen von Produkten zur Self-Service-Registrierung aufgrund der Art, wie sie zugewiesen werden: 

* Zuweisung auf Organisationsebene: Ein Azure AD-Administrator weist das Produkt der gesamten Organisation zu, und ein Benutzer kann den Dienst bei dieser Zuweisung auf Organisationsebene selbst dann aktiv nutzen, wenn er nicht einzeln lizenziert wurde.
* Zuweisung auf Benutzerebene: Ein einzelner Benutzer weist das Produkt während einer Self-Service-Registrierung im Wesentlichen sich selbst (ohne einen Administrator) zu. Sobald die Organisation von einem Administrator verwaltet wird (siehe dazu [Administratorübernahme eines nicht verwalteten Verzeichnisses](domains-admin-takeover.md)), kann der Administrator das Produkt Benutzern (ohne Self-Service-Registrierung) direkt zuweisen.  

Wenn Sie mit dem Löschen des Produkts zur Self-Service-Registrierung beginnen, löscht diese Aktion die Daten dauerhaft und entfernt den gesamten Benutzerzugriff auf den Dienst. Dann wird jeder Benutzer blockiert, dem das Angebot einzeln oder auf Organisationsebene zugewiesen wurde, sodass er sich nicht mehr anmelden oder auf vorhandene Daten zugreifen kann. Wenn Sie bei dem Produkt zur Self-Service-Registrierung wie [Microsoft Power BI-Dashboards](https://docs.microsoft.com/power-bi/service-export-to-pbix) oder [Rights Management Services-Richtlinienkonfiguration](https://docs.microsoft.com/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy) einen Datenverlust verhindern möchten, sorgen Sie dafür, dass die Daten gesichert und an einem anderen Ort gespeichert werden.

Weitere Informationen zu den derzeit verfügbaren Produkten und Diensten zur Self-Service-Registrierung finden Sie unter [Verfügbare Self-Service-Programme](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide#available-self-service-programs).

Informationen dazu, was zu erwarten ist, wenn ein Testabonnement für Office 365 ausläuft (ohne bezahlte Partner/CSP-, Enterprise Agreement- oder Volumenlizenzen), finden Sie in der folgenden Tabelle. Weitere Informationen zum Datenaufbewahrungs- und Abonnementlebenszyklus von Office 365 finden Sie unter  [Was geschieht mit meinen Daten und dem Zugriff darauf, wenn mein Office 365 Business-Abonnement endet?](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires?view=o365-worldwide).

Produktstatus | Daten | Zugriff auf Daten
------------- | ---- | --------------
Aktiv (30 Tage für die Testversion) | Daten für alle zugänglich | Benutzer haben normalen Zugriff auf Produkte, Dateien oder Apps zur Self-Service-Registrierung<br>Administratoren besitzen normalen Zugriff auf das Microsoft 365 Admin Center und -Ressourcen
Deleted | Daten gelöscht | Benutzer können auf Produkte, Dateien oder Apps zur Self-Service-Registrierung nicht zugreifen<br>Administratoren können auf das Microsoft 365 Admin Center zugreifen, um andere Abonnements zu erwerben und zu verwalten

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>Wie kann ich ein Produkt zur Self-Service-Registrierung im Azure-Portal löschen?

Sie können ein Produkt zur Self-Service-Registrierung wie Microsoft Power BI oder Azure Rights Management Services in den Status **Löschen** versetzen, damit es im Azure-Portal sofort gelöscht wird.

1. Melden Sie sich beim  [Azure AD Admin Center](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)  mit einem Konto mit Rechten eines globalen Administrators in der Organisation an. Wenn Sie versuchen, das Verzeichnis „Contoso“ zu löschen, das die anfängliche Standarddomäne „contoso.onmicrosoft.com“ hat, melden Sie sich mit einem Benutzerprinzipalnamen wie admin@contoso.onmicrosoft.com an.

2. Wählen Sie **Lizenzen** und dann  **Self-Service-Registrierungsprodukte** aus. Sie können alle Produkte zur Self-Service-Registrierung getrennt von den auf Arbeitsplätzen basierenden Abonnements anzeigen. Wählen Sie das Produkt aus, das Sie dauerhaft löschen möchten. Hier ist ein Beispiel in Microsoft Power BI:

    ![Der Benutzername wurde falsch eingegeben oder nicht gefunden](./media/directory-delete-howto/licenses-page.png)

3. Wählen Sie  **Löschen**  aus, um das Produkt zu löschen, und akzeptieren Sie die Bedingungen, dass die Daten sofort und unwiderruflich gelöscht werden. Durch diese Löschaktion werden alle Benutzer und der Organisationszugriff auf das Produkt entfernt. Klicken Sie auf „Ja“, damit der Löschvorgang fortgesetzt wird.  

    ![Der Benutzername wurde falsch eingegeben oder nicht gefunden](./media/directory-delete-howto/delete-product.png)

4. Wenn Sie **Ja** auswählen, wird das Löschen des Produkts zur Self-Service-Registrierung gestartet. In einer Benachrichtigung werden Sie informiert, dass der Löschvorgang ausgeführt wird.  

    ![Der Benutzername wurde falsch eingegeben oder nicht gefunden](./media/directory-delete-howto/progress-message.png)

5. Jetzt wurde der Status des Produkts zur Self-Service-Registrierung in **Gelöscht** geändert. Wenn Sie die Seite aktualisieren, sollte das Produkt auf der Seite **Self-Service-Registrierungsprodukte** nicht mehr angezeigt werden.  

    ![Der Benutzername wurde falsch eingegeben oder nicht gefunden](./media/directory-delete-howto/product-deleted.png)

6. Sobald Sie alle Produkte gelöscht haben, können Sie sich wieder im Azure AD Admin Center anmelden. Dort sollten keine Aktionen erforderlich sein und keine Produkte das Löschen Ihres Verzeichnisses blockieren. Sie sollten Ihr Azure AD-Verzeichnis nun erfolgreich löschen können.

    ![Der Benutzername wurde falsch eingegeben oder nicht gefunden](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>Nächste Schritte

[Dokumentation zu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
