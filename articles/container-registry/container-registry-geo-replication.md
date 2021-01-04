---
title: Georeplikation für eine Registrierung
description: Erste Schritte zum Erstellen und Verwalten einer Azure-Containerregistrierung mit Georeplikation, die es der Registrierung ermöglicht, mehrere Regionen mit regionale Multimasterreplikaten zu versorgen. Georeplikation ist eine Funktion der Premium-Dienstebene.
author: stevelas
ms.topic: article
ms.date: 07/21/2020
ms.author: stevelas
ms.openlocfilehash: 636896edf8180052508f366bcc548efe13dec1e2
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95810046"
---
# <a name="geo-replication-in-azure-container-registry"></a>Georeplikation in Azure Container Registry

Unternehmen, die eine lokale Umgebung oder ein Hotbackup wünschen, entscheiden sich für die Ausführung von Diensten in mehreren Azure-Regionen. Als bewährte Methode ermöglicht die Platzierung einer Containerregistrierung in jeder Region, in der Images ausgeführt werden, netzwerknahe Vorgänge, die schnelle und zuverlässige Übertragungen auf Image-Ebene gestatten. Die Georeplikation ermöglicht einer Azure-Containerregistrierung, als zentrale Registrierung zu fungieren, die mehreren Regionen regionale Multimasterregistrierungen zur Verfügung stellt. 

Eine Registrierung mit Georeplikation bietet folgende Vorteile:

* Einzelne Registrierungs-, Image- und Tagnamen sind in mehreren Regionen verwendbar
* Verbessern der Leistung und Zuverlässigkeit regionaler Bereitstellungen mit netzwerknahem Registrierungszugriff
* Senken Sie die Kosten für die Datenübertragung, indem Sie Imageebenen aus einer lokalen, replizierten Registrierung in derselben Region, in der sich Ihr Containerhost befindet, oder einer nahegelegenen pullen.
* Zentrale Verwaltung einer Registrierung über mehrere Regionen hinweg

> [!NOTE]
> Für den Fall, dass Sie Kopien von Containerimages in mehreren Azure-Containerregistrierungen verwalten müssen, unterstützt die Azure-Containerregistrierung auch [den Import von Images](container-registry-import-images.md). Beispielsweise können Sie in einem DevOps-Workflow ein Image aus einer Entwicklungsregistrierung in eine Produktionsregistrierung importieren, ohne dazu Docker-Befehle verwenden zu müssen.
>

## <a name="example-use-case"></a>Beispiel eines Anwendungsfalls
Contoso betreibt eine öffentlich zugängliche Website in den USA, Kanada und Europa. Um diese Märkte mit lokalen und netzwerknahen Inhalten zu bedienen, betreibt Contoso [Azure Kubernetes Service](../aks/index.yml)-Cluster (AKS) in den Regionen „USA, Westen“, „USA, Osten“, „Kanada, Mitte“ und „Europa, Westen“. Die Websiteanwendung, die als Docker-Image bereitgestellt ist, verwendet in allen Regionen den gleichen Code und das gleiche Image. Der für die jeweilige Region lokale Inhalt wird aus einer Datenbank abgerufen, die in jeder Region individuell bereitgestellt wird. Jede regionale Bereitstellung hat eine eigene Konfiguration für Ressourcen wie die lokale Datenbank.

Das Bereitstellungsteam befindet sich in Seattle im US-Bundesstaat Washington und nutzt das Rechenzentrum USA, Westen.

![Übertragung per Push in mehrere Registrierungen](media/container-registry-geo-replication/before-geo-replicate.png)<br />*Übertragung per Push in mehrere Registrierungen*

Vor der Nutzung der Georeplikationsfunktionen verfügte Contoso über eine Registrierung in der Region „USA, Westen“ und eine zusätzliche Registrierung in der Region „Europa, Westen“. Um diese verschiedenen Regionen zu versorgen, übertrug das Entwicklungsteam Images per Push in zwei verschiedene Registrierungen.

```bash
docker push contoso.azurecr.io/public/products/web:1.2
docker push contosowesteu.azurecr.io/public/products/web:1.2
```
![Abruf per Pull aus mehreren Registrierungen](media/container-registry-geo-replication/before-geo-replicate-pull.png)<br />*Abruf per Pull aus mehreren Registrierungen*

Es folgen typische Herausforderungen bei mehreren Registrierungen:

