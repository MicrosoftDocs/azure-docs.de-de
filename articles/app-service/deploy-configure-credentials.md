---
title: Konfigurieren von Anmeldeinformationen für die Bereitstellung
description: Erfahren Sie, welche Arten von Anmeldeinformationen für die Bereitstellung in Azure App Service verfügbar sind und wie Sie diese konfigurieren und verwenden.
ms.topic: article
ms.date: 08/14/2019
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: e5793d21f27128162095e2d86e13006c5b6e7b7c
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007992"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Konfigurieren von Anmeldeinformationen für die Azure App Service-Bereitstellung
[Azure App Service](./overview.md) unterstützt zwei Arten von Anmeldeinformationen für [lokale Git-Bereitstellungen](deploy-local-git.md) und [FTP/S-Bereitstellungen](deploy-ftp.md). Diese Anmeldeinformationen sind nicht identisch mit den Anmeldeinformationen Ihres Azure-Abonnements.

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

## <a name="configure-user-level-credentials"></a><a name="userscope"></a>Konfigurieren von Anmeldeinformationen auf Benutzerebene

Sie können die Anmeldeinformationen auf Benutzerebene auf der [Ressourcenseite](../azure-resource-manager/management/manage-resources-portal.md#manage-resources) einer App konfigurieren. Unabhängig von der App, in der Sie diese Anmeldeinformationen konfigurieren, gelten sie für alle Apps und für alle Abonnements in Ihrem Azure-Konto. 

### <a name="in-the-cloud-shell"></a>In der Cloud Shell

Führen Sie zum Konfigurieren des Bereitstellungsbenutzers in der [Cloud Shell](https://shell.azure.com) den Befehl [az webapp deployment user set](/cli/azure/webapp/deployment/user#az-webapp-deployment-user-set) aus. Ersetzen Sie \<username> und \<password> durch Ihren Benutzernamen und Ihr Kennwort für die Bereitstellung. 

- Der Benutzername muss in Azure eindeutig sein und darf bei lokalen Git-Pushes nicht das Symbol „@“ enthalten. 
- Das Kennwort muss mindestens acht Zeichen lang sein und zwei der folgenden drei Elemente enthalten: Buchstaben, Zahlen und Symbole. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

In der JSON-Ausgabe wird das Kennwort als `null` angezeigt. Wenn Sie den Fehler `'Conflict'. Details: 409` erhalten, müssen Sie den Benutzernamen ändern. Wenn Sie den Fehler `'Bad Request'. Details: 400` erhalten, müssen Sie ein sichereres Kennwort verwenden. 

### <a name="in-the-portal"></a>Im Portal

Im Azure-Portal muss mindestens eine App vorhanden sein, bevor Sie auf die Seite mit den Anmeldeinformationen für die Bereitstellung zugreifen können. So konfigurieren Sie die Anmeldeinformationen auf Benutzerebene

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) im linken Menü die Optionen **App Services** >  **\<any_app>**  > **Bereitstellungscenter** > **FTP** > **Dashboard** aus.

    ![Zeigt, wie Sie das FTP-Dashboard im Bereitstellungscenter in Azure App Services auswählen.](./media/app-service-deployment-credentials/access-no-git.png)

    Oder wenn Sie Git-Bereitstellung bereits konfiguriert haben, wählen Sie **App Services** >  **&lt;beliebige_App>**  > **Bereitstellungscenter** > **FTP/Anmeldeinformationen** aus.

    ![Zeigt, wie Sie das FTP-Dashboard für Ihre konfigurierte Git-Bereitstellung im Bereitstellungscenter in Azure App Services auswählen.](./media/app-service-deployment-credentials/access-with-git.png)

2. Wählen Sie **Benutzeranmeldeinformationen** aus, konfigurieren Sie den Benutzernamen und das Kennwort, und wählen Sie dann **Anmeldeinformationen speichern** aus.

Wenn Sie Anmeldeinformationen für die Bereitstellung festgelegt haben, finden Sie den *Git*-Benutzernamen für Bereitstellungen auf der Seite **Übersicht** Ihrer App.

![Zeigt, wie Sie den Benutzernamen der Git-Bereitstellung auf der Übersichtsseite Ihrer App finden.](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Wenn die Git-Bereitstellung konfiguriert ist, wird auf der Seite ein **Git-/Bereitstellungsbenutzername** angezeigt, andernfalls ein **FTP-/Bereitstellungsbenutzername**.

> [!NOTE]
> Ihr Kennwort auf Benutzerebene für die Bereitstellung wird von Azure nicht angezeigt. Wenn Sie das Kennwort vergessen, können Sie Ihre Anmeldeinformationen über die Schritte in diesem Abschnitt zurücksetzen.
>
> 

## <a name="use-user-level-credentials-with-ftpftps"></a>Verwenden von Anmeldeinformationen auf Benutzerebene mit FTP/FTPS

Für die Authentifizierung an einem FTP/FTPS-Endpunkt mit Anmeldeinformationen auf Benutzerebene ist ein Benutzername im folgenden Format erforderlich: `<app-name>\<user-name>`

Weil Anmeldeinformationen auf Benutzerebene mit dem Benutzer und nicht einer bestimmten Ressource verknüpft sind, muss der Benutzername dieses Format aufweisen, damit die Anmeldeaktion an den richtigen App-Endpunkt weitergeleitet wird.

## <a name="get-and-reset-app-level-credentials"></a><a name="appscope"></a>Abrufen und Zurücksetzen der Anmeldeinformationen auf App-Ebene
So rufen Sie die Anmeldeinformationen auf App-Ebene ab

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) im linken Menü **App Services** >  **&lt;beliebige_App>**  > **Bereitstellungscenter** > **FTP/Anmeldeinformationen** aus.

2. Wählen Sie **App-Anmeldeinformationen** aus und dann den Link **Kopieren**, um den Benutzernamen oder das Kennwort zu kopieren.

Um die Anmeldeinformationen auf App-Ebene zurückzusetzen, wählen Sie im selben Dialogfeld **Anmeldeinformationen zurücksetzen** aus.

## <a name="disable-basic-authentication"></a>Deaktivieren der Standardauthentifizierung

Einige Organisationen müssen Sicherheitsanforderungen erfüllen und möchten daher den Zugriff über FTP oder WebDeploy deaktivieren. Auf diese Weise können die Mitglieder der Organisation nur auf Ihre App Services über APIs zugreifen, die von Azure Active Directory (Azure AD) gesteuert werden.

### <a name="ftp"></a>FTP

Um den FTP-Zugriff auf die Website zu deaktivieren, führen Sie den folgenden CLI-Befehl aus. Ersetzen Sie die Platzhalter durch die Ressourcengruppe und den Websitenamen. 

```bash
az resource update --resource-group <resource-group> --name ftp --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

Um zu bestätigen, dass der FTP-Zugriff blockiert ist, können Sie versuchen, sich mit einem FTP-Client wie FileZilla zu authentifizieren. Zum Abrufen der Veröffentlichungsanmeldeinformationen navigieren Sie zum Übersichtsblatt Ihrer Website, und klicken Sie auf „Veröffentlichungsprofil herunterladen“. Verwenden Sie den FTP-Hostnamen, den Benutzernamen und das Kennwort für die Datei, um sich zu authentifizieren, und Sie erhalten eine 401-Fehlerantwort, die angibt, dass Sie nicht autorisiert sind.

### <a name="webdeploy-and-scm"></a>WebDeploy und SCM

Führen Sie den folgenden CLI-Befehl aus, um Standardauthentifizierungszugriff auf den WebDeploy-Port und die SCM-Website zu deaktivieren. Ersetzen Sie die Platzhalter durch die Ressourcengruppe und den Websitenamen. 

```bash
az resource update --resource-group <resource-group> --name scm --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

Um zu bestätigen, dass die Anmeldeinformationen für das Veröffentlichungsprofil für WebDeploy blockiert werden, versuchen Sie, eine [Web-App mit Visual Studio 2019 zu veröffentlichen](/visualstudio/deployment/quickstart-deploy-to-azure).

### <a name="disable-access-to-the-api"></a>Deaktivieren des Zugriffs auf die API

Die API im vorherigen Abschnitt basiert auf rollenbasierter Zugriffssteuerung von Azure (Azure RBAC). Das bedeutet, dass Sie eine [benutzerdefinierte Rolle erstellen](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role) und der Rolle Benutzer mit niedrigeren Berechtigungen zuweisen können, sodass diese keine Standardauthentifizierung für beliebige Websites aktivieren können. Um die benutzerdefinierte Rolle zu konfigurieren, [befolgen Sie diese Anweisungen](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#create-a-custom-rbac-role).

Sie können auch [Azure Monitor](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#audit-with-azure-monitor) verwenden, um alle erfolgreichen Authentifizierungsanforderungen zu überwachen, sowie [Azure Policy](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#enforce-compliance-with-azure-policy), um diese Konfiguration für alle Standorte in Ihrem Abonnement zu erzwingen.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie diese Anmeldeinformationen zum Bereitstellen Ihrer App über [lokales Git](deploy-local-git.md) oder [FTP/S](deploy-ftp.md) verwenden.
