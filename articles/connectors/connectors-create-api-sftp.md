---
title: Verbinden mit einem SFTP-Konto (veraltet)
description: Automatisieren von Aufgaben und Prozessen, die Dateien auf einem SFTP-Server mithilfe von Azure Logic Apps überwachen, erstellen, verwalten, senden und empfangen
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 11/01/2019
tags: connectors
ROBOTS: NOINDEX
ms.openlocfilehash: 70fb956af7ff45c7b54f04d7ed441ec39f9d80a5
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92673809"
---
# <a name="monitor-create-and-manage-sftp-files-in-azure-logic-apps"></a>Überwachen, Erstellen und Verwalten von SFTP-Dateien in Azure Logic Apps

> [!IMPORTANT]
> Verwenden Sie den [SFTP-SSH-Connector](../connectors/connectors-sftp-ssh.md), da der SFTP-Connector veraltet ist. Sie können keine SFTP-Trigger und -Aktionen mehr im Logik-App-Designer auswählen.

Zum Automatisieren von Aufgaben, die Dateien auf einem [SFTP](https://www.ssh.com/ssh/sftp/)-Server (Secure File Transfer Protocol) überwachen, erstellen, senden und empfangen, können Sie mithilfe von Azure Logic Apps und dem SFTP-Connector Integrationsworkflows erstellen und automatisieren. SFTP ist ein Netzwerkprotokoll, das Dateizugriff, Dateiübertragung und Dateiverwaltung über jeden beliebigen zuverlässigen Datenstrom ermöglicht. Im Folgenden finden Sie einige Beispielaufgaben, die automatisiert werden können:

* Sie können die Vorgänge überwachen, wenn Dateien hinzugefügt oder geändert werden.
* Sie können Dateien abrufen, erstellen, kopieren, aktualisieren, auflisten und löschen.
* Sie können Dateiinhalte und Metadaten abrufen.
* Sie können Archive in Ordner extrahieren.

Sie können Trigger verwenden, die Ereignisse auf Ihrem SFTP-Server überwachen und die Ausgabe für andere Aktionen verfügbar machen. Sie können Aktionen verwenden, die verschiedene Aufgaben auf Ihrem SFTP-Server ausführen. Darüber hinaus können die Ausgaben von SFTP-Aktionen auch von anderen Aktionen in Ihrer Logik-App verwendet werden. Wenn Sie beispielsweise regelmäßig Dateien von Ihrem SFTP-Server abrufen, können Sie mithilfe des Office 365 Outlook-Connectors oder des Outlook.com-Connectors E-Mail-Benachrichtigungen zu diesen Dateien und ihren Inhalten senden. Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

## <a name="limits"></a>Einschränkungen

Der SFTP-Connector verarbeitet nur Dateien, die *50 MB oder kleiner* sind und unterstützt keine [Nachrichtensegmentierung](../logic-apps/logic-apps-handle-large-messages.md). Verwenden Sie für größere Dateien den [SFTP-SSH-Connector](../connectors/connectors-sftp-ssh.md). Informationen zu den Unterschieden zwischen dem SFTP-Connector und dem SFTP-SSH-Connector finden Sie unter [Vergleichen von SFTP-SSH und SFTP](../connectors/connectors-sftp-ssh.md#comparison) im SFTP-SSH-Artikel.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/).

* Ihre SFTP-Serveradresse und Kontoanmeldeinformationen, über die Ihre Logik-App auf Ihr SFTP-Konto zugreifen kann. Damit Sie das [SSH-Protokoll](https://www.ssh.com/ssh/protocol/) (Secure Shell) nutzen können, benötigen Sie außerdem Zugriff auf einen privaten SSH-Schlüssel und das Kennwort für den privaten SSH-Schlüssel.

  > [!NOTE]
  >
  > Der SFTP-Connector unterstützt folgende Formate für private Schlüssel: OpenSSH, ssh.com und PuTTY.
  >
  > Wenn Sie Ihre Logik-App erstellen, müssen Sie nach dem Hinzufügen des SFTP-Triggers oder der gewünschten Aktion Verbindungsinformationen für Ihren SFTP-Server angeben. 
  > Wenn Sie einen privaten SSH-Schlüssel verwenden, stellen Sie sicher, dass Sie den Schlüssel aus der Datei für Ihren privaten SSH-Schlüssel * **kopieren** _ und diesen Schlüssel in die Verbindungsdetails _*_einfügen_*_. _*_Der Schlüssel darf nicht manuell eingegeben oder bearbeitet werden_*_ , da sonst ein Verbindungsfehler auftreten kann. 
  > Weitere Informationen finden Sie weiter unten in diesem Artikel.

_ Grundlegende Kenntnisse zum [Erstellen von Logik-Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Die Logik-App, in der Sie auf Ihr SFTP-Konto zugreifen möchten. Um mit einem SFTP-Trigger zu beginnen, [erstellen Sie eine leere Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md). Um eine SFTP-Aktion zu verwenden, starten Sie Ihre Logik-App mit einem anderen Trigger, z.B. dem **Wiederholungstrigger**.

## <a name="how-sftp-triggers-work"></a>Funktionsweise von SFTP-Triggern

SFTP-Trigger rufen das SFTP-Dateisystem ab und suchen nach jeder Datei, die seit dem letzten Abruf geändert wurde. Bei einigen Tools können Sie den Zeitstempel beibehalten, wenn sich die Dateien ändern. In diesen Fällen müssen Sie diese Funktion deaktivieren, sodass der Trigger arbeiten kann. Hier sind einige gängige Einstellungen:

| SFTP-Client | Aktion |
|-------------|--------|
| Winscp | Navigieren Sie zu **Optionen** > **Voreinstellungen** > **Übertragen** > **Bearbeiten** > **Zeitstempel beibehalten** > **Deaktivieren**. |
| FileZilla | Wechseln Sie zu **Übertragung** > **Änderungszeitpunkt der übertragenen Dateien beibehalten** > **Deaktivieren**. |
|||

Wenn ein Trigger eine neue Datei findet, überprüft er, ob die neue Datei vollständig ist und nicht nur teilweise geschrieben wurde. Zum Beispiel werden bei einer Datei möglicherweise gerade Änderungen vorgenommen, wenn der Trigger den Dateiserver überprüft. Um zu vermeiden, dass eine nur zum Teil geschriebene Datei zurückgegeben wird, vermerkt der Trigger den Zeitstempel für die Datei mit den kürzlichen Änderungen, gibt diese Datei jedoch nicht sofort zurück. Der Trigger gibt die Datei erst dann zurück, wenn der Server erneut abgerufen wird. Dieses Verhalten kann in manchen Fällen zu Verzögerungen führen, die bis zu zweimal länger als das Abrufintervall des Triggers sein können.

## <a name="connect-to-sftp"></a>Herstellen einer Verbindung mit SFTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und öffnen Sie Ihre Logik-App im Logik-App-Designer, sofern sie nicht bereits geöffnet ist.

1. Geben Sie im Fall einer leeren Logik-App im Suchfeld die Zeichenfolge „sftp“ als Filter ein. Wählen Sie in der Triggerliste den gewünschten Trigger aus.

   Oder

   Wählen Sie für vorhandene Logik-Apps im letzten Schritt zum Hinzufügen einer Aktion die Option **Neuer Schritt** aus. Geben Sie im Suchfeld den Begriff „sftp“ als Filter ein. Wählen Sie in der Liste mit den Aktionen die gewünschte Aktion aus.

   Wenn Sie zwischen Schritten eine Aktion einfügen möchten, bewegen Sie den Mauszeiger über den Pfeil zwischen den Schritten. Wählen Sie das daraufhin angezeigte Pluszeichen ( **+** ) und dann **Aktion hinzufügen** aus.

1. Geben Sie die erforderlichen Informationen für Ihre Verbindung ein.

   > [!IMPORTANT]
   >
   > Wenn Sie Ihren privaten SSH-Schlüssel in der Eigenschaft **SSH private key** eingeben, führen Sie diese zusätzlichen Schritte aus, um sicherzustellen, dass Sie den vollständigen und korrekten Wert für diese Eigenschaft angeben. 
   > Ein ungültiger Schlüssel führt zu einem Verbindungsfehler.

   Sie können jeden Text-Editor verwenden. Hier finden Sie Beispielschritte, die zeigen, wie Sie Ihren Schlüssel am Beispiel von Notepad.exe korrekt kopieren und einfügen.

   1. Öffnen Sie die Datei für den privaten SSH-Schlüssel in einem Text-Editor. Bei diesen Schritten verwenden wir als Beispiel Editor.

   1. Wählen Sie im Editor-Menü **Bearbeiten** die Option **Alles markieren** aus.

   1. Klicken Sie auf **Bearbeiten** > **Kopieren**.

   1. Fügen Sie in den von Ihnen hinzugefügten SFTP-Trigger oder die von Ihnen hinzugefügte Aktion den *gesamten* kopierten Schlüssel in die Eigenschaft **SSH private key** ein, die mehrere Zeilen unterstützt. **_Stellen Sie sicher, dass Sie den Schlüssel einfügen._* _ _*_Sie dürfen den Schlüssel nicht manuell eingeben oder bearbeiten._*_

1. Wenn Sie die Verbindungsdetails eingegeben haben, wählen Sie _*Erstellen** aus.

1. Geben Sie die erforderlichen Details für Ihren ausgewählten Trigger oder Ihre ausgewählte Aktion an, und fahren Sie mit dem Erstellen Ihres Logik-App-Workflows fort.

## <a name="examples"></a>Beispiele

<a name="file-add-modified"></a>

### <a name="sftp-trigger-when-a-file-is-added-or-modified"></a>SFTP-Trigger: When a file is added or modified (Wenn eine Datei hinzugefügt oder geändert wird)

Dieser Trigger startet einen Logik-App-Workflow, wenn auf einem SFTP-Server eine Datei hinzugefügt oder geändert wird. Sie können beispielsweise eine Bedingung hinzufügen, die den Inhalt der Datei überprüft und den Inhalt basierend darauf abruft, ob er eine bestimmte Bedingung erfüllt. Sie können dann eine Aktion hinzufügen, die den Inhalt der Datei abruft und in einem Ordner auf dem SFTP-Server ablegt.

**Beispiel für Unternehmen** : Sie können mit diesem Trigger beispielsweise einen SFTP-Ordner auf neue Dateien überwachen, die Kundenbestellungen darstellen. Anschließend können Sie eine SFTP-Aktion wie etwa **Dateiinhalt abrufen** verwenden, um den Inhalt einer Bestellung zur weiteren Verarbeitung abzurufen und in einer Bestelldatenbank zu speichern.

<a name="get-content"></a>

### <a name="sftp-action-get-content"></a>SFTP-Aktion: Inhalte abrufen

Diese Aktion ruft den Inhalt einer Datei auf einem SFTP-Server ab. Sie können z.B. den Trigger aus dem vorherigen Beispiel sowie eine Bedingung hinzufügen, die vom Dateiinhalt erfüllt werden muss. Wenn die Bedingung als TRUE ausgewertet wird, kann die Aktion ausgeführt werden, die den Inhalt abruft.

## <a name="connector-reference"></a>Connector-Referenz

Technische Details zu Triggern, Aktionen und Beschränkungen aus der OpenAPI-Beschreibung (ehemals Swagger) des Connectors finden Sie auf der [Referenzseite](../data-factory/connector-sftp.md) des Connectors.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu anderen [Logic Apps-Connectors](../connectors/apis-list.md)