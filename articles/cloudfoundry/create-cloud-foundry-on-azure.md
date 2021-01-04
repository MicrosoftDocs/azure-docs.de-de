---
title: Erstellen eines Pivotal Cloud Foundry-Clusters in Azure
description: Erfahren Sie, wie Sie die Parameter einrichten, die zum Bereitstellen eines PCF-Clusters (Pivotal Cloud Foundry) in Azure erforderlich sind.
services: Cloud Foundry
documentationcenter: CloudFoundry
author: ruyakubu
manager: brunoborges
editor: ruyakubu
ms.assetid: ''
ms.author: ruyakubu
ms.date: 09/13/2018
ms.devlang: ''
ms.service: azure
ms.tgt_pltfrm: multiple
ms.topic: tutorial
ms.workload: web
ms.openlocfilehash: 65d8ade438228d7af71de1fc66639e5b6de2edda
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93040804"
---
# <a name="create-a-pivotal-cloud-foundry-cluster-on-azure"></a>Erstellen eines Pivotal Cloud Foundry-Clusters in Azure

Dieses Tutorial enthält Schritte zum schnellen Erstellen und Generieren der Parameter, die zum Bereitstellen eines PCF-Clusters (Pivotal Cloud Foundry) in Azure erforderlich sind. Die Pivotal Cloud Foundry-Lösung finden Sie, indem Sie auf dem Azure [Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry) eine Suche durchführen.

![Suchen nach Pivotal Cloud Foundry in Azure](media/deploy/pcf-marketplace.png)


## <a name="generate-an-ssh-public-key"></a>Generieren eines öffentlichen SSH-Schlüssels

Es gibt mehrere Möglichkeiten, unter Windows, Mac oder Linux einen öffentlichen SSH-Schlüssel (Secure Shell) zu generieren.

```Bash
ssh-keygen -t rsa -b 2048
```

Weitere Informationen finden Sie unter [Verwenden von SSH-Schlüsseln mit Windows in Azure](../virtual-machines/linux/ssh-from-windows.md).

## <a name="create-a-service-principal"></a>Erstellen eines Dienstprinzipals

> [!NOTE]
>
> Zum Erstellen eines Dienstprinzipals ist die Berechtigung „Kontobesitzer“ erforderlich. Sie können auch ein Skript schreiben, um die Erstellung des Dienstprinzipals zu automatisieren. Sie können beispielsweise den Azure CLI-Befehl [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest) verwenden.

1. Melden Sie sich bei Ihrem Azure-Konto an.

    `az login`

    ![Azure CLI-Anmeldung](media/deploy/az-login-output.png )
 
    Kopieren Sie den Wert von „id“ als **Abonnement-ID** und den Wert von „tenantId“ zur späteren Verwendung.

2. Legen Sie Ihr Standardabonnement für diese Konfiguration fest.

    `az account set -s {id}`

3. Erstellen Sie eine Azure Active Directory-Anwendung für Ihre PCF-Instanz. Geben Sie ein eindeutiges alphanumerisches Kennwort an. Speichern Sie das Kennwort als **clientSecret** zur späteren Verwendung.

    `az ad app create --display-name "Svc Principal for OpsManager" --password {enter-your-password} --homepage "{enter-your-homepage}" --identifier-uris {enter-your-homepage}`

    Kopieren Sie den Wert von „appId“ in der Ausgabe als **clientID** zur späteren Verwendung.

    > [!NOTE]
    >
    > Wählen Sie Ihre eigene Anwendungsstartseite und den Bezeichner-URI aus, z. B. „http\://www\.contoso.com“.

4. Erstellen Sie einen Dienstprinzipal mit Ihrer neuen App-ID (appId).

    `az ad sp create --id {appId}`

5. Legen Sie als Berechtigung für den Dienstprinzipal die Rolle „Mitwirkender“ fest.

    `az role assignment create --assignee "{enter-your-homepage}" --role "Contributor"`

    Sie können stattdessen auch den folgenden Befehl verwenden.

    `az role assignment create --assignee {service-principal-name} --role "Contributor"`

    ![Rollenzuweisung für den Dienstprinzipal](media/deploy/svc-princ.png )

