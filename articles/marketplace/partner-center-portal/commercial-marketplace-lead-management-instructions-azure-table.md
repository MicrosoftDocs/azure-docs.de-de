---
title: Leadverwaltung mit Azure-Tabellenspeicher – kommerzieller Microsoft-Marketplace
description: Hier erfahren Sie, wie Sie Azure-Tabellenspeicher zum Konfigurieren von Leads für Microsoft AppSource und Azure Marketplace verwenden.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 08/25/2020
ms.openlocfilehash: 71b9c96c1855180106f7dfa9a31f0ee8b06ceb67
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97347862"
---
# <a name="use-azure-table-storage-to-manage-commercial-marketplace-leads"></a>Verwenden von Azure-Tabellenspeicher zum Verwalten von Leads im kommerziellen Marketplace

Wenn Ihr CRM-System (Customer Relationship Management) in Partner Center nicht explizit für den Empfang von Microsoft AppSource- und Azure Marketplace-Leads unterstützt wird, können Sie für die Verarbeitung dieser Leads einen Azure-Tabellenspeicher verwenden. Sie können dann die Daten exportieren und in Ihr CRM-System importieren. In diesem Artikel wird erläutert, wie Sie ein Azure Storage-Konto und eine Tabelle unter diesem Konto erstellen. Außerdem können Sie mit Power Automate einen neuen Flow erstellen, um eine E-Mail-Benachrichtigung zu senden, wenn Ihr Angebot einen Lead erhält.

## <a name="configure-an-azure-storage-account"></a>Konfigurieren eines Azure Storage-Kontos

