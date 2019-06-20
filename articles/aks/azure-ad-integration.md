---
title: Integrieren von Azure Active Directory in Azure Kubernetes Service
description: Erstellen von Azure Active Directory-fähigen AKS-Clustern (Azure Kubernetes Service)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/26/2019
ms.author: iainfou
ms.openlocfilehash: a6ed8ec37a3b20ccdbd2b013ba308518d8e3b97c
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65849892"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>Integrieren von Azure Active Directory in Azure Kubernetes Service

Azure Kubernetes Service (AKS) kann für die Verwendung von Azure Active Directory (AD) für die Benutzerauthentifizierung konfiguriert werden. Bei dieser Konfiguration können Sie sich mit Ihrem Azure Active Directory-Authentifizierungstoken bei einem AKS-Cluster anmelden. Zusätzlich können Clusteradministratoren die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) von Kubernetes basierend auf einer Benutzeridentität oder einer Verzeichnisgruppenzugehörigkeit konfigurieren.

In diesem Artikel erfahren Sie, wie Sie über das Azure-Portal die Voraussetzungen für AKS und Azure AD bereitstellen, anschließend einen Azure AD-fähigen Cluster bereitstellen und eine einfache RBAC-Rolle im AKS-Cluster erstellen. Sie können [diese Schritte auch mit der Azure CLI ausführen][azure-ad-cli].

Es gelten die folgenden Einschränkungen:

- Azure AD kann nur aktiviert werden, wenn Sie einen neuen, RBAC-fähigen Cluster erstellen. Es ist nicht möglich, Azure AD für einen vorhandenen AKS-Cluster zu aktivieren.

## <a name="authentication-details"></a>Authentifizierungsdetails

Die Azure AD-Authentifizierung wird für AKS-Cluster mit OpenID Connect bereitgestellt. OpenID Connect ist eine Identitätsebene, die auf dem OAuth 2.0-Protokoll aufbaut. Weitere Informationen zu OpenID Connect finden Sie in der [OpenID Connect-Dokumentation][open-id-connect].

Innerhalb des Kubernetes-Clusters werden Authentifizierungstoken mithilfe der Webhooktokenauthentifizierung überprüft. Die Webhooktokenauthentifizierung wird als Teil des AKS-Clusters konfiguriert und verwaltet. Weitere Informationen zur Webhooktokenauthentifizierung finden Sie in der [Dokumentation zur Webhookauthentifizierung][kubernetes-webhook].

Zur Bereitstellung der Azure AD-Authentifizierung für einen AKS-Cluster werden zwei Azure AD-Anwendungen erstellt. Die erste Anwendung ist eine Serverkomponente, die die Benutzerauthentifizierung bereitstellt. Die zweite Anwendung ist eine Clientkomponente, die verwendet wird, wenn Sie von der CLI zur Authentifizierung aufgefordert werden. Diese Clientanwendung verwendet die Serveranwendung für die eigentliche Authentifizierung der Anmeldeinformationen, die vom Client bereitgestellt werden.

> [!NOTE]
> Bei der Konfiguration von Azure AD für die AKS-Authentifizierung werden zwei Azure AD-Anwendungen konfiguriert. Die Schritte zum Delegieren von Berechtigungen für jede der Anwendungen müssen von einem Azure-Mandantenadministrator ausgeführt werden.

## <a name="create-server-application"></a>Erstellen einer Serveranwendung

Die erste Azure AD-Anwendung wird verwendet, um eine Azure AD-Gruppenmitgliedschaft zu erhalten. Erstellen Sie diese Anwendung im Azure-Portal.

1. Wählen Sie **Azure Active Directory** > **App-Registrierungen** > **Neue Registrierung** aus.

    * Geben Sie der Anwendung einen Namen, z.B. *AKSAzureADServer*.
    * Wählen Sie für **Unterstützte Kontotypen** die Option *Nur Konten in diesem Organisationsverzeichnis* aus.
    * Wählen Sie als Typ für **Umleitungs-URI** die Option *Web* aus, und geben Sie einen beliebigen URI-formatierten Wert ein, z.B. *https://aksazureadserver* .
    * Wählen Sie anschließend **Registrieren** aus.

1. Wählen Sie **Manifest** aus, und setzen Sie den Wert von `groupMembershipClaims` auf `"All"`.

    ![Aktualisieren der Gruppenmitgliedschaft auf „All“](media/aad-integration/edit-manifest.png)

    **Speichern** Sie die Änderungen, wenn Sie fertig sein.

