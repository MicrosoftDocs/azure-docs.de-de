---
title: Migrieren von Orchestrator zu Azure Automation
description: Beschreibt, wie Runbooks und Integrationspakete von System Center Orchestrator nach Azure Automation migriert werden.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ae47cba8f8e9a7cdf914c0b3ea5dfb9fa6c259a9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60738212"
---
# <a name="migrating-from-orchestrator-to-azure-automation-beta"></a>Migrieren von Orchestrator zu Azure Automation (Beta)
Runbooks in [System Center Orchestrator](https://technet.microsoft.com/library/hh237242.aspx) basieren auf Aktivitäten der Integrationspakete, die speziell für Orchestrator geschrieben wurden, während Runbooks in Azure Automation auf Windows PowerShell basieren.  [Grafische Runbooks](automation-runbook-types.md#graphical-runbooks) in Azure Automation haben ein ähnliches Aussehen wie Orchestrator-Runbooks, die enthaltenen Aktivitäten repräsentieren PowerShell-Cmdlets, untergeordnete Runbooks und Objekte.

Das [System Center Orchestrator Migration Toolkit](https://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) umfasst Tools, die Sie beim Konvertieren von Runbooks von Orchestrator nach Azure Automation unterstützen.  Zusätzlich zur Konvertierung der Runbooks selbst müssen Sie die Integrationspakete mit den von Runbooks verwendeten Aktivitäten mithilfe von Windows PowerShell-Cmdlets in Integrationsmodule konvertieren.  

Nachfolgend wird das grundlegende Verfahren zum Konvertieren von Orchestrator-Runbooks in Azure Automation gezeigt.  Jeder dieser Schritte wird in den nachstehenden Abschnitten ausführlich beschrieben.

1. Laden Sie das [System Center Orchestrator Migration Toolkit](https://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) herunter, das die in diesem Artikel beschriebenen Tools und Module enthält.
2. Importieren Sie das [Modul mit Standardaktivitäten](#standard-activities-module) in Azure Automation.  Dieses umfasst konvertierte Versionen der standardmäßigen Orchestrator-Aktivitäten, die von konvertierten Runbooks verwendet werden können.
3. Importieren Sie für die von Ihren Runbooks verwendeten Integrationspakete, die auf System Center zugreifen, die [System Center Orchestrator-Integrationsmodule](#system-center-orchestrator-integration-modules) in Azure Automation.
4. Konvertieren Sie benutzerdefinierte Integrationspakete und Integrationspakete von Drittanbietern mit dem [Integration Pack Converter](#integration-pack-converter) , und importieren Sie sie in Azure Automation.
5. Konvertieren Sie Orchestrator-Runbooks mit dem [Runbook Converter](#runbook-converter) , und installieren Sie sie in Azure Automation.
6. Erstellen Sie die erforderlichen Orchestrator-Ressourcen in Azure Automation manuell, da diese Ressourcen nicht von Runbook Converter konvertiert werden.
7. Konfigurieren Sie einen [Hybrid Runbook Worker](#hybrid-runbook-worker) in Ihrem lokalen Rechenzentrum, um die konvertierten Runbooks auszuführen, die auf lokale Ressourcen zugreifen werden.

## <a name="service-management-automation"></a>Service Management Automation (SMA)
[Service Management Automation](https://technet.microsoft.com/library/dn469260.aspx) (SMA) dient wie Orchestrator zum Speichern und Ausführen von Runbooks in Ihrem lokalen Datencenter und führt dieselben Integrationsmodule aus wie Azure Automation. Der [Runbook Converter](#runbook-converter) konvertiert Orchestrator-Runbooks in grafische Runbooks, diese werden jedoch in SMA nicht unterstützt.  Sie können in SMA dennoch das [Modul mit Standardaktivitäten](#standard-activities-module) und die [System Center Orchestrator-Integrationsmodule](#system-center-orchestrator-integration-modules) installieren, müssen jedoch Ihre [Runbooks manuell umschreiben](https://technet.microsoft.com/library/dn469262.aspx).

## <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker
Runbooks in Orchestrator werden auf einem Datenbankserver gespeichert und auf Runbookservern ausgeführt, beides in Ihrem lokalen Datencenter.  Runbooks in Azure Automation werden in der Azure-Cloud gespeichert und können mit einem [Hybrid-Runbook-Worker](automation-hybrid-runbook-worker.md)in Ihrem lokalen Datencenter ausgeführt werden.  Üblicherweise führen Sie aus Orchestrator konvertierte Runbooks in dieser Weise aus, da sie zur Ausführung auf lokalen Servern konzipiert sind.

## <a name="integration-pack-converter"></a>Integration Pack Converter
Der Integration Pack Converter konvertiert Integrationspakete, die mit dem [OIT (Orchestrator Integration Toolkit)](https://technet.microsoft.com/library/hh855853.aspx) erstellt wurden, in Integrationsmodule basierend auf Windows PowerShell, die in Azure Automation oder Service Management Automation importiert werden können.  

Wenn Sie den Integration Pack Converter ausführen, wird ein Assistent gestartet, der Ihnen die Auswahl einer Integrationspaketdatei (.oip) ermöglicht.  Der Assistent listet anschließend die im Integrationspaket enthaltenen Aktivitäten auf, und Sie können die zu migrierenden Aktivitäten auswählen.  Wenn Sie den Assistenten abschließen, wird ein Integrationsmodul erstellt, dass ein entsprechendes Cmdlet für jede Aktivität im ursprünglichen Integrationspaket enthält.

### <a name="parameters"></a>Parameter
Alle Eigenschaften einer Aktivität im Integrationspaket werden in Parameter des zugehörigen Cmdlets im Integrationsmodul konvertiert.  Windows PowerShell-Cmdlets enthalten einen Satz [allgemeiner Parameter](https://technet.microsoft.com/library/hh847884.aspx) , die für alle Cmdlets verwendet werden können.  Beispielsweise können über den Parameter "-Verbose" detaillierte Informationen zur Verwendung eines Cmdlets ausgegeben werden.  Die Cmdlets dürfen keinen Parameter aufweisen, die denselben Namen verwenden wie ein allgemeiner Parameter.  Wenn eine Aktivität über eine Eigenschaft mit demselben Namen wie ein allgemeiner Parameter verfügt, fordert der Assistent Sie auf, einen anderen Namen für den Parameter anzugeben.

### <a name="monitor-activities"></a>Überwachen von Aktivitäten
Runbooks für die Überwachung beginnen in Orchestrator mit einer [Überwachungsaktivität](https://technet.microsoft.com/library/hh403827.aspx) und werden fortlaufend ausgeführt, bis ein bestimmtes Ereignis ausgelöst wird.  Azure Automation bietet keine Unterstützung für Runbooks zur Überwachung, deshalb werden im Integrationspaket enthaltene Überwachungsaktivitäten nicht konvertiert.  Stattdessen wird im Integrationsmodul ein Platzhalter-Cmdlet für die Überwachungsaktivität erstellt.  Dieses Cmdlet hat keine Funktionalität, ermöglicht aber die Installation von konvertierten Runbooks, die die Überwachungsaktivität verwenden.  Dieses Runbook kann nicht in Azure Automation ausgeführt werden, kann jedoch zur Änderung installiert werden.

### <a name="integration-packs-that-cannot-be-converted"></a>Integrationspakete, die nicht konvertiert werden können
Integrationspakete, die nicht mit dem OIT erstellt wurden, können mit dem Integration Pack Converter nicht konvertiert werden. Es gibt auch einige Integrationspakete, die von Microsoft bereitgestellt werden, die derzeit nicht mit diesem Tool konvertiert werden können.  Konvertierte Versionen dieser Integrationspakete [stehen zum Download bereit](#system-center-orchestrator-integration-modules) , sodass sie in Azure Automation oder in Service Management Automation installiert werden können.

## <a name="standard-activities-module"></a>Modul mit Standardaktivitäten
Orchestrator umfasst einen Satz an [Standardaktivitäten](https://technet.microsoft.com/library/hh403832.aspx) , die nicht in einem Integrationspaket enthalten sind, aber von vielen Runbooks verwendet werden.  Das Modul mit Standardaktivitäten ist ein Integrationsmodul das eine Cmdlet-Entsprechung für jede dieser Aktivitäten umfasst.  Sie müssen dieses Integrationsmodul in Azure Automation installieren, bevor Sie konvertierte Runbooks importieren, die eine Standardaktivität verwenden.

Zusätzlich zur Unterstützung von konvertierten Runbooks können die Cmdlets im Modul mit Standardaktivitäten von einem Benutzer mit Orchestrator-Erfahrung dazu verwendet werden, neue Runbooks in Azure Automation zu erstellen.  Wenngleich die Funktionalität aller Standardaktivitäten mithilfe von Cmdlets ausgeführt werden kann, weicht die Funktionsweise möglicherweise ab.  Die Cmdlets im konvertierten Modul mit Standardaktivitäten arbeiten in gleicher Weise wie die entsprechenden Aktivitäten und verwenden dieselben Parameter.  Dies kann den Erstellern von vorhandenen Orchestrator-Runbooks beim Wechsel auf Azure Automation-Runbooks helfen.

## <a name="system-center-orchestrator-integration-modules"></a>System Center Orchestrator-Integrationsmodule
Microsoft stellt [Integrationspakete](https://technet.microsoft.com/library/hh295851.aspx) zum Erstellen von Runbooks zur Automatisierung von System Center-Komponenten und anderen Produkten bereit.  Einige diese Integrationspakete basieren derzeit auf dem OIT, können derzeit jedoch aufgrund von bekannten Problemen nicht in Integrationsmodule konvertiert werden.  [System Center Orchestrator-Integrationsmodule](https://www.microsoft.com/download/details.aspx?id=49555) enthalten konvertierte Versionen dieser Integrationspakete, die in Azure Automation und Service Management Automation importiert werden können.  

Aktualisierte Versionen der Integrationspakete basierend auf dem OIT, die mit dem Integration Pack Converter konvertiert werden können, werden von der RTM-Version dieses Tools veröffentlicht.  Sie erhalten auch Unterstützung bei der Konvertierung von Runbooks mit Aktivitäten aus den Integrationspaketen, die nicht auf OIT basieren.

## <a name="runbook-converter"></a>Runbook Converter
Der Runbook Converter konvertiert Orchestrator-Runbooks in [grafische Runbooks](automation-runbook-types.md#graphical-runbooks), die in Azure Automation importiert werden können.  

Runbook Converter wird als PowerShell-Modul mit einem Cmdlet namens **ConvertFrom-SCORunbook** implementiert, das die Konvertierung durchführt.  Wenn Sie das Tool installieren, wird eine Verknüpfung zu einer PowerShell-Sitzung erstellt, mit der das Cmdlet geladen wird.   

Nachfolgend wird das grundlegende Verfahren zum Konvertieren eines Orchestrator-Runbooks und zum Importieren in Azure Automation erläutert.  Die folgenden Abschnitte enthalten weitere Informationen zum Verwenden des Tools und zum Arbeiten mit konvertierten Runbooks.

1. Exportieren Sie ein oder mehrere Runbooks aus Orchestrator.
2. Rufen Sie Integrationsmodule für alle Aktivitäten im Runbook ab.
3. Konvertieren Sie die Orchestrator-Runbooks in die exportierte Datei.
4. Überprüfen Sie die Informationen in Protokollen, um die Konvertierung zu überprüfen und um alle erforderlichen manuellen Aufgaben zu ermitteln.
5. Importieren Sie konvertierte Runbooks in Azure Automation.
6. Erstellen Sie alle erforderlichen Ressourcen in Azure Automation.
7. Bearbeiten Sie das Runbook in Azure Automation, um alle erforderlichen Aktivitäten zu ändern.

### <a name="using-runbook-converter"></a>Verwenden von Runbook Converter
Die Syntax für **ConvertFrom-SCORunbook** lautet wie folgt:

```powershell
ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string>
```

* RunbookPath – Pfad zur Exportdatei mit den zu konvertierenden Runbooks.
* Modul – Durch Trennzeichen getrennte Liste der Integrationsmodule mit Aktivitäten in den Runbooks.
* OutputFolder – Pfad zum Ordner, in dem konvertierte grafische Runbooks erstellt werden.

Der folgende Beispielbefehl konvertiert die Runbooks in eine Exportdatei namens **MyRunbooks.ois_export**.  Diese Runbooks verwenden Active Directory und Data Protection Manager-Integrationspakete.

```powershell
ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks"
```

### <a name="log-files"></a>Protokolldateien
Der Runbook Converter erstellt die folgenden Protokolldateien am gleichen Speicherort wie das konvertierte Runbook.  Wenn die Dateien bereits vorhanden sind, werden sie mit den Informationen aus der letzten Konvertierung überschrieben.

| Datei | Inhalt: |
|:--- |:--- |
| Runbook Converter - Progress.log |Ausführliche Konvertierungsschritte einschließlich Informationen für jede erfolgreich konvertierte Aktivität und Warnung für jede nicht konvertierte Aktivität. |
| Runbook Converter - Summary.log |Zusammenfassung der letzten Konvertierung einschließlich Warnungen und Folgeaufgaben, die Sie durchführen müssen, z. B. Erstellen einer Variablen für das konvertierte Runbook. |

### <a name="exporting-runbooks-from-orchestrator"></a>Exportieren von Runbooks aus Orchestrator
Der Runbook Converter arbeitet mit einer Exportdatei aus Orchestrator, die ein oder mehrere Runbooks enthält.  Es wird ein entsprechendes Azure Automation-Runbook für jedes Orchestrator-Runbook in der Exportdatei erstellt.  

Klicken Sie zum Exportieren aus Orchestrator mit der rechten Maustaste auf den Namen des Runbooks in Runbook Designer, und wählen Sie **Exportieren**aus.  Um alle Runbooks in einem Ordner zu exportieren, klicken Sie mit der rechten Maustaste auf den Namen des Ordners, und wählen Sie **Exportieren**aus.

### <a name="runbook-activities"></a>Runbookaktivitäten
Der Runbook Converter konvertiert jede Aktivität im Orchestrator-Runbook in eine entsprechende Aktivität in Azure Automation.  Für die Aktivitäten, die nicht konvertiert werden können, wird eine Platzhalteraktivität im Runbook mit Warntext erstellt.  Nachdem Sie das konvertierte Runbook in Azure Automation importiert haben, müssen Sie eine dieser Aktivitäten mit gültigen Aktivitäten ersetzen, die die erforderliche Funktionalität ausführen.

Alle Orchestrator-Aktivitäten im [Modul mit Standardaktivitäten](#standard-activities-module) werden konvertiert.  Es gibt einige Orchestrator-Standardaktivitäten, die sich jedoch nicht in diesem Modul befinden und die nicht konvertiert werden.  **Plattformereignis senden** verfügt beispielsweise über kein Azure Automation-Äquivalent, da das Ereignis Orchestrator-spezifisch ist.

[Überwachungsaktivitäten](https://technet.microsoft.com/library/hh403827.aspx) werden nicht konvertiert, da für sie kein Äquivalent in Azure Automation vorhanden ist.  Die Ausnahme sind Überwachungsaktivitäten in [konvertierten Integrationspaketen](#integration-pack-converter) , die in die Platzhalteraktivität konvertiert werden.

Jede Aktivität aus einem [konvertierten Integrationspaket](#integration-pack-converter) wird konvertiert, wenn Sie den Pfad zum Integrationsmodul mit dem **Modules** -Parameter angeben.  Für System Center-Integrationspakete können Sie die [System Center Orchestrator-Integrationsmodule](#system-center-orchestrator-integration-modules)verwenden.

### <a name="orchestrator-resources"></a>Orchestrator-Ressourcen
Der Runbook Converter konvertiert nur Runbooks, keine anderen Orchestrator-Ressourcen wie z. B. Leistungsindikatoren, Variablen oder Verbindungen.  Leistungsindikatoren werden in Azure Automation nicht unterstützt.  Variablen und Verbindungen werden unterstützt; Sie müssen sie jedoch manuell erstellen.  Die Protokolldateien weisen Sie darauf hin, dass das Runbook solche Ressourcen erfordert, und geben entsprechende Ressourcen an, die Sie für eine ordnungsgemäße Funktionsweise in Azure Automation für das konvertierte Runbook erstellen müssen.

Ein Runbook kann beispielsweise eine Variable verwenden, um einen bestimmten Wert in einer Aktivität auszufüllen.  Das konvertierte Runbook konvertiert die Aktivität und gibt eine Variablenressource in Azure Automation mit dem gleichen Namen wie die Orchestrator-Variable an.  Dies wird in der **Runbook Converter - Summary.log** -Datei vermerkt, die nach der Konvertierung erstellt wird.  Sie müssen diese Variablenressource manuell in Azure Automation erstellen, bevor Sie das Runbook verwenden.

### <a name="input-parameters"></a>Eingabeparameter
Runbooks in Orchestrator akzeptieren Eingabeparameter mit der Aktivität **Daten initialisieren** .  Wenn das konvertierte Runbook diese Aktivität enthält, wird im Azure Automation-Runbook ein [Eingabeparameter](automation-graphical-authoring-intro.md#runbook-input-and-output) für jeden Parameter in der Aktivität erstellt.  Eine [Workflowskriptsteuerelement](automation-graphical-authoring-intro.md#activities) -Aktivität wird im konvertierten Runbook erstellt, das jeden Parameter abruft und zurückgibt.  Alle Aktivitäten im Runbook, die einen Eingabeparameter verwenden, verweisen auf die Ausgabe dieser Aktivität.

Der Grund für die Nutzung dieser Strategie ist eine optimale Spiegelung der Funktionalität im Orchestrator-Runbook.  Aktivitäten in neuen grafischen Runbooks sollten mithilfe einer Runbook-Eingabedatenquelle direkt auf Eingabeparameter verweisen.

### <a name="invoke-runbook-activity"></a>Aufrufen einer Runbookaktivität
Runbooks in Orchestrator starten andere Runbooks mit der Aktivität **Runbook aufrufen** . Wenn das konvertierte Runbook diese Aktivität enthält und die Option **Auf Abschluss warten** festgelegt ist, wird dafür eine Runbookaktivität im konvertierten Runbook erstellt.  Wenn die Option **Auf Abschluss warten** nicht festgelegt ist, wird eine Workflowskriptaktivität erstellt, die **Start-AzureAutomationRunbook** verwendet, um das Runbook zu starten.  Nachdem Sie das konvertierte Runbook in Azure Automation importiert haben, müssen Sie diese Aktivität mit den in der Aktivität angegebenen Angaben ändern.

## <a name="related-articles"></a>Verwandte Artikel
* [System Center 2012 – Orchestrator](https://technet.microsoft.com/library/hh237242.aspx)
* [Service Management Automation](https://technet.microsoft.com/library/dn469260.aspx)
* [Hybrid-Runbook-Worker](automation-hybrid-runbook-worker.md)
* [Orchestrator-Standardaktivitäten](https://technet.microsoft.com/library/hh403832.aspx)
* [Herunterladen des System Center Orchestrator Migration Toolkit](https://www.microsoft.com/en-us/download/details.aspx?id=47323)

