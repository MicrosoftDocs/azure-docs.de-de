---
title: Upgrade einer klassischen Azure-Containerregistrierung
description: Profitieren Sie von dem erweiterten Featureset der verwalteten Containerregistrierungen in Basic-, Standard- und Premium-SKUs, indem Sie Ihre nicht verwaltete klassische Containerregistrierung aktualisieren.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/26/2019
ms.author: danlep
ms.openlocfilehash: a5099feee34eb5497b68987485412e29ad5d5365
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60480341"
---
# <a name="upgrade-a-classic-container-registry"></a>Upgrade einer klassischen Containerregistrierung

Azure Container Registry (ACR) ist in verschiedenen Dienstebenen verfügbar, [sogenannten SKUs](container-registry-skus.md). Die erste Version von ACR bot nur eine einzige SKU (Classic), der viele Features fehlen, die bei den SKUs „Basic“, „Standard“ und „Premium“ (zusammenfassend als *verwaltete* Registrierungen bezeichnet) selbstverständlich sind.

Die SKU „Klassisch“ wird eingestellt und ist nach April 2019 nicht mehr verfügbar. Dieser Artikel erläutert, wie Sie Ihre nicht verwaltete klassische Registrierung in eine der verwalteten SKUs migrieren, sodass Sie von den erweiterten Features profitieren können.

## <a name="why-upgrade"></a>Gründe für ein Upgrade

Die SKU „Klassisch“ wird **eingestellt** und ist nach **April 2019** nicht mehr verfügbar. Für alle vorhandenen klassischen Registrierungen muss vor April 2019 ein Upgrade durchgeführt werden. Die Portalverwaltungsfunktionen der klassischen Registrierungen werden auslaufen. Die Erstellung neuer klassischer Register wird nach April 2019 deaktiviert.

Aufgrund der geplanten Einstellung und des eingeschränkten Funktionsumfangs von klassischen nicht verwalteten Registrierungen muss für alle klassischen Registrierungen ein Upgrade auf verwaltete Registrierungen (Basic, Standard oder Premium) ausgeführt werden. Die SKUs auf höherer Ebene integrieren die Registrierung nahtloser mit den Funktionen von Azure. Weitere Informationen zu Preisen und Funktionen der verschiedenen Dienstebenen finden Sie unter [Containerregistrierungs-SKUs](container-registry-skus.md).

Die klassische Registrierung ist von dem Speicherkonto abhängig, das von Azure beim Erstellen der Registrierung automatisch in Ihrem Azure-Abonnement bereitgestellt wird. Im Gegensatz dazu nutzen die Basic-, Standard- und Premium-SKUs die [erweiterten Speicherfeatures](container-registry-storage.md) von Azure und bieten eine transparente Speicherung Ihrer Images. In Ihrem eigenen Abonnement wird kein separates Speicherkonto erstellt.

Ein Speicher mit verwalteter Registrierung bietet folgende Vorteile:

