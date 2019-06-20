---
title: Georeplikation einer Azure-Containerregistrierung
description: Erste Schritte beim Erstellen und Verwalten von Azure-Containerregistrierungen mit Georeplikation.
services: container-registry
author: stevelas
manager: jeconnoc
ms.service: container-registry
ms.topic: overview
ms.date: 05/24/2019
ms.author: stevelas
ms.openlocfilehash: a26b261a900dfae742e00d9540e744524b781815
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66384118"
---
# <a name="geo-replication-in-azure-container-registry"></a>Georeplikation in Azure Container Registry

Unternehmen, die eine lokale Umgebung oder ein Hotbackup wünschen, entscheiden sich für die Ausführung von Diensten in mehreren Azure-Regionen. Als bewährte Methode ermöglicht die Platzierung einer Containerregistrierung in jeder Region, in der Images ausgeführt werden, netzwerknahe Vorgänge, die schnelle und zuverlässige Übertragungen auf Image-Ebene gestatten. Die Georeplikation ermöglicht einer Azure-Containerregistrierung, als zentrale Registrierung zu fungieren, die mehreren Regionen regionale Multimasterregistrierungen zur Verfügung stellt. 

Eine Registrierung mit Georeplikation bietet folgende Vorteile:

* Einzelner Registrierungs-/Image-/Tagname in mehreren Regionen verwendbar
* Netzwerknaher Registrierungszugriff in regionalen Bereitstellungen
* Keine zusätzlichen Ausgangsgebühren, da Images aus einer lokalen, replizierten Registrierung in der gleichen Region wie Ihr Containerhost abgerufen werden
* Zentrale Verwaltung einer Registrierung über mehrere Regionen hinweg

> [!NOTE]
> Für den Fall, dass Sie Kopien von Containerimages in mehreren Azure-Containerregistrierungen verwalten müssen, unterstützt die Azure-Containerregistrierung auch [den Import von Images](container-registry-import-images.md). Beispielsweise können Sie in einem DevOps-Workflow ein Image aus einer Entwicklungsregistrierung in eine Produktionsregistrierung importieren, ohne dazu Docker-Befehle verwenden zu müssen.
>

## <a name="example-use-case"></a>Beispiel eines Anwendungsfalls
Contoso betreibt eine öffentlich zugängliche Website in den USA, Kanada und Europa. Um diese Märkte mit lokalen und netzwerknahen Inhalten zu bedienen, betreibt Contoso [Azure Kubernetes Service](/azure/aks/)-Cluster (AKS) in den Regionen „USA, Westen“, „USA, Osten“, „Kanada, Mitte“ und „Europa, Westen“. Die Websiteanwendung, die als Docker-Image bereitgestellt ist, verwendet in allen Regionen den gleichen Code und das gleiche Image. Der für die jeweilige Region lokale Inhalt wird aus einer Datenbank abgerufen, die in jeder Region individuell bereitgestellt wird. Jede regionale Bereitstellung hat eine eigene Konfiguration für Ressourcen wie die lokale Datenbank.

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
* Übertragung per Push in eine einzelne Registrierung, während ACR die Georeplikation verwaltet. Sie können regionale [Webhooks](container-registry-webhook.md) konfigurieren, um über Ereignisse in bestimmten Replikaten benachrichtigt zu werden.

## <a name="configure-geo-replication"></a>Konfigurieren der Georeplikation

Die Konfiguration der Georeplikation ist so einfach wie das Klicken auf Regionen auf einer Karte. Sie können die Georeplikation auch mithilfe von Tools verwalten, z.B. mithilfe der [az acr replication](/cli/azure/acr/replication)-Befehle in der Azure CLI.

Die Georeplikation ist nur bei [Premium-Registrierungen](container-registry-skus.md) möglich. Wenn Ihre Registrierung noch nicht im Premium-Tarif betrieben wird, können Sie im [Azure-Portal](https://portal.azure.com) von den Tarifen Basic und Standard zu Premium wechseln:

![Wechseln von SKUs im Azure-Portal](media/container-registry-skus/update-registry-sku.png)

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
* Wenn Sie mithilfe von Push oder Pull Images in oder aus einer georeplizierten Registrierung übertragen, sendet der Azure Traffic Manager die Anforderung im Hintergrund an die Registrierung in der nächstgelegenen Region.
* Nachdem Sie ein Image oder Tag mithilfe von Push in die nächstgelegene Region übertragen haben, benötigt Azure Container Registry etwas Zeit, um die Manifeste und Ebenen in die von Ihnen ausgewählten verbleibenden Regionen zu replizieren. Für die Replikation größerer Images wird mehr Zeit benötigt als für kleinere Images. Images und Tags werden anhand eines Modells für letztliche Konsistenz über die Replikationsregionen hinweg synchronisiert.
* Um Workflows zu verwalten, die von der Pushübertragung von Updates in eine georeplizierte Registrierung abhängen, wird die Konfiguration von [Webhooks](container-registry-webhook.md) zur Antwort auf die Pushereignisse empfohlen. Sie können regionale Webhooks innerhalb einer georeplizierten Registrierung einrichten, um Pushereignisse nachzuverfolgen, wenn diese über die georeplizierten Regionen hinweg abgeschlossen werden.


## <a name="geo-replication-pricing"></a>Georeplikation – Preise

Die Georeplikation ist ein Funktionsmerkmal des [Premium-Tarifs](container-registry-skus.md) von Azure Container Registry. Wenn Sie eine Registrierung in die gewünschten Regionen replizieren, fallen für jede Region Premium-Registrierungsgebühren an.

Im vorhergehenden Beispiel hat Contoso zwei Registrierungen zu einer konsolidiert und Replikate den Regionen „USA, Osten“, „Kanada, Mitte“ und „Europa, Westen“ hinzugefügt. Contoso zahlt nun viermal den Premium-Tarif pro Monat, ohne zusätzliche Konfiguration oder Verwaltung. Jede Region ruft nun ihre Images lokal per Pull ab und verbessert so die Leistung und Zuverlässigkeit ohne Netzwerkausgangsgebühren von USA, Westen nach Kanada und USA, Osten.

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die dreiteilige Tutorialreihe [Georeplikation in Azure Container Registry](container-registry-tutorial-prepare-registry.md) an. Durchlaufen Sie das Erstellen einer georeplizierten Registrierung und eines Containers, und stellen Sie diesen anschließend mit einem einzigen `docker push`-Befehl in mehreren regionalen Web-Apps für Container-Instanzen bereit.

> [!div class="nextstepaction"]
> [Georeplikation in Azure Container Registry](container-registry-tutorial-prepare-registry.md)
