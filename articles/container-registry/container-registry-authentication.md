---
title: Authentifizieren mit einer Azure-Containerregistrierung
description: Authentifizierungsoptionen für eine Azure-Containerregistrierung, wie etwa Anmelden mit einer Azure Active Directory-Identität, mithilfe von Dienstprinzipalen sowie mittels optionalen Administratoranmeldeinformationen.
services: container-registry
author: dlepow
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 12/21/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9682b9b832a8fd7374cb84e8cc6faad69df15945
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61333866"
---
# <a name="authenticate-with-a-private-docker-container-registry"></a>Authentifizieren mit einer privaten Docker-Containerregistrierung

Es gibt mehrere Möglichkeiten, sich mit einer Azure-Containerregistrierung zu authentifizieren, von denen jede für eine oder mehrere Registrierungsverwendungsszenarien gilt.

Sie können sich direkt mittels einer [individuellen Anmeldung](#individual-login-with-azure-ad) bei einer Registrierung anmelden, und Ihre Anwendungen und Containerorchestratoren können eine unbeaufsichtigte oder „monitorlose“ Authentifizierung mithilfe eines Azure Active Directory-[Dienstprinzipals](#service-principal) (Azure AD) durchführen.

## <a name="individual-login-with-azure-ad"></a>Individuelle Anmeldung bei Azure AD

Authentifizieren Sie sich bei der direkten Arbeit mit der Registrierung wie der Pullübertragung von Images auf Ihre und der Pushübertragung von Images von Ihrer Entwicklungsarbeitsstation mithilfe des [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) -Befehls in der [Azure CLI](/cli/azure/install-azure-cli):

```azurecli
az acr login --name <acrName>
```

Bei der Anmeldung mit `az acr login` verwendet die CLI das Token, das erstellt wurde, als Sie [az login](/cli/azure/reference-index#az-login) ausgeführt haben, zur nahtlosen Authentifizierung Ihrer Sitzung mit Ihrer Registrierung. Sobald Sie sich auf diese Weise angemeldet haben, werden Ihre Anmeldeinformationen zwischengespeichert, und nachfolgende `docker`-Befehle in Ihrer Sitzung benötigen weder einen Benutzernamen noch das Kennwort. 

Für den Zugriff auf die Registrierung ist das von `az acr login` verwendete Token 1 Stunde lang gültig. Daher empfehlen wir Ihnen, sich immer in der Registrierung anzumelden, bevor Sie einen `docker`-Befehl ausführen. Wenn das Token abgelaufen ist, können Sie es aktualisieren, indem Sie den `az acr login`-Befehl erneut zur Authentifizierung verwenden. 

Die Verwendung von `az acr login` mit Azure-Identitäten ermöglicht [rollenbasierten Zugriff](../role-based-access-control/role-assignments-portal.md). In einigen Szenarien können Sie sich bei einer Registry mit Ihrer eigenen individuellen Identität in Azure AD anmelden. Für dienstübergreifende Szenarien oder um die Anforderungen einer Arbeitsgruppe zu erfüllen, in der Sie keinen individuellen Zugriff verwalten möchten, können Sie sich auch mit einer [verwalteten Identität für Azure-Ressourcen](container-registry-authentication-managed-identity.md) anmelden.

## <a name="service-principal"></a>Dienstprinzipal

Wenn Sie Ihrer Registrierung einen [Dienstprinzipal](../active-directory/develop/app-objects-and-service-principals.md) zuweisen, kann Ihre Anwendung oder Ihr Dienst diesen für die monitorlose Authentifizierung verwenden. Dienstprinzipale ermöglichen [rollenbasierten Zugriff](../role-based-access-control/role-assignments-portal.md) auf eine Registrierung, und Sie können einer Registrierung mehrere Dienstprinzipale zuweisen. Mit mehreren Dienstprinzipalen können Sie unterschiedliche Zugriffsberechtigungen für verschiedene Anwendungen definieren.

Die folgenden Rollen für eine Containerregistrierung sind verfügbar:

* **AcrPull**: Pull

* **AcrPush**: Pull und Push

* **Besitzer**: Pull, Push und Zuweisen von Rollen an andere Benutzer

Eine vollständige Liste der Rollen finden Sie unter [Azure Container Registry – Rollen und Berechtigungen](container-registry-roles.md).

Informationen zu CLI-Skripts zum Erstellen der App-ID und des Kennworts eines Dienstprinzipals für die Authentifizierung mit einer Azure-Containerregistrierung oder zur Verwendung eines vorhandenen Dienstprinzipals finden Sie unter [Azure Container Registry-Authentifizierung mit Dienstprinzipalen](container-registry-auth-service-principal.md).

Dienstprinzipale ermöglichen monitorlose Verbindungen mit einer Registrierung in Pull- und Pushszenarios, wie beispielsweise:

  * *Pull*: Bereitstellen von Containern aus einer Registrierung für Orchestrierungssysteme, z.B. Kubernetes, DC/OS und Docker Swarm. Sie können auch Pullvorgänge aus Containerregistrierungen in verwandte Azure-Dienste durchführen, z. B. [Azure Container Service](container-registry-auth-aks.md), [Azure Container Instances](container-registry-auth-aci.md), [App Service](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](/azure/service-fabric/) und weitere Dienste.

  * *Push*: Erstellen von Containerimages und deren Übertragung per Pushvorgang in eine Registrierung mithilfe von Lösungen für Continuous Integration und Continuous Deployment wie Azure Pipelines oder Jenkins.

Sie können sich auch direkt mit einem Dienstprinzipal anmelden. Wenn Sie den folgenden Befehl ausführen, geben Sie bei Aufforderung interaktiv die Haupt-AppID (Benutzername) und das Kennwort des Dienstes an. Best Practices zur Verwaltung von Anmeldeinformationen finden Sie in der Befehlsreferenz [Docker-Anmeldung](https://docs.docker.com/engine/reference/commandline/login/):

```
docker login myregistry.azurecr.io
```

Nach der Anmeldung speichert Docker die Anmeldeinformationen zwischen, sodass Sie die App-ID nicht im Kopf behalten müssen.

> [!TIP]
> Sie können das Kennwort eines Dienstprinzipals neu generieren, indem Sie den Befehl [az ad sp reset-credentials](/cli/azure/ad/sp?view=azure-cli-latest) ausführen.
>

## <a name="admin-account"></a>Administratorkonto

Jede Containerregistrierung enthält ein Administratorbenutzerkonto, das standardmäßig deaktiviert ist. Sie können den Administratorbenutzer aktivieren und seine Anmeldeinformationen im Azure-Portal oder mithilfe der Azure CLI oder anderer Azure-Tools verwalten.

> [!IMPORTANT]
> Das Administratorkonto ist dafür ausgelegt, dass ein einzelner Benutzer auf die Registrierung zugreift (hauptsächlich für Testzwecke). Sie sollten die Administratorkonto-Anmeldeinformationen nicht für mehrere Benutzer freigeben. Alle Benutzer, die sich mit dem Administratorkonto authentifizieren, werden als ein einzelner Benutzer mit Push- und Pullzugriff auf die Registrierung angezeigt. Wenn dieses Konto geändert oder deaktiviert wird, wird der Zugriff auf die Registrierung für alle Benutzer deaktiviert, die dessen Anmeldeinformationen verwenden. Für Benutzer und Dienstprinzipale wird für monitorlose Szenarien einzelne Identität empfohlen.
>

Das Administratorkonto erhält zwei Kennwörter, die beide erneut generiert werden können. Die beiden Kennwörter ermöglichen Ihnen, Verbindungen mit der Registrierung aufrechtzuerhalten, indem Sie ein Kennwort verwenden, während Sie das andere Kennwort neu generieren. Wenn das Administratorkonto aktiviert ist, können Sie den Benutzernamen und eines der Kennwörter bei Aufforderung an den Befehl `docker login` übergeben, um die Standardauthentifizierung für die Registrierung zu erhalten. Beispiel:

```
docker login myregistry.azurecr.io 
```


Um den Administratorbenutzer für eine vorhandene Registrierung zu aktivieren, können Sie den `--admin-enabled`-Parameter des [az acr update](/cli/azure/acr?view=azure-cli-latest#az-acr-update)-Befehls in der Azure CLI verwenden:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Sie können den Administratorbenutzer im Azure-Portal aktivieren, indem Sie zu Ihrer Registrierung navigieren, **Zugriffsschlüssel** unter **EINSTELLUNGEN** und dann **Aktivieren** unter **Administratorbenutzer** auswählen.

![Aktivieren der Administratorbenutzeroberfläche im Azure-Portal][auth-portal-01]

## <a name="next-steps"></a>Nächste Schritte

* [Freigeben Ihres ersten Image mit der Azure CLI](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