1. Wählen Sie im linken Navigationsbereich der Azure AD-Anwendung die Option **Zertifikate und Geheimnisse** aus.

    * Wählen Sie **+ Neuer geheimer Clientschlüssel** aus.
    * Fügen Sie eine Schlüsselbeschreibung hinzu, z.B. *AKS Azure AD-Server*. Wählen Sie eine Ablaufzeit und dann **Hinzufügen** aus.
    * Notieren Sie sich den Schlüsselwert. Er wird nur dieses erste Mal angezeigt. Beim Bereitstellen eines Azure AD-fähigen AKS-Clusters wird dieser Wert als `Server application secret` bezeichnet.

1. Wählen Sie im linken Navigationsbereich der Azure AD-Anwendung die Option **API-Berechtigungen** und dann **+ Berechtigung hinzufügen** aus.

    * Wählen Sie unter **Microsoft-APIs** die Option *Microsoft Graph* aus.
    * Wählen Sie **Delegierte Berechtigungen** aus, und aktivieren Sie **Verzeichnis > Directory.Read.All (Verzeichnisdaten lesen)** .
        * Wenn keine standardmäßige delegierte Berechtigung für **Benutzer > User.Read (Anmelden und Benutzerprofil lesen)** vorhanden ist, aktivieren Sie diese Berechtigung.
    * Wählen Sie **Anwendungsberechtigungen** aus, und aktivieren Sie **Verzeichnis > Directory.Read.All (Verzeichnisdaten lesen)** .

        ![Festlegen von Graph-Berechtigungen](media/aad-integration/graph-permissions.png)

    * Wählen Sie **Berechtigungen hinzufügen** aus, um die Änderungen zu speichern.

    * Wählen Sie unter dem Abschnitt **Einwilligung erteilen** die Option **Administratoreinwilligung erteilen** aus. Diese Schaltfläche ist abgeblendet und nicht verfügbar, wenn das aktuelle Konto kein Mandantenadministrator ist.

        Wenn die Berechtigungen erfolgreich gewährt wurden, wird die folgende Benachrichtigung im Portal angezeigt:

        ![Benachrichtigung über erfolgreich erteilte Berechtigungen](media/aad-integration/permissions-granted.png)

1. Wählen Sie im linken Navigationsbereich der Azure AD-Anwendung die Option **Eine API verfügbar machen** und dann **+ Bereich hinzufügen** aus.
    
    * Legen Sie einen Wert für *Bereichsname*, *Anzeigename der Administratoreinwilligung* und *Beschreibung der Administratoreinwilligung* (z.B. *AKSAzureADServer*) fest.
    * Stellen Sie sicher, dass **Zustand** auf *Aktiviert* gesetzt ist.

        ![Verfügbarmachen der Server-App als eine API für die Verwendung mit anderen Diensten](media/aad-integration/expose-api.png)

    * Wählen Sie **Bereich hinzufügen** aus.

1. Kehren Sie zur Seite **Übersicht** der Anwendung zurück, und notieren Sie sich die **Anwendungs-ID (Client)** . Beim Bereitstellen eines Azure AD-fähigen AKS-Clusters wird dieser Wert als `Server application ID` bezeichnet.

   ![Abrufen der Anwendungs-ID](media/aad-integration/application-id.png)

## <a name="create-client-application"></a>Erstellen einer Clientanwendung

Die zweite Azure AD-Anwendung wird beim Anmelden mit der Kubernetes-CLI (`kubectl`) verwendet.

1. Wählen Sie **Azure Active Directory** > **App-Registrierungen** > **Neue Registrierung** aus.

    * Geben Sie der Anwendung einen Namen, z.B. *AKSAzureADClient*.
    * Wählen Sie für **Unterstützte Kontotypen** die Option *Nur Konten in diesem Organisationsverzeichnis* aus.
    * Wählen Sie als Typ für **Umleitungs-URI** die Option *Web* aus, und geben Sie einen beliebigen URI-formatierten Wert ein, z.B. *https://aksazureadclient* .
    * Wählen Sie anschließend **Registrieren** aus.

