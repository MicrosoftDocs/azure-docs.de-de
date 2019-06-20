---
title: Azure Table | Azure Marketplace
description: Konfigurieren der Leadverwaltung mit Azure-Tabellen
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: pabutler
ms.openlocfilehash: 08f9d794822dfd7879efc7c4813ecc46f92f6a45
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147898"
---
# <a name="lead-management-instructions-for-azure-table"></a>Anweisungen für die Leadverwaltung für Azure-Tabellen

Dieser Artikel beschreibt, wie Sie eine Azure-Tabelle zum Speichern von Vertriebschancen (Leads) konfigurieren. Sie können in Azure-Tabellen Informationen zu Kunden speichern und anpassen.


## <a name="how-to-configure-azure-table"></a>Konfigurieren einer Azure-Tabelle

1. Wenn Sie nicht über ein Azure-Konto verfügen, können Sie ein [kostenloses Testkonto erstellen](https://azure.microsoft.com/pricing/free-trial/).
2. Nachdem Ihr Azure-Konto aktiviert wurde, melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
3. Gehen Sie folgendermaßen vor, um im Azure-Portal ein Speicherkonto zu erstellen.  
    1. Klicken Sie im Menü auf der linken Seite auf **+ Ressource erstellen**.  Auf der rechten Seite wird der Bereich (das Blatt) **Neu** angezeigt.
    2. Wählen Sie im Bereich **Neu** die Option **Speicher** aus.  Auf der rechten Seite wird eine Liste **Empfohlen** angezeigt.
    3. Wählen Sie **Speicherkonto** aus, um mit der Kontoerstellung zu beginnen.  Befolgen Sie die Anweisungen im Artikel [Erstellen eines Speicherkontos](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

    ![Schritte zum Erstellen eines Azure Storage-Kontos](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

    Weitere Informationen zu Speicherkonten finden Sie im [Schnellstarttutorial](https://docs.microsoft.com/azure/storage/).  Weitere Informationen zu den Preisen für Storage finden Sie unter [Preise für Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

4. Warten Sie, bis Ihr Speicherkonto bereitgestellt wird. Dieser Prozess nimmt in der Regel einige Minuten in Anspruch.  Greifen Sie über die **Homepage** des Azure-Portals auf Ihr Speicherkonto zu, indem Sie **All Ihre Ressourcen anzeigen** oder im linken Navigationsbereich des Azure-Portals **Alle Ressourcen** auswählen.

    ![Zugriff auf Ihr Azure-Speicherkonto](./media/cloud-partner-portal-lead-management-instructions-azure-table/azure-storage-access.png)

5. Kopieren Sie im Speicherkontobereich die Speicherkonto-Verbindungszeichenfolge für den Schlüssel, und fügen Sie sie im Cloud-Partnerportal im Feld **Verbindungszeichenfolge für Speicherkonto** ein. Beispiel für eine Verbindungszeichenfolge:

```sql
DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
```

  ![Azure-Speicherschlüssel](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

Sie können die Daten in Ihrer Speichertabelle im [Azure Storage-Explorer](https://azurestorageexplorer.codeplex.com/) oder einem ähnlichen Tool anzeigen. Außerdem können Sie die Daten aus Azure-Tabellen exportieren.


## <a name="use-microsoft-flow-with-an-azure-table-optional"></a>Verwenden von Microsoft Flow mit einer Azure-Tabelle (*optional*) 

Sie können [Microsoft Flow](https://docs.microsoft.com/flow/) verwenden, um automatisch jedes Mal, wenn ein Lead zur Azure-Tabelle hinzugefügt wird, eine Benachrichtigung zu senden. Wenn Sie kein Konto besitzen, können Sie sich [für ein kostenloses Konto registrieren](https://flow.microsoft.com/).


### <a name="lead-notification-example"></a>Beispiel zu Leadbenachrichtigung

Verwenden Sie dieses Beispiel als Leitfaden für die Erstellung eines grundlegenden Flows, in dem automatisch eine E-Mail-Benachrichtigung gesendet wird, wenn einer Azure-Tabelle ein neuer Lead hinzugefügt wird. In diesem Beispiel wird eine Wiederholung eingerichtet, um stündlich Leadinformationen zu senden, wenn der Tabellenspeicher aktualisiert wird.

1. Melden Sie sich bei Ihrem Microsoft Flow-Konto an.
2. Wählen Sie in der linken Navigationsleiste die Option **Meine Flows** aus.
3. Wählen Sie in der oberen Navigationsleiste die Option **+ Neu** aus.  
4. Wählen Sie in der Dropdownliste den Eintrag **+ Ohne Vorlage neu erstellen** aus.
5. Wählen Sie unter „Flow ohne Vorlage erstellen“ die Option **Ohne Vorlage neu erstellen** aus.

   ![Neuen Flow ohne Vorlage erstellen](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-create-from-blank.png)

6. Wählen Sie auf der Seite „Connectors und Trigger durchsuchen“ die Option **Trigger** aus.
7. Wählen Sie unter **Trigger** die Option **Wiederholung** aus.
8. Übernehmen Sie im Fenster **Wiederholung** die Standardeinstellung „1“ für **Intervall**. Wählen Sie in der Dropdownliste **Häufigkeit** die Option **Stunde** aus.

   >[!NOTE] 
   >Obwohl in diesem Beispiel ein 1-Stunden-Intervall verwendet wird, können Sie das Intervall und die Häufigkeit auswählen, die für Ihre Geschäftsanforderungen am besten geeignet sind.

   ![Häufigkeit für Wiederholung auf 1 Stunde festlegen](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-recurrence-dropdown.png)

9. Wählen Sie **+ Neuer Schritt**aus.
10. Suchen Sie nach „Vergangene Zeit abrufen“, und wählen Sie dann **Vergangene Zeit abrufen** unter „Aktionen“ aus. 

    ![Nach „Vergangene Zeit abrufen“-Aktion suchen und diese auswählen](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-search-getpasttime.png)

11. Legen Sie im Fenster **Vergangene Zeit abrufen** das **Intervall** auf „1“ fest.  Wählen Sie in der Dropdownliste **Zeiteinheit** die Einheit **Stunde** aus.
    >[!IMPORTANT] 
    >Stellen Sie sicher, dass dieses Intervall und diese Zeiteinheit mit dem Intervall und der Häufigkeit übereinstimmen, die Sie für „Wiederholung“ konfiguriert haben.

    ![Intervall für „Vergangene Zeit abrufen“ festlegen](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getpast-time.png)

    >[!TIP] 
    >Sie können Ihren Flow jederzeit überprüfen, um sich zu vergewissern, dass jeder Schritt ordnungsgemäß konfiguriert ist. Um Ihren Flow zu überprüfen, wählen Sie **Flowprüfung** in der Menüleiste „Flow“ aus.

Im nächsten Schritt stellen Sie eine Verbindung mit Ihrer Azure-Tabelle her, und richten Sie die Verarbeitungslogik ein, gemäß der neue Leads verarbeitet werden sollen.

1. Nach dem Schritt „Vergangene Zeit abrufen“ wählen Sie **+ Neuer Schritt** aus, und suchen Sie nach „Entitäten abrufen“.
2. Wählen Sie unter **Aktionen** die Option **Entitäten abrufen** aus, und wählen Sie dann **Erweiterte Optionen anzeigen** aus.
3. Geben Sie im Fenster **Entitäten abrufen** Informationen in die folgenden Felder ein:

   - **Tabelle**: Geben Sie den Namen Ihres Azure-Tabellenspeichers ein. In der nächsten Bildschirmaufnahme ist die Eingabeaufforderung gezeigt, wenn für dieses Beispiel „MarketPlaceLeads“ eingegeben wurde. 

     ![Benutzerdefinierten Wert für den Azure-Tabellennamen auswählen](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-table-name.png)

   - **Filterabfrage**: Klicken Sie auf dieses Feld. In einem Popupfenster wird das Symbol **Vergangene Zeit abrufen** angezeigt. Wählen Sie **Vergangene Zeit** aus, um dies als Zeitstempel zum Filtern der Abfrage zu verwenden. Alternativ können Sie die folgende Funktion in das Feld einfügen: CreatedTime `Timestamp gt datetime'@{body('Get_past_time')}'` 

     ![Filterabfragefunktion einrichten](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-filterquery.png)

4. Wählen Sie **Neuer Schritt** aus, um eine Bedingung hinzufügen, gemäß der die Azure-Tabelle auf neue Leads überprüft wird.

   ![„Neuer Schritt“ verwenden, um eine Bedingung für Überprüfen der Azure-Tabelle hinzuzufügen](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-add-filterquery-new-step.png)

5. Wählen Sie im Fenster **Aktion auswählen** die Option **Aktionen** aus, und wählen Sie dann das Steuerelement **Bedingung** aus.

     ![Steuerelement zum Hinzufügen einer Bedingung](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-action-condition-control.png)

6. Wählen Sie im Fenster **Bedingung** das Feld **Wert auswählen** aus, und wählen Sie dann **Ausdruck** im Popupenster aus.
7. Fügen Sie `length(body('Get_entities')?['value'])` in das Feld ***fx*** ein. Wählen Sie **OK** aus, um diese Funktion hinzuzufügen. So schließen Sie das Einrichten der Bedingung ab:

   - Wählen Sie „ist größer als“ in der Dropdownliste aus.
   - Geben Sie „0“ als Wert ein. 

     ![Eine Funktion zur Bedingung hinzufügen](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-fx0.png)

8. Richten Sie die Aktion ein, die entsprechend dem Ergebnis der Bedingung ausgeführt werden soll.

     ![Aktion entsprechend den Bedingungsergebnissen einrichten](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-pick-action.png)

9. Hat die Bedingung das Ergebnis **Wenn nein**, führen Sie keine Aktion aus. 
10. Hat die Bedingung das Ergebnis **Wenn ja**, lösen Sie eine Aktion aus, in der eine E-Mail an Ihr Office 365-Konto gesendet wird. Wählen Sie **Aktion hinzufügen**aus.
11. Wählen Sie **E-Mail senden** aus. 
12. Geben Sie im Fenster **E-Mail senden** Informationen in die folgenden Felder ein:

    - **An**: Geben Sie eine E-Mail-Adresse für jede Person ein, die diese Benachrichtigung erhalten soll.
    - **Betreff**: Geben Sie einen Betreff für die E-Mail ein. Beispiel:  Neue Leads!
    - **Text**:   Fügen Sie den Text hinzu, der in jede E-Mail einbezogen werden soll (optional), und fügen Sie dann den Text `body('Get_entities')?['value']` als eine Funktion ein, um Leadinformationen einzufügen.

      >[!NOTE] 
      >Sie können weitere statische oder dynamische-Datenpunkte in den Text dieser E-Mail einfügen.

      ![E-Mail für Leadbenachrichtigung einrichten](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-emailbody-fx.png)

13. Wählen Sie **Speichern** aus, um den Flow zu speichern. Microsoft Flow testet den Flow automatisch auf Fehler. Sind keine Fehler vorhanden, wird Ihr Flow gestartet, nachdem er gespeichert wurde.

Die nächste Bildschirmaufnahme zeigt ein Beispiel, wie der fertige Flow aussehen soll.

[![Fertige Flowsequenz](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end-thmb.png)](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end.png)

(*Klicken Sie auf die Abbildung, um sie zu vergrößern.* )


### <a name="manage-your-flow"></a>Verwalten Ihres Flows

Das Verwalten Ihres Flows, nachdem er gestartet wurde, ist einfach.  Sie haben vollständige Kontrolle über den Flow. Sie können ihn z. B. beenden und bearbeiten, und Sie können einen Ausführungsverlauf anzeigen und Analysen abrufen. Die nächste Bildschirmaufnahme zeigt die Optionen, die zum Verwalten eines Flows verfügbar sind. 

 ![Verwalten eines Flows](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-manage-completed.png)

Der Flow wird solange ausgeführt, bis Sie ihn mit der Option **Flow deaktivieren** beendet haben.

Erhalten Sie keine E-Mail-Benachrichtigungen zu Leads, wurden keine neuen Leads zur Azure-Tabelle hinzugefügt. Sind irgendwelche Flowfehler vorhanden, erhalten Sie eine E-Mail, die wie das Beispiel in der nächsten Bildschirmaufnahme aussieht.

 ![E-Mail-Benachrichtigung zu Flowfehler](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-failure-note.png)


## <a name="next-steps"></a>Nächste Schritte

[Konfigurieren von Kundenleads](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)
