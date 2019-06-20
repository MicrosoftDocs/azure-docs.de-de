---
title: Herstellen einer Verbindung mit dem FTP-Server mit SSH – Azure Logic Apps | Microsoft-Dokumentation
description: Automatisieren von Aufgaben, die Dateien auf einem SFTP-Server mithilfe von SSH und Azure Logic Apps überwachen, erstellen, verwalten, senden und empfangen
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, LADocs
ms.topic: article
tags: connectors
ms.date: 01/15/2019
ms.openlocfilehash: 5f82c654b443d58c9ce38c2fb0f48c1654daeb34
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64922243"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-ssh-and-azure-logic-apps"></a>Überwachen, Erstellen und Verwalten von SFTP-Dateien mithilfe von SSH und Azure Logic Apps

Um Aufgaben zu automatisieren, die Dateien auf einem [Secure File Transfer Protocol (SFTP)](https://www.ssh.com/ssh/sftp/)-Server überwachen, erstellen, senden und empfangen, indem Sie das [Secure Shell (SSH)](https://www.ssh.com/ssh/protocol/)-Protokoll verwenden, können Sie Integrationsworkflows mithilfe von Azure Logic Apps und dem SFTP-SSH-Connector erstellen und automatisieren. SFTP ist ein Netzwerkprotokoll, das Dateizugriff, Dateiübertragung und Dateiverwaltung über jeden beliebigen zuverlässigen Datenstrom ermöglicht. Im Folgenden finden Sie einige Beispielaufgaben, die automatisiert werden können: 

* Sie können die Vorgänge überwachen, wenn Dateien hinzugefügt oder geändert werden.
* Sie können Dateien abrufen, erstellen, kopieren, umbenennen, aktualisieren, auflisten und löschen.
* Sie können Ordner erstellen.
* Sie können Dateiinhalte und Metadaten abrufen.
* Sie können Archive in Ordner extrahieren.

Sie können Trigger verwenden, die Ereignisse auf Ihrem SFTP-Server überwachen und die Ausgabe für andere Aktionen verfügbar machen. Sie können Aktionen verwenden, die verschiedene Aufgaben auf Ihrem SFTP-Server ausführen. Darüber hinaus können die Ausgaben von SFTP-Aktionen auch von anderen Aktionen in Ihrer Logik-App verwendet werden. Wenn Sie beispielsweise regelmäßig Dateien von Ihrem SFTP-Server abrufen, können Sie mithilfe des Office 365 Outlook-Connectors oder des Outlook.com-Connectors E-Mail-Benachrichtigungen zu diesen Dateien und ihren Inhalten senden.
Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

## <a name="limits"></a>Einschränkungen

* SFTP-SSH-Aktionen können Dateien lesen bzw. in Dateien schreiben, die *1 GB oder kleiner* sind, indem sie die Daten *in Teilen zu 15 MB* und nicht als 1-GB-Teil verwalten.

* Bei Dateien, die *größer als 1 GB* sind, können Aktionen auf [Nachrichtenblöcke](../logic-apps/logic-apps-handle-large-messages.md) zurückgreifen. SFTP-SSH-Trigger unterstützen derzeit keine Blockerstellung.

Weitere Unterschiede finden Sie unter [Vergleichen von SFTP-SSH und SFTP](#comparison) im nächsten Abschnitt.

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>Vergleichen von SFTP-SSH und SFTP

Hier sind weitere wesentliche Unterschiede zwischen dem SFTP-SSH-Connector und dem SFTP-Connector, bei denen der SFTP-SSH-Connector diese Funktionen bietet:

* Er verwendet die [SSH.NET](https://github.com/sshnet/SSH.NET)-Bibliothek, die eine Open Source-SSH-Bibliothek (Secure Shell) mit Unterstützung für .NET ist.

  > [!NOTE]
  >
  > Der SFTP-SSH-Connector unterstützt *nur* diese privaten Schlüssel, Formate, Algorithmen und Fingerabdrücke:
  >
  > * **Formate für private Schlüssel**: Die Schlüssel „RSA“ (Rivest Shamir-Adleman) und „DSA“ (Digital Signature Algorithm) und OpenSSH- und ssh.com-Formate
  > * **Verschlüsselungsalgorithmen**: DES-EDE3-CBC, DES EDE3 CFB, DES-CBC, AES-128-CBC, AES-192-CBC und AES-256-CBC
  > * **Fingerabdruck**: MD5

* Im Vergleich zum SFTP-Connector können Aktionen Dateien lesen bzw. in Dateien schreiben, die *bis zu 1 GB* groß sind, verarbeiten die Daten jedoch in Teilen von 15 MB, nicht als 1-GB-Teil. Bei Dateien, die größer als 1 GB sind, können Aktionen auch auf [Nachrichtenblöcke](../logic-apps/logic-apps-handle-large-messages.md) zurückgreifen. SFTP-SSH-Trigger unterstützen derzeit keine Blockerstellung.

* Er stellt die Aktion **Ordner erstellen** bereit, wodurch ein Ordner unter dem angegebenen Pfad auf dem SFTP-Server erstellt wird.

* Er stellt die Aktion **Datei umbenennen** bereit, wodurch eine Datei auf dem SFTP-Server umbenannt wird.

* Er speichert die Verbindung zum SFTP-Server *für bis zu 1 Stunde*, wodurch die Leistung verbessert und die Anzahl der Verbindungsversuche auf dem Server reduziert wird. Um die Dauer für dieses Verhalten beim Zwischenspeichern festzulegen, bearbeiten Sie die Eigenschaft <a href="https://man.openbsd.org/sshd_config#ClientAliveInterval" target="_blank">**ClientAliveInterval**</a> in der SSH-Konfiguration auf Ihrem SFTP-Server.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich <a href="https://azure.microsoft.com/free/" target="_blank">für ein kostenloses Azure-Konto registrieren</a>. 

* Ihre SFTP-Serveradresse und Kontoanmeldeinformationen, über die Ihre Logik-App auf Ihr SFTP-Konto zugreifen kann. Außerdem benötigen Sie Zugriff auf einen privaten SSH-Schlüssel und das Kennwort für den privaten SSH-Schlüssel. 

  > [!IMPORTANT]
  >
  > Der SFTP-SSH-Connector unterstützt *nur* diese privaten Schlüssel, Formate, Algorithmen und Fingerabdrücke:
  > 
  > * **Formate für private Schlüssel**: Die Schlüssel „RSA“ (Rivest Shamir-Adleman) und „DSA“ (Digital Signature Algorithm) und OpenSSH- und ssh.com-Formate
  > * **Verschlüsselungsalgorithmen**: DES-EDE3-CBC, DES EDE3 CFB, DES-CBC, AES-128-CBC, AES-192-CBC und AES-256-CBC
  > * **Fingerabdruck**: MD5
  >
  > Wenn Sie Ihre Logik-App erstellen, müssen Sie nach dem Hinzufügen des SFTP-SSH-Triggers oder der gewünschten Aktion Verbindungsinformationen für Ihren SFTP-Server bereitstellen. 
  > Wenn Sie einen privaten SSH-Schlüssel verwenden, stellen Sie sicher, dass Sie ***den Schlüssel aus Ihrer privaten SSH-Schlüsseldatei kopieren*** und diesen Schlüssel in die Verbindungsdetails ***einfügen***. ***Der Schlüssel darf nicht manuell eingegeben oder bearbeitet werden***, da anderenfalls ein Verbindungsfehler auftritt. 
  > Weitere Informationen finden Sie weiter unten in diesem Artikel.

* Grundlegende Kenntnisse über die [Erstellung von Logik-Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Die Logik-App, in der Sie auf Ihr SFTP-Konto zugreifen möchten. Um mit einem SFTP-SSH-Trigger zu beginnen, [erstellen Sie eine leere Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md). Um eine SFTP-SSH-Aktion zu verwenden, starten Sie Ihre Logik-App mit einem anderen Trigger, z.B. dem **Wiederholungstrigger**.

## <a name="connect-to-sftp-with-ssh"></a>Herstellen einer Verbindung zu SFTP mit SSH

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und öffnen Sie Ihre Logik-App im Logik-App-Designer, sofern sie nicht bereits geöffnet ist.

1. Geben Sie im Fall einer leeren Logik-App im Suchfeld die Zeichenfolge „sftp ssh“ als Filter ein. Wählen Sie in der Triggerliste den gewünschten Trigger aus. 

   Oder

   Wählen Sie für vorhandene Logik-Apps im letzten Schritt zum Hinzufügen einer Aktion die Option **Neuer Schritt** aus. 
   Geben Sie im Suchfeld den Begriff „sftp ssh“ als Filter ein. 
   Wählen Sie in der Liste mit den Aktionen die gewünschte Aktion aus.

   Wenn Sie zwischen Schritten eine Aktion einfügen möchten, bewegen Sie den Mauszeiger über den Pfeil zwischen den Schritten. 
   Wählen Sie das daraufhin angezeigte Pluszeichen ( **+** ) und dann **Aktion hinzufügen** aus.

1. Geben Sie die erforderlichen Informationen für Ihre Verbindung ein.

   > [!IMPORTANT] 
   >
   > Wenn Sie Ihren privaten SSH-Schlüssel in der Eigenschaft **SSH private key** eingeben, führen Sie diese zusätzlichen Schritte aus, um sicherzustellen, dass Sie den vollständigen und korrekten Wert für diese Eigenschaft angeben. 
   > Ein ungültiger Schlüssel führt zu einem Verbindungsfehler.
   
   Sie können jeden Text-Editor verwenden. Hier finden Sie Beispielschritte, die zeigen, wie Sie Ihren Schlüssel am Beispiel von Notepad.exe korrekt kopieren und einfügen.
    
   1. Öffnen Sie die Datei für den privaten SSH-Schlüssel in einem Text-Editor. 
   Bei diesen Schritten verwenden wir als Beispiel Editor.

   1. Wählen Sie im Menü **Bearbeiten** von Editor die Option **Alles markieren** aus.

   1. Klicken Sie auf **Bearbeiten** > **Kopieren**.

   1. Fügen Sie in den von Ihnen hinzugefügten SFTP-SSH-Trigger oder die von Ihnen hinzugefügte Aktion den kopierter Schlüssel *complete* in die Eigenschaft **SSH private key** ein, die mehrere Zeilen unterstützt. 
   Stellen Sie sicher, dass Sie den Schlüssel ***einfügen***. ***Sie dürfen ihn nicht manuell eingeben oder bearbeiten***.

1. Wenn Sie die Verbindungsdetails eingegeben haben, wählen Sie **Erstellen** aus.

1. Geben Sie jetzt die erforderlichen Details für Ihren ausgewählten Trigger oder Ihre ausgewählte Aktion an, und fahren Sie mit dem Erstellen Ihres Logik-App-Workflows fort.

## <a name="trigger-limits"></a>Triggergrenzwerte

Die SFTP-SSH-Trigger rufen das SFTP-Dateisystem ab und suchen nach jeder Datei, die seit des letzten Abrufs geändert wurde. Bei einigen Tools können Sie den Zeitstempel beibehalten, wenn sich die Dateien ändern. In diesen Fällen müssen Sie diese Funktion deaktivieren, sodass der Trigger arbeiten kann. Hier sind einige gängige Einstellungen:

| SFTP-Client | Aktion | 
|-------------|--------| 
| Winscp | Navigieren Sie zu **Optionen** > **Voreinstellungen** > **Übertragen** > **Bearbeiten** > **Zeitstempel beibehalten** > **Deaktivieren**. |
| FileZilla | Wechseln Sie zu **Übertragung** > **Änderungszeitpunkt der übertragenen Dateien beibehalten** > **Deaktivieren**. | 
||| 

Wenn ein Trigger eine neue Datei findet, überprüft er, ob die neue Datei vollständig ist und nicht nur teilweise geschrieben wurde. Zum Beispiel werden bei einer Datei möglicherweise gerade Änderungen vorgenommen, wenn der Trigger den Dateiserver überprüft. Um zu vermeiden, dass eine nur zum Teil geschriebene Datei zurückgegeben wird, vermerkt der Trigger den Zeitstempel für die Datei mit den kürzlichen Änderungen, gibt diese Datei jedoch nicht sofort zurück. Der Trigger gibt die Datei erst dann zurück, wenn der Server erneut abgerufen wird. Dieses Verhalten kann in manchen Fällen zu Verzögerungen führen, die bis zu zweimal länger als das Abrufintervall des Triggers sein können. 

Beim Anfordern von Dateiinhalten rufen Trigger keine Dateien ab, die größer als 15 MB sind. Befolgen Sie das folgende Muster, um Dateien abzurufen, die größer als 15 MB sind: 

* Verwenden Sie einen Trigger, der Dateieigenschaften zurückgibt, z.B. **Beim Hinzufügen oder Ändern einer Datei (nur Eigenschaften)** .

* Auf den Trigger muss eine Aktion folgen, die die gesamte Datei liest (etwa **Dateiinhalt über Pfad abrufen**), und die Aktion muss [Nachrichtenblöcke](../logic-apps/logic-apps-handle-large-messages.md) verwenden.

## <a name="examples"></a>Beispiele

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>SFTP – SSH-Trigger: When a file is added or modified (Wenn eine Datei hinzugefügt oder geändert wird)

Dieser Trigger startet einen Logik-App-Workflow, wenn auf einem SFTP-Server eine Datei hinzugefügt oder geändert wird. Sie können beispielsweise eine Bedingung hinzufügen, die den Inhalt der Datei überprüft und den Inhalt basierend darauf abruft, ob er eine bestimmte Bedingung erfüllt. Sie können dann eine Aktion hinzufügen, die den Inhalt der Datei abruft und in einem Ordner auf dem SFTP-Server ablegt. 

**Beispiel für Unternehmen**: Sie können mit diesem Trigger beispielsweise einen SFTP-Ordner auf neue Dateien überwachen, die Kundenbestellungen darstellen. Anschließend können Sie eine SFTP-Aktion wie etwa **Dateiinhalt abrufen** verwenden, um den Inhalt einer Bestellung zur weiteren Verarbeitung abzurufen und in einer Bestelldatenbank zu speichern.

Beim Anfordern von Dateiinhalten rufen Trigger keine Dateien ab, die größer als 15 MB sind. Befolgen Sie das folgende Muster, um Dateien abzurufen, die größer als 15 MB sind: 

* Verwenden Sie einen Trigger, der Dateieigenschaften zurückgibt, z.B. **Beim Hinzufügen oder Ändern einer Datei (nur Eigenschaften)** .

* Auf den Trigger muss eine Aktion folgen, die die gesamte Datei liest (etwa **Dateiinhalt anhand des Pfads abrufen**), und die Aktion muss [Nachrichtenblöcke](../logic-apps/logic-apps-handle-large-messages.md) verwenden.

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-content-using-path"></a>SFTP – SSH-Aktion: Dateiinhalt über Pfad abrufen

Diese Aktion ruft den Inhalt einer Datei auf einem SFTP-Server ab. Sie können z.B. den Trigger aus dem vorherigen Beispiel sowie eine Bedingung hinzufügen, die vom Dateiinhalt erfüllt werden muss. Wenn die Bedingung als TRUE ausgewertet wird, kann die Aktion ausgeführt werden, die den Inhalt abruft. 

Beim Anfordern von Dateiinhalten rufen Trigger keine Dateien ab, die größer als 15 MB sind. Befolgen Sie das folgende Muster, um Dateien abzurufen, die größer als 15 MB sind: 

* Verwenden Sie einen Trigger, der Dateieigenschaften zurückgibt, z.B. **Beim Hinzufügen oder Ändern einer Datei (nur Eigenschaften)** .

* Auf den Trigger muss eine Aktion folgen, die die gesamte Datei liest (etwa **Dateiinhalt über Pfad abrufen**), und die Aktion muss [Nachrichtenblöcke](../logic-apps/logic-apps-handle-large-messages.md) verwenden.

## <a name="connector-reference"></a>Connector-Referenz

Technische Details zu Triggern, Aktionen und Beschränkungen aus der OpenAPI-Beschreibung (ehemals Swagger) des Connectors finden Sie auf der [Referenzseite](/connectors/sftpconnector/) des Connectors.

## <a name="get-support"></a>Support

* Sollten Sie Fragen haben, besuchen Sie das [Azure Logic Apps-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Wenn Sie Features vorschlagen oder für Vorschläge abstimmen möchten, besuchen Sie die [Website für Logic Apps-Benutzerfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu anderen [Logic Apps-Connectors](../connectors/apis-list.md)