* Die Cluster „USA, Osten“, „USA, Westen“ und „Kanada, Mitte“ rufen Daten per Pull aus der Registrierung „USA, Westen“ ab. Dabei fallen Ausgangsgebühren an, da jeder dieser Remotecontainerhosts Images per Pull aus den Rechenzentren von „USA, Westen“ abruft.
* Das Entwicklungsteam muss Images in die Registrierungen „USA, Westen“ und „Europa, Westen“ per Push übertragen.
* Das Entwicklungsteam muss jede regionale Bereitstellung mit Imagenamen konfigurieren und verwalten, die auf die lokale Registrierung verweisen.
* Der Zugriff auf die Registrierung muss für jede Region konfiguriert werden.

## <a name="benefits-of-geo-replication"></a>Vorteile der Georeplikation

![Abruf per Pull aus Registrierung mit Georeplikation](media/container-registry-geo-replication/after-geo-replicate-pull.png)

Die Georeplikationsfunktion von Azure Container Registry bietet die folgenden Vorteile:

* Verwalten einer einzelnen Registrierung in allen Regionen: `contoso.azurecr.io`
* Verwalten einer einzelnen Konfiguration von Imagebereitstellungen, da alle Regionen die gleiche Image-URL verwenden: `contoso.azurecr.io/public/products/web:1.2`
* Übertragung per Push in eine einzelne Registrierung, während ACR die Georeplikation verwaltet. ACR repliziert nur eindeutige Ebenen, wodurch die Regionen übergreifende Datenübertragung reduziert wird. 
* Konfigurieren Sie regionale [Webhooks](container-registry-webhook.md), um über Ereignisse in bestimmten Replikaten benachrichtigt zu werden.

## <a name="configure-geo-replication"></a>Konfigurieren der Georeplikation

Die Konfiguration der Georeplikation ist so einfach wie das Klicken auf Regionen auf einer Karte. Außerdem können Sie die Georeplikation mithilfe von Tools verwalten, wozu auch die [az acr replication](/cli/azure/acr/replication)-Befehle in der Azure-Befehlszeilenschnittstelle gehören, oder Sie können eine für die Georeplikation aktivierte Registrierung mit einer [Azure Resource Manager-Vorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry-geo-replication) bereitstellen.

