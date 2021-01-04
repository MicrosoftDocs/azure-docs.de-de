---
title: Herstellen einer Verbindung mit dem SFTP-Server mit SSH
description: Automatisieren von Aufgaben, die Dateien auf einem SFTP-Server mithilfe von SSH und Azure Logic Apps überwachen, erstellen, verwalten, senden und empfangen
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 11/03/2020
tags: connectors
ms.openlocfilehash: 31714eee2e79481bbc8afb47718ed38e178d5b82
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324247"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-ssh-and-azure-logic-apps"></a>Überwachen, Erstellen und Verwalten von SFTP-Dateien mithilfe von SSH und Azure Logic Apps

Um Aufgaben zu automatisieren, die Dateien auf einem [Secure File Transfer Protocol (SFTP)](https://www.ssh.com/ssh/sftp/)-Server überwachen, erstellen, senden und empfangen, indem Sie das [Secure Shell (SSH)](https://www.ssh.com/ssh/protocol/)-Protokoll verwenden, können Sie Integrationsworkflows mithilfe von Azure Logic Apps und dem SFTP-SSH-Connector erstellen und automatisieren. SFTP ist ein Netzwerkprotokoll, das Dateizugriff, Dateiübertragung und Dateiverwaltung über jeden beliebigen zuverlässigen Datenstrom ermöglicht.

> [!NOTE]
> Der SFTP-SSH-Connector unterstützt die folgenden SFTP-Server derzeit nicht:
> 
> * IBM DataPower
> * MessageWay
> * OpenText Secure MFT
> * OpenText GXS

Im Folgenden finden Sie einige Beispielaufgaben, die automatisiert werden können:

* Sie können die Vorgänge überwachen, wenn Dateien hinzugefügt oder geändert werden.
* Sie können Dateien abrufen, erstellen, kopieren, umbenennen, aktualisieren, auflisten und löschen.
* Sie können Ordner erstellen.
* Sie können Dateiinhalte und Metadaten abrufen.
* Sie können Archive in Ordner extrahieren.

Sie können Trigger verwenden, die Ereignisse auf Ihrem SFTP-Server überwachen und die Ausgabe für andere Aktionen verfügbar machen. Sie können Aktionen verwenden, die verschiedene Aufgaben auf Ihrem SFTP-Server ausführen. Darüber hinaus können die Ausgaben von SFTP-Aktionen auch von anderen Aktionen in Ihrer Logik-App verwendet werden. Wenn Sie beispielsweise regelmäßig Dateien von Ihrem SFTP-Server abrufen, können Sie mithilfe des Office 365 Outlook-Connectors oder des Outlook.com-Connectors E-Mail-Benachrichtigungen zu diesen Dateien und ihren Inhalten senden. Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

Weitere Unterschiede zwischen dem SFTP-SSH-Connector und dem SFTP-Connector finden Sie unter [Vergleichen von SFTP-SSH und SFTP](#comparison) weiter unten in diesem Thema.

## <a name="limits"></a>Einschränkungen

* Der SFTP-SSH-Connector unterstützt die Authentifizierung entweder mit einem privaten Schlüssel oder mit einem Kennwort, aber nicht beides.

* SFTP-SSH-Aktionen, die [Blockerstellung](../logic-apps/logic-apps-handle-large-messages.md) unterstützen, können Dateien bis zu einer Größe von 1 GB verarbeiten, während SFTP-SSH-Aktionen, die keine Blockerstellung unterstützen, Dateien bis zu einer Größe von 50 MB verarbeiten können. Obgleich die Standardblockgröße 15 MB beträgt, kann sich diese Größe dynamisch ändern, beginnend mit 5 MB und allmählich ansteigend auf den Maximalwert von 50 MB, basierend auf Faktoren wie Netzwerkwartezeiten, Serverreaktionszeiten usw.

  > [!NOTE]
  > Für Logik-Apps in einer [Integrationsdienstumgebung (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) erfordert die mit ISE bezeichnete Version dieses Connectors Chunking, um stattdessen die [ISE-Nachrichtengrenzwerte](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) zu verwenden.

  Sie können dieses adaptive Verhalten außer Kraft setzen, indem Sie eine [konstante Blockgröße angeben](#change-chunk-size), die stattdessen verwendet werden soll. Diese Größe kann zwischen 5 MB und 50 MB liegen. Nehmen Sie beispielsweise an, Sie verfügen über eine 45 MB große Datei und ein Netzwerk, das diese Dateigröße ohne Latenzen unterstützt. Die adaptive Segmentierung führt zu mehreren Aufrufen anstelle von einem Aufruf. Um die Anzahl der Aufrufe zu reduzieren, können Sie versuchen, die Blockgröße auf 50 MB festzulegen. Wenn in einem anderen Szenario mit einer Blockgröße von beispielsweise 15 MB ein Timeout bei Ihrer Logik-App auftritt, können Sie versuchen, die Größe auf 5 MB zu reduzieren.

  Die Blockgröße ist einer Verbindung zugeordnet, was beduetet, dass Sie dieselbe Verbindung für Aktionen verwenden können, die Blockerstellung unterstützen, und dann für Aktionen, die keine Blockerstellung unterstützen. In diesem Fall liegt die Blockgröße für Aktionen, die keine Blockerstellung unterstützen, zwischen 5 MB und 50 MB. In dieser Tabelle sehen Sie, welche SFTP-SSH-Aktionen Blockerstellung unterstützen:

  | Aktion | Unterstützung für Blockerstellung | Unterstützung für die Außerkraftsetzung der Blockgröße |
  |--------|------------------|-----------------------------|
  | **Datei kopieren** | Nein | Nicht verfügbar |
  | **Datei erstellen** | Ja | Ja |
  | **Ordner erstellen** | Nicht verfügbar | Nicht verfügbar |
  | **Datei löschen** | Nicht verfügbar | Nicht verfügbar |
  | **Archiv in Ordner extrahieren** | Nicht verfügbar | Nicht verfügbar |
  | **Dateiinhalte abrufen** | Ja | Ja |
  | **Dateiinhalt anhand des Pfads abrufen** | Ja | Ja |
  | **Dateimetadaten abrufen** | Nicht verfügbar | Nicht verfügbar |
  | **Dateimetadaten anhand des Pfads abrufen** | Nicht verfügbar | Nicht verfügbar |
  | **Dateien im Ordner aufführen** | Nicht verfügbar | Nicht verfügbar |
  | **Datei umbenennen** | Nicht verfügbar | Nicht verfügbar |
  | **Datei aktualisieren** | Nein | Nicht verfügbar |
  ||||

* SFTP-SSH-Trigger unterstützen keine Nachrichtensegmentierung. Trigger wählen beim Anfordern von Dateiinhalten nur Dateien aus, die 15 MB oder kleiner sind. Verwenden Sie stattdessen das folgende Muster, um Dateien abzurufen, die größer als 15 MB sind:

  1. Verwenden Sie einen SFTP-SSH-Trigger, der nur Dateieigenschaften zurückgibt, z. B. **Beim Hinzufügen oder Ändern einer Datei (nur Eigenschaften)** .

  1. Lassen Sie auf den Trigger die SFTP-SSH-Aktion **Get file content** (Dateiinhalt abrufen) folgen, die die vollständige Datei liest und implizit Nachrichtensegmentierung verwendet.

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>Vergleichen von SFTP-SSH und SFTP

Hier sind weitere wesentliche Unterschiede zwischen dem SFTP-SSH-Connector und dem SFTP-Connector, bei denen der SFTP-SSH-Connector diese Funktionen bietet:

* Er verwendet die [SSH.NET](https://github.com/sshnet/SSH.NET)-Bibliothek, die eine Open Source-SSH-Bibliothek (Secure Shell) mit Unterstützung für .NET ist.

* Er stellt die Aktion **Ordner erstellen** bereit, wodurch ein Ordner unter dem angegebenen Pfad auf dem SFTP-Server erstellt wird.

* Er stellt die Aktion **Datei umbenennen** bereit, wodurch eine Datei auf dem SFTP-Server umbenannt wird.

* Er speichert die Verbindung zum SFTP-Server *für bis zu 1 Stunde* , wodurch die Leistung verbessert und die Anzahl der Verbindungsversuche auf dem Server reduziert wird. Um die Dauer für dieses Verhalten beim Zwischenspeichern festzulegen, bearbeiten Sie die Eigenschaft [**ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) in der SSH-Konfiguration auf Ihrem SFTP-Server.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/).

* Ihre SFTP-Serveradresse und Kontoanmeldeinformationen, über die Ihre Logik-App auf Ihr SFTP-Konto zugreifen kann. Außerdem benötigen Sie Zugriff auf einen privaten SSH-Schlüssel und das Kennwort für den privaten SSH-Schlüssel. Zum Verwenden der Blockerstellung beim Hochladen großer Dateien benötigen Sie sowohl Lese- als auch Schreibberechtigungen für den Stammordner auf Ihrem SFTP-Server. Andernfalls erhalten Sie einen Fehler „401 – Nicht autorisiert“.

  > [!IMPORTANT]
  >
  > Der SFTP-SSH-Connector unterstützt *nur* diese privaten Schlüssel, Formate, Algorithmen und Fingerabdrücke:
  >
  > * **Formate für private Schlüssel** : Die Schlüssel „RSA“ (Rivest Shamir-Adleman) und „DSA“ (Digital Signature Algorithm) und OpenSSH- und ssh.com-Formate. Wenn Ihr privater Schlüssel im PuTTY-Dateiformat (PPK) vorliegt, [konvertieren Sie den Schlüssel zuerst in das OpenSSH-Dateiformat (PEM)](#convert-to-openssh).
  >
  > * **Verschlüsselungsalgorithmen** : DES-EDE3-CBC, DES EDE3 CFB, DES-CBC, AES-128-CBC, AES-192-CBC und AES-256-CBC
  >
  > * **Fingerabdruck** : MD5
  >
  > Nach dem Hinzufügen des SFTP-SSH-Triggers oder der gewünschten Aktion zu Ihrer Logik-App müssen Sie Verbindungsinformationen für Ihren SFTP-Server bereitstellen. Wenn Sie Ihren privaten SSH-Schlüssel für diese Verbindung angeben, * *_geben Sie den Schlüssel nicht manuell ein, oder bearbeiten Sie ihn manuell_* _, da dies zu einem Fehler bei der Verbindung führen könnte. Stellen Sie stattdessen sicher, dass Sie _*_den Schlüssel aus Ihrer Datei für private SSH-Schlüssel kopieren_*_ und diesen Schlüssel in die Verbindungsdetails _*_einfügen_*_. 
  > Weitere Informationen finden Sie im Abschnitt [Herstellen einer Verbindung zu SFTP mit SSH](#connect) weiter unten in diesem Artikel.

_ Grundlegende Kenntnisse zum [Erstellen von Logik-Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Die Logik-App, in der Sie auf Ihr SFTP-Konto zugreifen möchten. Um mit einem SFTP-SSH-Trigger zu beginnen, [erstellen Sie eine leere Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md). Um eine SFTP-SSH-Aktion zu verwenden, starten Sie Ihre Logik-App mit einem anderen Trigger, z.B. dem **Wiederholungstrigger**.

## <a name="how-sftp-ssh-triggers-work"></a>Funktionsweise von SFTP-SSH-Triggern

SFTP-SSH-Trigger rufen das SFTP-Dateisystem ab und suchen nach jeder Datei, die seit dem letzten Abruf geändert wurde. Bei einigen Tools können Sie den Zeitstempel beibehalten, wenn sich die Dateien ändern. In diesen Fällen müssen Sie diese Funktion deaktivieren, sodass der Trigger arbeiten kann. Hier sind einige gängige Einstellungen:

| SFTP-Client | Aktion |
|-------------|--------|
| Winscp | Navigieren Sie zu **Optionen** > **Voreinstellungen** > **Übertragen** > **Bearbeiten** > **Zeitstempel beibehalten** > **Deaktivieren**. |
| FileZilla | Wechseln Sie zu **Übertragung** > **Änderungszeitpunkt der übertragenen Dateien beibehalten** > **Deaktivieren**. |
|||

Wenn ein Trigger eine neue Datei findet, überprüft er, ob die neue Datei vollständig ist und nicht nur teilweise geschrieben wurde. Zum Beispiel werden bei einer Datei möglicherweise gerade Änderungen vorgenommen, wenn der Trigger den Dateiserver überprüft. Um zu vermeiden, dass eine nur zum Teil geschriebene Datei zurückgegeben wird, vermerkt der Trigger den Zeitstempel für die Datei mit den kürzlichen Änderungen, gibt diese Datei jedoch nicht sofort zurück. Der Trigger gibt die Datei erst dann zurück, wenn der Server erneut abgerufen wird. Dieses Verhalten kann in manchen Fällen zu Verzögerungen führen, die bis zu zweimal länger als das Abrufintervall des Triggers sein können.

<a name="convert-to-openssh"></a>

## <a name="convert-putty-based-key-to-openssh"></a>PuTTY-basierten Schlüssel in OpenSSH konvertieren

Wenn Ihr privater Schlüssel im PuTTY-Format vorliegt das die Dateinamenerweiterung PPK (PuTTY Private Key) verwendet, konvertieren Sie zunächst den Schlüssel in das OpenSSH-Format, das die Dateinamenerweiterung PEM (Privacy Enhanced Mail) verwendet.

### <a name="unix-based-os"></a>Unix-basiertes Betriebssystem

1. Wenn die PuTTY-Tools nicht bereits auf Ihrem System installiert sind, holen Sie dies jetzt nach, z. B.:

   `sudo apt-get install -y putty`

1. Führen Sie diesen Befehl aus, der eine Datei erstellt, die Sie mit dem SFTP-SSH-Connector verwenden können:

   `puttygen <path-to-private-key-file-in-PuTTY-format> -O private-openssh -o <path-to-private-key-file-in-OpenSSH-format>`

   Beispiel:

   `puttygen /tmp/sftp/my-private-key-putty.ppk -O private-openssh -o /tmp/sftp/my-private-key-openssh.pem`

### <a name="windows-os"></a>Windows-Betriebssystem

1. Wenn Sie dies noch nicht getan haben, [laden Sie das neueste PuTTY-Generator-Tool (PuTTYgen.exe) herunter](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html), und starten Sie dann das Tool.

1. Wählen Sie in diesem Bildschirm **Laden** aus.

   ![„Laden“ auswählen](./media/connectors-sftp-ssh/puttygen-load.png)

1. Navigieren Sie zu Ihrer privaten Schlüsseldatei im PuTTY-Format, und wählen Sie **Öffnen** aus.

1. Wählen Sie im Menü **Konvertierungen** den Eintrag **OpenSSH-Schlüssel exportieren** aus.

   ![„OpenSSH-Schlüssel exportieren“ auswählen](./media/connectors-sftp-ssh/export-openssh-key.png)

1. Speichern Sie die private Schlüsseldatei mit der Dateinamenerweiterung `.pem`.

## <a name="considerations"></a>Überlegungen

In diesem Abschnitt werden Überlegungen zu den Triggern und Aktionen dieses Connectors beschrieben.

<a name="create-file"></a>

### <a name="create-file"></a>Datei erstellen

Zum Erstellen einer Datei auf Ihrem SFTP-Server können Sie die SFTP-SSH-Aktion **Datei erstellen** verwenden. Wenn die Datei mit dieser Aktion erstellt wird, ruft der Logic Apps-Dienst auch automatisch Ihren SFTP-Server auf, um die Metadaten der Datei abzurufen. Wenn Sie jedoch die neu erstellte Datei verschieben, bevor der Logic Apps-Dienst den Aufruf zum Abrufen der Metadaten ausführen kann, erhalten Sie einen `404`Fehler mit der Meldung `'A reference was made to a file or folder which does not exist'`. Um das Lesen der Dateimetadaten nach dem Erstellen der Datei zu überspringen, führen Sie die Schritte zum [Hinzufügen der Eigenschaft **Alle Dateimetadaten abrufen** und Festlegen der Eigenschaft auf **Keine**](#file-does-not-exist) aus.

<a name="connect"></a>

## <a name="connect-to-sftp-with-ssh"></a>Herstellen einer Verbindung zu SFTP mit SSH

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und öffnen Sie Ihre Logik-App im Logik-App-Designer, sofern sie nicht bereits geöffnet ist.

1. Geben Sie für leere Logik-Apps im Suchfeld `sftp ssh` als Filter ein. Wählen Sie in der Triggerliste den gewünschten Trigger aus.

   Oder

   Wählen Sie für vorhandene Logik-Apps im letzten Schritt zum Hinzufügen einer Aktion die Option **Neuer Schritt** aus. Geben Sie im Suchfeld den Begriff `sftp ssh` als Filter ein. Wählen Sie in der Liste mit den Aktionen die gewünschte Aktion aus.

   Wenn Sie zwischen Schritten eine Aktion einfügen möchten, bewegen Sie den Mauszeiger über den Pfeil zwischen den Schritten. Wählen Sie das angezeigte Pluszeichen ( **+** ) aus, und wählen Sie dann **Aktion hinzufügen** aus.

1. Geben Sie die erforderlichen Informationen für Ihre Verbindung ein.

   > [!IMPORTANT]
   >
   > Wenn Sie Ihren privaten SSH-Schlüssel in der Eigenschaft **SSH private key** eingeben, führen Sie diese zusätzlichen Schritte aus, um sicherzustellen, dass Sie den vollständigen und korrekten Wert für diese Eigenschaft angeben. Ein ungültiger Schlüssel führt zu einem Verbindungsfehler.

   Sie können jeden Text-Editor verwenden. Hier finden Sie Beispielschritte, die zeigen, wie Sie Ihren Schlüssel am Beispiel von Notepad.exe korrekt kopieren und einfügen.

   1. Öffnen Sie die Datei für den privaten SSH-Schlüssel in einem Text-Editor. Bei diesen Schritten verwenden wir als Beispiel Editor.

   1. Wählen Sie im Editor-Menü **Bearbeiten** die Option **Alles markieren** aus.

   1. Klicken Sie auf **Bearbeiten** > **Kopieren**.

   1. Fügen Sie in den von Ihnen hinzugefügten SFTP-SSH-Trigger oder die von Ihnen hinzugefügte Aktion den kopierter Schlüssel *complete* in die Eigenschaft **SSH private key** ein, die mehrere Zeilen unterstützt.  **_Stellen Sie sicher, dass Sie den Schlüssel einfügen._* _ _*_Sie dürfen den Schlüssel nicht manuell eingeben oder bearbeiten._*_

1. Wenn Sie die Verbindungsdetails eingegeben haben, wählen Sie _*Erstellen** aus.

1. Geben Sie jetzt die erforderlichen Details für Ihren ausgewählten Trigger oder Ihre ausgewählte Aktion an, und fahren Sie mit dem Erstellen Ihres Logik-App-Workflows fort.

<a name="change-chunk-size"></a>

## <a name="override-chunk-size"></a>Außerkraftsetzen der Blockgröße

Um das adaptive Standardverhalten außer Kraft zu setzen, bei dem Blöcke verwendet werden, können Sie eine konstante Blockgröße zwischen 5 MB und 50 MB angeben.

1. Wählen Sie in der rechten oberen Ecke der Aktion die Schaltfläche mit den Auslassungspunkten ( **...** ) und dann **Einstellungen** aus.

   ![Öffnen der SFTP-SSH-Einstellungen](./media/connectors-sftp-ssh/sftp-ssh-connector-setttings.png)

1. Geben Sie unter **Inhaltsübertragung** in der Eigenschaft **Blockgröße** einen Integerwert zwischen `5` und `50` ein, z. B.: 

   ![Angeben der stattdessen zu verwendenden Blockgröße](./media/connectors-sftp-ssh/specify-chunk-size-override-default.png)

1. Klicken Sie auf **Fertig** , wenn Sie fertig sind.

## <a name="examples"></a>Beispiele

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>SFTP – SSH-Trigger: When a file is added or modified (Wenn eine Datei hinzugefügt oder geändert wird)

Dieser Trigger startet einen Logik-App-Workflow, wenn auf einem SFTP-Server eine Datei hinzugefügt oder geändert wird. Sie können beispielsweise eine Bedingung hinzufügen, die den Inhalt der Datei überprüft und den Inhalt basierend darauf abruft, ob er eine bestimmte Bedingung erfüllt. Sie können dann eine Aktion hinzufügen, die den Inhalt der Datei abruft und in einem Ordner auf dem SFTP-Server ablegt.

**Beispiel für Unternehmen** : Sie können mit diesem Trigger beispielsweise einen SFTP-Ordner auf neue Dateien überwachen, die Kundenbestellungen darstellen. Anschließend können Sie eine SFTP-Aktion wie etwa **Dateiinhalt abrufen** verwenden, um den Inhalt einer Bestellung zur weiteren Verarbeitung abzurufen und in einer Bestelldatenbank zu speichern.

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-file-content-using-path"></a>SFTP – SSH-Aktion: Dateiinhalt anhand des Pfads abrufen

Diese Aktion ruft den Inhalt einer Datei auf einem SFTP-Server durch Angeben des Dateipfads ab. Sie können z.B. den Trigger aus dem vorherigen Beispiel sowie eine Bedingung hinzufügen, die vom Dateiinhalt erfüllt werden muss. Wenn die Bedingung als TRUE ausgewertet wird, kann die Aktion ausgeführt werden, die den Inhalt abruft.

<a name="troubleshooting-errors"></a>

## <a name="troubleshoot-errors"></a>Beheben von Fehlern

In diesem Abschnitt werden mögliche Lösungen für häufige Fehler oder Probleme beschrieben.

<a name="file-does-not-exist"></a>

### <a name="404-error-a-reference-was-made-to-a-file-or-folder-which-does-not-exist"></a>404-Fehler: „Es wurde auf eine Datei oder einen Ordner verwiesen, die oder der nicht vorhanden ist.“

Dieser Fehler kann auftreten, wenn Ihre Logik-App über die SFTP-SSH-Aktion **Datei erstellen** eine neue Datei auf Ihrem SFTP-Server erstellt, diese dann aber sofort verschoben wird, bevor der Logic Apps-Dienst die Metadaten der Datei abrufen kann. Wenn Ihre Logik-App die Aktion **Datei erstellen** ausführt, ruft der Logic Apps-Dienst auch automatisch Ihren SFTP-Server auf, um die Metadaten der Datei abzurufen. Wenn die Datei jedoch verschoben wird, kann der Logic Apps-Dienst sie nicht mehr finden, daher erhalten Sie die Fehlermeldung `404`.

Wenn Sie das Verschieden der Datei nicht vermeiden oder verzögern können, können Sie das Lesen der Dateimetadaten nach der Dateierstellung überspringen, indem Sie die folgenden Schritte ausführen:

1. Öffnen Sie in der Aktion **Datei erstellen** die Liste **Neuen Parameter hinzufügen** , wählen Sie die Eigenschaft **Alle Dateimetadaten abrufen** aus, und legen Sie den Wert auf **Nein** fest.

1. Wenn Sie diese Dateimetadaten zu einem späteren Zeitpunkt benötigen, können Sie die Aktion **Dateimetadaten abrufen** verwenden.

<a name="connection-attempt-failed"></a>

### <a name="504-error-a-connection-attempt-failed-because-the-connected-party-did-not-properly-respond-after-a-period-of-time-or-established-connection-failed-because-connected-host-has-failed-to-respond-or-request-to-the-sftp-server-has-taken-more-than-000030-seconds"></a>Fehler 504: „Ein Verbindungsversuch ist fehlgeschlagen, da die Gegenstelle nach einer bestimmten Zeitspanne nicht ordnungsgemäß reagiert hat, oder die hergestellte Verbindung fehlerhaft war, da der verbundene Host nicht reagiert hat“ oder „Die Anforderung an den SFTP-Server dauerte länger als '00:00:30' Sekunden“.

Dieser Fehler kann auftreten, wenn Ihre Logik-App nicht in der Lage ist, erfolgreich eine Verbindung mit dem SFTP-Server herzustellen. Es gibt verschiedene mögliche Ursachen für dieses Problem. Probieren Sie daher die folgenden Optionen für die Problembehandlung aus:

* Das Verbindungstimeout beträgt 20 Sekunden. Überprüfen Sie, ob die Leistung Ihres SFTP-Servers ausreicht und zwischengeschaltete Geräte (z. B. Firewalls) keinen Overhead erzeugen. 

* Wenn Sie eine Firewall eingerichtet haben, müssen Sie die **IP-Adresse des verwalteten Connectors** der Positivliste hinzufügen. Informationen zum Ermitteln der IP-Adressen für die Region Ihrer Logik-App finden Sie unter [Grenzwerte und Konfiguration für Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#multi-tenant-azure---outbound-ip-addresses).

* Wenn dieser Fehler nur zeitweilig auftritt, ändern Sie die Einstellung für die **Wiederholungsrichtlinie** in der SFTP-SSH-Aktion in eine Wiederholungsanzahl, die höher als die standardmäßigen vier Wiederholungsversuche ist.

* Überprüfen Sie, ob der SFTP-Server die Anzahl der Verbindungen von jeder IP-Adresse begrenzt. Wenn ein Grenzwert gilt, müssen Sie möglicherweise die Anzahl der parallelen Logik-App-Instanzen begrenzen.

* Erhöhen Sie die [**ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval)-Eigenschaft in der SSH-Konfiguration auf dem SFTP-Server auf etwa eine Stunde, um den Aufwand für die Verbindungsherstellung zu verringern.

* Sie können im SFTP-Serverprotokoll überprüfen, ob die Anforderung von der Logik-App den SFTP-Server erreicht hat. Wenn Sie weitere Informationen zum Konnektivitätsproblem benötigen, können Sie auch eine Netzwerkablaufverfolgung in Ihrer Firewall und auf dem SFTP-Server ausführen.

## <a name="connector-reference"></a>Connector-Referenz

Weitere technische Details zu diesem Connector, z. B. Trigger, Aktionen und Grenzwerte, wie sie in der Swagger-Datei des Connectors beschrieben werden, finden Sie auf der [Referenzseite des Connectors](/connectors/sftpwithssh/).

> [!NOTE]
> Für Logik-Apps in einer [Integrationsdienstumgebung (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) erfordert die mit ISE bezeichnete Version dieses Connectors Chunking, um stattdessen die [ISE-Nachrichtengrenzwerte](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) zu verwenden.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu anderen [Logic Apps-Connectors](../connectors/apis-list.md)
