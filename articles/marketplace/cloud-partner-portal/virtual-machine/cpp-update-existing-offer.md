---
title: Aktualisieren eines vorhandenen VM-Angebots im Azure Marketplace
description: In diesem Artikel wird erläutert, wie Sie ein vorhandenes VM-Angebot im Azure Marketplace aktualisieren.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 08/27/2018
ms.author: Ankit.Sud
ms.openlocfilehash: 4a75d706d55512201786b2b74376047ff75380a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938120"
---
# <a name="update-an-existing-vm-offer-on-azure-marketplace"></a>Aktualisieren eines vorhandenen VM-Angebots im Azure Marketplace

In diesem Artikel werden die verschiedenen Aspekte beim Aktualisieren Ihres VM-Angebots im [Cloud-Partnerportal](https://cloudpartner.azure.com/) und beim anschließenden erneuten Veröffentlichen des Angebots erläutert. 

Es kann eine Reihe von Gründen dafür geben, dass Sie Ihr Angebot aktualisieren möchten, beispielweise folgende:

-  Hinzufügen einer neuen VM-Imageversion zu vorhandenen SKUs
-  Ändern der Regionen, in denen eine SKU verfügbar ist
-  Hinzufügen neuer SKUs
-  Aktualisieren der Marketplace-Metadaten für das Angebot oder einzelne SKUs
-  Aktualisieren der Preise für Angebote mit nutzungsbasierter Bezahlung

Um Ihnen bei diesen Änderungen zu helfen, bietet das Portal die Features **Vergleichen** und **Verlauf**.  

>[!Note]
>Die Nutzung des Cloud Solution Provider (CSP)-Partnerkanals ist jetzt verfügbar.  Unter [Cloud Solution Providers](../../cloud-solution-providers.md) finden Sie weitere Informationen zum Vermarkten Ihres Angebots über die Microsoft CSP-Partnerkanäle.

## <a name="unpermitted-changes-to-vm-offer-or-sku"></a>Nicht zugelassene Änderungen an VM-Angeboten oder -SKUs

Einige Attribute von VM-Angeboten oder -SKUs können nicht geändert werden, sobald das Angebot im Azure Marketplace freigeschaltet ist. Dabei handelt es sich hauptsächlich um die folgenden Attribute:

-  **Angebots-ID** und **Herausgeber-ID** des Angebots
-  **SKU-ID** von vorhandenen SKUs
-  Anzahl von Datenträgern vorhandener SKUs
-  Abrechnungs-/Lizenzmodelländerungen an vorhandenen SKUs
-  Preiserhöhungen für eine veröffentlichte SKU


## <a name="common-update-operations"></a>Gängige Aktualisierungsvorgänge

Es gibt zwar eine Vielzahl von Merkmalen, die Sie in einem VM-Angebot ändern können, aber die folgenden Vorgänge kommen häufig vor.

### <a name="update-the-vm-image-version-for-a-sku"></a>Aktualisieren der VM-Imageversion für eine SKU

Es ist üblich, dass ein VM-Image regelmäßig mit Sicherheitspatches, zusätzlichen Features usw. aktualisiert wird.  In solchen Szenarien sollten Sie mithilfe der folgenden Schritte das VM-Image aktualisieren, das Ihre SKU referenziert:

1.  Melden Sie sich beim [Cloud-Partnerportal](https://cloudpartner.azure.com/) an.

2.  Suchen Sie unter **Alle Angebote** nach dem Angebot, das Sie aktualisieren möchten.

3.  Klicken Sie auf der Registerkarte **SKUs** auf die SKU, die mit dem zu aktualisierenden VM-Image verknüpft ist.

4.  Klicken Sie unter **Datenträgerversion** auf **+Neue Datenträgerversion**, um ein neues VM-Image hinzuzufügen.

5.  Geben Sie die **Datenträgerversion** der neuen VM-Images an. Die Datenträgerversion muss dem Format der [semantischen Version](https://semver.org/) entsprechen. Versionen sollten im Format „X.Y.Z“ angegeben werden. „X“, „Y“ und „Z“ sind dabei ganze Zahlen. Überprüfen Sie, ob die neue Version, die Sie bereitstellen möchten, höher ist als alle vorherigen Versionen. Andernfalls wird die neue Version nach dem erneuten Veröffentlichen weder im Portal noch im Azure Marketplace angezeigt.

6.  Geben Sie als **URL der Betriebssystem-VHD** den [Shared Access Signature-URI](./cpp-get-sas-uri.md) (SAS-URI) ein, der für die Betriebssystem-VHD erstellt wurde. 

    > [!WARNING] 
    > Die Anzahl von Datenträgern für Daten kann zwischen den verschiedenen Versionen der SKU nicht geändert werden. Wenn in vorherigen Versionen Datenträger für Daten konfiguriert wurden, muss diese neue Version die gleiche Anzahl von Datenträgern aufweisen.

7.  Klicken Sie auf **Veröffentlichen**, um den Workflow zum Veröffentlichen Ihrer neuen VM-Version im Azure Marketplace zu starten.


### <a name="change-region-availability-of-a-sku"></a>Ändern der regionalen Verfügbarkeit einer SKU

Im Laufe der Zeit sollten Sie Ihr Angebot bzw. Ihre SKU in weiteren Regionen zur Verfügung stellen.  Alternativ können Sie die Unterstützung für das Angebot bzw. die SKU in einer bestimmten Region beenden.
Führen Sie die folgenden Schritte aus, um die Verfügbarkeit zu ändern:

1.  Melden Sie sich beim [Cloud-Partnerportal](https://cloudpartner.azure.com/) an.

2.  Suchen Sie unter **Alle Angebote** nach dem Angebot, das aktualisiert werden soll.

3.  Klicken Sie auf der Registerkarte **SKUs** auf die SKU, deren Verfügbarkeit Sie ändern möchten.

4.  Klicken Sie im Feld **Verfügbare Länder/Regionen** auf die Schaltfläche **Länder auswählen**.

5.  Fügen Sie im Popupelement mit den verfügbaren Regionen die Regionen für diese SKU hinzu, oder entfernen Sie sie.

6.  Klicken Sie auf **Veröffentlichen**, um den Veröffentlichungsworkflow zu starten und die verfügbaren Regionen für Ihre SKUs zu aktualisieren.

Wenn eine SKU in einer neuen Region zur Verfügung gestellt wird, haben Sie die Möglichkeit, mithilfe der Funktion **Preisdaten exportieren** die Preise für diese bestimmte Region anzugeben. Wenn Sie eine Region erneut hinzufügen, die bereits zuvor einmal verfügbar war, können Sie die Preise in dieser Region nicht aktualisieren, da Preisänderungen nicht zulässig sind.


### <a name="add-a-new-sku"></a>Hinzufügen einer neuen SKU

Führen Sie die folgenden Schritte aus, um eine neue SKU für Ihr vorhandenes Angebot zur Verfügung zu stellen: 

1.  Melden Sie sich beim [Cloud-Partnerportal](https://cloudpartner.azure.com/) an.

2.  Suchen Sie unter **Alle Angebote** nach dem Angebot, das aktualisiert werden soll.

3.  Klicken Sie auf der Registerkarte **SKUs** auf **Neue SKU hinzufügen**, und geben Sie im Popupelement eine **SKU-ID** an.

4.  Veröffentlichen Sie die VM erneut, wie im Artikel [Veröffentlichen eines virtuellen Computers in Azure Marketplace](./cpp-publish-offer.md) beschrieben.

5.  Klicken Sie auf **Veröffentlichen**, um den Workflow zum Veröffentlichen Ihrer neuen SKU zu starten.


### <a name="update-offer-marketplace-metadata"></a>Aktualisieren der Marketplace-Metadaten eines Angebots

Führen Sie die folgenden Schritte aus, um die mit Ihrem Angebot verknüpften Marketplace-Metadaten – Firmenname, Logos usw. – zu aktualisieren: 

1.  Melden Sie sich beim [Cloud-Partnerportal](https://cloudpartner.azure.com/) an.

2.  Suchen Sie unter **Alle Angebote** nach dem Angebot, das aktualisiert werden soll.

3.  Wechseln Sie zur Registerkarte **Marketplace**, und befolgen Sie dann die Anweisungen im Artikel [Veröffentlichen eines virtuellen Computers in Azure Marketplace](./cpp-publish-offer.md), um die Metadatenänderungen anzuwenden.

4.  Klicken Sie auf **Veröffentlichen**, um den Workflow zum Veröffentlichen Ihrer Änderungen zu starten.


### <a name="update-pricing-on-published-offers"></a>Aktualisieren der Preise für veröffentlichte Angebote

Nachdem Ihr Angebot mit nutzungsbasierter Bezahlung veröffentlicht wurde, können Sie die SKU-Preise nicht direkt erhöhen.  (Sie können jedoch eine neue SKU im gleichen Angebot erstellen, die alte SKU löschen und dann Ihr Angebot für neue Kunden erneut veröffentlichen.)  Im Gegensatz dazu können Sie den Preis eines veröffentlichten Angebots mithilfe der folgenden Schritte senken:

1.  Melden Sie sich beim [Cloud-Partnerportal](https://cloudpartner.azure.com/) an.

2.  Suchen Sie unter **Alle Angebote** nach dem Angebot, das Sie aktualisieren möchten.

3.  Klicken Sie auf die SKU, für die Sie die Preise verringern möchten.

4.  Wenn Sie die Preise in der 1x1-GUI festgelegt haben, können Sie sie direkt in der Benutzeroberfläche ändern. Wenn Sie die Preise per Import/Export einer Kalkulationstabelle festgelegt haben, können Sie die Preise nur über das Import/Export-Feature senken.

3.  Klicken Sie auf **Speichern**.

4.  Klicken Sie auf **Veröffentlichen**, um den Workflow zum Veröffentlichen Ihrer Änderungen zu starten.

Die neuen reduzierten Preise werden für neue Kunden angezeigt, sobald das Angebot auf der Website live geschaltet wurde.  Diese neuen Preise werden wie folgt für die Kunden angewendet:

- Neuen Kunden wird dieser neue Preis berechnet. 
- Für Bestandskunden wird die Preissenkung rückwirkend ab dem Anfang des Abrechnungszyklus berücksichtigt, in dem die Preissenkung wirksam wurde.
Bestandskunden, für die bereits in dem Zyklus abgerechnet wurde, in dem eine Preissenkung stattgefunden hat, erhalten während ihres nächsten Abrechnungszyklus eine Rückerstattung, die dem niedrigeren Preis entspricht.


<!-- TD: This has been implemented, need to change the SKU Tab topic to reflect and move this section there. -->
### <a name="simplified-currency-pricing"></a>Vereinfachte Währungspreise

Ab dem 1. September 2018 steht im Portal ein neuer Abschnitt mit dem Namen **Vereinfachte Währungspreise** zur Verfügung. Microsoft optimiert das Azure Marketplace-Geschäft dadurch, dass besser prognostizierbare Preise und Auflistungen von Ihren Kunden auf der ganzen Welt ermöglicht werden. Zu dieser Optimierung gehört auch, dass weniger Währungen verwendet werden, in denen Microsoft Ihren Kunden Rechnungen stellt.

Für den neuen Abschnitt werden die Preise in diesen neuen Währungen gestaltet. Sobald alle Kunden zu diesen neuen Abrechnungswährungen migriert wurden, wird der ursprüngliche Preisabschnitt entfernt, und es wird nur noch der Abschnitt mit den vereinfachten Währungspreisen vorhanden sein.

Sie haben bis zum 1. November 2018 Zeit, neue Preise für die Regionen festzulegen, für die sich die Abrechnungswährung ändert. Für Regionen, für die sich die Abrechnungswährung nicht ändert, können Sie die Preise nicht erhöhen.

> [!NOTE] 
> Wenn Sie APIs verwenden, um Ihr Angebot zu veröffentlichen, wird in der JSON-Angebotsdatei möglicherweise ein neuer Abschnitt angezeigt. Dieser wird mit `virtualMachinePricingV2` oder `monthlyPricingV2` kommentiert, je nach dem Typ des Angebots. 

Wenn Sie Fragen zu dieser Änderung haben, wenden Sie sich an den [Azure Marketplace-Support](../../support-azure-marketplace.md).


## <a name="compare-feature"></a>Vergleichen-Funktion

Wenn Sie Änderungen an einem bereits veröffentlichten Angebot vorgenommen haben, können Sie das **Vergleichen**-Feature verwenden, um die vorgenommenen Änderungen zu prüfen. So verwenden Sie dieses Feature:

1.  Sie können zu jedem Zeitpunkt des Bearbeitungsprozesses auf die Schaltfläche **Vergleichen** für Ihr Angebot klicken.

    ![Schaltfläche des Features „Vergleichen“](./media/publishvm_037.png)


2.  Zeigen Sie nebeneinander Versionen von Marketingressourcen und Metadaten an.


## <a name="history-of-publishing-actions"></a>Verlauf der Veröffentlichungsaktionen

Um bereits ausgeführte Veröffentlichungsaktivitäten anzuzeigen, klicken Sie im Cloud-Partnerportal in der linken Navigationsmenüleiste auf das Element **Verlauf**. Auf dieser Registerkarte werden mit Zeitstempeln versehene Aktionen angezeigt, die während der Lebensdauer Ihrer Azure Marketplace-Angebote ausgeführt wurden.  
<!-- TD: Add after section authored: For more information, see [History page](../portal-tour/cpp-history-page.md). -->

