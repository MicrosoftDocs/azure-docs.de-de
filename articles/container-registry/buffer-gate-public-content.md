---
title: Verwalten öffentlicher Inhalte in einer privaten Containerregistrierung
description: Methoden und Workflows in Azure Container Registry zum Verwalten von Abhängigkeiten in öffentlichen Images von Docker Hub und anderen öffentlichen Inhalten
author: dlepow
ms.topic: article
ms.author: danlep
ms.date: 11/20/2020
ms.openlocfilehash: 0c92899528d417f9c91f8f8930ca4932dc74e850
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024737"
---
# <a name="manage-public-content-with-azure-container-registry"></a>Verwalten öffentlicher Inhalte mit Azure Container Registry

Dieser Artikel bietet eine Übersicht über Methoden und Workflows zur Verwendung einer lokalen Registrierung, z. B. [Azure Container Registry](container-registry-intro.md), zum Verwalten von Kopien öffentlicher Inhalte, z. B. Containerimages in Docker Hub. 


## <a name="risks-with-public-content"></a>Risiken bei öffentlichen Inhalten

In Ihrer Umgebung bestehen möglicherweise Abhängigkeiten von öffentlichen Inhalten, z. B. von öffentlichen Containerimages, [Helm-Charts](https://helm.sh/), [Open Policy Agent](https://www.openpolicyagent.org/)-Richtlinien (OPA-Richtlinien) oder anderen Artefakten. Beispielsweise könnten Sie [nginx](https://hub.docker.com/_/nginx) für das Dienstrouting oder `docker build FROM alpine` ausführen, indem Sie Images direkt aus Docker Hub oder einer anderen öffentlichen Registrierung pullen. 

Ohne geeignete Kontrollen können Abhängigkeiten von Inhalten öffentlicher Registrierungen zu Risiken für die Workflows zur Imageentwicklung und -bereitstellung führen. Um diese Risiken zu minimieren, sollten Sie nach Möglichkeit lokale Kopien von öffentlichen Inhalten aufbewahren. Weitere Informationen finden Sie im [Blog der Open Container Initiative](https://opencontainers.org/posts/blog/2020-10-30-consuming-public-content/). 

## <a name="authenticate-with-docker-hub"></a>Authentifizieren mit Docker Hub

Wenn Sie derzeit im Rahmen eines Erstellungs- oder Bereitstellungsworkflows öffentliche Images von Docker Hub pullen, empfiehlt es sich in einem ersten Schritt, [die Authentifizierung über ein Docker Hub-Konto durchzuführen](https://docs.docker.com/docker-hub/download-rate-limit/#how-do-i-authenticate-pull-requests), anstatt einen anonymen Pull Request auszuführen.

Beim Ausführen von häufigen anonymen Pull Requests wird möglicherweise ein Docker-Fehler wie `ERROR: toomanyrequests: Too Many Requests.` oder `You have reached your pull rate limit.` angezeigt. Authentifizieren Sie sich bei Docker Hub, um diese Fehler zu verhindern.

> [!NOTE]
> Ab dem 2. November 2020 gelten [die Grenzwerte für die Downloadrate](https://docs.docker.com/docker-hub/download-rate-limit) für anonyme und authentifizierte Anforderungen an Docker Hub von Docker-Konten im Plan „Free“. Diese Grenzwerte werden durch die IP-Adresse bzw. die Docker-ID erzwungen. 
>
> Berücksichtigen Sie bei der Schätzung der Anzahl Ihrer Pull Requests, dass bei der Verwendung von Diensten von Cloudanbietern oder bei der Arbeit hinter einer unternehmenseigenen NAT mehrere Benutzer in Docker Hub insgesamt als Teilmenge von IP-Adressen gelten. Wenn Sie für Anforderungen an Docker Hub eine Authentifizierung über ein kostenpflichtiges Docker-Konto hinzufügen, werden potenzielle Dienstunterbrechungen aufgrund der Drosselung der Ratenbegrenzung vermieden.
>
> Ausführliche Informationen finden Sie unter [Docker Pricing and Subscriptions](https://www.docker.com/pricing) (Docker – Preise und Abonnements) und [Docker Terms of Service](https://www.docker.com/legal/docker-terms-service) (Docker-Vertragsbedingungen).

### <a name="docker-hub-access-token"></a>Docker Hub-Zugriffstoken

Docker Hub unterstützt [persönliche Zugriffstoken](https://docs.docker.com/docker-hub/access-tokens/) als Alternativen zu einem Docker-Kennwort für die Authentifizierung bei Docker Hub. Token werden für automatisierte Dienste empfohlen, die Images von Docker Hub pullen. Sie können mehrere Token für verschiedene Benutzer oder Dienste generieren und Token widerrufen, wenn sie nicht mehr benötigt werden.

Wenn Sie sich mit einem Token bei `docker login` authentifizieren möchten, lassen Sie das Kennwort in der Befehlszeile weg. Wenn Sie zur Eingabe eines Kennworts aufgefordert werden, geben Sie stattdessen das Token ein. Wenn Sie die zweistufige Authentifizierung für das Docker Hub-Konto aktiviert haben, müssen Sie bei der Anmeldung über die Docker-Befehlszeilenschnittstelle ein persönliches Zugriffstoken verwenden.

### <a name="authenticate-from-azure-services"></a>Authentifizieren über Azure-Dienste

Mehrere Azure-Dienste, einschließlich App Service und Azure Container Instances, unterstützen das Pullen von Images aus öffentlichen Registrierungen, z. B. Docker Hub für Containerbereitstellungen. Wenn Sie ein Image über Docker Hub bereitstellen möchten, empfiehlt es sich, die Einstellungen für die Authentifizierung über ein Docker Hub-Konto zu konfigurieren. Beispiele:

**App Service**

* **Imagequelle:** Docker Hub
* **Repositoryzugriff:** Privat
* **Anmeldung:** \<Docker Hub username>
* **Kennwort**: \<Docker Hub token>

Weitere Informationen finden Sie unter [Docker Hub authenticated pulls on App Service](https://azure.github.io/AppService/2020/10/15/Docker-Hub-authenticated-pulls-on-App-Service.html) (Über Docker Hub authentifizierte Pulls in App Service).

**Azure Container Instances**

* **Imagequelle:** Docker Hub oder andere Registrierung
* **Imagetyp:** Privat
* **Anmeldeserver für die Imageregistrierung:** docker.io
* **Benutzername für die Imageregistrierung:** \<Docker Hub username>
* **Kennwort für die Imageregistrierung:** \<Docker Hub token>
* **Image:** docker.io/\<repo name\>:\<tag>

## <a name="import-images-to-an-azure-container-registry"></a>Importieren von Images in eine Azure Container Registry-Instanz
 
Zum Verwalten von Kopien öffentlicher Images können Sie zunächst eine Instanz von Azure Container Registry erstellen, wenn Sie noch nicht über eine verfügen. Sie können Registrierung über die [Azure-Befehlszeilenschnittstelle](container-registry-get-started-azure-cli.md), das [Azure-Portal](container-registry-get-started-portal.md), [Azure PowerShell](container-registry-get-started-powershell.md) oder andere Tools erstellen. 

Als einmaliger Schritt wird empfohlen, Basisimages und andere öffentliche Inhalte in Ihre Azure Container Registry-Instanz zu [importieren](container-registry-import-images.md). Der Befehl [az acr import](/cli/azure/acr#az_acr_import) in der Azure-Befehlszeilenschnittstelle unterstützt den Import von Images aus öffentlichen Registrierungen, z. B. Docker Hub und Microsoft Container Registry, und aus anderen privaten Containerregistrierungen. 

Für `az acr import` ist keine lokale Docker-Installation erforderlich. Sie können diesen Befehl in einer lokalen Installation der Azure-Befehlszeilenschnittstelle oder direkt in Azure Cloud Shell ausführen. Er unterstützt Images mit allen Betriebssystemen, Images mit mehreren Architekturen oder OCI-Artefakte (z. B. Helm-Charts).

Beispiel:

```azurecli-interactive
az acr import \
  --name myregistry \
  --source docker.io/library/hello-world:latest \
  --image hello-world:latest \
  --username <Docker Hub username> \
  --password <Docker Hub token>
```

Je nach den Anforderungen Ihrer Organisation können Sie den Import in einer dedizierten Registrierung oder in einem Repository in einer freigegebenen Registrierung durchführen.

## <a name="automate-application-image-updates"></a>Automatisieren der Aktualisierung von Anwendungsimages

Entwickler von Anwendungsimages müssen sicherstellen, dass der Code auf lokale Inhalte unter ihrer Kontrolle verweist. Beispielsweise muss eine `Docker FROM`-Anweisung in einem Dockerfile auf ein Image in einer privaten Basisimageregistrierung anstelle einer öffentlichen Registrierung verweisen. 

Zum Erweitern des Imageimports richten Sie einen [Azure Container Registry-Task](container-registry-tasks-overview.md) ein, um Buildvorgänge für Anwendungsimages zu automatisieren, wenn Basisimages aktualisiert werden. Ein automatisierter Buildtask kann [Updates des Basisimages](container-registry-tasks-base-images.md) und [Updates des Quellcodes](container-registry-tasks-overview.md#trigger-task-on-source-code-update) nachverfolgen.

Ein detailliertes Beispiel finden Sie unter [Verwenden und Verwalten von öffentlichen Inhalten mit Azure Container Registry Tasks](tasks-consume-public-content.md). 

> [!NOTE]
> Ein einzelner vorkonfigurierter Task kann automatisch alle Anwendungsimages neu erstellen, die auf ein abhängiges Basisimage verweisen. 
 
## <a name="next-steps"></a>Nächste Schritte
 
* Erfahren Sie mehr über die Verwendung von [ACR Tasks](container-registry-tasks-overview.md) zum Erstellen, Ausführen, Pushen und Patchen von Containerimages in Azure.
* Unter [Verwenden und Verwalten von öffentlichen Inhalten mit Azure Container Registry Tasks](tasks-consume-public-content.md) finden Sie Informationen zu einem automatisierten Workflow mit Kontrollmechanismen zum Aktualisieren von Basisimages in Ihrer Umgebung. 
* Weitere Beispiele zum Automatisieren von Imagebuildvorgängen und Imageupdates finden Sie in den [Tutorials zu ACR Tasks](container-registry-tutorial-quick-task.md).
