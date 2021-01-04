---
title: Leadverwaltung mit Azure Blob Storage – kommerzieller Microsoft-Marketplace
description: Erfahren Sie, wie Sie Azure Blob zum Konfigurieren von Leads für Microsoft AppSource und Azure Marketplace verwenden.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 05/01/2020
ms.openlocfilehash: bd2f3d40b1aea1c0133f95e069ebfd527d30bd59
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491102"
---
# <a name="use-azure-blob-storage-to-manage-commercial-marketplace-leads"></a>Verwenden von Azure Blob Storage zum Verwalten von Leads im kommerziellen Marketplace

>[!Caution]
>Die Unterstützung des kommerziellen Marketplace für Azure Blob Storage wurde eingestellt. Dies stellt keine Möglichkeit zum Verarbeiten von Leads aus Ihrem Angebot mehr dar. Wenn Sie derzeit über ein Angebot im kommerziellen Marketplace verfügen, das mit der Leadverwaltung für Azure Blob konfiguriert ist, erhalten Sie keine Kundenleads mehr. Aktualisieren Sie die Leadverwaltungskonfiguration, und legen Sie eine andere Leadverwaltungsoption fest. Weitere Informationen zu den anderen Optionen erhalten Sie auf der [Landing Page für die Leadverwaltung](./commercial-marketplace-get-customer-leads.md).

 Wenn Ihr CRM-System (Customer Relationship Management) im Partner Center nicht explizit für den Empfang von Microsoft AppSource- und Azure Marketplace-Leads unterstützt wird, können Sie Azure Blob Storage verwenden. Sie können dann die Daten exportieren und in Ihr CRM-System importieren. Die Anweisungen in diesem Artikel führen Sie durch die Schritte zum Erstellen eines Azure Storage-Kontos und eines Blobs unter diesem Konto. Außerdem können Sie mit Power Automate einen neuen Flow erstellen, um eine E-Mail-Benachrichtigung zu senden, wenn Ihr Angebot einen Lead erhält.

>[!NOTE]
>Der in diesen Anweisungen verwendete Power Automate-Connector erfordert ein kostenpflichtiges Power Automate-Abonnement. Berücksichtigen Sie dies, bevor Sie die Anweisungen in diesem Artikel befolgen.

## <a name="configure-azure-blob-storage"></a>Konfigurieren von Azure Blob Storage

1. Wenn Sie nicht über ein Azure-Konto verfügen, können Sie ein [kostenloses Testkonto erstellen](https://azure.microsoft.com/pricing/free-trial/).

2. Nachdem Ihr Azure-Konto aktiviert wurde, melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

3. Gehen Sie folgendermaßen vor, um im Azure-Portal ein Speicherkonto zu erstellen.  
    1. Wählen Sie im Menü auf der linken Seite **+ Ressource erstellen** aus.  Auf der rechten Seite wird der Bereich (das Blatt) **Neu** angezeigt.
    2. Wählen Sie im Bereich **Neu** die Option **Speicher** aus.  Auf der rechten Seite wird eine Liste **Empfohlen** angezeigt.
    3. Wählen Sie das **Speicherkonto** aus, um mit der Kontoerstellung zu beginnen.  Befolgen Sie die Anweisungen im Artikel [Erstellen eines Speicherkontos](../../storage/common/storage-account-create.md?tabs=azure-portal).

    ![Schritte zum Erstellen eines Azure Storage-Kontos](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-create.png)

    Weitere Informationen zu Speicherkonten finden Sie in diesem [Schnellstart](../../storage/blobs/storage-quickstart-blobs-portal.md).  Weitere Informationen zu den Preisen für Storage finden Sie unter [Preise für Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

4. Warten Sie, bis Ihr Speicherkonto bereitgestellt wird. Dieser Prozess nimmt in der Regel einige Minuten in Anspruch.  Greifen Sie über die **Homepage** des Azure-Portals auf Ihr Speicherkonto zu, indem Sie **All Ihre Ressourcen anzeigen** oder im linken Navigationsbereich des Azure-Portals **Alle Ressourcen** auswählen.

    ![Zugriff auf Ihr Azure-Speicherkonto](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-access.png)

5. Wählen Sie im Speicherkontobereich **Zugriffsschlüssel** aus, und kopieren Sie den Wert von *Verbindungszeichenfolge* für den Schlüssel. Speichern Sie diesen Wert, da dies der Wert von *Verbindungszeichenfolge für Speicherkonto* ist, den Sie im Veröffentlichungsportal angeben müssen, um Leads für Ihr Marketplace-Angebot zu erhalten.

     Beispiel für eine Verbindungszeichenfolge:

     ```sql
     DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
     ```

    ![Screenshot: Seite „Zugriffsschlüssel“ mit hervorgehobenem Textfeld „Verbindungszeichenfolge“](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-keys-2.png)

6. Klicken Sie auf Ihrer Speicherkontoseite auf **BLOBs**.

   ![Screenshot: Azure-Speicherkontoseite mit ausgewählter Option „Blobs“](./media/commercial-marketplace-lead-management-instructions-azure-blob/select-blobs.png)

7. Klicken Sie auf der BLOBs-Seite auf die Schaltfläche **+ Container**.

8. Geben Sie einen **Namen** für den neuen Container ein. Der Containername muss klein geschrieben werden, mit einem Buchstaben oder einer Zahl beginnen und darf nur Buchstaben, Zahlen und Bindestriche (-) enthalten. Weitere Informationen zu Container- und Blobnamen finden Sie unter [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Benennen von Containern, Blobs und Metadaten und Verweisen auf diese).

    Speichern Sie diesen Wert, da dies der *Containername* ist, den Sie im Veröffentlichungsportal angeben müssen, um Leads für Ihr Marketplace-Angebot zu erhalten.

9. Legen Sie die öffentliche Zugriffsebene für den Container auf **Privat (kein anonymer Zugriff)** fest.

10. Wählen Sie **OK** aus, um den Container zu erstellen.

    ![Neuer Container](./media/commercial-marketplace-lead-management-instructions-azure-blob/new-container.png)

## <a name="configure-your-offer-to-send-leads-to-azure-blob-storage"></a>Konfigurieren des Angebots zum Senden von Leads an Azure Blob Storage

Führen Sie die folgenden Schritte aus, um die Leadverwaltungsinformationen für Ihr Angebot im Veröffentlichungsportal zu konfigurieren:

1. Navigieren Sie zur Seite **Angebotseinrichtung** für Ihr Angebot.
2. Wählen Sie im Abschnitt **Kundenleads** die Option **Verbinden** aus.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-azure-blob/customer-leads.png" alt-text="Kundenleads":::

3. Wählen Sie im Popupfenster „Verbindungsdetails“ **Azure Blob** als Leadziel aus.

    ![Verbindungsdetails](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-details.png) 

4. Geben Sie **Containername** und **Verbindungszeichenfolge für Speicherkonto** an, die Sie durch Befolgen dieser Anweisungen erhalten haben.

    * Beispiel für Containername: `marketplaceleadcontainer`
    * Beispiel für Verbindungszeichenfolge für Speicherkonto: `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net` ![Verbindungsdetails](./media/commercial-marketplace-lead-management-instructions-azure-blob/connection-details.png) 

5. Wählen Sie **Speichern** aus.

    > [!NOTE]
    > Sie müssen die Konfiguration der übrigen Einstellungen des Angebots abschließen und veröffentlichen, damit Sie Leads für das Angebot erhalten.