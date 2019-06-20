---
title: Aktualisieren eines vorhandenen Azure-Containerangebots | Azure Marketplace
description: Hier erfahren Sie, wie Sie ein vorhandenes Containerangebot im Azure Marketplace aktualisieren.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: article
ms.date: 11/01/2018
ms.author: pabutler
ms.openlocfilehash: 0e0db961dec69880eff6ef640e9210af6fcbc978
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64942598"
---
# <a name="update-an-existing-container-offer"></a>Aktualisieren eines vorhandenen Containerangebots

In diesem Artikel werden die Schritte zum Aktualisieren Ihres Containerangebots im [Cloud-Partnerportal](https://cloudpartner.azure.com/) erläutert.

Sie könnten Ihr Angebot beispielsweise aus den folgenden Gründen aktualisieren:

-  Hinzufügen einer neuen Containerimageversion zu vorhandenen SKUs
-  Hinzufügen von neuen SKUs
-  Aktualisieren von Marketplace-Metadaten für das Angebot oder einzelne SKUs

Um Ihnen bei diesen Änderungen zu helfen, bietet das Portal die Features **Vergleichen** und **Verlauf**.  


## <a name="unpermitted-changes-to-a-container-offer-or-sku"></a>Nicht zugelassene Änderungen an Containerangeboten oder -SKUs

Es gibt Attribute eines Containerangebots oder einer SKU, die nicht geändert werden können, nachdem das Angebot im Azure Marketplace live geschaltet wurde. Sie können die folgenden Einstellungen nicht ändern:

-  **Angebots-ID** und **Herausgeber-ID** des Angebots
-  **SKU-ID** von vorhandenen SKUs
-  Versionstags, z.B.: `1.0.1`
-  Abrechnungs-/Lizenzmodelländerungen an vorhandenen SKUs

## <a name="common-update-operations"></a>Gängige Aktualisierungsvorgänge

Die folgenden Aktualisierungsvorgänge werden häufig durchgeführt.

### <a name="update-container-image-version-for-a-sku"></a>Aktualisieren der Containerimageversion für eine SKU

Es ist üblich, dass ein Containerimage regelmäßig mit Sicherheitspatches, zusätzlichen Features usw. aktualisiert wird. In diesem Szenario aktualisieren Sie mithilfe der folgenden Schritte das Containerimage, das Ihre SKU referenziert:

1. Melden Sie sich beim [Cloud-Partnerportal](https://cloudpartner.azure.com/) an.
2. Suchen Sie unter **Alle Angebote** nach dem Angebot, das Sie aktualisieren möchten.
3. Wählen Sie auf der Registerkarte **SKUs** die SKU aus, die mit dem zu aktualisierenden Containerimage verknüpft ist.
4. Wählen Sie unter **Containerimage** die Option **+ Neue Imageversion** aus, um ein neues Containerimage hinzuzufügen.
5. Geben Sie die neuen **Containerimageversionen** an. Für die Imageversion müssen die gleichen Tagrichtlinien wie für frühere Versionen gelten. Versionstags sollten im Format „X.Y.Z“ angegeben werden. „X“, „Y“ und „Z“ sind dabei ganze Zahlen. Stellen Sie sicher, dass die neue Version, die Sie bereitstellen, größer als alle früheren Versionen ist.
6. Wählen Sie **Veröffentlichen** aus, um den Workflow zum Veröffentlichen Ihrer neuen Containerimageversion im Azure Marketplace zu starten.

### <a name="add-a-new-sku"></a>Hinzufügen einer neuen SKU

Führen Sie die folgenden Schritte aus, um eine neue SKU für Ihr Angebot zur Verfügung zu stellen:

1. Melden Sie sich beim [Cloud-Partnerportal](https://cloudpartner.azure.com/) an.
2. Suchen Sie unter **Alle Angebote** nach dem Angebot, das Sie aktualisieren möchten.
3. Wählen Sie auf der Registerkarte **SKUs** die Option **Neue SKU hinzufügen** aus, und geben Sie im Popupfenster eine **SKU-ID** an.
4. Veröffentlichen Sie den Container gemäß den Schritten unter [Publish container offer](./cpp-publish-offer.md) (Veröffentlichen eines Containerangebots) erneut.
5. Wählen Sie **Veröffentlichen** aus, um den Workflow zum Veröffentlichen Ihrer neuen SKU zu starten.

### <a name="update-offer-marketplace-metadata"></a>Aktualisieren der Marketplace-Metadaten eines Angebots

Führen Sie die folgenden Schritte aus, um die mit Ihrem Angebot verknüpften Marketplace-Metadaten zu aktualisieren. (Beispiel: Firmenname, Logos usw.)

1. Melden Sie sich beim [Cloud-Partnerportal](https://cloudpartner.azure.com/) an.
2. Suchen Sie unter **Alle Angebote** nach dem Angebot, das aktualisiert werden soll.
3. Wechseln Sie zur Registerkarte **Marketplace**. Gehen Sie anhand der Anweisungen im Artikel [Publish container offer](./cpp-publish-offer.md) (Veröffentlichen eines Containerangebots) vor, um Änderungen an den Metadaten vorzunehmen.
4. Wählen Sie **Veröffentlichen** aus, um den Workflow zum Veröffentlichen Ihrer Änderungen zu starten.

## <a name="compare-feature"></a>Vergleichsfunktion

Wenn Sie Änderungen an einem veröffentlichten Angebot vornehmen, können Sie die Funktion **Vergleichen** verwenden, um die vorgenommenen Änderungen zu prüfen.

### <a name="to-use-the-compare-feature"></a>So verwenden Sie die Funktion „Vergleichen“

1. Wählen Sie zu einem beliebigen Zeitpunkt im Bearbeitungsprozess „Vergleichen“ für Ihr Angebot aus.
2. Sehen Sie sich die Versionen der Marketingressourcen und Metadaten nebeneinander an.


## <a name="history-of-publishing-actions"></a>Verlauf der Veröffentlichungsaktionen

Um bereits ausgeführte Veröffentlichungsaktivitäten anzuzeigen, wählen Sie im Cloud-Partnerportal auf der linken Navigationsmenüleiste die Registerkarte **Verlauf** aus. Sie sehen mit Zeitstempeln versehene Aktionen, die während der Lebensdauer Ihrer Azure Marketplace-Angebote ausgeführt wurden.