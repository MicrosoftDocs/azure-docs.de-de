---
title: Erstellen eines Azure-Anwendungsangebots im kommerziellen Marketplace
description: Erfahren Sie, wie Sie ein neues Azure-Anwendungsangebot zum Auflisten oder Verkaufen im Azure Marketplace oder über das CSP-Programm (Cloud Solution Provider) über das Portal für den kommerziellen Marketplace in Microsoft Partner Center erstellen.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 3cc5e5114b435965eee4aa096e5898538b0a56e7
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369765"
---
# <a name="how-to-create-an-azure-application-offer-in-the-commercial-marketplace"></a>Erstellen eines Azure-Anwendungsangebots im kommerziellen Marketplace

Als Herausgeber im kommerziellen Marketplace können Sie ein Azure-Anwendungsangebot erstellen, damit potenzielle Kunden Ihre Lösung erwerben können. In diesem Artikel wird erläutert, wie Sie ein Azure-Anwendungsangebot für den kommerziellen Microsoft-Marketplace erstellen.

Lesen Sie den Artikel [Planen eines Azure-Anwendungsangebots für den kommerziellen Marketplace](plan-azure-application-offer.md), wenn Sie dies noch nicht getan haben. Er stellt Ressourcen bereit und unterstützt Sie bei der Erfassung der Informationen und Objekte, die Sie beim Erstellen Ihres Angebots benötigen.

## <a name="create-a-new-offer"></a>Erstellen eines neuen Angebots

1. Melden Sie sich bei [Partner Center](https://partner.microsoft.com/dashboard/home) an.

1. Wählen Sie im linken Navigationsmenü **Kommerzieller Marketplace** > **Übersicht** aus.

1. Klicken Sie auf der Seite „Übersicht“ auf **+ Neues Angebot** > **Azure-Anwendung**.

    ![Darstellung des Navigationsmenüs auf der linken Seite.](./media/create-new-azure-app-offer/new-offer-azure-app.png)

1. Geben Sie im Dialogfeld **Neues Angebot** eine **Angebots-ID** ein. Dies ist ein eindeutiger Bezeichner für jedes Angebot in Ihrem Konto. Diese ID ist in der URL der Auflistung im kommerziellen Marketplace und ggf. in den Azure Resource Manager-Vorlagen sichtbar. Wenn Sie in diesem Feld z. B. test-offer-1 eingeben, lautet die Webadresse für das Angebot `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.

     * Jedes Angebot in Ihrem Konto muss über eine eindeutige Angebots-ID verfügen.
     * Verwenden Sie nur Kleinbuchstaben und Zahlen. Sie kann Bindestriche und Unterstriche enthalten, jedoch keine Leerzeichen, und ist auf 50 Zeichen beschränkt.
     * Nachdem Sie **Erstellen** ausgewählt haben, kann die Angebots-ID nicht mehr geändert werden.

1. Geben Sie einen **Angebotsalias** ein. Dies ist der Name, der für das Angebot im Partner Center verwendet wird.

     * Dieser Name ist nur im Partner Center sichtbar und unterscheidet sich vom Angebotsnamen und anderen Werten, die den Kunden angezeigt werden.
     * Nachdem Sie **Erstellen** ausgewählt haben, kann der Angebotsalias nicht mehr geändert werden.

1. Wählen Sie **Erstellen** aus, um das Angebot zu generieren und fortzufahren.

## <a name="configure-your-azure-application-offer-setup-details"></a>Konfigurieren der Details der Einrichtung Ihres Azure-Anwendungsangebots

Wählen Sie auf der Registerkarte **Angebotseinrichtung** unter **Details zur Konfiguration** aus, ob Sie eine Testversion konfigurieren möchten. Außerdem verbinden Sie Ihr CRM-System (Customer Relationship Management, Kundenbeziehungsmanagement) mit Ihrem Angebot im kommerziellen Marketplace.

### <a name="enable-a-test-drive-optional"></a>Aktivieren einer Testversion (optional)

Eine Testversion ist eine hervorragende Möglichkeit, Ihr Angebot potenziellen Kunden zu präsentieren, indem Sie ihnen für eine festgelegte Anzahl von Stunden Zugang zu einer vorkonfigurierten Umgebung gewähren. Wenn Sie eine Testversion anbieten, können Sie die Konvertierungsrate erhöhen und hochqualifizierte Leads generieren. Weitere Informationen zu Testversionen finden Sie unter [Testversion](plan-azure-application-offer.md#test-drive).

#### <a name="to-enable-a-test-drive"></a>So aktivieren Sie eine Testversion

- Aktivieren Sie unter **Testversion** das Kontrollkästchen **Aktivieren einer Testversion**.

### <a name="customer-lead-management"></a>Kundenleadverwaltung

Verbinden Sie Ihr Customer Relationship Management (CRM)-System mit Ihrem Angebot im kommerziellen Marketplace, damit Sie Kundenkontaktinformationen erhalten, wenn ein Kunde Interesse bekundet oder Ihr Produkt einsetzt.

#### <a name="to-configure-the-connection-details-in-partner-center"></a>So konfigurieren Sie die Verbindungsdetails im Partner Center

1. Wählen Sie unter **Kundenleads** den Link **Verbinden** aus.
1. Wählen Sie im Dialogfeld **Verbindungsdetails** eine Leadzielgruppe aus der Liste aus.
1. Füllen Sie alle angezeigten Felder aus. Ausführliche Schritte finden Sie in den folgenden Artikeln:

   - [Konfigurieren des Angebots zum Senden von Leads an die Azure-Tabelle](partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [Konfigurieren Ihres Angebots zum Senden von Leads an Dynamics 365 Customer Engagement](partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (früher Dynamics CRM Online)
   - [Konfigurieren Ihres Angebots zum Senden von Leads an den HTTPS-Endpunkt](partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [Konfigurieren Ihres Angebots zum Senden von Leads zu Marketo](partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [Konfigurieren Ihres Angebots zum Senden von Leads zu Salesforce](partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. Klicken Sie ggf. auf den Link **Überprüfen**, um die angegebene Konfiguration zu überprüfen.
1. Wählen Sie **Verbinden** aus, um das Dialogfeld zu schließen.
1. Wählen Sie **Entwurf speichern** aus, bevor Sie mit der nächsten Registerkarte fortfahren: Eigenschaften

> [!NOTE]
> Stellen Sie sicher, dass die Verbindung mit dem Leadziel immer auf dem neuesten Stand ist, damit keine Leads verloren gehen. Aktualisieren Sie diese Verbindungen unbedingt, sobald sich etwas geändert hat.

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren der Angebotseigenschaften Ihrer Azure-Anwendung](create-new-azure-apps-offer-properties.md)
