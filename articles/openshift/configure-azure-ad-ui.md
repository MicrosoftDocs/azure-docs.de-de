---
title: 'Azure Red Hat OpenShift mit OpenShift 4: Konfigurieren der Azure Active Directory-Authentifizierung mithilfe des Azure-Portals und der OpenShift-Webkonsole'
description: Informationen zur Azure Active Directory-Authentifizierung für einen Azure Red Hat OpenShift-Cluster mit OpenShift 4 mithilfe des Azure-Portals und der OpenShift-Webkonsole
ms.service: container-service
ms.topic: article
ms.date: 03/12/2020
author: sabbour
ms.author: asabbour
keywords: aro, openshift, az aro, red hat, cli
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 1b9e4d1f1b989caa317384292d013af255530f11
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92748067"
---
# <a name="configure-azure-active-directory-authentication-for-an-azure-red-hat-openshift-4-cluster-portal"></a>Konfigurieren der Azure Active Directory-Authentifizierung für einen Azure Red Hat OpenShift 4-Cluster (Portal)

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial mindestens Version 2.6.0 der Azure CLI ausführen. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="before-you-begin"></a>Voraussetzungen

Erstellen Sie die **OAuth-Rückruf-URL** des Clusters, und notieren Sie sich diese. Ersetzen Sie **aro-rg** durch den Namen Ihrer Ressourcengruppe und **aro-cluster** durch den Namen Ihres Clusters.

> [!NOTE]
> Der Abschnitt `AAD` in der OAuth-Rückruf-URL sollte mit dem Namen des OAuth-Identitätsanbieters identisch sein, den Sie später einrichten.

```azurecli-interactive
domain=$(az aro show -g aro-rg -n aro-cluster --query clusterProfile.domain -o tsv)
location=$(az aro show -g aro-rg -n aro-cluster --query location -o tsv)
echo "OAuth callback URL: https://oauth-openshift.apps.$domain.$location.aroapp.io/oauth2callback/AAD"
```

## <a name="create-an-azure-active-directory-application-for-authentication"></a>Erstellen einer Azure Active Directory-Anwendung für die Authentifizierung

Melden Sie sich beim Azure-Portal an, navigieren Sie zum Blatt [App-Registrierungen](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade), und klicken Sie dann auf **Neue Registrierungs** , um eine neue Anwendung zu erstellen.

Geben Sie einen Namen für die Anwendung ein, z. B. **aro-azuread-auth** , und fügen Sie den **Umleitungs-URI** ein. Verwenden Sie dafür den Wert der zuvor abgerufenen OAuth-Rückruf-URL.

![Registrierung einer neuen Anwendung](media/aro4-ad-registerapp.png)

Navigieren Sie zu **Certificates & secrets** (Zertifikate und Geheimnisse), klicken Sie auf **Neuer geheimer Clientschlüssel** , und geben Sie die Details ein. Notieren Sie sich den Schlüsselwert, da Sie ihn später noch benötigen. Sie können Sie ihn danach nicht noch einmal abrufen.

![Erstellen eines Geheimnisses](media/aro4-ad-clientsecret.png)

Navigieren Sie zur **Übersicht** , und notieren Sie sich die **Anwendungs-ID (Client)** sowie die **Verzeichnis-ID (Mandant)** . Sie benötigen diese in einem späteren Schritt.

![Abrufen der Anwendungs-ID (Client) und der Verzeichnis-ID (Mandant)](media/aro4-ad-ids.png)

## <a name="configure-optional-claims"></a>Konfigurieren optionaler Ansprüche

Anwendungsentwickler können [optionale Ansprüche](../active-directory/develop/active-directory-optional-claims.md) in ihren Azure AD-Anwendungen verwenden, um anzugeben, welche Ansprüche in Token vorhanden sein sollen, die an ihre Anwendungen gesendet werden.

Sie können optionale Ansprüche zu folgenden Zwecken verwenden:

* Auswählen zusätzlicher Ansprüche, die in Token für Ihre Anwendung aufgenommen werden sollen
* Ändern des Verhaltens bestimmter Ansprüche, die von Azure AD in Token zurückgegeben werden
* Hinzufügen und Zugreifen auf benutzerdefinierte Ansprüche für Ihre Anwendung