6. Vergewissern Sie sich, dass Sie sich mit der App-ID (appID), dem Kennwort (password) und der Mandanten-ID (tenantID) erfolgreich bei Ihrem Dienstprinzipal anmelden können.

    `az login --service-principal -u {appId} -p {your-password}  --tenant {tenantId}`

7. Erstellen Sie eine JSON-Datei im folgenden Format. Verwenden Sie die Werte für **subscriptionID** , **tenantID** , **clientID** und **clientSecret** , die Sie zuvor kopiert haben. Speichern Sie die Datei .

    ```json
    {
        "subscriptionID": "{enter-your-subscription-Id-here}",
        "tenantID": "{enter-your-tenant-Id-here}",
        "clientID": "{enter-your-app-Id-here}",
        "clientSecret": "{enter-your-key-here}"
    }
    ```

## <a name="get-the-pivotal-network-token"></a>Abrufen des Tokens für das Pivotal-Netzwerk

1. Melden Sie sich bei Ihrem Konto für das [Pivotal-Netzwerk](https://network.pivotal.io) an, oder führen Sie die Registrierung dafür durch.
2. Klicken Sie oben rechts auf der Seite auf Ihren Profilnamen. Wählen Sie **Profil bearbeiten** aus.
3. Scrollen Sie zum unteren Rand der Seite, und kopieren Sie den Wert von **LEGACY API TOKEN**. Dies ist der Wert für das **Token für das Pivotal-Netzwerk** , den Sie später verwenden.

## <a name="provision-your-cloud-foundry-cluster-on-azure"></a>Bereitstellen des Cloud Foundry-Clusters in Azure

Sie verfügen jetzt über alle Parameter, die Sie für die Bereitstellung Ihres [Pivotal Cloud Foundry-Clusters in Azure](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry) benötigen.
Geben Sie die Parameter ein, und erstellen Sie Ihren PCF-Cluster.

## <a name="verify-the-deployment-and-sign-in-to-the-pivotal-ops-manager"></a>Überprüfen der Bereitstellung und Anmelden bei Pivotal Operations Manager

1. Für Ihren PCF-Cluster wird ein Bereitstellungsstatus angezeigt.

    ![Azure-Bereitstellungsstatus](media/deploy/deployment.png )

2. Klicken Sie im Navigationsbereich auf der linken Seite auf den Link **Bereitstellungen** , um Anmeldeinformationen für PCF Operations Manager abzurufen. Wählen Sie auf der nächsten Seite den **Bereitstellungsnamen** aus.
3. Klicken Sie im linken Navigationsbereich auf den Link **Ausgaben** , um die URL, den Benutzernamen und das Kennwort für PCF Operations Manager anzuzeigen. Der Wert von „OPSMAN-FQDN“ ist die URL.
 
    ![Ausgabe der Cloud Foundry-Bereitstellung](media/deploy/deploy-outputs.png )
 
4. Rufen Sie die URL in einem Webbrowser auf. Geben Sie die Anmeldeinformationen aus dem vorherigen Schritt ein, um sich anzumelden.

    ![Pivotal-Anmeldeseite](media/deploy/pivotal-login.png )
         
    > [!NOTE]
    >
    > Wenn im Internet Explorer-Browser ein Fehler mit der Warnmeldung „Website ist nicht sicher“ auftritt, können Sie auf **Weitere Informationen** klicken und die Webseite aufrufen. Klicken Sie im Firefox-Browser auf **Erweitert** , und fügen Sie die Zertifizierung hinzu, um fortzufahren.

5. In PCF Operations Manager werden die bereitgestellten Azure-Instanzen angezeigt. Jetzt können Sie Ihre Anwendungen hier bereitstellen und verwalten.
               
    ![Bereitgestellte Azure-Instanz in Pivotal](media/deploy/ops-mgr.png )