* Containerimages werden [im Ruhezustand verschlüsselt](container-registry-storage.md#encryption-at-rest).
* Images werden mit einem [georedundanten Speicher](container-registry-storage.md#geo-redundant-storage) gespeichert, sodass die Sicherung Ihrer Images durch die Replikation in mehreren Regionen (nur Premium SKU) gewährleistet ist.
* Die Möglichkeit, [zwischen SKUs zu wechseln](container-registry-skus.md#changing-skus), um mit einer höheren SKU einen höheren Durchsatz zu ermöglichen. Jede SKU von ACR erfüllt Ihre Bedürfnisse beim Durchsatz Ihrem Bedarf entsprechend.
* Ein einheitliches Sicherheitsmodell für die Registrierung und den zugehörigen Speicher sorgt für eine vereinfachte Rechteverwaltung. Sie verwalten die Berechtigungen nur für die Containerregistrierung und müssen sich nicht auch noch um Berechtigungen für ein separates Speicherkonto kümmern.

Weitere Details zur Speicherung von Images in ACR finden Sie unter [Container image storage in Azure Container Registry](container-registry-storage.md) (Speichern von Containerimages in Azure Container Registry).

## <a name="migration-considerations"></a>Überlegungen zur Migration

Wenn Sie ein Upgrade von einer klassischen Registrierung zu einer verwalteten Registrierung durchführen, muss Azure alle vorhandenen Containerimages aus dem über ACR erstellten Speicherkonto in Ihrem Abonnement in ein von Azure verwaltetes Speicherkonto kopieren. Je nach Größe Ihrer Registrierung kann dieser Vorgang wenige Minuten oder mehrere Stunden dauern. Zu Schätzzwecken ist mit einer Migrationszeit von ca. 0,5 GiB pro Minute zu rechnen.

Während des Konvertierungsprozesses werden `docker push` Operationen während der letzten 10 % der Migration deaktiviert. `docker pull` funktioniert weiterhin normal.

Löschen oder ändern Sie während des Konvertierungsprozesses keine Inhalte des Speicherkontos, das Ihre klassische Registrierung sichert. Dadurch können Ihre Containerimages beschädigt werden.

Sobald die Migration abgeschlossen ist, wird das Speicherkonto in Ihrem Abonnement, das ursprünglich Ihre klassische Registrierung gesichert hat, von Azure Container Registry nicht mehr verwendet. Nachdem Sie überprüft haben, ob die Migration erfolgreich war, sollten Sie das Speicherkonto möglicherweise löschen, um Kosten zu sparen.

>[!IMPORTANT]
> Das Upgrade von der klassischen zu einer der verwalteten SKUs ist ein **nicht umkehrbarer Prozess**. Nachdem Sie eine klassische Registrierung in eine Basic-, Standard- oder Premium-SKU konvertiert haben, können Sie die klassische Registrierung nicht wiederherstellen. Sie können jedoch frei zwischen den verwalteten SKUs wechseln, um ausreichend Kapazität für Ihre Registrierung zur Verfügung zu haben.

## <a name="how-to-upgrade"></a>Aktualisieren

Für das Upgrade einer nicht verwalteten klassischen Registrierung zu einer der verwalteten SKUs gibt es verschiedene Möglichkeiten. In den folgenden Abschnitten wird die Vorgehensweise für die [Azure-Befehlszeilenschnittstelle][azure-cli] und das [Azure-Portal][azure-portal] beschrieben.

## <a name="upgrade-in-azure-cli"></a>Upgrade über die Azure-Befehlszeilenschnittstelle

Um eine klassische Registrierung über die Azure-Befehlszeilenschnittstelle zu aktualisieren, führen Sie den Befehl [az acr update][az-acr-update] aus und geben die neue SKU für die Registrierung an. Im folgenden Beispiel wird die klassische Registrierung mit dem Namen *myclassicregistry* auf die Premium-SKU aktualisiert:

```azurecli-interactive
az acr update --name myclassicregistry --sku Premium
```

Wenn die Migration abgeschlossen ist, sollte eine Ausgabe ähnlich der folgenden angezeigt werden. Beachten Sie, dass der `sku`-Wert „Premium“ und der `storageAccount`-Wert `null` lautet. Dies weist darauf hin, dass Azure jetzt den Imagespeicher für diese Registrierung verwaltet.

```JSON
{
  "adminUserEnabled": false,
  "creationDate": "2017-12-12T21:23:29.300547+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry",
  "location": "eastus",
  "loginServer": "myregistry.azurecr.io",
  "name": "myregistry",
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "name": "Premium",
    "tier": "Premium"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Wenn Sie eine verwaltete Registrierungs-SKU angeben, deren maximale Kapazität kleiner ist als die Größe Ihrer klassischen Registrierung, wird Ihnen eine Fehlermeldung ähnlich der folgenden angezeigt.

`Cannot update the registry SKU due to reason: Registry size 12936251113 bytes exceeds the quota value 10737418240 bytes for SKU Basic. The suggested SKU is Standard.`

Wenn Sie eine solche Fehlermeldung erhalten, führen Sie den Befehl [az acr update][az-acr-update] erneut aus und geben die vorgeschlagene SKU an. Dies ist die nächsthöhere SKU, die Ihre Images aufnehmen kann.

## <a name="upgrade-in-azure-portal"></a>Upgrade über das Azure-Portal

Wenn Sie ein Upgrade einer klassischen Registrierung über das Azure-Portal ausführen, wählt Azure automatisch die Standard- oder Premium-SKU aus, je nachdem, welche SKU Ihre Images aufnehmen kann. Ein Beispiel: Wenn Ihre Registrierung weniger als 100 GiB Images enthält, wählt Azure automatisch die SKU „Standard“ (maximal 100 GiB) und konvertiert die klassische Registrierung in diese SKU.

Um Ihre klassische Registrierung über das Azure-Portal zu aktualisieren, navigieren Sie zur **Übersicht** über die Containerregistrierung und wählen **Auf verwaltete Registrierung aktualisieren** aus.

![Schaltfläche zum Aktualisieren einer klassischen Registrierung auf der Benutzeroberfläche des Azure-Portals][update-classic-01-upgrade]

Wählen Sie **OK** aus, um zu bestätigen, dass Sie das Upgrade auf eine verwaltete Registrierung durchführen möchten.

Während der Migration wird der **Bereitstellungsstatus** der Registrierung im Portal als *Wird aktualisiert* angezeigt. Wie bereits erwähnt, sind `docker push` Operationen während der letzten 10 % der Migration deaktiviert. Sie dürfen das Speicherkonto, das von der klassischen Registrierung verwendet wird, während des Migrationsvorgangs weder löschen noch aktualisieren. Dies kann zur Beschädigung der Images führen.

![Fortschritt der Aktualisierung einer klassischen Registrierung auf der Benutzeroberfläche des Azure-Portals][update-classic-03-updating]

Wenn die Migration abgeschlossen ist, wird der **Bereitstellungsstatus** als *Erfolgreich* angezeigt, und Sie können den normalen Betrieb mit Ihrer Registrierung wieder aufnehmen.

![Abschlussstatus der Aktualisierung einer klassischen Registrierung auf der Benutzeroberfläche des Azure-Portals][update-classic-04-updated]

## <a name="next-steps"></a>Nächste Schritte

Nach dem Upgrade einer klassischen Registrierung auf eine verwaltete Registrierung verwendet Azure das Speicherkonto nicht mehr, das ursprünglich die klassische Registrierung gesichert hat. Um Kosten einzusparen, können Sie das Speicherkonto oder den Blobcontainer im Konto löschen, der die alten Containerimages enthielt.

<!-- IMAGES -->
[update-classic-01-upgrade]: ./media/container-registry-upgrade/update-classic-01-upgrade.png
[update-classic-02-confirm]: ./media/container-registry-upgrade/update-classic-02-confirm.png
[update-classic-03-updating]: ./media/container-registry-upgrade/update-classic-03-updating.png
[update-classic-04-updated]: ./media/container-registry-upgrade/update-classic-04-updated.png

<!-- LINKS - internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[azure-cli]: /cli/azure/install-azure-cli
[azure-portal]: https://portal.azure.com