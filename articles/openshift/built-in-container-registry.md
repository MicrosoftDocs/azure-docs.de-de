---
title: Konfigurieren der integrierten Containerregistrierung für Azure Red Hat OpenShift 4
description: Konfigurieren der integrierten Containerregistrierung für Azure Red Hat OpenShift 4
author: jiangma
ms.author: jiangma
ms.service: container-service
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: 9ed53721b66dc03bad24e0510e0c8a970c61aec1
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492419"
---
# <a name="configure-built-in-container-registry-for-azure-red-hat-openshift-4"></a>Konfigurieren der integrierten Containerregistrierung für Azure Red Hat OpenShift 4

Azure Red Hat OpenShift bietet eine integrierte Containerimageregistrierung namens [OpenShift Container Registry (OCR)](https://docs.openshift.com/aro/4/registry/architecture-component-imageregistry.html), die die Möglichkeit hinzufügt, neue Imagerepositorys bei Bedarf automatisch bereitzustellen. So erhalten Benutzer einen integrierten Speicherort, in den ihre Anwendungsbuilds die resultierenden Images per Push übertragen können.

In diesem Artikel konfigurieren Sie die integrierte Containerimageregistrierung für einen Azure Red Hat OpenShift 4-Cluster (ARO). Sie lernen Folgendes:

> [!div class="checklist"]
> * Einrichten von Azure AD
> * Einrichten von OpenID Connect
> * Zugreifen auf die integrierte Containerimageregistrierung

## <a name="before-you-begin"></a>Voraussetzungen

In diesem Artikel wird vorausgesetzt, dass ein ARO-Cluster vorhanden ist. Wenn Sie einen ARO-Cluster, erhalten Sie die notwendigen Informationen im ARO-Tutorial [Erstellen eines Azure Red Hat OpenShift 4-Clusters](./tutorial-create-cluster.md). Erstellen Sie den Cluster unbedingt mit dem `--pull-secret`-Argument für `az aro create`.  Dies ist notwendig, um die Azure Active Directory-Authentifizierung und die integrierte Containerregistrierung zu konfigurieren.

Wenn der Cluster vorhanden ist, stellen Sie eine Verbindung mit dem Cluster her, indem Sie die Schritte im Tutorial[Herstellen einer Verbindung mit einem Azure Red Hat OpenShift 4-Cluster](./tutorial-connect-cluster.md) ausführen.
   * Stellen Sie sicher, dass Sie die Schritte unter „Installieren der OpenShift-CLI“ ausführen, da der `oc`-Befehl weiter unten in diesem Artikel verwendet wird.
   * Notieren Sie sich die URL der Clusterkonsole, die wie `https://console-openshift-console.apps.<random>.<region>.aroapp.io/`aussieht. Die Werte für `<random>` und `<region>` werden später in diesem Artikel verwendet.
   * Notieren Sie sich die `kubeadmin`-Anmeldeinformationen. Sie werden ebenfalls später in diesem Artikel verwendet.

### <a name="configure-azure-active-directory-authentication"></a>Konfigurieren der Azure Active Directory-Authentifizierung 

Azure Active Directory (Azure AD) implementiert OpenID Connect (OIDC). Mit OIDC können Sie Azure AD für die Anmeldung beim ARO-Cluster verwenden. Führen Sie die Schritte zum [Konfigurieren der Azure Active Directory-Authentifizierung](configure-azure-ad-cli.md) aus, um Ihren Cluster einzurichten.

## <a name="access-the-built-in-container-image-registry"></a>Zugreifen auf die integrierte Containerimageregistrierung

Nachdem Sie die Methoden für die Authentifizierung beim ARO-Cluster eingerichtet haben, können wir jetzt den Zugriff auf die integrierte Registrierung aktivieren.

#### <a name="define-the-azure-ad-user-to-be-an-administrator"></a>Definieren des Azure AD-Benutzers als Administrator

1. Melden Sie sich mit den Anmeldeinformationen eines Azure AD-Benutzers über Ihren Browser bei der OpenShift-Webkonsole an. Wir nutzen die OpenShift OpenID-Authentifizierung für Azure Active Directory, um OpenID zum Definieren des Administrators zu verwenden.

   1. Verwenden Sie eine InPrivate, Incognito- oder andere entsprechende Browserfensterfunktion, um sich bei der Konsole anzumelden. Das Fenster wird nach dem Aktivieren von OIDC anders aussehen.
   
   :::image type="content" source="media/built-in-container-registry/oidc-enabled-login-window.png" alt-text="OpenID Connect-aktiviertes Anmeldefenster.":::
   1. Wählen Sie **openid** aus.

   > [!NOTE]
   > Notieren Sie sich den Benutzernamen und das Kennwort, die Sie hier zum Anmelden verwenden. Dieser Benutzername und dieses Kennwort fungieren in diesem und anderen Artikeln als Administrator für andere Aktionen.
2. Melden Sie sich mit der OpenShift-CLI an, indem Sie die folgenden Schritte ausführen.  Für die Diskussion wird dieser Prozess als `oc login` bezeichnet.
   1. Erweitern Sie rechts oben in der Webkonsole das Kontextmenü des angemeldeten Benutzers, und wählen Sie dann **Anmeldebefehl kopieren** aus.
   2. Melden Sie sich bei Bedarf als derselbe Benutzer bei einem neuen Registerkartenfenster an.
   3. Wählen Sie **Token anzeigen** aus.
   4. Kopieren Sie den unter **Mit diesem Token anmelden** aufgeführten Wert in die Zwischenablage, und führen Sie ihn wie hier gezeigt in einer Shell aus.

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

3. Führen Sie `oc whoami` in der Konsole aus, und notieren Sie sich die Ausgabe als **\<aad-user>** .  Wir werden diesen Wert später im Artikel verwenden.
4. Melden Sie sich von der OpenShift-Webkonsole ab. Wählen Sie die Schaltfläche oben rechts im Browserfenster mit der Bezeichnung **\<aad-user>** und dann **Abmelden** aus.


#### <a name="grant-the-azure-ad-user-the-necessary-roles-for-registry-interaction"></a>Gewähren der erforderlichen Rollen für die Registrierungsinteraktion für den Azure AD-Benutzer

1. Melden Sie sich mit den `kubeadmin`-Anmeldeinformationen über Ihren Browser bei der OpenShift-Webkonsole an.
1. Melden Sie sich mit dem Token für `kubeadmin` bei der OpenShift-CLI an, indem Sie die oben für `oc login` beschriebenen Schritte ausführen, aber erst nach der Anmeldung bei der Webkonsole mit `kubeadmin`.
1. Führen Sie die folgenden Befehle aus, um den Zugriff auf die integrierte Registrierung für den **aad-user** zu aktivieren.

   ```bash
   # Switch to project "openshift-image-registry"
   oc project openshift-image-registry
   
   # Output should look similar to the following.
   # Now using project "openshift-image-registry" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   ```

   ```bash
   # Expose the registry using "DefaultRoute"
   oc patch configs.imageregistry.operator.openshift.io/cluster --patch '{"spec":{"defaultRoute":true}}' --type=merge

   # Output should look similar to the following.
   # config.imageregistry.operator.openshift.io/cluster patched
   ```

   ```bash
   # Add roles to "aad-user" for pulling and pushing images
   # Note: replace "<aad-user>" with the one you wrote down before
   oc policy add-role-to-user registry-viewer <aad-user>

   # Output should look similar to the following.
   # clusterrole.rbac.authorization.k8s.io/registry-viewer added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

   ```bash
   oc policy add-role-to-user registry-editor <aad-user>
   # Output should look similar to the following.
   # clusterrole.rbac.authorization.k8s.io/registry-editor added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

#### <a name="obtain-the-container-registry-url"></a>Abrufen der URL der Containerregistrierung

Verwenden Sie den Befehl `oc get route` wie im Folgenden gezeigt, um die URL der Containerregistrierung abzurufen.

```bash
# Note: the value of "Container Registry URL" in the output is the fully qualified registry name.
HOST=$(oc get route default-route --template='{{ .spec.host }}')
echo "Container Registry URL: $HOST"
```

   > [!NOTE]
   > Beachten Sie die Konsolenausgabe von **Container Registry URL**. Sie wird als voll qualifizierter Registrierungsname für dieses Handbuch und nachfolgende Informationen verwendet.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie die integrierte Containerimageregistrierung eingerichtet haben, können Sie mit der Bereitstellung einer Anwendung in OpenShift beginnen. Informationen für Java-Anwendungen finden Sie unter [Bereitstellen einer Java-Anwendung mit Open Liberty/WebSphere Liberty in Azure Red Hat OpenShift 4-Clustern](howto-deploy-java-liberty-app.md).