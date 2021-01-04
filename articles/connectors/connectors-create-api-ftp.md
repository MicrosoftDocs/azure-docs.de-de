---
title: Herstellen einer Verbindung mit dem FTP-Server
description: Automatisieren von Aufgaben und Workflows, die Dateien auf einem FTP-Server mithilfe von Azure Logic Apps erstellen, überwachen und verwalten
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 12/15/2019
tags: connectors
ms.openlocfilehash: f4cad2b658547d56d00efdd5e1496110f8e4a5e6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999575"
---
# <a name="create-monitor-and-manage-ftp-files-by-using-azure-logic-apps"></a>Erstellen, Überwachen und Verwalten von FTP-Dateien mithilfe von Azure Logic Apps

Mit Azure Logic Apps und dem FTP-Connector können Sie automatisierte Aufgaben und Workflows erstellen, die Dateien über Ihr Konto auf einem FTP-Server erstellen, überwachen, senden und empfangen sowie weitere Aktionen ausführen, z. B. folgende:

* Sie können die Vorgänge überwachen, wenn Dateien hinzugefügt oder geändert werden.
* Sie können Dateien abrufen, erstellen, kopieren, aktualisieren, auflisten und löschen.
* Sie können Dateiinhalte und Metadaten abrufen.
* Sie können Archive in Ordner extrahieren.

