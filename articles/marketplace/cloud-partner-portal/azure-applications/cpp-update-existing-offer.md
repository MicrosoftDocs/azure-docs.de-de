---
title: Aktualisieren eines vorhandenen Azure-Anwendungsangebots | Azure Marketplace
description: Aktualisieren eines vorhandenen Azure-Anwendungsangebots im Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: pabutler
ms.openlocfilehash: a36df757e3a2682af641101ed82583a0cd293e0a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64942821"
---
# <a name="update-an-existing-azure-application-offer"></a>Aktualisieren eines vorhandenen Azure-Anwendungsangebots

Es gibt verschiedene Arten von Aktualisierungen, die Sie möglicherweise an Ihrem Angebot vornehmen möchten, nachdem es veröffentlicht und freigeschaltet wurde. Jede Änderung, die Sie an der neuen Version Ihres Angebots vorgenommen haben, sollte gespeichert und veröffentlicht werden, damit sie im Marketplace wirksam wird. In diesem Artikel werden die Schritte zum Aktualisieren Ihrer verwalteten Anwendung im [Cloud-Partnerportal](https://cloudpartner.azure.com/) erläutert.

Sie könnten Ihr Angebot beispielsweise aus den folgenden Gründen aktualisieren:

- Hinzufügen einer neuen Imageversion zu vorhandenen SKUs
- Hinzufügen von neuen SKUs
- Aktualisieren von Marketplace-Metadaten für das Angebot oder einzelne SKUs

Um Ihnen bei diesen Änderungen zu helfen, bietet das Portal die Features **Vergleichen** und **Verlauf**.

## <a name="unpermitted-changes-to-an-azure-application-offer-or-sku"></a>Unzulässige Änderungen an einem Azure-Anwendungsangebot oder einer SKU

Es gibt Attribute eines Containerangebots oder einer SKU, die nicht geändert werden können, nachdem das Angebot im Azure Marketplace live geschaltet wurde. Sie können die folgenden Einstellungen nicht ändern:

- Angebots-ID und Herausgeber-ID des Angebots
- SKU-ID von vorhandenen SKUs
- Versionstags, z.B.: `1.0.1`
- Abrechnungs-/Lizenzmodelländerungen an vorhandenen SKUs

## <a name="common-update-operations"></a>Gängige Aktualisierungsvorgänge

Die folgenden Aktualisierungsvorgänge werden häufig durchgeführt.

### <a name="update-image-version-for-a-sku"></a>Aktualisieren der Imageversion für eine SKU

Es ist üblich, dass ein Image regelmäßig mit Sicherheitspatches, zusätzlichen Features usw. aktualisiert wird. In diesem Szenario aktualisieren Sie mithilfe der folgenden Schritte das Image, das Ihre SKU referenziert:

1. Melden Sie sich beim [Cloud-Partnerportal](https://cloudpartner.azure.com/) an.
2. Suchen Sie unter **Alle Angebote** nach dem Angebot, das Sie aktualisieren möchten.
3. Wählen Sie auf der Registerkarte **SKUs** die SKU aus, die mit dem zu aktualisierenden Image verknüpft ist.
4. Wählen Sie **+ Neue Imageversion** aus, um ein neues Image hinzuzufügen.
5. Geben Sie die neuen Imageversionen an. Für die Imageversion müssen die gleichen Tagrichtlinien wie für frühere Versionen gelten. Versionstags sollten im Format „X.Y.Z“ angegeben werden. „X“, „Y“ und „Z“ sind dabei ganze Zahlen. Stellen Sie sicher, dass die neue Version, die Sie bereitstellen, größer als alle früheren Versionen ist.
6. Wählen Sie **Veröffentlichen** aus, um den Workflow zum Veröffentlichen Ihrer neuen Containerimageversion im Azure Marketplace zu starten.

### <a name="add-a-new-sku"></a>Hinzufügen einer neuen SKU

Führen Sie die folgenden Schritte aus, um eine neue SKU für Ihr Angebot zur Verfügung zu stellen:

1. Melden Sie sich beim [Cloud-Partnerportal](https://cloudpartner.azure.com/) an.
2. Suchen Sie unter **Alle Angebote** nach dem Angebot, das Sie aktualisieren möchten.
3. Wählen Sie auf der Registerkarte **SKUs** die Option **Neue SKU hinzufügen** aus, und geben Sie im Popupfenster eine **SKU-ID** an.
4. Veröffentlichen Sie das Angebot durch Ausführen der Schritte, die unter [Veröffentlichen eines Azure-Anwendungsangebots](./cpp-publish-offer.md) beschrieben sind.
5. Wählen Sie **Veröffentlichen** aus, um den Workflow zum Veröffentlichen Ihrer neuen SKU zu starten.

### <a name="update-offer-marketplace-metadata"></a>Aktualisieren der Marketplace-Metadaten eines Angebots

Führen Sie die folgenden Schritte aus, um die mit Ihrem Angebot verknüpften Marketplace-Metadaten zu aktualisieren. (Beispiel: Firmenname, Logos usw.)

1. Melden Sie sich beim [Cloud-Partnerportal](https://cloudpartner.azure.com/) an.
2. Suchen Sie unter **Alle Angebote** nach dem Angebot, das aktualisiert werden soll.
3. Wechseln Sie zur Registerkarte **Marketplace**. Nehmen Sie entsprechend den Anweisungen unter [Veröffentlichen eines Azure-Anwendungsangebots](./cpp-publish-offer.md) Metadatenänderungen vor.
4. Wählen Sie **Veröffentlichen** aus, um den Workflow zum Veröffentlichen Ihrer Änderungen zu starten.
 
>[!Note]
>Die Nutzung des Cloud Solution Provider (CSP)-Partnerkanals ist jetzt verfügbar.  Unter [Cloud Solution Providers](../../cloud-solution-providers.md) finden Sie weitere Informationen zum Vermarkten Ihres Angebots über die Microsoft CSP-Partnerkanäle.

## <a name="deleting-an-existing-offer"></a>Löschen eines vorhandenen Angebots

Möglicherweise möchten Sie Ihr Angebot aus dem Marketplace entfernen. Löschen eines Angebots wirkt sich nicht auf den aktuelle Käufe dieses Angebots aus. Diese Kundenkäufe funktionieren weiterhin wie zuvor. Nach Abschluss des Löschvorgangs ist das Angebot jedoch nicht mehr für neue Käufe verfügbar.

Durch Beenden des Angebots wird der Dienst und/oder der Lizenzvertrag zwischen Ihnen und Ihren bestehenden Kunden beendet.
Eine Anleitung und Richtlinien zur Entfernung und Beendigung von Angeboten finden Sie im Microsoft Marketplace-Herausgebervertrag (siehe Abschnitt 7) und in den Teilnahmerichtlinien (siehe Abschnitt 6.2).

Weitere Informationen finden Sie unter [Löschen eines Angebots oder einer SKU im Azure Marketplace](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-managed-app-offer-delete).

## <a name="compare-feature"></a>Vergleichsfunktion

Wenn Sie Änderungen an einem veröffentlichten Angebot vornehmen, können Sie die Funktion „Vergleichen“ verwenden, um die vorgenommenen Änderungen zu prüfen.

So verwenden Sie die Funktion „Vergleichen“

1. Wählen Sie zu einem beliebigen Zeitpunkt im Bearbeitungsprozess „Vergleichen“ für Ihr Angebot aus.
2. Sehen Sie sich die Versionen der Marketingressourcen und Metadaten nebeneinander an.

## <a name="history-of-publishing-actions"></a>Verlauf der Veröffentlichungsaktionen

Um bereits ausgeführte Veröffentlichungsaktivitäten anzuzeigen, wählen Sie im Cloud-Partnerportal auf der linken Navigationsmenüleiste die Registerkarte **Verlauf** aus. Sie sehen mit Zeitstempeln versehene Aktionen, die während der Lebensdauer Ihrer Azure Marketplace-Angebote ausgeführt wurden.

## <a name="next-steps"></a>Nächste Schritte

[Azure-Anwendungsangebot](./cpp-azure-app-offer.md)