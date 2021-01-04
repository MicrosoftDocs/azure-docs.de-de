---
title: Erstellen eines Beratungsdienstangebots im kommerziellen Microsoft-Marketplace
description: In diesem Artikel erfahren Sie, wie Sie ein neues Angebot für Beratungsdienste für Microsoft AppSource oder den Azure Marketplace mithilfe des kommerziellen Marketplace-Programms in Microsoft Partner Center erstellen.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 10/27/2020
ms.openlocfilehash: e9a0b2fe883fa46010fda74c58908128d05919e6
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92754222"
---
# <a name="how-to-create-a-consulting-service-offer-in-the-commercial-marketplace"></a>Erstellen eines Angebots für einen Beratungsdienst im kommerziellen Marketplace

In diesem Artikel wird erläutert, wie Sie ein Angebot für einen Beratungsdienst für den kommerziellen Microsoft-Marketplace mithilfe von Partner Center erstellen. 

## <a name="before-you-begin"></a>Voraussetzungen

Zum Veröffentlichen eines Beratungsdienstangebots müssen Sie gewisse Berechtigungsvoraussetzungen erfüllen, die Ihre Kenntnisse in Ihrem Feld belegen. Lesen Sie den Artikel [Planen eines Beratungsdienstangebots für den kommerziellen Marketplace](./plan-consulting-service-offer.md), wenn Sie dies noch nicht getan haben. Darin werden die Voraussetzungen für einen Beratungsdienst und die Ressourcen beschrieben, die Sie zum Erstellen des Angebots mit Partner Center benötigen.

## <a name="create-a-new-consulting-service-offer"></a>Erstellen eines neuen Beratungsdienstangebots

1. Melden Sie sich bei [Partner Center](https://partner.microsoft.com/dashboard/home) an.
2.  Wählen Sie im linken Navigationsmenü **Kommerzieller Marketplace** > **Übersicht** aus.
3.  Klicken Sie auf der Registerkarte „Übersicht“ auf **+ Neues Angebot** > **Beratungsdienst**.

    ![Darstellung des Navigationsmenüs auf der linken Seite.](./media/new-offer-consulting-service.png)

4. Geben Sie im Dialogfeld **Neues Angebot** eine **Angebots-ID** ein. Diese ID ist in der URL des kommerziellen Marketplace-Angebots sichtbar. Wenn Sie in diesem Feld z. B. test-offer-1 eingeben, lautet die Webadresse für das Angebot `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.

    * Jedes Angebot in Ihrem Konto muss über eine eindeutige Angebots-ID verfügen.
    * Verwenden Sie nur Kleinbuchstaben und Zahlen. Die Angebots-ID kann Bindestriche und Unterstriche enthalten, jedoch keine Leerzeichen, und ist auf 50 Zeichen beschränkt.
    * Nachdem Sie **Erstellen** ausgewählt haben, kann die Angebots-ID nicht mehr geändert werden.

5. Geben Sie einen **Angebotsalias** ein. Dies ist der Name, der für das Angebot im Partner Center verwendet wird. Er wird nicht in den Onlinegeschäften angezeigt und unterscheidet sich vom Angebotsnamen, der Kunden angezeigt wird.
6. Wählen Sie **Erstellen** aus, um das Angebot zu generieren und fortzufahren.

## <a name="configure-lead-management"></a>Konfigurieren der Leadverwaltung

Verbinden Sie Ihr CRM-System (Customer Relationship Management) mit Ihrem Angebot im kommerziellen Marketplace, damit Sie Kundenkontaktinformationen erhalten, wenn ein Kunde Interesse an Ihrem Beratungsdienst äußert. Sie können diese Verbindung während oder nach der Erstellung des Angebots jederzeit ändern. Eine detaillierte Anleitung finden Sie unter [Kundenleads aus Ihrem Angebot im kommerziellen Marketplace](./partner-center-portal/commercial-marketplace-get-customer-leads.md).

So konfigurieren Sie die Leadverwaltung in Partner Center:

1.  Navigieren Sie in Partner Center zur Registerkarte **Angebotseinrichtung**.
2.  Wählen Sie unter **Kundenleads** den Link **Verbinden** aus.
3.  Wählen Sie im Dialogfeld **Verbindungsdetails** eine Leadzielgruppe aus der Liste aus.
4.  Füllen Sie alle angezeigten Felder aus. Ausführliche Schritte finden Sie in den folgenden Artikeln:

    * [Konfigurieren des Angebots zum Senden von Leads an die Azure-Tabelle](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
    * [Konfigurieren Ihres Angebots zum Senden von Leads an Dynamics 365 Customer Engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (früher Dynamics CRM Online)
    * [Konfigurieren Ihres Angebots zum Senden von Leads an den HTTPS-Endpunkt](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
    * [Konfigurieren Ihres Angebots zum Senden von Leads zu Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
    * [Konfigurieren Ihres Angebots zum Senden von Leads zu Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

5.  Klicken Sie auf den Link **Überprüfen** , um die angegebene Konfiguration zu überprüfen.
6.  Wenn Sie die Verbindungsdetails konfiguriert haben, klicken Sie auf **Verbinden**.
7.  Wähen Sie **Entwurf speichern** aus.

Nachdem Sie Ihr Angebot zur Veröffentlichung in Partner Center eingereicht haben, wird die Verbindung überprüft und Ihnen wird ein Testlead gesendet. Testen Sie die Leadverbindung, während sich Ihr Angebot vor der Liveschaltung in der Vorschauphase befindet, indem Sie selbst versuchen, das Angebot in der Vorschauumgebung zu kaufen.

> [!TIP]
> Achten Sie darauf, dass die Verbindung mit dem Leadziel immer auf dem neuesten Stand ist, damit keine Leads verloren gehen.

## <a name="next-steps"></a>Nächste Schritte

* [Konfigurieren der Eigenschaften eines Beratungsdienstangebots](./create-consulting-service-offer-properties.md)