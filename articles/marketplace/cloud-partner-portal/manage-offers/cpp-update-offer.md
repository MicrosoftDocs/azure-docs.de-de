---
title: Aktualisieren von Marketplace-Angeboten | Azure Marketplace
description: Aktualisieren von Angeboten in Azure Marketplace und AppSource über das Cloud-Partnerportal
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: pabutler
ms.openlocfilehash: 73e2812c678dca7e21089ee9cc091db756d7e25a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64942342"
---
# <a name="update-azure-marketplace-and-appsource-offers"></a>Aktualisieren von Azure Marketplace- und AppSource-Angeboten

Es gibt verschiedene Arten von Aktualisierungen, die Sie auf Ihr Angebot anwenden können, nachdem es veröffentlicht wurde.  Das [Cloud-Partnerportal](https://cloudpartner.azure.com/) unterstützt Sie bei der korrekten Änderung der Attribute eines Angebots, einschließlich:

-  Hinzufügen einer neuen VM-Image- oder Paketversion zu einer vorhandenen SKU
-  Ändern von Regionen, in denen eine SKU verfügbar ist
-  Hinzufügen von neuen SKUs
-  Aktualisieren von Marketplace-Metadaten für Angebote oder SKUs 
-  Aktualisieren von Preisen für Angebote mit nutzungsbasierter Bezahlung

Das Portal verfügt auch über Features, die Ihnen bei der Verwaltung von Änderungen helfen, wie z. B. die Möglichkeit, Features zu vergleichen und einen Featureverlauf für ein Angebot anzuzeigen.  Nachdem Sie ein Angebot oder eine SKU geändert haben, muss es oder sie erneut veröffentlicht werden, bevor die Änderungen freigeschaltet werden.  In diesem Artikel werden die verschiedenen Aspekte zur Aktualisierung Ihres Marketplace-Angebots beleuchtet.

## <a name="unpermitted-changes-to-an-offersku"></a>Nicht zugelassene Änderungen an Angeboten/-SKUs

Einige Attribute von Angeboten oder SKUs können nicht geändert werden, nachdem das Angebot bzw. die SKU im Marketplace freigeschaltet wurde.  Die entsprechenden Felder sind auf der Registerkarte **Editor** des Portals deaktiviert, z. B.:  

- Angebots-ID und Herausgeber-ID
- SKU-ID 
- Anzahl von Datenträgern vorhandener SKUs
- Abrechnungs-/Lizenzmodelländerungen an vorhandenen SKUs
- Versionstags, z.B.: `1.0.1`


## <a name="common-update-operations"></a>Gängige Aktualisierungsvorgänge

In den folgenden Abschnitten wird erläutert, wie Sie einige der häufigsten Aktualisierungsvorgänge durchführen können.  Diese Vorgänge sind nicht für alle Angebotstypen verfügbar.  Sie müssen sich beim Cloud-Partnerportal anmelden, um diese Vorgänge zu starten.


### <a name="update-offer-contacts"></a>Aktualisieren von Angebotskontakten

Gehen Sie wie folgt vor, um die Supportkontakte Ihres Angebots zu aktualisieren.
1. Wählen Sie Ihr Angebot auf der Seite **Alle Angebote** aus.
2. Wählen Sie die Registerkarte **Kontakte** aus. Aktualisieren Sie Ihre Kontakte.
3. Wählen Sie die Schaltfläche **Speichern** aus.
4. Klicken Sie auf **Veröffentlichen**, um den Veröffentlichungsprozess zu starten.


### <a name="change-regions-an-offer-or-sku-is-available-in"></a>Ändern von Regionen, in denen ein Angebot oder eine SKU verfügbar ist

Im Laufe der Zeit sollten Sie Ihr Angebot bzw. Ihre SKU in weiteren Regionen zur Verfügung stellen.
Alternativ können Sie die Unterstützung für das Angebot bzw. die SKU in einer bestimmten Region beenden.
Führen Sie die folgenden Schritte aus, um diese Änderungen zu implementieren.

1. Suchen Sie auf der Seite **Alle Angebote** nach dem Angebot, das Sie aktualisieren möchten.

Für Azure Marketplace-Angebote:

1. Wählen Sie die Registerkarte **SKUs** aus.  Wählen Sie die SKU aus, die Sie ändern möchten.
1. Klicken Sie unter dem Feld **Verfügbare Länder/Regionen** auf die Schaltfläche **Länder auswählen**.
1. Fügen Sie im Dialogfeld mit den verfügbaren Regionen die Regionen für diese SKU hinzu, oder entfernen Sie sie.

Für AppSource-Angebote:

1. Wählen Sie die Registerkarte **Storefrontdetails** aus.
1. Klicken Sie neben der Bezeichnung **Unterstützte Länder/Regionen** auf **Unterstützte Länder/Regionen**. 
1. Fügen Sie im Dialogfeld mit den unterstützten Länder/Regionen die Regionen für dieses Angebot hinzu, oder entfernen Sie sie.

Für beide Marketplaces:

1. Wählen Sie **Veröffentlichen** aus, um den Veröffentlichungsvorgang zu starten. 

Wenn eine SKU in einer neuen Region zur Verfügung gestellt wird, haben Sie die Möglichkeit, mithilfe der Funktion **Preisdaten exportieren** die Preise für diese bestimmte Region anzugeben. Wenn Sie eine Region erneut hinzufügen, die zuvor einmal verfügbar war, können Sie die Preise in dieser Region nicht aktualisieren, da Preisänderungen nicht zulässig sind.


### <a name="add-a-new-sku"></a>Hinzufügen einer neuen SKU 

Führen Sie die folgenden Schritte aus, um eine neue SKU für ein vorhandenes Angebot zur Verfügung zu stellen:

1. Suchen Sie Ihr Angebot auf der Seite **Alle Angebote**.
3. Klicken Sie im Formular **SKUs** auf **Neue SKU hinzufügen**, und geben Sie im Popupfenster eine **SKU-ID** an.
4. Führen Sie die restlichen Schritte aus, die unter [Veröffentlichen eines VM-Angebots](../virtual-machine/cpp-publish-offer.md) aufgeführt sind.
5. Wählen Sie **Veröffentlichen** aus, um den Veröffentlichungsvorgang zu starten.


### <a name="update-offer-marketplace-assets"></a>Aktualisieren der Marketplace-Objekte eines Angebots

Möglicherweise gibt es Szenarien, in denen Sie die text- und bildbasierten Objekte des Marketplaces aktualisieren müssen, z. B. Ihre Firmenlogos, die Angebotsbeschreibung usw. Verwenden Sie die folgenden Schritte, um diese Objekte zu aktualisieren.

1. Suchen Sie Ihr Angebot auf der Seite **Alle Angebote**. 
2. Wählen Sie die Registerkarte **Marketplace** aus, und folgen Sie den Anweisungen im Thema *Marketplace-Registerkarte* Ihres Angebots.
3. Wählen Sie **Veröffentlichen** aus, um den Veröffentlichungsvorgang zu starten.


### <a name="update-pricing-on-published-offers"></a>Aktualisieren der Preise für veröffentlichte Angebote

Nachdem Ihr Angebot mit nutzungsbasierter Bezahlung veröffentlicht wurde, können Sie die Preise einer vorhandenen SKU nicht erhöhen.  Sie können jedoch stattdessen eine SKU im gleichen Angebot erstellen, die alte SKU löschen und Ihr Angebot dann erneut veröffentlichen. Sie können den Preis für bereits veröffentlichte Angebote senken. So verringern Sie den Preis eines Angebots:

1. Wählen Sie die SKU aus, für die Sie die Preise verringern möchten.
2. Sie müssen den niedrigeren Preis durch den gleichen Mechanismus festlegen, den Sie ursprünglich verwendet haben: entweder direkt in der Benutzeroberfläche des Portals oder über die Import/Export-Tabelle.
3. Klicken Sie auf **Speichern**.
4. Wählen Sie **Veröffentlichen** aus, um den Veröffentlichungsvorgang zu starten.

Die Preise sind für neue Kunden sichtbar, sobald sie im Marketplace vollständig freigeschaltet sind, und alle neuen Kunden zahlen dann den neuen niedrigeren Preis.  Für Bestandskunden wird die Preissenkung rückwirkend ab dem Anfang des Abrechnungszyklus berücksichtigt, in dem die Preissenkung wirksam wurde.  Bestandskunden, für die bereits in dem Zyklus abgerechnet wurde, in dem eine Preissenkung stattgefunden hat, erhalten während ihres nächsten Abrechnungszyklus eine Rückerstattung, die dem niedrigeren Preis entspricht.


## <a name="compare-feature"></a>Vergleichsfunktion

Wenn Sie Änderungen an einem veröffentlichten Angebot vornehmen, können Sie die Funktion *Vergleichen* verwenden, um die Änderungen zu prüfen. So verwenden Sie dieses Feature:

1. Zu jedem Zeitpunkt des Bearbeitungsvorgangs können Sie für Ihr Angebot auf der Registerkarte **Editor** auf die Schaltfläche **Vergleichen** klicken.
2. In einem Vergleichsfenster werden die gespeicherten Änderungen an diesem Angebot im Vergleich zum Marketplace-Angebot nebeneinander angezeigt. 

![Schaltfläche „Angebot vergleichen“ auf der Registerkarte „Editor“](./media/offer-compare-button.png)


## <a name="history-of-publishing-actions"></a>Verlauf der Veröffentlichungsaktionen

Um bereits ausgeführte Veröffentlichungsaktivitäten anzuzeigen, wählen Sie im Cloud-Partnerportal auf der linken vertikalen Menüleiste die Registerkarte **Verlauf** aus.  Die Seite „Verlauf“ ermöglicht eine flexible Filterung nach mehreren Merkmalen und unterstützt Spaltensortierung.  Jedes Veröffentlichungsereignis ist mit einem Zeitstempel versehen.  Weitere Informationen finden Sie unter [Seite „Überwachungsverlauf“](../portal-tour/cpp-history-page.md).


## <a name="next-steps"></a>Nächste Schritte

Sie können auch das Cloud-Partnerportal nutzen, um [veröffentlichte SKUs oder Angebote](./cpp-delete-offer.md) zu löschen.