Die Georeplikation ist bei [Premium-Registrierungen](container-registry-skus.md) möglich. Wenn Ihre Registrierung noch nicht im Premium-Tarif betrieben wird, können Sie im [Azure-Portal](https://portal.azure.com) von den Tarifen Basic und Standard zu Premium wechseln:

![Ändern von Dienstebenen über das Azure-Portal](media/container-registry-skus/update-registry-sku.png)

Um die Georeplikation für Ihre Premium-Registrierung zu konfigurieren, melden Sie sich unter https://portal.azure.com beim Azure-Portal an.

Navigieren Sie zu Ihrer Azure Container Registry-Instanz, und wählen Sie **Replikationen** aus:

![Replikationen auf der Benutzeroberfläche für Containerregistrierungen im Azure-Portal](media/container-registry-geo-replication/registry-services.png)

Eine Karte mit allen aktuellen Azure-Regionen wird angezeigt:

 ![Karte mit Regionen im Azure-Portal](media/container-registry-geo-replication/registry-geo-map.png)

* Blaue Sechsecke stellen aktuelle Replikate dar.
* Grüne Sechsecke stellen mögliche Replikatregionen dar.
* Graue Sechsecke stellen Azure-Regionen dar, die noch nicht für die Replikation verfügbar sind.

Um ein Replikat konfigurieren, wählen Sie ein grünes Sechseck aus und klicken dann auf **Erstellen**:

 ![Benutzeroberfläche zum Erstellen von Replikaten im Azure-Portal](media/container-registry-geo-replication/create-replication.png)

Um zusätzliche Replikate zu konfigurieren, wählen Sie die grünen Sechsecke für andere Regionen aus und klicken dann auf **Erstellen**.

ACR beginnt, Images in den konfigurierten Replikaten zu synchronisieren. Sobald der Vorgang abgeschlossen ist, gibt das Portal *Bereit* zurück. Der Status des Replikats im Portal wird nicht automatisch aktualisiert. Klicken Sie auf die Schaltfläche „Aktualisieren“, um den aktualisierten Status anzuzeigen.

## <a name="considerations-for-using-a-geo-replicated-registry"></a>Überlegungen zur Verwendung einer georeplizierten Registrierung

* Jede Region in einer georeplizierten Registrierung ist nach der Einrichtung unabhängig. Azure Container Registry-SLAs gelten für jede georeplizierte Region.
* Wenn Sie mithilfe von Push oder Pull Images in eine georeplizierte Registrierung oder daraus übertragen, sendet der Azure Traffic Manager die Anforderung im Hintergrund an die Registrierung in der im Sinne der Netzwerklatenz nächstgelegenen Region.
* Nachdem Sie ein Image oder Tag mithilfe von Push in die nächstgelegene Region übertragen haben, benötigt Azure Container Registry etwas Zeit, um die Manifeste und Ebenen in die von Ihnen ausgewählten verbleibenden Regionen zu replizieren. Für die Replikation größerer Images wird mehr Zeit benötigt als für kleinere Images. Images und Tags werden anhand eines Modells für letztliche Konsistenz über die Replikationsregionen hinweg synchronisiert.
* Um Workflows zu verwalten, die von der Pushübertragung von Updates in eine georeplizierte Registrierung abhängen, wird die Konfiguration von [Webhooks](container-registry-webhook.md) zur Antwort auf die Pushereignisse empfohlen. Sie können regionale Webhooks innerhalb einer georeplizierten Registrierung einrichten, um Pushereignisse nachzuverfolgen, wenn diese über die georeplizierten Regionen hinweg abgeschlossen werden.
* Zum Verarbeiten von Blobdaten, die Inhaltsebenen darstellen, verwendet Azure Container Registry Datenendpunkte. Sie können [dedizierte Datenendpunkte](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints) für Ihre Registrierung in jeder georeplizierten Region Ihrer Registrierung aktivieren. Diese Endpunkte ermöglichen die Konfiguration streng verteilter Firewallzugriffsregeln. Zu Troubleshootingzwecken können Sie optional [das Routing zu einer Replikation deaktivieren](#temporarily-disable-routing-to-replication), die replizierten Daten dabei jedoch behalten.
* Wenn Sie einen [privaten Link](container-registry-private-link.md) für Ihre Registrierung über private Endpunkte in einem virtuellen Netzwerk konfigurieren, werden standardmäßig in jeder der georeplizierten Regionen dedizierte Datenendpunkte aktiviert. 

## <a name="delete-a-replica"></a>Löschen eines Replikats

Nachdem Sie ein Replikat für Ihre Registrierung konfiguriert haben, können Sie es jederzeit löschen, wenn es nicht mehr benötigt wird. Löschen Sie ein Replikat mit dem Azure-Portal oder mit einem anderen Tool, etwa dem [az acr replication delete](/cli/azure/acr/replication#az-acr-replication-delete)-Befehl in der Azure-Befehlszeilenschnittstelle.

So löschen Sie ein Replikat im Azure-Portal

1. Navigieren Sie zu Ihrer Azure Container Registry-Instanz, und wählen Sie **Replikationen** aus.
1. Wählen Sie den Namen eines Replikats aus, und wählen Sie **Löschen** aus. Bestätigen Sie, dass Sie das Replikat löschen möchten.

So verwenden Sie die Azure CLI, um ein Replikat von *myregistry* in der Region „USA, Osten“ zu löschen:

```azurecli
az acr replication delete --name eastus --registry myregistry
```

## <a name="geo-replication-pricing"></a>Georeplikation – Preise

Georeplikation ist ein Funktionsmerkmal des [Premium-Tarifs](container-registry-skus.md) von Azure Container Registry. Wenn Sie eine Registrierung in die gewünschten Regionen replizieren, fallen für jede Region Premium-Registrierungsgebühren an.

Im vorhergehenden Beispiel hat Contoso zwei Registrierungen zu einer konsolidiert und Replikate den Regionen „USA, Osten“, „Kanada, Mitte“ und „Europa, Westen“ hinzugefügt. Contoso zahlt nun viermal den Premium-Tarif pro Monat, ohne zusätzliche Konfiguration oder Verwaltung. Jede Region ruft nun ihre Images lokal per Pull ab und verbessert so die Leistung und Zuverlässigkeit ohne Netzwerkausgangsgebühren von USA, Westen nach Kanada und USA, Osten.

## <a name="troubleshoot-push-operations-with-geo-replicated-registries"></a>Problembehandlung bei Pushvorgängen mit georeplizierten Registrierungen
 
Ein Docker-Client, der ein Image per Push in eine georeplizierte Registrierung überträgt, kann möglicherweise nicht alle Imageebenen und das zugehörige Manifest in eine einzelne replizierte Region übertragen. Dies kann der Fall sein, wenn Azure Traffic Manager Registrierungsanforderungen an die dem Netzwerk nächstgelegene replizierte Registrierung weiterleitet. Wenn sich die Registrierung in der *Nähe* von zwei Replikationsregionen befindet, können Imageebenen und das Manifest an die beiden Standorte verteilt werden, und der Pushvorgang schlägt beim Überprüfen des Manifests fehl. Dieses Problem wird aufgrund der Art verursacht, auf die der DNS-Name der Registrierung auf manchen Linux-Hosts aufgelöst wird. Dieses Problem tritt nicht unter Windows auf, das einen clientseitigen DNS-Cache bereitstellt.
 
Wenn dieses Problem auftritt, können Sie einen clientseitigen DNS-Cache wie `dnsmasq` auf den Linux-Host anwenden. Dadurch wird sichergestellt, dass der Name der Registrierung konsistent aufgelöst wird. Wenn Sie einen virtuellen Linux-Computer in Azure verwenden, um einen Pushvorgang in eine Registrierung auszuführen, finden Sie unter [DNS-Namensauflösungsoptionen für virtuelle Linux-Computer in Azure](../virtual-machines/linux/azure-dns.md) weitere Informationen.

Um die DNS-Auflösung beim Pushen von Images auf das nächstgelegene Replikat zu optimieren, konfigurieren Sie eine georeplizierte Registrierung in denselben Azure-Regionen wie die Quelle der Pushvorgänge oder die nächstgelegene Region, wenn Sie außerhalb von Azure arbeiten.

### <a name="temporarily-disable-routing-to-replication"></a>Vorübergehendes Deaktivieren des Routings zur Replikation

Zum Troubleshooting von Vorgängen mit einer georeplizierten Registrierung sollten Sie das Traffic Manager-Routing zu einer oder mehreren Replikationen vorrübergehend deaktivieren. Ab der Azure CLI-Version 2.8 können Sie eine `--region-endpoint-enabled`-Option (Vorschau) konfigurieren, wenn Sie eine replizierte Region erstellen oder aktualisieren. Wenn Sie die `--region-endpoint-enabled`-Option einer Replikation auf `false` festlegen, leitet der Traffic Manager Pushanforderungen oder Pull Requests von Docker nicht mehr in diese Region weiter. Standardmäßig ist das Routing für alle Replikationen aktiviert. Die Datensynchronisierung wird für alle Replikationen ausgeführt, unabhängig davon, ob das Routing aktiviert oder deaktiviert ist.

Zum Deaktivieren des Routings zu einer vorhandenen Replikation führen Sie zuerst [az acr replication list][az-acr-replication-list] aus, um die Replikationen in der Registrierung aufzuführen. Führen Sie dann [az acr replication update][az-acr-replication-update] aus, und legen Sie `--region-endpoint-enabled false` für eine bestimmte Replikation fest. Das Konfigurieren der Einstellung für die *westus*-Replikation in *myregistry* sieht beispielsweise folgendermaßen aus:

```azurecli
# Show names of existing replications
az acr replication list --registry --output table

# Disable routing to replication
az acr replication update --name westus \
  --registry myregistry --resource-group MyResourceGroup \
  --region-endpoint-enabled false
```

So wird das Routing zu einer Replikation wiederhergestellt:

```azurecli
az acr replication update --name westus \
  --registry myregistry --resource-group MyResourceGroup \
  --region-endpoint-enabled true
```

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die dreiteilige Tutorialreihe [Georeplikation in Azure Container Registry](container-registry-tutorial-prepare-registry.md) an. Durchlaufen Sie das Erstellen einer georeplizierten Registrierung und eines Containers, und stellen Sie diesen anschließend mit einem einzigen `docker push`-Befehl in mehreren regionalen Web-Apps für Container-Instanzen bereit.

> [!div class="nextstepaction"]
> [Georeplikation in Azure Container Registry](container-registry-tutorial-prepare-registry.md)

[az-acr-replication-list]: /cli/azure/acr/replication#az-acr-replication-list
[az-acr-replication-update]: /cli/azure/acr/replication#az-acr-replication-update
