---
title: 'Azure Red Hat OpenShift mit OpenShift 4: Konfigurieren von Azure Active Directory-Authentifizierung über die Befehlszeile'
description: Erfahren Sie, wie Sie Azure Active Directory-Authentifizierung für einen Azure Red Hat OpenShift-Cluster mit OpenShift 4 mithilfe der Befehlszeile konfigurieren.
ms.service: container-service
ms.topic: article
ms.date: 03/12/2020
author: sabbour
ms.author: asabbour
keywords: aro, openshift, az aro, red hat, cli
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 0d69fa10408618fb188b42e1dd8f7b9d02820cc3
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96862410"
---
# <a name="configure-azure-active-directory-authentication-for-an-azure-red-hat-openshift-4-cluster-cli"></a>Konfigurieren von Azure Active Directory-Authentifizierung für einen Azure Red Hat OpenShift 4-Cluster (CLI)

Wenn Sie die Befehlszeilenschnittstelle (CLI) lokal installieren und verwenden möchten, müssen Sie für diesen Artikel mindestens Version 2.6.0 der Azure CLI ausführen. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

Rufen Sie die clusterspezifischen URLs ab, die zum Konfigurieren der Azure Active Directory-Anwendung verwendet werden.

Legen Sie die Variablen für den Namen der Ressourcengruppe und des Clusters fest.

Ersetzen Sie **\<resource_group>** durch den Namen Ihrer Ressourcengruppe und **\<aro_cluster>** durch den Namen Ihres Clusters.

```azurecli-interactive
resource_group=<resource_group>
aro_cluster=<aro_cluster>
```

Erstellen Sie die OAuth-Rückruf-URL des Clusters, und speichern Sie sie in einer Variablen **oauthCallbackURL**. 

> [!NOTE]
> Der Abschnitt `AAD` in der OAuth-Rückruf-URL sollte mit dem Namen des OAuth-Identitätsanbieters identisch sein, den Sie später einrichten.


```azurecli-interactive
domain=$(az aro show -g $resource_group -n $aro_cluster --query clusterProfile.domain -o tsv)
location=$(az aro show -g $resource_group -n $aro_cluster --query location -o tsv)
apiServer=$(az aro show -g $resource_group -n $aro_cluster --query apiserverProfile.url -o tsv)
webConsole=$(az aro show -g $resource_group -n $aro_cluster --query consoleProfile.url -o tsv)
```

Das Format der oauthCallbackURL sieht bei benutzerdefinierten Domänen etwas anders aus:

* Führen Sie den folgenden Befehl aus, wenn Sie eine benutzerdefinierte Domäne (z. B. `contoso.com`) verwenden. 

    ```azurecli-interactive
    oauthCallbackURL=https://oauth-openshift.apps.$domain/oauth2callback/AAD
    ```

* Wenn Sie keine benutzerdefinierte Domäne verwenden, ist `$domain` eine aus acht Zeichen bestehende alphanumerische Zeichenfolge, die um `$location.aroapp.io` erweitert wird.

    ```azurecli-interactive
    oauthCallbackURL=https://oauth-openshift.apps.$domain.$location.aroapp.io/oauth2callback/AAD
    ```

> [!NOTE]
> Der Abschnitt `AAD` in der OAuth-Rückruf-URL sollte mit dem Namen des OAuth-Identitätsanbieters identisch sein, den Sie später einrichten.

## <a name="create-an-azure-active-directory-application-for-authentication"></a>Erstellen einer Azure Active Directory-Anwendung für die Authentifizierung

Ersetzen Sie **\<client_secret>** durch ein sicheres Kennwort für die Anwendung.

```azurecli-interactive
client_secret=<client_secret>
```

Erstellen Sie eine Azure Active Directory Anwendung, und rufen Sie den erstellten Anwendungsbezeichner ab.

```azurecli-interactive
app_id=$(az ad app create \
  --query appId -o tsv \
  --display-name aro-auth \
  --reply-urls $oauthCallbackURL \
  --password $client_secret)
```

Rufen Sie die Mandanten-ID des Abonnements ab, das Besitzer der Anwendung ist.

```azure
tenant_id=$(az account show --query tenantId -o tsv)
```

## <a name="create-a-manifest-file-to-define-the-optional-claims-to-include-in-the-id-token"></a>Erstellen einer Manifestdatei zum Definieren der optionalen Ansprüche, die im ID-Token enthalten sein sollen

Anwendungsentwickler können [optionale Ansprüche](../active-directory/develop/active-directory-optional-claims.md) in ihren Azure AD-Anwendungen verwenden, um anzugeben, welche Ansprüche in Token vorhanden sein sollen, die an ihre Anwendungen gesendet werden.

Sie können optionale Ansprüche zu folgenden Zwecken verwenden:

- Auswählen zusätzlicher Ansprüche, die in Token für Ihre Anwendung aufgenommen werden sollen
- Ändern des Verhaltens bestimmter Ansprüche, die von Azure AD in Token zurückgegeben werden
- Hinzufügen und Zugreifen auf benutzerdefinierte Ansprüche für Ihre Anwendung