Sie können Trigger verwenden, die Antworten von Ihrem FTP-Server erhalten und die Ausgabe für andere Aktionen verfügbar machen. Sie können Ausführungsaktionen in Ihren Logik-Apps verwenden, um Dateien auf Ihrem FTP-Server zu verwalten. Sie können die Ausgaben von FTP-Aktionen auch in anderen Aktionen verwenden. Wenn Sie z.B. regelmäßig Dateien von Ihrem FTP-Server abrufen, können Sie mithilfe des Office 365 Outlook-Connectors oder mithilfe des Outlook.com-Connectors E-Mails zu diesen Dateien und ihren Inhalten senden. Wenn Sie mit Logik-Apps noch nicht vertraut sind, lesen Sie [Was ist Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

## <a name="limitations"></a>Einschränkungen

* Der FTP-Connector unterstützt nur explizites FTP über TLS/SSL (FTPS) und ist nicht kompatibel mit implizitem FTPS.

* Standardmäßig können FTP-Aktionen Dateien mit einer Größe von *50 MB oder kleiner* lesen oder schreiben. FTP-Aktionen unterstützen die [Nachrichtensegmentierung](../logic-apps/logic-apps-handle-large-messages.md), um Dateien zu verarbeiten, die größer als 50 MB sind. Die Aktion **Get file content** (Dateiinhalt abrufen) verwendet implizit die Nachrichtensegmentierung.

* FTP-Trigger unterstützen keine Segmentierung. Trigger wählen beim Anfordern von Dateiinhalten nur Dateien aus, die 50 MB oder kleiner sind. Befolgen Sie das folgende Muster, um Dateien abzurufen, die größer als 50 MB sind:

  * Verwenden Sie einen FTP-Trigger, der Dateieigenschaften wie beispielsweise **When a file is added or modified (properties only) (Wenn eine Datei hinzugefügt oder geändert wird (nur Eigenschaften))** .

  * Auf den Trigger muss die FTP-Aktion **Get file content** (Dateiinhalt abrufen) folgen, die die vollständige Datei liest und implizit Segmentierung verwendet.

* Wenn Sie über einen lokalen FTP-Server verfügen, empfiehlt es sich, eine [Integrationsdienstumgebung (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) zu erstellen oder [Azure App Service-Hybridverbindungen](../app-service/app-service-hybrid-connections.md) zu verwenden, wobei Sie mit beiden Methoden auf lokale Datenquellen zugreifen können, ohne ein lokales Datengateway zu verwenden.

## <a name="how-ftp-triggers-work"></a>Funktionsweise von FTP-Triggern

Die FTP-Trigger rufen das FTP-Dateisystem ab und suchen nach jeder Datei, die seit dem letzten Abruf geändert wurde. Bei einigen Tools können Sie den Zeitstempel beibehalten, wenn sich die Dateien ändern. In diesen Fällen müssen Sie diese Funktion deaktivieren, sodass der Trigger arbeiten kann. Hier sind einige gängige Einstellungen:

| SFTP-Client | Aktion |
|-------------|--------|
| Winscp | Navigieren Sie zu **Optionen** > **Voreinstellungen** > **Übertragen** > **Bearbeiten** > **Zeitstempel beibehalten** > **Deaktivieren**. |
| FileZilla | Wechseln Sie zu **Übertragung** > **Änderungszeitpunkt der übertragenen Dateien beibehalten** > **Deaktivieren**. |
|||

Wenn ein Trigger eine neue Datei findet, überprüft er, ob die neue Datei vollständig ist und nicht nur teilweise geschrieben wurde. Zum Beispiel werden bei einer Datei möglicherweise gerade Änderungen vorgenommen, wenn der Trigger den Dateiserver überprüft. Um zu vermeiden, dass eine nur zum Teil geschriebene Datei zurückgegeben wird, vermerkt der Trigger den Zeitstempel für die Datei mit den kürzlichen Änderungen, gibt diese Datei jedoch nicht sofort zurück. Der Trigger gibt die Datei erst dann zurück, wenn der Server erneut abgerufen wird. Dieses Verhalten kann in manchen Fällen zu Verzögerungen führen, die bis zu zweimal länger als das Abrufintervall des Triggers sein können.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/).

* Die Adresse Ihres FTP-Hostservers und die zugehörigen Anmeldeinformationen.

  Der FTP-Connector erfordert, dass Ihr FTP-Server über das Internet erreichbar ist und für den Betrieb im Modus *Passiv* eingerichtet ist. Über Ihre Anmeldeinformationen kann mit Ihrer Logik-App eine Verbindung mit Ihrem FTP-Konto hergestellt sowie auf das Konto zugegriffen werden.

* Grundlegende Kenntnisse über die [Erstellung von Logik-Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Die Logik-App, in der Sie auf Ihr FTP-Konto zugreifen möchten. Um mit einem FTP-Trigger zu beginnen, [erstellen Sie eine leere Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md). Um eine FTP-Aktion zu verwenden, starten Sie Ihre Logik-App mit einem anderen Trigger, z. B. dem **Wiederholungstrigger**.

## <a name="connect-to-ftp"></a>Herstellen einer FTP-Verbindung

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und öffnen Sie Ihre Logik-App im Logik-App-Designer.

1. Geben Sie für leere Logik-Apps im Suchfeld `ftp` als Filter ein. Wählen Sie in der **Trigger** liste den gewünschten Trigger aus.

   Oder

   Wählen Sie für vorhandene Logik-Apps im letzten Schritt zum Hinzufügen einer Aktion die Option **Neuer Schritt** und dann **Aktion hinzufügen** aus. Geben Sie im Suchfeld den Begriff `ftp` als Filter ein. Wählen Sie in der Liste mit den **Aktionen** die gewünschte Aktion aus.

   Wenn Sie zwischen Schritten eine Aktion einfügen möchten, bewegen Sie den Mauszeiger über den Pfeil zwischen den Schritten. Wählen Sie das angezeigte Pluszeichen ( **+** ) aus, und wählen Sie dann **Aktion hinzufügen** aus.

1. Geben Sie Ihre Verbindungsinformationen ein, und klicken Sie auf **Erstellen**.

1. Geben Sie die Informationen für Ihren ausgewählten Trigger oder Ihre ausgewählte Aktion an, und fahren Sie mit dem Erstellen Ihres Logik-App-Workflows fort.

## <a name="examples"></a>Beispiele

<a name="file-added-modified"></a>

### <a name="add-ftp-trigger"></a>Hinzufügen eines FTP-Triggers

Der Trigger **Beim Hinzufügen oder Ändern einer Datei (nur Eigenschaften)** startet einen Logik-App-Workflow, wenn er erkennt, dass eine Datei auf einem FTP-Server hinzugefügt oder geändert wurde. Sie können z. B. eine Bedingung hinzufügen, die den Inhalt der Datei überprüft und entscheidet, ob dieser Inhalt abgerufen wird. Die Entscheidung basiert darauf, ob der Inhalt eine bestimmte Bedingung erfüllt oder nicht. Zum Schluss können Sie eine Aktion hinzufügen, die den Inhalt der Datei abruft und in einem anderen Ordner auf dem SFTP-Server ablegt.

Sie können mit diesem Trigger beispielsweise einen FTP-Ordner auf neue Dateien überwachen, in denen Kundenbestellungen beschrieben werden. Dann können Sie eine FTP-Aktion wie **Dateimetadaten abrufen** verwenden, um die Eigenschaften für diese neue Datei abzurufen, und dann **Dateiinhalt abrufen** verwenden, um den Inhalt aus dieser Datei zur weiteren Verarbeitung abzurufen und diese Bestellung in einer Bestelldatenbank zu speichern.

Das folgende Beispiel zeigt, wie Sie den Trigger **Beim Hinzufügen oder Ändern einer Datei (nur Eigenschaften)** verwenden.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und öffnen Sie Ihre Logik-App im Logik-App-Designer, sofern sie nicht bereits geöffnet ist.

1. Geben Sie für leere Logik-Apps im Suchfeld `ftp` als Filter ein. Wählen Sie in der Triggerliste den folgenden Trigger aus: **Beim Hinzufügen oder Ändern einer Datei (nur Eigenschaften)**

   ![Suchen und Auswählen des FTP-Triggers](./media/connectors-create-api-ftp/select-ftp-trigger-logic-app.png)

1. Geben Sie die erforderlichen Informationen zu Ihrer Verbindung ein, und wählen Sie dann **Erstellen** aus.

   Standardmäßig werden mit diesem Connector Dateien im Textformat übertragen. Um Dateien im Binärformat zu übertragen, z.B. für Fälle mit Codierung, wählen Sie **Binäre Übertragung** aus.

   ![Herstellen einer Verbindung mit dem FTP-Server](./media/connectors-create-api-ftp/create-ftp-connection-trigger.png)

1. Wählen Sie im Feld **Ordner** das Ordnersymbol aus, damit eine Liste angezeigt wird. Um den Ordner zu finden, den Sie auf neue oder bearbeitete Dateien überwachen möchten, wählen Sie den Pfeil nach rechts ( **>** ) aus, navigieren zu diesem Ordner, und wählen Sie dann den Ordner aus.

   ![Suchen und Auswählen des zu überwachenden Ordners](./media/connectors-create-api-ftp/select-folder-ftp-trigger.png)

   Der ausgewählte Ordner wird im Feld **Ordner** angezeigt.

   ![Der ausgewählte Ordner wird in der Eigenschaft „Ordner“ angezeigt.](./media/connectors-create-api-ftp/selected-folder-ftp-trigger.png)

1. Speichern Sie Ihre Logik-App. Wählen Sie auf der Symbolleiste des Designers **Speichern** aus.

Nachdem Ihre Logik-App jetzt über einen Trigger verfügt, fügen Sie die Aktionen hinzu, die Sie ausführen möchten, wenn Ihre Logik-App eine neue oder bearbeitete Datei findet. In diesem Beispiel können Sie eine FTP-Aktion hinzufügen, die den neuen oder aktualisierten Inhalt abruft.

<a name="get-content"></a>

### <a name="add-ftp-action"></a>Hinzufügen einer FTP-Aktion

Die Aktion **Dateimetadaten abrufen** ruft die Eigenschaften für eine Datei ab, die sich auf Ihrem FTP-Server befindet, und die Aktion **Dateiinhalt abrufen** ruft den Dateiinhalt basierend auf den Informationen zu dieser Datei auf dem FTP-Server ab. Beispielsweise können Sie den Trigger aus dem vorherigen Beispiel und diese Aktionen hinzufügen, um den Inhalt der Datei abzurufen, nachdem sie hinzugefügt oder bearbeitet wurde.

1. Wählen Sie unter dem Trigger oder anderen Aktionen **Neuer Schritt** aus.

1. Geben Sie im Suchfeld den Begriff `ftp` als Filter ein. Wählen Sie in der Liste mit den Aktionen diese Aktion aus: **Dateimetadaten abrufen**

   ![Auswählen der Aktion „Dateimetadaten abrufen“](./media/connectors-create-api-ftp/select-get-file-metadata-ftp-action.png)

1. Wenn Sie bereits über eine Verbindung zu Ihrem FTP-Server und Konto verfügen, wechseln Sie zum nächsten Schritt. Andernfalls stellen Sie die erforderlichen Informationen zu dieser Verbindung bereit, und wählen Sie dann **Erstellen** aus.

   ![Erstellen einer FTP-Serververbindung](./media/connectors-create-api-ftp/create-ftp-connection-action.png)

1. Nachdem die Aktion **Dateimetadaten abrufen** angezeigt wurde, klicken Sie in das Feld **Datei**, damit die dynamische Inhaltsliste angezeigt wird. Sie können jetzt Eigenschaften für die Ausgaben aus den vorherigen Schritten auswählen. Wählen Sie in der dynamischen Inhaltsliste unter **Dateimetadaten abrufen** die Eigenschaft **Liste der Datei-IDs** aus, die auf die Sammlung verweist, in der die Datei hinzugefügt oder aktualisiert wurde.

   ![Suchen und Auswählen der Eigenschaft „Liste der Datei-IDs“](./media/connectors-create-api-ftp/select-list-of-files-id-output.png)

   Die Eigenschaft **Liste der Datei-IDs** wird jetzt im Feld **Datei** angezeigt.

   ![Ausgewählte Eigenschaft „Liste der Datei-IDs“](./media/connectors-create-api-ftp/selected-list-file-ids-ftp-action.png)

1. Fügen Sie nun diese FTP-Aktion hinzu: **Dateiinhalte abrufen**

   ![Suchen und Auswählen der Aktion „Dateiinhalt abrufen“](./media/connectors-create-api-ftp/select-get-file-content-ftp-action.png)

1. Nachdem die Aktion **Dateiinhalt abrufen** angezeigt wurde, klicken Sie in das Feld **Datei**, damit die dynamische Inhaltsliste angezeigt wird. Sie können jetzt Eigenschaften für die Ausgaben aus den vorherigen Schritten auswählen. Wählen Sie in der dynamischen Inhaltsliste unter **Dateimetadaten abrufen** die Eigenschaft **ID** aus, die auf die Datei verweist, die hinzugefügt oder aktualisiert wurde.

   ![Suchen und Auswählen der Eigenschaft „ID“](./media/connectors-create-api-ftp/get-file-content-id-output.png)

   Die Eigenschaft **ID** wird jetzt im Feld **Datei** angezeigt.

   ![Ausgewählte Eigenschaft „ID“](./media/connectors-create-api-ftp/selected-get-file-content-id-ftp-action.png)

1. Speichern Sie Ihre Logik-App.

## <a name="test-your-logic-app"></a>Testen Ihrer Logik-App

Um zu überprüfen, ob Ihr Workflow den erwarteten Inhalt zurückgibt, fügen Sie eine weitere Aktion hinzu, die Ihnen den Inhalt aus der hochgeladenen oder aktualisierten Datei sendet.

1. Fügen Sie unter der Aktion **Dateiinhalt abrufen** eine Aktion hinzu, die Ihnen den Inhalt der Datei senden kann. In diesem Beispiel wird die Aktion **E-Mail senden** für Office 365 Outlook hinzugefügt.

   ![Hinzufügen einer Aktion zum Senden von E-Mails](./media/connectors-create-api-ftp/select-send-email-action.png)

1. Nachdem die Aktion angezeigt wird, geben Sie die Informationen an, und schließen Sie die Eigenschaften ein, die Sie testen möchten. Nehmen Sie beispielsweise die Eigenschaft **Dateiinhalt** auf, die in der dynamischen Inhaltsliste angezeigt wird, nachdem Sie im Abschnitt **Dateiinhalt abrufen** **Weitere Informationen** ausgewählt haben.

   ![Angeben von Informationen zur E-Mail-Aktion](./media/connectors-create-api-ftp/selected-send-email-action.png)

1. Speichern Sie Ihre Logik-App. Um die Logik-App auszuführen und auszulösen, wählen Sie auf der Symbolleiste **Ausführen** aus, und fügen Sie dann dem FTP-Ordner, der jetzt von ihrer Logik-App überwacht wird, eine Datei hinzu.

## <a name="connector-reference"></a>Connector-Referenz

Weitere technische Details zu diesem Connector, z. B. Trigger, Aktionen und Grenzwerte, wie sie in der Swagger-Datei des Connectors beschrieben werden, finden Sie auf der [Referenzseite des Connectors](/connectors/ftpconnector/).

> [!NOTE]
> Für Logik-Apps in einer [Integrationsdienstumgebung (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) verwendet die mit ISE bezeichnete Version dieses Connectors stattdessen die [ISE-Nachrichtengrenzwerte](../logic-apps/logic-apps-limits-and-config.md#message-size-limits).

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu anderen [Logic Apps-Connectors](../connectors/apis-list.md)