Wir konfigurieren OpenShift so, dass der `email`-Anspruch verwendet wird und ein Fallback auf `upn` erfolgt, um den bevorzugten Benutzernamen festzulegen, indem der `upn` als Teil des von Azure Active Directory zurückgegebenen ID-Tokens hinzugefügt wird.

Navigieren Sie zu **Tokenkonfiguration (Vorschau)** , und klicken Sie auf **Optionalen Anspruch hinzufügen**. Klicken Sie auf **ID** , und überprüfen Sie anschließend die Ansprüche **email** und **upn**.

![Screenshot: Hinzugefügte E-Mail- und upn-Ansprüche](media/aro4-ad-tokens.png)

## <a name="assign-users-and-groups-to-the-cluster-optional"></a>Zuweisen von Benutzern und Gruppen zum Cluster (optional)

Anwendungen, die für einen Azure AD-Mandanten (Azure Active Directory) registriert sind, sind standardmäßig für alle Benutzer des Mandanten verfügbar, deren Authentifizierung erfolgreich war. Azure AD ermöglicht Mandantenadministratoren und Entwicklern die Beschränkung einer App auf eine bestimmte Gruppe von Benutzern oder Sicherheitsgruppen im Mandanten.

Befolgen Sie die Anweisungen in der Azure Active Directory-Dokumentation, um der [App Benutzer und Gruppen zuzuweisen](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md#app-registration).

## <a name="configure-openshift-openid-authentication"></a>Konfigurieren der OpenID-Authentifizierung für OpenShift

Rufen Sie die `kubeadmin`-Anmeldeinformationen ab. Führen Sie den folgenden Befehl aus, um das Kennwort für den Benutzer `kubeadmin` zu suchen:

```azurecli-interactive
az aro list-credentials \
  --name aro-cluster \
  --resource-group aro-rg
```

Die folgende Beispielausgabe zeigt, dass sich das Kennwort in `kubeadminPassword` befindet.

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

Sie können die URL der Clusterkonsole über den folgenden Befehl abrufen. Diese sieht in etwa wie folgt aus: `https://console-openshift-console.apps.<random>.<region>.aroapp.io/`.

```azurecli-interactive
 az aro show \
    --name aro-cluster \
    --resource-group aro-rg \
    --query "consoleProfile.url" -o tsv
```

Starten Sie die Konsolen-URL in einem Browser, und melden Sie sich mit den `kubeadmin`-Anmeldeinformationen an.

Navigieren Sie zu **Verwaltung** , und klicken Sie erst auf **Clustereinstellungen** und anschließend auf die Registerkarte **Globale Konfiguration**. Scrollen Sie, um **OAuth** auszuwählen.

Scrollen Sie nach unten, und klicken Sie unter **Identitätsanbieter** erst auf **Hinzufügen** und dann auf **OpenID Connect**.
![Auswählen der Option „OpenID Connect“ im Dropdownfeld „Identitätsanbieter“](media/aro4-oauth-idpdrop.png)

Geben Sie den Namen **ADD** , die **Client-ID** als **Anwendungs-ID** und den **Geheimen Clientschlüssel** ein. Die **Aussteller-URL** wird wie folgt formatiert: `https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. Ersetzen Sie den Platzhalter durch die Mandanten-ID, die Sie zuvor abgerufen haben.

![OAuth-Details ausfüllen](media/aro4-oauth-idp-1.png)

Scrollen Sie nach unten zum Abschnitt **Ansprüche** , und aktualisieren Sie den **Bevorzugten Benutzernamen** , um den Wert des Anspruchs **upn** zu verwenden.

![Angeben der Details zu Ansprüchen](media/aro4-oauth-idp-2.png)

## <a name="verify-login-through-azure-active-directory"></a>Überprüfen der Anmeldung über Azure Active Directory

Wenn Sie sich jetzt von der OpenShift-Webkonsole abmelden und versuchen, sich wieder anzumelden, wird Ihnen eine neue Option angezeigt, mit der Sie sich mit **AAD** anmelden können. Möglicherweise müssen Sie einige Minuten warten.

![Anmeldebildschirm mit Azure Active Directory-Option](media/aro4-login-2.png)
