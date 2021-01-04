---
title: Verwalten von Änderungsnachverfolgung und Bestand in Azure Automation
description: In diesem Artikel wird beschrieben, wie Sie Änderungsnachverfolgung und Bestand verwenden, um Änderungen an Software und Microsoft-Diensten in Ihrer Umgebung nachzuverfolgen.
services: automation
ms.subservice: change-inventory-management
ms.date: 11/02/2020
ms.topic: conceptual
ms.openlocfilehash: 99cdc4191320efb37b37e4ec38e808f3961a1207
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288740"
---
# <a name="manage-change-tracking-and-inventory"></a>Verwalten der Änderungsnachverfolgung und des Bestands

Wenn Sie eine neue Datei oder einen neuen Registrierungsschlüssel zur Nachverfolgung hinzufügen, aktiviert Azure Automation dieses Element für [Änderungsnachverfolgung und Bestand](overview.md). In diesem Artikel wird beschrieben, wie Nachverfolgung konfiguriert, Nachverfolgungsergebnisse überprüft und Warnungen behandelt werden, wenn Änderungen erkannt werden.

Bevor Sie die Verfahren in diesem Artikel verwenden, stellen Sie sicher, dass Sie „Änderungsnachverfolgung und Bestand“ über eines der folgenden Verfahren auf Ihren VMs aktiviert haben:

* [Aktivieren von „Änderungsnachverfolgung und Bestand“ über ein Automation-Konto](enable-from-automation-account.md)
* [Aktivieren von „Änderungsnachverfolgung und Bestand“ durch Durchsuchen im Azure-Portal](enable-from-portal.md)
* [Aktivieren von „Änderungsnachverfolgung und Bestand“ über ein Runbook](enable-from-runbook.md)
* [Aktivieren von „Änderungsnachverfolgung und Bestand“ über einen virtuellen Azure-Computer](enable-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>Beschränken des Bereichs für die Bereitstellung

Von „Änderungsnachverfolgung und Bestand“ wird eine Bereichskonfiguration innerhalb des Arbeitsbereichs verwendet, um die Zielcomputer für Änderungen anzugeben. Weitere Informationen finden Sie unter [Arbeiten mit Bereichskonfigurationen für „Änderungsnachverfolgung und Bestand“](manage-scope-configurations.md).

## <a name="track-files"></a>Dateien nachverfolgen

Sie können mit „Änderungsnachverfolgung und Bestand“ Änderungen an Dateien und Ordnern/Verzeichnissen verfolgen. In diesem Abschnitt erfahren Sie, wie Sie die Dateinachverfolgung unter Windows und Linux konfigurieren.

### <a name="configure-file-tracking-on-windows"></a>Konfigurieren der Dateinachverfolgung unter Windows

Führen Sie zum Konfigurieren der Dateinachverfolgung auf Windows-Computern die folgenden Schritte aus:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Wählen Sie im Azure-Portal **Alle Dienste** aus. Geben Sie in der Liste mit den Ressourcen **Automation** ein. Sobald Sie mit der Eingabe beginnen, werden Vorschläge in der Liste auf Grundlage Ihrer Eingabe gefiltert. Wählen Sie die Option **Automation-Konten**.

3. Wählen Sie in Ihrer Liste der Automation-Konten das Konto aus, das Sie beim Aktivieren von „Änderungsnachverfolgung und Bestand“ ausgewählt haben.

4. Wählen Sie in Ihrem Automation-Konto unter **Konfigurationsverwaltung** die Option **Änderungsnachverfolgung** aus.

5. Währen Sie **Einstellungen bearbeiten** (das Zahnradsymbol) aus.

6. Wählen Sie auf der Seite „Arbeitsbereichskonfiguration“ die Option **Windows-Dateien** aus, und klicken Sie dann auf **+ Hinzufügen** , um eine neue nachzuverfolgende Datei hinzuzufügen.

7. Geben Sie im Bereich „Windows-Datei für Änderungsnachverfolgung hinzufügen“ die Informationen zu der nachzuverfolgenden Datei bzw. dem Ordner ein, und klicken Sie auf **Speichern**. Die folgende Tabelle definiert die Eigenschaften, die Sie für die Informationen verwenden können.

    |Eigenschaft  |BESCHREIBUNG  |
    |---------|---------|
    |Aktiviert     | TRUE, wenn die Einstellung angewendet wird, andernfalls FALSE.        |
    |Item Name     | Anzeigename der nachzuverfolgenden Datei        |
    |Group     | Gruppenname für die logische Gruppierung von Dateien        |
    |Pfad eingeben     | Der für die Datei zu überprüfende Pfad, z. B. **c:\temp\\\*.txt**. Sie können auch Umgebungsvariablen verwenden, beispielsweise `%winDir%\System32\\\*.*`.       |
    |Pfadtyp     | Der Typ des Pfads. Mögliche Werte sind „Datei“ und „Ordner“.        |    
    |Rekursion     | TRUE, wenn ob beim Suchen nach dem nachzuverfolgenden Element die Rekursion verwendet wird, andernfalls FALSE.        |    
    |Dateiinhalt hochladen | „True“, um den Dateiinhalt von nachverfolgten Änderungen hochzuladen, andernfalls „False“.|

    Wenn Sie die Überwachung von Dateien und Ordnern mithilfe von Platzhaltern konfigurieren möchten, berücksichtigen Sie Folgendes:

    - Platzhalter werden zum Nachverfolgen mehrerer Dateien benötigt.
    - Platzhalter können nur im letzten Segment eines Pfads verwendet werden, z. B. „ *C:\Ordner\Datei* “ oder „ */etc/* .conf*“.
    - Wenn eine Umgebungsvariable einen ungültigen Pfad enthält, ist die Überprüfung zwar erfolgreich, bei der Ausführung der Inventur tritt jedoch ein Fehler für den Pfad auf.
    - Vermeiden Sie beim Festlegen des Pfads die Verwendung allgemeiner Pfade wie *c:* .**, da in diesem Fall zu viele Ordner durchlaufen werden müssen.

8. Stellen Sie sicher, dass Sie „True“ für **Dateiinhalt hochladen** angeben. Diese Einstellung ermöglicht die Nachverfolgung von Dateiinhalten für den angegebenen Dateipfad.

### <a name="configure-file-tracking-on-linux"></a>Konfigurieren der Dateinachverfolgung unter Linux

Führen Sie zum Konfigurieren der Dateinachverfolgung auf Linux-Computern die folgenden Schritte aus:

1. Währen Sie **Einstellungen bearbeiten** (das Zahnradsymbol) aus.

2. Wählen Sie auf der Seite „Arbeitsbereichskonfiguration“ die Option **Linux-Dateien** aus, und wählen Sie dann **+ Hinzufügen** aus, um eine neue nachzuverfolgende Datei hinzuzufügen.

3. Geben Sie auf der Seite **Linux-Datei für Änderungsnachverfolgung hinzufügen** die Informationen zu der Datei oder dem Verzeichnis ein, die bzw. das nachverfolgt werden soll, und wählen Sie dann **Speichern** aus. Die folgende Tabelle definiert die Eigenschaften, die Sie für die Informationen verwenden können.

    |Eigenschaft  |Beschreibung  |
    |---------|---------|
    |Aktiviert     | TRUE, wenn die Einstellung angewendet wird, andernfalls FALSE.        |
    |Item Name     | Anzeigename der nachzuverfolgenden Datei        |
    |Group     | Gruppenname für die logische Gruppierung von Dateien        |
    |Pfad eingeben     | Der zu überprüfende Pfad für die Datei, z. B. **/etc/*.conf**.       |
    |Pfadtyp     | Der Typ des Pfads. Mögliche Werte sind „Datei“ und „Verzeichnis“.        |
    |Rekursion     | TRUE, wenn ob beim Suchen nach dem nachzuverfolgenden Element die Rekursion verwendet wird, andernfalls FALSE.        |
    |Sudo verwenden     | „True“, um sudo bei der Suche nach dem Element zu verwenden, andernfalls „False“.         |
    |Links     | Die Einstellung, die bestimmt, wie symbolische Verknüpfungen beim Durchlaufen von Verzeichnissen behandelt werden sollen. Mögliche Werte:<br> „Ignore“: Symbolische Verknüpfungen werden ignoriert, und die referenzierten Dateien/Verzeichnisse werden nicht einbezogen.<br>„Follow“: Folgt den symbolischen Verknüpfungen bei der Rekursion und bindet auch die referenzierten Dateien/Verzeichnisse ein.<br>„Manage“: Folgt den symbolischen Verknüpfungen und ermöglicht das Ändern von zurückgegebenen Inhalten.<br>**Hinweis:** Die Option „Manage“ wird nicht empfohlen, da sie das Abrufen von Dateiinhalten nicht unterstützt.    |
    |Dateiinhalt hochladen | „True“, um den Dateiinhalt von nachverfolgten Änderungen hochzuladen, andernfalls „False“. |

4. Stellen Sie sicher, dass Sie **True** für **Dateiinhalt hochladen** angeben. Diese Einstellung ermöglicht die Nachverfolgung von Dateiinhalten für den angegebenen Dateipfad.

   ![Linux-Datei hinzufügen](./media/manage-change-tracking/add-linux-file.png)

## <a name="track-file-contents"></a>Dateiinhalt nachverfolgen

Die Nachverfolgung von Dateiinhalten ermöglicht Ihnen, den Inhalt einer Datei vor und nach einer verfolgten Änderung einzusehen. Die Funktion speichert den Dateiinhalt nach jeder Änderung in einem [Speicherkonto](../../storage/common/storage-account-overview.md). Im Folgenden finden Sie einige Regeln, die beim Nachverfolgen von Dateiinhalten einzuhalten sind:

* Für die Speicherung von Dateiinhalten ist ein Standardspeicherkonto unter Verwendung des Ressourcen-Manager-Bereitstellungsmodells erforderlich.
* Verwenden Sie keine Speicherkonten mit Premium- oder klassischem Bereitstellungsmodell. Weitere Informationen finden Sie unter [Informationen zu Azure-Speicherkonten](../../storage/common/storage-account-create.md).
* Sie können das Speicherkonto nur mit einem einzigen Automation-Konto verbinden.
* Änderungsnachverfolgung und Bestand muss in Ihrem Automation-Konto aktiviert sein.

### <a name="enable-tracking-for-file-content-changes"></a>Nachverfolgung von Dateiinhaltsänderungen aktivieren

Führen Sie die folgenden Schritte aus, um das Nachverfolgen von Änderungen an Dateiinhalten zu aktivieren:

1. Währen Sie **Einstellungen bearbeiten** (das Zahnradsymbol) aus.

2. Wählen Sie **Dateiinhalt** und dann **Link** aus. Durch diese Auswahl wird die Seite **Inhaltsspeicherort für Änderungsnachverfolgung hinzufügen** geöffnet.

   ![Hinzufügen des Inhaltsorts](./media/manage-change-tracking/enable.png)

3. Wählen Sie das Abonnement und das Speicherkonto aus, die für die Speicherung von Dateiinhalten verwendet werden sollen.

5. Wenn Sie die Nachverfolgung von Dateiinhalten für alle vorhandenen nachverfolgten Dateien aktivieren möchten, wählen Sie **Ein** für **Dateiinhalte für alle Einstellungen hochladen**. Sie können diese Einstellung für jeden Dateipfad nachträglich ändern.

   ![Speicherkonto festlegen](./media/manage-change-tracking/storage-account.png)

6. Änderungsnachverfolgung und Bestand zeigen Speicherkonto und SAS-URIs (Shared Access Signature) an, wenn die Nachverfolgung von Dateiinhaltsänderungen aktiviert ist. Die Signaturen laufen nach 365 Tagen ab, und Sie können Sie neu erstellen, indem Sie **Erneut generieren** auswählen.

   ![Auflisten von Kontoschlüsseln](./media/manage-change-tracking/account-keys.png)

### <a name="view-the-contents-of-a-tracked-file"></a>Anzeigen des Inhalts einer nachverfolgten Datei

Sobald Änderungsnachverfolgung und Bestand eine Änderung an einer nachverfolgten Datei erkennen, können Sie den Inhalt der Datei im Bereich „Änderungsdetails“ anzeigen.  

![Änderungen auflisten](./media/manage-change-tracking/change-list.png)

1. Wählen Sie auf der Seite **Nachverfolgung ändern** in Ihrem Automation-Konto eine Datei in der Liste der Änderungen aus, und wählen Sie dann **Änderungen am Dateiinhalt anzeigen** aus, um den Inhalt der Datei anzuzeigen. Im Änderungsdetailsbereich werden die Standarddateiinformationen von vorher und nachher für jede Eigenschaft angezeigt.

   ![Änderungsdetails](./media/manage-change-tracking/change-details.png)

2. Der Dateiinhalt wird in einer nebeneinander angeordneten Ansicht angezeigt. Sie können **Inline** auswählen, um eine Inlineansicht der Änderungen anzuzeigen.

## <a name="track-registry-keys"></a>Registrierungsschlüssel nachverfolgen

Führen Sie zum Konfigurieren der Nachverfolgung von Registrierungsschlüsseln auf Windows-Computern die folgenden Schritte aus:

1. Wählen Sie auf der Seite **Nachverfolgung ändern** in Ihrem Automation-Konto **Einstellungen bearbeiten**  (das Zahnradsymbol) aus.

2. Wählen Sie auf der Seite „Arbeitsbereichskonfiguration“ **Windows-Registrierung** aus.

3. Wählen Sie **+ Hinzufügen** aus, um einen neuen Registrierungsschlüssel zur Nachverfolgung hinzuzufügen.

4. Geben Sie auf der Seite **Windows-Registrierung für Änderungsnachverfolgung hinzufügen** die Informationen zu dem Schlüssel ein, der nachverfolgt werden soll, und wählen Sie dann **Speichern** aus. Die folgende Tabelle definiert die Eigenschaften, die Sie für die Informationen verwenden können. Wenn Sie einen Registrierungspfad angeben, muss es sich um den Schlüssel und nicht um einen Wert handeln.

    |Eigenschaft  |Beschreibung  |
    |---------|---------|
    |Aktiviert     | „True“, wenn eine Einstellung angewendet wird, andernfalls „False“.        |
    |Item Name     | Anzeigename des nachzuverfolgenden Registrierungsschlüssels.        |
    |Group     | Gruppenname für die logische Gruppierung von Registrierungsschlüsseln.        |
    |Windows-Registrierungsschlüssel   | Schlüsselname mit Pfad, z. B. `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup`.      |

## <a name="search-logs-for-change-records"></a>Protokolle nach Änderungsdatensätzen durchsuchen

Sie können verschiedene Suchvorgänge in den Azure Monitor-Protokollen für Änderungsdatensätze durchführen. Klicken Sie bei geöffneter Seite „Änderungsnachverfolgung“ auf **Log Analytics** , um die Seite „Protokolle“ zu öffnen. Die folgende Tabelle enthält Beispiele für Protokollsuchen nach Änderungsdatensätzen.

|Abfrage  |BESCHREIBUNG  |
|---------|---------|
|`ConfigurationData`<br>&#124; `where ConfigDataType == "WindowsServices" and SvcStartupType == "Auto"`<br>&#124; `where SvcState == "Stopped"`<br>&#124; `summarize arg_max(TimeGenerated, *) by SoftwareName, Computer`         | Zeigt die aktuellen Bestandsdatensätze für Microsoft-Dienste an, die auf „Auto“ festgelegt, aber als „Beendet“ gemeldet wurden. Die Ergebnisse werden auf den aktuellen Datensatz für den angegebenen Softwarenamen und Computer beschränkt.    |
|`ConfigurationChange`<br>&#124; `where ConfigChangeType == "Software" and ChangeCategory == "Removed"`<br>&#124; `order by TimeGenerated desc`|Zeigt Änderungsdatensätze für entfernte Software an.|

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu Bereichskonfigurationen finden Sie unter [Arbeiten mit Bereichskonfigurationen für „Änderungsnachverfolgung und Bestand“](manage-scope-configurations.md).
* Wenn Sie Protokolle durchsuchen müssen, die in Azure Monitor-Protokollen gespeichert sind, finden Sie unter [Protokollsuchvorgänge in Azure Monitor-Protokollen](../../azure-monitor/log-query/log-query-overview.md) weitere Informationen.
* Wenn Sie mit den Bereitstellungen fertig sind, lesen Sie unter [„Änderungsnachverfolgung und Bestand“ entfernen](remove-feature.md) weiter.
* Informationen zum Löschen Ihrer VMs aus „Änderungsnachverfolgung und Bestand“ finden Sie unter [Entfernen von VMs aus Änderungsnachverfolgung und Bestand](remove-vms-from-change-tracking.md).
* Informationen zum Behandeln von Problemen bei diesem Feature finden Sie unter [Problembehandlung bei Änderungsnachverfolgung und Bestand](../troubleshoot/change-tracking.md).