1. Wenn Sie nicht über ein Azure-Konto verfügen, können Sie ein [kostenloses Testkonto erstellen](https://azure.microsoft.com/pricing/free-trial/).
1. Nachdem Ihr Azure-Konto aktiviert wurde, melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Gehen Sie folgendermaßen vor, um im Azure-Portal ein Speicherkonto zu erstellen:

    1. Wählen Sie im Menü auf der linken Seite **+ Ressource erstellen** aus. Der Bereich **Neu** wird rechts angezeigt.
    1. Wählen Sie im Bereich **Neu** die Option **Speicher** aus. Die Liste **Highlights** wird rechts angezeigt.
    1. Wählen Sie **Speicherkonto** aus, um mit der Kontoerstellung zu beginnen. Befolgen Sie die Anweisungen unter [Erstellen eines Speicherkontos](../../storage/common/storage-account-create.md?tabs=azure-portal).

        :::image type="content" source="media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-create.png" alt-text="Schritte zum Erstellen eines Azure Storage-Kontos":::.

        Weitere Informationen zu Speicherkonten finden Sie im [Schnellstarttutorial](../../storage/index.yml). Weitere Informationen zu den Preisen für Storage finden Sie unter [Übersicht über die Preise für Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

1. Warten Sie, bis das Speicherkonto bereitgestellt wurde. Dieser Vorgang dauert in der Regel einige Minuten.

## <a name="create-a-table-in-your-storage-account"></a>Erstellen einer Tabelle in Ihrem Speicherkonto

1. Wählen Sie auf der Seite **Home** des Azure-Portals **Alle Ihre Ressourcen anzeigen** aus, um auf Ihr Speicherkonto zuzugreifen. Sie können auch auf der linken Menüleiste des Azure-Portals **Alle Ressourcen** auswählen.

    :::image type="content" source="media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-access.png" alt-text="Zugriff auf Ihr Azure-Speicherkonto":::.

1. Wählen Sie im Speicherkontobereich **Zugriffsschlüssel** aus, und kopieren Sie den Wert von **Verbindungszeichenfolge** für den Schlüssel. Speichern Sie diesen Wert, da es sich um den Wert **Verbindungszeichenfolge für Speicherkonto** handelt, den Sie im Veröffentlichungsportal angeben müssen, um Leads für Ihr Azure Marketplace-Angebot zu erhalten.

    Hier ist ein Beispiel für eine Verbindungszeichenfolge.

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.screens.net
    ```

    :::image type="content" source="media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-keys.png" alt-text="Azure-Speicherschlüssel":::.


1. Wählen Sie im Speicherkontobereich **Tabellen** und dann **+ Tabelle** aus, um eine Tabelle zu erstellen. Geben Sie einen Namen für die Tabelle ein, und wählen Sie **OK** aus. Speichern Sie diesen Wert, da Sie ihn benötigen, wenn Sie einen Flow für den Empfang von E-Mail-Benachrichtigungen beim Erhalt von Leads konfigurieren möchten.

    ![Azure-Tabellen](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-tables.png)

    Sie können die Daten in Ihrer Speichertabelle im [Azure Storage-Explorer](https://www.storageexplorer.com) oder einem anderen Tool anzeigen. Sie können die Daten auch in die Azure-Tabelle exportieren.

## <a name="optional-use-power-automate-to-get-lead-notifications"></a>(Optional) Verwenden von Power Automate zum Abrufen von Leadbenachrichtigungen

Mit [Power Automate](/flow/) können Sie immer dann automatisch eine Benachrichtigung senden lassen, wenn einer Azure Storage-Tabelle ein Lead hinzugefügt wird. Wenn Sie kein Konto besitzen, können Sie sich [für ein kostenloses Konto registrieren](https://flow.microsoft.com/).

### <a name="lead-notification-example"></a>Beispiel zu Leadbenachrichtigung

In diesem Beispiel wird ein Flow erstellt, in dem automatisch eine E-Mail-Benachrichtigung gesendet wird, wenn ein neuer Lead dem Azure-Tabellenspeicher hinzugefügt wird. In diesem Beispiel wird eine Wiederholung eingerichtet, um stündlich Leadinformationen zu senden, wenn der Tabellenspeicher aktualisiert wird.

1. Melden Sie sich bei Ihrem Power Automate-Konto an.
1. Wählen Sie auf der linken Navigationsleiste die Option **Meine Flows** aus.
1. Wählen Sie auf der oberen Leiste **+ Neu** aus.
1. Wählen Sie in der Dropdownliste **+ Geplant – ohne Vorlage** aus.

   ![„+ Geplant – ohne Vorlage“ unter „Meine Flows“](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-scheduled-from-blank.png)

1. Wählen Sie im Fenster **Geplanten Flow erstellen** unter **Wiederholen alle** das Intervall **1** und die Häufigkeit **Stunde** aus. Bei Bedarf können Sie den Flow auch benennen. Klicken Sie auf **Erstellen**.

   >[!NOTE]
   >Auch wenn in diesem Beispiel ein Intervall von einer Stunde verwendet wird, können Sie das Intervall und die Häufigkeit auswählen, die für Ihre Geschäftsanforderungen am besten geeignet sind.

   ![Erstellen eines geplanten Flows](./media/commercial-marketplace-lead-management-instructions-azure-table/build-scheduled-flow.png)

1. Wählen Sie **+ Neuer Schritt** aus.
1. Suchen Sie im Fenster **Aktion auswählen** nach **Vergangene Zeit abrufen**. Wählen Sie dann unter **Aktionen** die Option **Vergangene Zeit abrufen** aus.

   ![Auswählen einer Aktion](./media/commercial-marketplace-lead-management-instructions-azure-table/choose-an-action.png)

1. Legen Sie im Fenster **Vergangene Zeit abrufen** den Wert für **Intervall** auf **1** fest. Wählen Sie in der Dropdownliste **Zeiteinheit** die Einheit **Stunde** aus.

    >[!IMPORTANT]
    >Vergewissern Sie sich, dass das gesendete Intervall und die Zeiteinheit aus Schritt 8 mit dem Intervall und der Häufigkeit übereinstimmen, die Sie in Schritt 5 für die Wiederholung konfiguriert haben.

    ![Festlegen des Intervalls als „Vergangene Zeit abrufen“](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-getpast-time.png)

   >[!TIP]
   >Sie können Ihren Flow jederzeit überprüfen, um sich zu vergewissern, dass jeder Schritt ordnungsgemäß konfiguriert ist. Um Ihren Flow zu überprüfen, wählen Sie auf der Menüleiste **Flow** die Option **Flowprüfung** aus.

   In den nächsten Schritten stellen Sie eine Verbindung mit Ihrer Tabelle her und richten die Verarbeitungslogik für neue Leads ein.

1. Wählen Sie **+ Neuer Schritt** aus. Suchen Sie dann im Fenster **Aktion auswählen** nach **Entitäten abrufen**.
1. Wählen Sie unter **Aktionen** die Option **Get entities (Azure Table Storage)** (Entitäten abrufen (Azure-Tabellenspeicher)) aus.
1. Geben Sie im Fenster **Azure Table Storage** Informationen für die folgenden Felder an, und wählen Sie **Erstellen** aus:

    * **Verbindungsname**: Geben Sie einen aussagekräftigen Namen für die Verbindung ein, die Sie zwischen diesem Flow und der Tabelle einrichten.
    * **Speicherkontoname:** Geben Sie den Namen des Speicherkontos für die Tabelle ein. Sie finden diesen Namen auf der Seite **Zugriffsschlüssel** des Speicherkontos.
    * **Freigegebener Speicherschlüssel:** Geben Sie den Schlüsselwert des Speicherkontos für die Tabelle an. Sie finden diesen Wert auf der Seite **Zugriffsschlüssel** des Speicherkontos.

      ![Fenster „Azure Table Storage“](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-storage.png)

   Nachdem Sie **Erstellen** ausgewählt haben, wird das Fenster **Entitäten abrufen** angezeigt. Wählen Sie **Erweiterte Optionen anzeigen** aus, und geben Sie Informationen für die folgenden Felder an:

   * **Tabelle**: Wählen Sie den Namen der Tabelle aus (aus [Tabelle erstellen](#create-a-table-in-your-storage-account)). Die nächste Abbildung zeigt die Eingabeaufforderung, wenn für dieses Beispiel die Tabelle `marketplaceleads` ausgewählt wurde.

     ![Fenster „Entitäten abrufen“](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities.png)

   * **Abfrage filtern**: Aktivieren Sie dieses Kontrollkästchen, und fügen Sie diese Funktion in das Feld ein: `Timestamp gt datetime'@{body('Get_past_time')}'`

     ![Feld „Filterabfrage“ unter „Entitäten abrufen“](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities-filter-query.png)

1. Nachdem Sie die Verbindung mit der Azure-Tabelle eingerichtet haben, wählen Sie **Neuer Schritt** aus, um eine Bedingung für das Durchsuchen der Azure-Tabelle nach neuen Leads hinzuzufügen.

1. Wählen Sie im Fenster **Aktion auswählen** die Option **Aktionen** aus. Wählen Sie dann **Bedingungssteuerelement** aus.

    ![Fenster zum Auswählen einer Aktion](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-choose-an-action.png)

1. Wählen Sie im Fenster **Bedingung** die Option **Wert auswählen** aus. Wählen Sie dann im Popupfenster die Option **Ausdruck** aus.

1. Fügen Sie `length(body('Get_entities')?['value'])` im Feld **fx** ein. Wählen Sie **OK** aus, um diese Funktion hinzuzufügen.

1. So schließen Sie das Einrichten der Bedingung ab:
    1. Wählen Sie in der Dropdownliste die Option **ist größer als** aus.
    2. Geben Sie **0** als Wert ein.

        ![Fenster „Bedingung“](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition.png)

   In den nächsten Schritten richten Sie die Aktion ein, die entsprechend dem Ergebnis der Bedingung ausgeführt werden soll:

   * Hat die Bedingung das Ergebnis **Wenn nein**, soll keine Aktion ausgeführt werden.
   * Hat die Bedingung das Ergebnis **Wenn ja**, soll eine Aktion ausgelöst werden, durch die eine Verbindung mit Ihrem Geschäfts-, Schul- oder Unikonto hergestellt wird, um eine E-Mail zu senden. 

1. Wählen Sie unter **Wenn ja** die Option **Aktion hinzufügen** aus.

    ![Option „Aktion hinzufügen“ für „Wenn ja“ im Fenster „Bedingung“](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes.png)

1. Wählen Sie **E-Mail senden (Office 365 Outlook)** aus.

    ![Option „E-Mail senden“ für „Wenn ja“ im Fenster „Bedingung“](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-send-email.png)

    >[!NOTE]
    >Um einen anderen E-Mail-Anbieter zu verwenden, suchen Sie stattdessen **E-Mail-Benachrichtigung senden (E-Mail)** , und wählen Sie diese Aktion aus. In den Anweisungen wird erläutert, wie Sie die Konfiguration mit Office 365 Outlook durchführen, die Anweisungen für andere E-Mail-Anbieter sind jedoch ähnlich.

1. Geben Sie im Fenster „Office 365 Outlook“ Informationen in den folgenden Feldern ein:

    1. **An**: Geben Sie für jede Person, die diese Benachrichtigung erhalten soll, eine E-Mail-Adresse ein.
    1. **Antragsteller:** Geben Sie ein Thema für die E-Mail ein. Beispiel: **Neue Leads!**
    1. **Text**: Fügen Sie den Text hinzu, der in jede E-Mail einbezogen werden soll (optional), und fügen Sie dann `body('Get_entities')?['value']` ein.

    >[!NOTE]
    >Sie können weitere statische oder dynamische-Datenpunkte in den Text dieser E-Mail einfügen.

    ![Option „Office 365 Outlook“ für „Wenn ja“ im Fenster „Bedingung“](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-outlook.png)

1. Wählen Sie **Speichern** aus, um den Flow zu speichern. Power Automate testet den Flow automatisch auf Fehler. Sind keine Fehler vorhanden, wird Ihr Flow gestartet, nachdem er gespeichert wurde.

Die folgende Abbildung zeigt anhand eines Beispiels, wie der fertige Flow aussehen sollte.

![Ein Beispiel für den abschließenden Flow](./media/commercial-marketplace-lead-management-instructions-azure-table/final-flow.png)

### <a name="manage-your-flow"></a>Verwalten Ihres Flows

Das Verwalten Ihres Flows nach dessen Start ist einfach. Sie haben vollständige Kontrolle über den Flow. Sie können ihn z. B. beenden und bearbeiten, und Sie können einen Ausführungsverlauf anzeigen und Analysen abrufen. Die nächste Abbildung zeigt die verfügbaren Optionen zum Verwalten eines Flows.

 ![Verwalten eines Flows](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-manage-completed.png)

Der Flow wird solange ausgeführt, bis Sie ihn mit der Option **Flow deaktivieren** beendet haben.

Erhalten Sie keine E-Mail-Benachrichtigungen zu Leads, wurden der Azure-Tabelle keine neuen Leads hinzugefügt. Sind Flowfehler vorhanden, erhalten Sie eine E-Mail wie in diesem Beispiel.

 ![E-Mail-Benachrichtigung zu Flowfehler](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-failure-note.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-table"></a>Konfigurieren des Angebots zum Senden von Leads an die Azure-Tabelle

Führen Sie die folgenden Schritte aus, um die Leadverwaltungsinformationen für Ihr Angebot im Veröffentlichungsportal zu konfigurieren.

1. Navigieren Sie zur Seite **Angebotseinrichtung** für Ihr Angebot.

1. Wählen Sie im Abschnitt **Kundenleads** die Option **Verbinden** aus.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-azure-table/customer-leads.png" alt-text="Kundenleads":::

1. Wählen Sie im Popupfenster **Verbindungsdetails** die Option **Azure-Tabelle** als **Leadziel** aus. 
     ![„Verbindungsdetails“ unter „Leadverwaltung“](./media/commercial-marketplace-lead-management-instructions-azure-table/connection-details.png)

1. Fügen Sie die Verbindungszeichenfolge aus dem Azure Storage-Konto, das Sie anhand der vorherigen Schritte erstellt haben, im Feld **Verbindungszeichenfolge für Speicherkonto** ein.
     ![„Verbindungsdetails für Speicherkonto“ unter „Leadverwaltung“](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-connection-details.png)

1. **Kontakt-E-Mail**: Geben Sie die E-Mail-Adressen der Personen in Ihrem Unternehmen an, die E-Mail-Benachrichtigungen erhalten sollen, wenn ein neuer Lead empfangen wird. Sie können mehrere durch Semikolons getrennte E-Mail-Adressen angeben.

1. Klicken Sie auf **OK**.

Wählen Sie die Schaltfläche **Überprüfen** aus, um sich zu vergewissern, dass eine Verbindung mit einem Leadziel hergestellt wurde. Bei erfolgreicher Verbindungsherstellung enthält das Leadziel einen Testlead.

>[!NOTE]
>Sie müssen die Konfiguration der übrigen Einstellungen des Angebots abschließen und veröffentlichen, damit Sie Leads für das Angebot erhalten.

Wenn Leads generiert werden, werden sie von Microsoft an die Azure-Tabelle gesendet. Wenn Sie einen Flow konfiguriert haben, wird auch eine E-Mail an die von Ihnen konfigurierte E-Mail-Adresse gesendet.