1. Wählen Sie im linken Navigationsbereich der Azure AD-Anwendung die Option **API-Berechtigungen** und dann **+ Berechtigung hinzufügen** aus.

    * Wählen Sie **Meine APIs** und dann die Azure AD-Serveranwendung aus, die Sie im vorherigen Schritt erstellt haben, z.B. *AKSAzureADServer*.
    * Wählen Sie **Delegierte Berechtigungen** aus, und aktivieren Sie dann Ihre Azure AD-Serveranwendung.

        ![Konfigurieren der Anwendungsberechtigungen](media/aad-integration/select-api.png)

    * Wählen Sie **Berechtigungen hinzufügen** aus.

    * Wählen Sie unter dem Abschnitt **Einwilligung erteilen** die Option **Administratoreinwilligung erteilen** aus. Diese Schaltfläche ist abgeblendet und nicht verfügbar, wenn das aktuelle Konto kein Mandantenadministrator ist.

        Wenn die Berechtigungen erfolgreich gewährt wurden, wird die folgende Benachrichtigung im Portal angezeigt:

        ![Benachrichtigung über erfolgreich erteilte Berechtigungen](media/aad-integration/permissions-granted.png)

1. Wählen Sie im linken Navigationsbereich der Azure AD-Anwendung **Authentifizierung** aus.

    * Wählen Sie unter **Standardclienttyp** die Option **Ja** aus, um *den Client als öffentlichen Client einzustufen*.

1. Notieren Sie sich die **Anwendungs-ID** im linken Navigationsbereich der Azure AD-Anwendung. Beim Bereitstellen eines Azure AD-fähigen AKS-Clusters wird dieser Wert als `Client application ID` bezeichnet.

   ![Abrufen der Anwendungs-ID](media/aad-integration/application-id-client.png)

## <a name="get-tenant-id"></a>Abrufen der Mandanten-ID

Abschließend rufen Sie die ID von Ihrem Azure-Mandanten ab. Dieser Wert wird beim Erstellen des AKS-Clusters verwendet.

Wählen Sie im Azure-Portal **Azure Active Directory** > **Eigenschaften** aus, und notieren Sie die **Verzeichnis-ID**. Beim Erstellen eines Azure AD-fähigen AKS-Clusters wird dieser Wert als `Tenant ID` bezeichnet.

![Abrufen der Azure-Mandanten-ID](media/aad-integration/tenant-id.png)

## <a name="deploy-cluster"></a>Bereitstellen eines Clusters

Verwenden Sie zum Erstellen einer Ressourcengruppe für den AKS-Cluster den Befehl [az group create][az-group-create].

```azurecli
az group create --name myResourceGroup --location eastus
```

Stellen Sie den Cluster mit dem Befehl [az aks create][az-aks-create] bereit. Ersetzen Sie die Werte im Beispielbefehl unten durch die Werte, die beim Erstellen der Azure AD-Anwendungen für Server-App-ID und -Geheimnis, Client-App-ID und Mandanten-ID gesammelt wurden:

```azurecli
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --generate-ssh-keys \
  --aad-server-app-id b1536b67-29ab-4b63-b60f-9444d0c15df1 \
  --aad-server-app-secret wHYomLe2i1mHR2B3/d4sFrooHwADZccKwfoQwK2QHg= \
  --aad-client-app-id 8aaf8bd5-1bdd-4822-99ad-02bfaa63eea7 \
  --aad-tenant-id 72f988bf-0000-0000-0000-2d7cd011db47
```

Die Erstellung des AKS-Clusters dauert einige Minuten.

## <a name="create-rbac-binding"></a>Erstellen einer RBAC-Bindung

Bevor ein Azure Active Directory-Konto mit dem AKS-Cluster verwendet werden kann, muss eine Rollenbindung oder eine Clusterrollenbindung erstellt werden. *Rollen* definieren die zu erteilenden Berechtigungen, und *Bindungen* wenden diese auf die gewünschten Benutzer an. Diese Zuweisungen können auf einen bestimmten Namespace oder im gesamten Cluster angewendet werden. Weitere Informationen finden Sie unter [Verwenden der RBAC-Autorisierung][rbac-authorization].

Verwenden Sie zunächst den Befehl [az aks get-credentials][az-aks-get-credentials] mit dem Argument `--admin`, um sich beim Cluster mit Administratorzugriff anzumelden.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Erstellen Sie als Nächstes eine Clusterrollenbindung (ClusterRoleBinding) für ein Azure AD-Konto, dem Sie Zugriff auf den AKS-Cluster gewähren möchten. Im folgenden Beispiel wird dem Konto Vollzugriff auf alle Namespaces im Cluster gewährt.

- Wenn sich der Benutzer, für den die RBAC-Bindung gewährt wird, im selben Azure AD-Mandanten befindet, weisen Sie Berechtigungen auf Grundlage des Benutzerprinzipalnamens (User Principal Name, UPN) zu. Fahren Sie mit dem Schritt zum Erstellen des YAML-Manifests für die Clusterrollenbindung fort.

