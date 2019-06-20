---
title: Azure Container Registry – Häufig gestellte Fragen
description: Antworten auf häufig gestellte Fragen im Zusammenhang mit dem Azure Container Registry-Dienst
services: container-registry
author: sajayantony
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 5/13/2019
ms.author: sajaya
ms.openlocfilehash: 1400c023e43179a9c8490334e262711486c75a2d
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417924"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>Häufig gestellte Fragen zu Azure Container Registry (ACR)

Dieser Artikel behandelt häufig gestellte Fragen zu Azure Container Registry.

## <a name="resource-management"></a>Ressourcenverwaltung

- [Kann ich eine Azure Container Registry-Instanz mithilfe einer Resource Manager-Vorlage erstellen?](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [Gibt es in ACR Überprüfungen auf Sicherheitsrisiken für Images?](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [Wie konfiguriere ich Kubernetes mit Azure Container Registry?](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [Wie erhalte ich Administratoranmeldeinformationen für eine Containerregistrierung?](#how-do-i-get-admin-credentials-for-a-container-registry)
- [Wie erhalte ich Administratoranmeldeinformationen in einer Resource Manager-Vorlage?](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [Das Löschen der Replikation schlägt mit dem Status „Verboten“ fehl, obwohl die Replikation über die Azure CLI oder Azure PowerShell gelöscht wird](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>Kann ich eine Azure Container Registry-Instanz mithilfe einer Resource Manager-Vorlage erstellen?

Ja. Hier ist [eine Vorlage](https://github.com/Azure/azure-cli/blob/master/src/command_modules/azure-cli-acr/azure/cli/command_modules/acr/template.json), mit deren Hilfe Sie eine Registrierung erstellen können.

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>Gibt es in ACR Überprüfungen auf Sicherheitsrisiken für Images?

Ja. Konsultieren Sie die Dokumentation von [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/) und [Aqua](http://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry).

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>Wie konfiguriere ich Kubernetes mit Azure Container Registry?

Siehe die Dokumentation zu [Kubernetes](http://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr) und Anweisungen zu [Azure Kubernetes Service](container-registry-auth-aks.md).

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>Wie erhalte ich Administratoranmeldeinformationen für eine Containerregistrierung?

> [!IMPORTANT]
> Das Administratorbenutzerkonto ist so ausgelegt, dass ein einzelner Benutzer auf die Registrierung zugreift (hauptsächlich für Testzwecke). Sie sollten die Administratorkonto-Anmeldeinformationen nicht für mehrere Benutzer freigeben. Für Benutzer und Dienstprinzipale wird für monitorlose Szenarien einzelne Identität empfohlen. Siehe [Übersicht über die Authentifizierung](container-registry-authentication.md).

Stellen Sie vor dem Abrufen von Administratoranmeldeinformationen sicher, dass die Registrierung des Administratorbenutzers aktiviert ist.

So rufen Sie Anmeldeinformationen mithilfe der Azure CLI ab

```azurecli
az acr credential show -n myRegistry
```

Mit Azure PowerShell:

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>Wie erhalte ich Administratoranmeldeinformationen in einer Resource Manager-Vorlage?

> [!IMPORTANT]
> Das Administratorbenutzerkonto ist so ausgelegt, dass ein einzelner Benutzer auf die Registrierung zugreift (hauptsächlich für Testzwecke). Sie sollten die Administratorkonto-Anmeldeinformationen nicht für mehrere Benutzer freigeben. Für Benutzer und Dienstprinzipale wird für monitorlose Szenarien einzelne Identität empfohlen. Siehe [Übersicht über die Authentifizierung](container-registry-authentication.md).

Stellen Sie vor dem Abrufen von Administratoranmeldeinformationen sicher, dass die Registrierung des Administratorbenutzers aktiviert ist.

So rufen Sie das erste Kennwort ab

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[0].value]"
}
```

So rufen Sie das zweite Kennwort ab

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[1].value]"
}
```

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>Das Löschen der Replikation schlägt mit dem Status „Verboten“ fehl, obwohl die Replikation über die Azure CLI oder Azure PowerShell gelöscht wird

Der Fehler wird angezeigt, wenn der Benutzer Berechtigungen für eine Registrierung hat, aber für das Abonnement keine Berechtigungen auf der Ebene „Leser“ hat. Um dieses Problem zu beheben, weisen Sie dem Benutzer die Berechtigung „Leser“ für das Abonnement zu:


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

## <a name="registry-operations"></a>Registrierungsvorgänge

- [Wie greife ich auf die HTTP-API V2 für Docker-Registrierungen zu?](#how-do-i-access-docker-registry-http-api-v2)
- [Wie lösche ich alle Manifeste, auf die kein Tag in einem Repository verweist?](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [Warum wird die Nutzung des Registrierungskontingents nach dem Löschen von Images nicht reduziert?](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [Wie überprüfe ich Speicherkontingentänderungen?](#how-do-i-validate-storage-quota-changes)
- [Wie authentifiziere ich mich bei meiner Registrierung bei Ausführung der CLI in einem Container?](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [Bietet Azure Container Registry eine reine TLS 1.2-Konfiguration, und wie wird TLS 1.2 aktiviert?](#does-azure-container-registry-offer-tls-v12-only-configuration-and-how-to-enable-tls-v12)
- [Wird Inhaltsvertrauen von Azure Container Registry unterstützt?](#does-azure-container-registry-support-content-trust)
- [Wie gewähre ich Zugriff auf Pull- oder Pushvorgänge für Images ohne Berechtigung zum Verwalten der Registrierungsressource?](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [Wie aktiviere ich die automatische Quarantäne von Images für eine Registrierung?](#how-do-i-enable-automatic-image-quarantine-for-a-registry)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>Wie greife ich auf die HTTP-API V2 für Docker-Registrierungen zu?

ACR unterstützt die HTTP-API V2 für Docker-Registrierungen. Der Zugriff auf die APIs erfolgt über `https://<your registry login server>/v2/`. Beispiel: `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>Wie lösche ich alle Manifeste, auf die kein Tag in einem Repository verweist?

Bash:

```bash
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

PowerShell:

```powershell
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

Hinweis: Sie können im Löschbefehl `-y` hinzufügen, um die Bestätigung zu überspringen.

Weitere Informationen finden Sie unter [Löschen von Containerimages in Azure Container Registry](container-registry-delete.md).

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>Warum wird die Nutzung des Registrierungskontingents nach dem Löschen von Images nicht reduziert?

Diese Situation kann eintreten, wenn andere Containerimages noch auf die zugrunde liegenden Ebenen verweisen. Wenn Sie ein Image ohne Verweise löschen, wird die Registrierungsnutzung binnen weniger Minuten aktualisiert.

### <a name="how-do-i-validate-storage-quota-changes"></a>Wie überprüfe ich Speicherkontingentänderungen?

Erstellen Sie mithilfe der folgenden Docker-Datei ein Image mit einer 1-GB-Ebene. Dadurch wird sichergestellt, dass das Image eine Ebene hat, die mit keinem anderen Image in der Registrierung gemeinsam genutzt wird.

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

Erstellen Sie das Image, und übertragen Sie es per Push mithilfe der Docker CLI in Ihre Registrierung.

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

Sie sollten sich im Azure-Portal davon überzeugen können, dass die Speichernutzung gestiegen ist. Sie können die Nutzung auch über die CLI abfragen.

```bash
az acr show-usage -n myregistry
```

Löschen Sie das Image mithilfe der Azure CLI oder des Portals, und überprüfen Sie nach wenigen Minuten die aktualisierte Nutzung.

```bash
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>Wie authentifiziere ich mich bei meiner Registrierung bei Ausführung der CLI in einem Container?

Sie müssen den Azure CLI-Container ausführen, indem Sie das Docker-Socket einbinden:

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

Installieren Sie im Container `docker`:

```bash
apk --update add docker
```

Authentifizieren Sie sich anschließend bei Ihrer Registrierung:

```azurecli
az acr login -n MyRegistry
```

### <a name="does-azure-container-registry-offer-tls-v12-only-configuration-and-how-to-enable-tls-v12"></a>Bietet Azure Container Registry eine reine TLS 1.2-Konfiguration, und wie wird TLS 1.2 aktiviert?

Ja. Aktivieren Sie TLS mithilfe eines beliebigen aktuellen Docker-Clients (Version 18.03.0 und höher). 

### <a name="does-azure-container-registry-support-content-trust"></a>Wird Inhaltsvertrauen von Azure Container Registry unterstützt?

Ja, Sie können vertrauenswürdige Images in Azure Container Registry verwenden, da [Docker Notary](https://docs.docker.com/notary/getting_started/) integriert wurde und aktiviert werden kann. Weitere Informationen finden Sie unter [Inhaltsvertrauen in Azure Container Registry](container-registry-content-trust.md).


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>Wo befindet sich die Datei für den Fingerabdruck?

Unter `~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata`:

* Öffentliche Schlüssel und Zertifikate aller Rollen (mit Ausnahme von Delegierungsrollen) werden in `root.json` gespeichert.
* Öffentliche Schlüssel und Zertifikate der Delegierungsrollen werden in der JSON-Datei der übergeordneten Rolle gespeichert (z.B. in `targets.json` für die Rolle `targets/releases`).

Es wird empfohlen, diese öffentlichen Schlüssel und Zertifikate nach der gesamten TUF-Überprüfung durch den Docker- und Notary-Client zu überprüfen.

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>Wie gewähre ich Zugriff auf Pull- oder Pushvorgänge für Images ohne Berechtigung zum Verwalten der Registrierungsressource?

ACR unterstützt [benutzerdefinierte Rollen](container-registry-roles.md), die unterschiedliche Berechtigungsebenen bereitstellen. Insbesondere ermöglichen die Rollen `AcrPull` und `AcrPush` den Benutzern Pull- bzw. Pushvorgänge für Images ohne die Berechtigung zur Verwaltung der Registrierungsressource in Azure.

* Azure-Portal: Ihre Registrierung -> Zugriffssteuerung (IAM) -> Hinzufügen (wählen Sie für die Rolle `AcrPull` oder `AcrPush` aus).
* Azure-Befehlszeilenschnittstelle: Ermitteln Sie die Ressourcen-ID der Registrierung, indem Sie den folgenden Befehl ausführen:

  ```azurecli
  az acr show -n myRegistry
  ```
  
  Anschließend können Sie die Rolle `AcrPull` oder `AcrPush` einem Benutzer zuweisen (im folgenden Beispiel wird `AcrPull` verwendet):

  ```azurecli
    az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
    ```

  Oder weisen Sie die Rolle einem Dienstprinzipal zu, der anhand seiner Anwendungs-ID identifiziert wird:

  ```
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

Die zugewiesene Person kann sich authentifizieren und auf Images in der Registrierung zugreifen.

* So authentifizieren Sie sich bei einer Registrierung
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* So listen Sie Repositorys auf

  ```azurecli
  az acr repository list -n myRegistry
  ```

 So rufen Sie ein Image per Pull ab
    
  ```azurecli
  docker pull myregistry.azurecr.io/hello-world
  ```

Bei ausschließlichem Verwenden der Rolle `AcrPull` oder `AcrPush` hat die zugewiesene Person nicht die Berechtigung, die Registrierungsressource in Azure zu verwalten. Mit `az acr list` oder `az acr show -n myRegistry` kann beispielsweise die Registrierung nicht angezeigt werden.

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>Wie aktiviere ich die automatische Quarantäne von Images für eine Registrierung?

Quarantäne von Images ist derzeit eine Previewfunktion von ACR. Sie können den Quarantänemodus einer Registry so aktivieren, dass nur die Images, die die Sicherheitsüberprüfung erfolgreich bestanden haben, für normale Benutzer sichtbar sind. Weitere Informationen finden Sie im [GitHub-Repository zu ACR](https://github.com/Azure/acr/tree/master/docs/preview/quarantine).

## <a name="diagnostics"></a>Diagnose

- [docker pull fails with error: net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers)](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers) (Docker-Pullvorgang mit Fehler fehlgeschlagen: Net/http: Anforderung während des Wartens auf Verbindung abgebrochen [Client.Timeout-Wert beim Warten auf Header überschritten])
- [docker push succeeds but docker pull fails with error: unauthorized: authentication required](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required) (Docker-Pushvorgang erfolgreich, aber Docker-Pullvorgang schlägt mit Fehler fehl: nicht autorisiert: Authentifizierung erforderlich)
- [Aktivieren und Abrufen der Debugprotokolle des Docker-Daemons](#enable-and-get-the-debug-logs-of-the-docker-daemon) 
- [Neue Benutzerberechtigungen gelten nach Aktualisierung möglicherweise nicht sofort](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [Authentifizierungsinformationen sind für direkte Aufrufe der REST-API nicht im ordnungsgemäßen Format angegeben](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [Warum werden im Azure-Portal nicht alle meine Repositorys oder Tags angezeigt?](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)
- [Wie erfasse ich HTTP-Ablaufverfolgungen unter Windows?](#how-do-i-collect-http-traces-on-windows)

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>docker pull fails with error: net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers)

 - Wenn dieser Fehler ein vorübergehendes Problem ist, wird eine Wiederholung erfolgreich sein.
 - Wenn `docker pull` kontinuierlich fehlschlägt, kann ein Problem mit dem Docker-Daemon vorliegen. Abhilfe für das Problem kann in der Regel ein Neustart des Docker-Daemons schaffen. 
 - Wenn dieses Problem nach Neustart des Docker-Daemons weiterhin auftritt, können Netzwerkprobleme auf dem Computer die Ursache sein. Um zu prüfen, ob die allgemeinen Netzwerkfunktionen auf dem Computer einwandfrei sind, rufen Sie einen Befehl wie `ping www.bing.com` auf.
 - Sie sollten für alle Docker-Clientvorgänge stets einen Wiederholungsmechanismus einrichten.

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>docker push succeeds but docker pull fails with error: unauthorized: authentication required

Dieser Fehler kann bei der Red Hat-Version des Docker-Daemons auftreten, bei der `--signature-verification` standardmäßig aktiviert ist. Sie können die Docker-Daemon-Optionen für Red Hat Enterprise Linux (RHEL) oder Fedora überprüfen, indem Sie den folgenden Befehl ausführen:

```bash
grep OPTIONS /etc/sysconfig/docker
```

Fedora 28 Server bietet beispielsweise die folgenden Docker-Daemon-Optionen:

```
OPTIONS='--selinux-enabled --log-driver=journald --live-restore'
```

Wenn `--signature-verification=false` fehlt, schlägt `docker pull` mit einem Fehler wie dem folgenden fehl:

```bash
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

So beheben Sie den Fehler
1. Fügen Sie der Docker-Daemon-Konfigurationsdatei `/etc/sysconfig/docker` die Option `--signature-verification=false` hinzu. Beispiel:

  ```
  OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'
  ```
2. Starten Sie den Docker-Daemon-Dienst neu, indem Sie den folgenden Befehl ausführen:

  ```bash
  sudo systemctl restart docker.service
  ```

Details zu `--signature-verification` finden Sie, indem Sie `man dockerd` ausführen.

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>Aktivieren und Abrufen der Debugprotokolle des Docker-Daemons  

Starten Sie `dockerd` mit der Option `debug`. Erstellen Sie zunächst die Docker-Daemon-Konfigurationsdatei (`/etc/docker/daemon.json`), falls noch nicht vorhanden, und fügen Sie die Option `debug` hinzu:

```json
{   
    "debug": true   
}
```

Starten Sie dann den Daemon neu. Beispiel für Ubuntu 14.04:

```bash
sudo service docker restart
```

Weitere Informationen finden Sie in der [Dokumentation zu Docker](https://docs.docker.com/engine/admin/#enable-debugging). 

 * Die Protokolle können je nach System an verschiedenen Speicherorten generiert werden. Unter Ubuntu 14.04 ist dies z.B. `/var/log/upstart/docker.log`.   
Weitere Informationen finden Sie in der [Dokumentation zu Docker](https://docs.docker.com/engine/admin/#read-the-logs).    

 * Für Docker für Windows werden die Protokolle unter %LOCALAPPDATA%/docker/ generiert. Möglicherweise sind jedoch noch nicht alle Debuginformationen enthalten.   

   Um auf das vollständige Daemon-Protokoll zugreifen zu können, müssen Sie ggf. einige zusätzliche Schritte durchführen:

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    Jetzt haben Sie Zugriff auf alle Dateien der VM, auf der `dockerd` ausgeführt wird. Das Protokoll befindet sich in `/var/log/docker.log`.

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>Neue Benutzerberechtigungen gelten nach Aktualisierung möglicherweise nicht sofort

Wenn Sie einem Dienstprinzipal neue Berechtigungen (neue Rollen) erteilen, wird die Änderung möglicherweise nicht sofort wirksam. Es gibt zwei mögliche Gründe:

* Verzögerung bei der Zuweisung einer Azure Active Directory-Rolle. Normalerweise erfolgt dieser Vorgang schnell, kann aber aufgrund einer Weitergabeverzögerung einige Minuten dauern.
* Berechtigungsverzögerung auf ACR-Tokenserver. Diese kann bis zu 10 Minuten dauern. Um Abhilfe zu schaffen, können Sie `docker logout` ausführen und sich dann nach einer Minute wieder mit dem gleichen Benutzer authentifizieren:

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

ACR unterstützt derzeit nicht das Löschen der Stammreplikation durch die Benutzer. Die Problemumgehung besteht darin, die Erstellung der Stammreplikation in die Vorlage aufzunehmen, aber ihre Erstellung zu überspringen, indem Sie `"condition": false` wie unten gezeigt hinzufügen:

```json
{
    "name": "[concat(parameters('acrName'), '/', parameters('location'))]",
    "condition": false,
    "type": "Microsoft.ContainerRegistry/registries/replications",
    "apiVersion": "2017-10-01",
    "location": "[parameters('location')]",
    "properties": {},
    "dependsOn": [
        "[concat('Microsoft.ContainerRegistry/registries/', parameters('acrName'))]"
     ]
},
```

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>Authentifizierungsinformationen sind für direkte Aufrufe der REST-API nicht im ordnungsgemäßen Format angegeben

Sie können auf den Fehler `InvalidAuthenticationInfo` stoßen, insbesondere wenn Sie das `curl`-Tool mit der Option `-L`, `--location` verwenden (um Umleitungen zu folgen).
Das Abrufen des Blobs mithilfe von `curl` mit der Option `-L` und der Standardauthentifizierung:

```bash
curl -L -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest
```

kann z.B. zur folgenden Antwort führen:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Error><Code>InvalidAuthenticationInfo</Code><Message>Authentication information is not given in the correct format. Check the value of Authorization header.
RequestId:00000000-0000-0000-0000-000000000000
Time:2019-01-01T00:00:00.0000000Z</Message></Error>
```

Die eigentliche Ursache ist, dass einige Implementierungen von `curl` auf Umleitungen mit Headern aus der ursprünglichen Anforderung folgen.

Um das Problem zu beheben, müssen Sie Umleitungen manuell ohne die Header folgen. Geben Sie die Antwortheader mit der Option `-D -` von `curl` aus, und extrahieren Sie dann den Header `Location`:

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>Warum werden im Azure-Portal nicht alle meine Repositorys oder Tags angezeigt? 

Wenn Sie den Microsoft Edge-Browser verwenden, werden maximal 100 Repositorys oder Tags aufgelistet. Wenn Ihre Registrierung mehr als 100 Repositorys oder Tags enthält, empfehlen wir Ihnen, entweder Firefox oder Chrome als Browser zu verwenden, um alle aufzulisten.

### <a name="how-do-i-collect-http-traces-on-windows"></a>Wie erfasse ich HTTP-Ablaufverfolgungen unter Windows?

#### <a name="prerequisites"></a>Voraussetzungen

- Aktivieren Sie die Entschlüsselung von HTTPS in Fiddler: <https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS>
- Aktivieren Sie Docker zur Verwendung eines Proxys über die Docker-Benutzeroberfläche: <https://docs.docker.com/docker-for-windows/#proxies>
- Setzen Sie die Einstellung nach Abschluss des Vorgangs unbedingt zurück.  Docker funktioniert nicht, wenn diese Option aktiviert ist und Fiddler nicht ausgeführt wird.

#### <a name="windows-containers"></a>Windows-Container

Konfigurieren des Docker-Proxys für 127.0.0.1:8888

#### <a name="linux-containers"></a>Linux-Container

Ermitteln Sie die IP-Adresse des virtuellen Switches des virtuellen Docker-Computers:

```powershell
(Get-NetIPAddress -InterfaceAlias "*Docker*" -AddressFamily IPv4).IPAddress
```

Konfigurieren Sie den Docker-Proxy für die Ausgabe des vorherigen Befehls und für den Port 8888 (z. B. 10.0.75.1:8888).

## <a name="tasks"></a>Aufgaben

- [Wie breche ich alle Ausführungen ab?](#how-do-i-batch-cancel-runs)
- [Wie binde ich den Ordner „.git“ in den Befehl „az acr build“ ein?](#how-do-i-include-the-git-folder-in-az-acr-build-command)

### <a name="how-do-i-batch-cancel-runs"></a>Wie breche ich alle Ausführungen ab?

Mit den folgenden Befehlen werden alle ausgeführten Aufgaben in der angegebenen Registrierung abgebrochen.

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>Wie binde ich den Ordner „.git“ in den Befehl „az acr build“ ein?

Wenn Sie einen lokalen Quellordner an den Befehl `az acr build` übergeben, wird der Ordner `.git` standardmäßig aus dem hochgeladenen Paket ausgeschlossen. Sie können eine `.dockerignore`-Datei mit der folgenden Einstellung erstellen. Sie weist den Befehl an, alle Dateien unter `.git` im hochgeladenen Paket wiederherzustellen. 

```
!.git/**
```

Diese Einstellung gilt auch für den Befehl `az acr run`.

## <a name="cicd-integration"></a>CI/CD-Integration

- [CircleCI](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [GitHub-Aktionen](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)


## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie mehr](container-registry-intro.md) zu Azure Container Registry.
