---
title: SKUs für ein Azure-Containerimage | Azure Marketplace
description: Konfigurieren von SKUs für einen Azure-Container
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 6953329bfabe99fc4bb28f2494cb412ba9cbbba0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64942914"
---
# <a name="container-skus-tab"></a>Registerkarte für Container-SKUs

Auf der Registerkarte **SKUs** auf der Seite **Neues Angebot** können Sie eine oder mehrere SKUs erstellen und Ihrem neuen Angebot zuordnen.  Mit unterschiedlichen SKUs können Sie eine Lösung nach Funktionsgruppen, Abrechnungsmodellen oder einem anderen Merkmal unterscheiden.

## <a name="sku-settings"></a>SKU-Einstellungen

Wenn Sie ein neues Angebot erstellen, sind dem Angebot keine SKUs zugeordnet. Führen Sie die folgenden Schritte aus, um eine neue SKU zu erstellen:

1. Wählen Sie auf der Registerkarte „SKUs“ die Option **Neue SKU** aus.

   ![Eingabeaufforderung für neue SKU](./media/containers-sku-settings.png)

2. Geben Sie die erforderlichen SKU- und Containerinformationen an. Jede SKU entspricht einem Containerimage. Eine SKU besteht aus zwei Teilen:

    -   SKU-Metadaten
    -   Containermetadaten


### <a name="sku-metadata"></a>SKU-Metadaten

Die SKU-Metadaten enthalten Storefront-Anzeigeinformationen für den Containereintrag.

![SKU-Metadaten](./media/containers-sku-details.png)


### <a name="container-metadata"></a>Containermetadaten

Die Containermetadaten enthalten Referenzinformationen aus den Details des Imagerepositorys in Azure Container Registry (ACR). Azure Marketplace kopiert dieses Image in eine Marketplace-spezifische, öffentliche Registrierung und macht das Image nach der Zertifizierung für Kunden verfügbar. Jede Anforderung eines Azure-Benutzers, ein Azure Marketplace-Containerimage zu nutzen, wird über die öffentliche Registrierung des Marketplace verarbeitet, nicht über ACR.

![Containermetadaten](./media/containers-image-repository.png)
    
Unter **Image Repository Details** (Details des Imagerepositorys) auf dem vorherigen Screenshot sind die folgenden Felder aufgeführt.  Erforderliche Felder sind durch ein Sternchen (*) gekennzeichnet.

-   **Abonnement-ID\*** : Die ID des Azure-Abonnements, in dem sich die ACR-Instanz befindet.
-   **Ressourcengruppenname\*** : Der Ressourcengruppenname der ACR-Instanz.
-   **Registrierungsname\*** : Der ACR-Name.
-   **Repositoryname\*** : Der Repositoryname. Dieser Name kann nicht mehr geändert werden, nachdem er festgelegt wurde. Verwenden Sie einen eindeutigen Namen, um Konflikte mit anderen Angeboten in Ihrem Konto zu vermeiden.
-   **Benutzername\*** : Der Benutzername (Administratorbenutzername), der dem ACR-Image zugeordnet ist.
-   **Kennwort\*** : Das Kennwort, das dem ACR-Image zugeordnet ist.

    >[!NOTE]
    >Der Benutzername und das Kennwort sind erforderlich, um sicherzustellen, dass Partner Zugriff auf die ACR haben, der im Veröffentlichungsprozess angegeben wurde.


### <a name="image-version"></a>Imageversion

Beim Veröffentlichen eines Containerimages können Sie Imagetags und SHA-Hashwerte angeben.

**Image Tag or Digest\* (Imagetag oder -hashwert)**
 
- Dieses Tag bzw. dieser Hashwert muss mindestens ein `latest`-Tag und ein Versionstag enthalten (z.B. beginnend mit `xx.xx.xx-`, wobei xx Zahlen sind). Es sollte sich um [Manifesttags](https://github.com/estesp/manifest-tool) handeln, die für mehrere Zielplattformen verwendet werden können. Zudem müssen alle Tags, auf die von einem Manifesttag verwiesen wird, hinzugefügt werden, damit wir sie hochladen können. 
- Mithilfe von Tags können Sie mehrere Versionen eines Containers hinzufügen. Alle Manifesttags (mit Ausnahme von `latest`) müssen entweder mit `X.Y-` oder `X.Y.Z-` beginnen, wobei X, Y und Z ganze Zahlen sind. <br/> Wenn ein `latest`-Tag beispielsweise auf `1.0.1-linux-x64`, `1.0.1-linux-arm32` und `1.0.1-windows-arm32` verweist, müssen diese Tags hier hinzugefügt werden.

>[!NOTE]
>Sie sollten Ihrem Image unbedingt ein **Testtag** hinzufügen, damit Sie das Image beim Testen erkennen können.


## <a name="next-steps"></a>Nächste Schritte

Erstellen Sie auf der Registerkarte [Marketplace](./cpp-marketplace-tab.md) eine Marketplace-Beschreibung für Ihr Angebot. 