- Befindet sich der Benutzer in einem anderen Azure AD-Mandanten, müssen Sie stattdessen die *objectId*-Eigenschaft abfragen und verwenden. Rufen Sie ggf. die *objectId* des erforderlichen Benutzerkontos mit dem Befehl [az ad user show][az-ad-user-show] ab. Geben Sie den Benutzerprinzipalname (UPN) des erforderlichen Benutzerkontos an:

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

Erstellen Sie eine Datei, z. B. *rbac-aad-user.yaml*, und fügen Sie den folgenden Inhalt ein. Ersetzen Sie in der letzten Zeile *userPrincipalName_or_objectId* durch den UPN oder die Objekt-ID (je nachdem, ob sich der Benutzer im gleichen Azure AD-Mandanten befindet).

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: userPrincipalName_or_objectId
```

Wenden Sie die Bindung wie im folgenden Beispiel gezeigt mit dem Befehl [kubectl apply][kubectl-apply] an:

```console
kubectl apply -f rbac-aad-user.yaml
```

Für alle Mitglieder einer Azure AD-Gruppe kann auch eine Rollenbindung erstellt werden. Azure AD-Gruppen werden wie im folgenden Beispiel gezeigt anhand der Gruppenobjekt-ID angegeben. Erstellen Sie eine Datei, z. B. *rbac-aad-group.yaml*, und fügen Sie den folgenden Inhalt ein. Aktualisieren Sie den Benutzernamen mit einem Namen aus Ihrem Azure AD-Mandanten:

 ```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
   kind: Group
   name: "894656e1-39f8-4bfe-b16a-510f61af6f41"
```

Wenden Sie die Bindung wie im folgenden Beispiel gezeigt mit dem Befehl [kubectl apply][kubectl-apply] an:

```console
kubectl apply -f rbac-aad-group.yaml
```

Weitere Informationen zum Sichern eines Kubernetes-Clusters mit RBAC finden Sie in der Dokumentation zur [Verwendung der RBAC-Autorisierung][rbac-authorization].

## <a name="access-cluster-with-azure-ad"></a>Zugriff auf Cluster mit Azure AD

Als Nächstes pullen Sie den Kontext für den Nicht-Administratorbenutzer mit dem Befehl [az aks get-credentials][az-aks-get-credentials].

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Nachdem Sie einen `kubectl`-Befehl ausgeführt haben, werden Sie aufgefordert, sich bei Azure zu authentifizieren. Befolgen Sie die Anweisungen auf dem Bildschirm zum Abschließen des Vorgangs wie im folgenden Beispiel gezeigt:

```console
$ kubectl get nodes

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-1   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-2   Ready     agent     1h        v1.13.5
```

Anschließend wird das Authentifizierungstoken zwischengespeichert. Sie werden nur dann erneut zur Anmeldung aufgefordert, wenn das Token abgelaufen ist oder die Kubernetes-Konfigurationsdatei neu erstellt wird.

Wenn nach erfolgreicher Anmeldung eine Fehlermeldung bezüglich der Autorisierung angezeigt wird, überprüfen Sie Folgendes:

```console
error: You must be logged in to the server (Unauthorized)
```

1. Sie haben die entsprechende Objekt-ID oder den UPN definiert, je nachdem, ob das Benutzerkonto im gleichen Azure AD-Mandanten vorhanden ist oder nicht.
2. Der Benutzer ist kein Mitglied der über 200 Gruppen.
3. Das Geheimnis, das in der Anwendungsregistrierung für den Server definiert ist, entspricht dem mit `--aad-server-app-secret` konfigurierten Wert.

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Steuern des Zugriffs auf Clusterressourcen mit Azure AD-Benutzern und -Gruppen finden Sie unter [Steuern des Zugriffs auf Clusterressourcen per rollenbasierter Zugriffssteuerung und mit Azure Active Directory-Identitäten in Azure Kubernetes Service][azure-ad-rbac].

Weitere Informationen zum Schützen von Kubernetes-Clustern finden Sie unter [Zugriffs- und Identitätsoptionen für Azure Kubernetes Service (AKS)][rbac-authorization].

Bewährte Methoden zur Identität und Ressourcensteuerung finden Sie unter [Best Practices für die Authentifizierung und Autorisierung in Azure Kubernetes Service (AKS)][operator-best-practices-identity].

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v1-protocols-openid-connect-code.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