Wir konfigurieren OpenShift so, dass der `email`-Anspruch verwendet wird und ein Fallback auf `upn` erfolgt, um den bevorzugten Benutzernamen festzulegen, indem der `upn` als Teil des von Azure Active Directory zurückgegebenen ID-Tokens hinzugefügt wird.

Erstellen Sie eine Datei **manifest.json**, um die Azure Active Directory-Anwendung zu konfigurieren.

```bash
cat > manifest.json<< EOF
[{
  "name": "upn",
  "source": null,
  "essential": false,
  "additionalProperties": []
},
{
"name": "email",
  "source": null,
  "essential": false,
  "additionalProperties": []
}]
EOF
```

## <a name="update-the-azure-active-directory-applications-optionalclaims-with-a-manifest"></a>Aktualisieren der optionalClaims der Azure Active Directory-Anwendung mit einem Manifest

```azurecli-interactive
az ad app update \
  --set optionalClaims.idToken=@manifest.json \
  --id $app_id
```

## <a name="update-the-azure-active-directory-application-scope-permissions"></a>Aktualisieren der Bereichsberechtigungen der Azure Active Directory-Anwendung

Um die Benutzerinformationen aus Azure Active Directory lesen zu können, müssen wir die richtigen Bereiche definieren.

Fügen Sie die Berechtigung für den Bereich **Azure Active Directory Graph.User.Read** hinzu, um die Anmeldung zu ermöglichen und das Benutzerprofil zu lesen.

```azurecli-interactive
az ad app permission add \
 --api 00000002-0000-0000-c000-000000000000 \
 --api-permissions 311a71cc-e848-46a1-bdf8-97ff7156d8e6=Scope \
 --id $app_id
```

> [!NOTE]
> Wenn Sie für diese Azure Active Directory-Instanz nicht als globaler Administrator authentifiziert sind, können Sie die Meldung ignorieren, um die Zustimmung zu erteilen. Standarddomänenbenutzer werden zur Erteilung ihrer Zustimmung aufgefordert, wenn sie sich das erste Mal mit ihren AAD-Anmeldeinformationen beim Cluster anmelden.

## <a name="assign-users-and-groups-to-the-cluster-optional"></a>Zuweisen von Benutzern und Gruppen zum Cluster (optional)

Anwendungen, die für einen Azure AD-Mandanten (Azure Active Directory) registriert sind, sind standardmäßig für alle Benutzer des Mandanten verfügbar, deren Authentifizierung erfolgreich war. Azure AD ermöglicht Mandantenadministratoren und Entwicklern die Beschränkung einer App auf eine bestimmte Gruppe von Benutzern oder Sicherheitsgruppen im Mandanten.

Befolgen Sie die Anweisungen in der Azure Active Directory-Dokumentation, um der [App Benutzer und Gruppen zuzuweisen](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md#app-registration).

## <a name="configure-openshift-openid-authentication"></a>Konfigurieren der OpenID-Authentifizierung für OpenShift

Rufen Sie die `kubeadmin`-Anmeldeinformationen ab. Führen Sie den folgenden Befehl aus, um das Kennwort für den Benutzer `kubeadmin` zu suchen:

```azurecli-interactive
kubeadmin_password=$(az aro list-credentials \
  --name $aro_cluster \
  --resource-group $resource_group \
  --query kubeadminPassword --output tsv)
```

Melden Sie sich mit dem folgenden Befehl beim API-Server des OpenShift-Clusters an. 

```azurecli-interactive
oc login $apiServer -u kubeadmin -p $kubeadmin_password
```

Erstellen Sie ein OpenShift-Geheimnis, um das Azure Active Directory-Anwendungsgeheimnis zu speichern.

```azurecli-interactive
oc create secret generic openid-client-secret-azuread \
  --namespace openshift-config \
  --from-literal=clientSecret=$client_secret
```

Erstellen Sie eine Datei **oidc.yaml**, um OpenShift OpenID-Authentifizierung für Azure Active Directory zu konfigurieren. 

```bash
cat > oidc.yaml<< EOF
apiVersion: config.openshift.io/v1
kind: OAuth
metadata:
  name: cluster
spec:
  identityProviders:
  - name: AAD
    mappingMethod: claim
    type: OpenID
    openID:
      clientID: $app_id
      clientSecret:
        name: openid-client-secret-azuread
      extraScopes:
      - email
      - profile
      extraAuthorizeParameters:
        include_granted_scopes: "true"
      claims:
        preferredUsername:
        - email
        - upn
        name:
        - name
        email:
        - email
      issuer: https://login.microsoftonline.com/$tenant_id
EOF
```

Wenden Sie die Konfiguration auf den Cluster an.

```azurecli-interactive
oc apply -f oidc.yaml
```

Sie erhalten eine Antwort, die in etwa der folgenden entspricht.

```output
oauth.config.openshift.io/cluster configured
```

## <a name="verify-login-through-azure-active-directory"></a>Überprüfen der Anmeldung über Azure Active Directory

Wenn Sie sich jetzt von der OpenShift-Webkonsole abmelden und versuchen, sich erneut anzumelden, wird Ihnen eine neue Option angezeigt, mit der Sie sich mit **AAD** anmelden können. Möglicherweise müssen Sie einige Minuten warten.

![Anmeldebildschirm mit Azure Active Directory-Option](media/aro4-login-2.png)
