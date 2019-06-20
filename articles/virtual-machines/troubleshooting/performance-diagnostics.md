---
title: Leistungsdiagnose für virtuelle Azure-Computer | Microsoft-Dokumentation
description: Hier finden Sie eine Einführung in die Azure-Leistungsdiagnose für Windows.
services: virtual-machines-windows'
documentationcenter: ''
author: anandhms
manager: cshepard
editor: przlplx
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 9/20/2018
ms.author: anandh
ms.openlocfilehash: c2089f9f6267f318dafe641a6a5b22e7e87427ca
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60308118"
---
# <a name="performance-diagnostics-for-azure-virtual-machines"></a>Leistungsdiagnose für virtuelle Azure-Computer

Das Leistungsdiagnosetool hilft Ihnen dabei, Leistungsprobleme zu beheben, die sich auf virtuelle Windows-Computer auswirken können. Zu den unterstützen Szenarien für die Problembehandlung gehören schnelle Überprüfungen bekannter Probleme und bewährter Methoden sowie komplexe Probleme durch geringe VM-Leistung oder hohe Auslastung von CPU, Datenträger-Speicherplatz oder Arbeitsspeicher. 

Sie können die Leistungsdiagnose direkt über das Azure-Portal ausführen, in dem Sie auch Erkenntnisse sowie Berichte zu verschiedenen Protokollen, umfangreichen Konfigurationen und Diagnosedaten überprüfen können. Es empfiehlt sich, die Leistungsdiagnose auszuführen und die Erkenntnisse und Diagnosedaten zu überprüfen, bevor Sie sich an den Microsoft-Support wenden.

> [!NOTE]
> Die Leistungsdiagnose wird zurzeit auf Windows-VMs unterstützt, auf denen .NET SDK 4.5 oder höher installiert ist. Die Schritte zur Ausführung der Leistungsdiagnose auf klassischen VMs finden Sie unter [VM-Erweiterung für Azure-Leistungsdiagnose](performance-diagnostics-vm-extension.md).

### <a name="supported-operating-systems"></a>Unterstützte Betriebssysteme
Windows 10, Windows 8, Windows 8 Enterprise, Windows 8 Pro, Windows 8.1, Windows Server 2016, Windows Server 2012, Windows Server 2012 Datacenter, Windows Server 2012 R2, Windows Server 2012 R2 Datacenter, Windows Server 2012 R2 Standard, Windows Server 2012 Standard, Windows Server 2008 R2, Windows Server 2008 R2 Datacenter, Windows Server 2008 R2 Enterprise, Windows Server 2008 R2 Foundation, Windows Server 2008 R2 SP1, Windows Server 2008 R2 Standard.

## <a name="install-and-run-performance-diagnostics-on-your-vm"></a>Installieren und Ausführen der Leistungsdiagnose auf Ihrer VM
Die Leistungsdiagnose installiert eine VM-Erweiterung, die ein Diagnosetool namens [PerfInsights](https://aka.ms/perfinsights) ausführt. Um die Leistungsdiagnose zu installieren und auszuführen, gehen Sie folgendermaßen vor:
1.  Wählen Sie in der linken Spalte **Virtuelle Computer** aus.
1.  Wählen Sie aus der Liste der VM-Namen den virtuellen Computer aus, auf dem Sie die Diagnose ausführen möchten.
1.  Wählen Sie in der rechten Spalte der Befehle die Option **Leistungsdiagnose** aus.

    ![Screenshot: Azure-Portal, Schaltfläche „Leistungsdiagnose installieren“ hervorgehoben](media/performance-diagnostics/performance-diagnostics-install.png)

    > [!NOTE]
    > In diesem Screenshot ist das Blatt mit den VM-Namen ausgeblendet.
1. Wählen Sie ein Speicherkonto aus (optional).

    Wenn Sie die Ergebnisse der Leistungsdiagnose für mehrere VMs in einem einzigen Speicherkonto speichern, können Sie durch Klicken auf die Schaltfläche **Einstellungen** auf der Symbolleiste ein Speicherkonto auswählen. Klicken Sie auf die Schaltfläche **OK**, wenn Sie das Speicherkonto ausgewählt haben.

    Wenn Sie kein Speicherkonto angeben, wird standardmäßig ein neues Speicherkonto erstellt.

    ![Screenshot: Blatt „Leistungsdiagnose“, Symbolleistenschaltfläche „Einstellungen“ hervorgehoben](media/performance-diagnostics/settings-button.png)

    ![Screenshot: Speicherkontoauswahl auf dem Blatt mit den Einstellungen für die Leistungsdiagnose](media/performance-diagnostics/select-storage-account.png)

1. Klicken Sie auf die Schaltfläche **Leistungsdiagnose installieren**.
1. Aktivieren Sie das Kontrollkästchen **Diagnose ausführen**, wenn Sie nach Abschluss der Installation eine Diagnose ausführen möchten. Wenn Sie diese Option aktivieren, können Sie das Szenario für die Leistungsanalyse und die entsprechenden Optionen auswählen.

    ![Screenshot: Schaltfläche „Leistungsdiagnose installieren“](media/performance-diagnostics/install-diagnostics-button.png)

## <a name="select-an-analysis-scenario-to-run"></a>Auswählen eines auszuführenden Analyseszenarios

Im Azure-Portal stehen die folgenden Analyseszenarien zur Verfügung. Wählen Sie je nach auftretendem Leistungsproblem eine geeignete Analyseoption aus. Wählen Sie die Dauer und die Ablaufverfolgungsoptionen für die Analyse aus.

* **Schnelle Leistungsanalyse**  
    Überprüft die VM auf bekannte Probleme, analysiert bewährte Methoden und sammelt Diagnosedaten. Die Analyse dauert mehrere Minuten. [Weitere Informationen](https://aka.ms/perfinsights/quick)

* **Leistungsanalyse**  
    Umfasst alle Überprüfungen in der schnellen Leistungsanalyse und überwacht einen hohen Ressourcenverbrauch. Verwenden Sie diese Version, um allgemeine Leistungsprobleme zu beheben, beispielsweise hohe Auslastung von CPU, Arbeitsspeicher und Datenträgerspeicher. Die Analyse nimmt je nach ausgewählter Dauer 30 Sekunden bis 15 Minuten in Anspruch. [Weitere Informationen](https://aka.ms/perfinsights/vmslow) 
    
* **Erweiterte Leistungsanalyse**  
    Enthält alle Überprüfungen der Leistungsanalyse und erfasst mindestens eine der Ablaufverfolgungen, wie in den folgenden Abschnitten beschrieben. Verwenden Sie dieses Szenario, um komplexe Probleme zu beheben, die zusätzliche Ablaufverfolgungen erfordern. Je nach Größe der VM und den ausgewählten Ablaufverfolgungsoptionen erhöht die Ausführung dieses Szenarios über einen längeren Zeitraum die Gesamtgröße der Diagnoseausgabe. Die Ausführung der Analyse nimmt je nach ausgewählter Dauer 30 Sekunden bis 15 Minuten in Anspruch. [Weitere Informationen](https://aka.ms/perfinsights/advanced) 
    
* **Azure Files-Analyse**  
    Enthält alle Überprüfungen der Leistungsanalyse und erfasst eine Netzwerkablaufverfolgung sowie SMB-Indikatoren. Verwenden Sie dieses Szenario, um Probleme mit der Leistung von Azure Files zu beheben. Die Ausführung der Analyse nimmt je nach ausgewählter Dauer 30 Sekunden bis 15 Minuten in Anspruch. [Weitere Informationen](https://aka.ms/perfinsights/azurefiles)


![Screenshot: Bereich „Diagnose ausführen“ auf dem Blatt „Leistungsdiagnose“](media/performance-diagnostics/run-diagnostics-pane.png)

### <a name="provide-symptoms-optional"></a>Angeben von Symptomen (optional)
Wählen Sie Symptome aus der Liste aus, oder fügen Sie neue Symptome hinzu. Dies hilft uns bei der zukünftigen Verbesserung der Analyse. 

### <a name="provide-support-request-number-if-available-optional"></a>Angeben der Supportanfragenummer, sofern verfügbar (optional)
Wenn Sie mit einem Microsoft-Supporttechniker an einem vorhandenen Supportticket arbeiten, geben Sie die Nummer des Supporttickets an. 

### <a name="review-the-privacy-policy-and-legal-terms-and-select-the-check-box-to-acknowledge-required"></a>Überprüfen der Datenschutzrichtlinie und rechtlichen Bestimmungen und Aktivieren des Kontrollkästchens zur Bestätigung (erforderlich)
Um die Diagnose auszuführen, müssen Sie den rechtlichen Bestimmungen zustimmen und die Datenschutzrichtlinie akzeptieren.

### <a name="select-ok-to-run-the-diagnostics"></a>Klicken auf „OK“, um die Diagnose auszuführen 
Wenn die Installation der Leistungsdiagnose startet, wird eine Benachrichtigung angezeigt. Nach Abschluss der Installation werden Sie in einer Benachrichtigung darüber informiert, dass die Installation erfolgreich war. Danach wird die ausgewählte Analyse über den angegebenen Zeitraum ausgeführt. Dies ist ein geeigneter Zeitpunkt, um das Leistungsproblem zu reproduzieren, sodass die Diagnosedaten zum richtigen Zeitpunkt erfasst werden können. 

Wenn die Analyse abgeschlossen ist, werden folgende Elemente in Azure-Tabellen und einen Blobcontainer (Binary Large Object) im angegebenen Speicherkonto hochgeladen:

*   Alle Erkenntnisse und zugehörigen Informationen über die Ausführung
*   Ein komprimierte Datei im ZIP-Format (namens **PerformanceDiagnostics_yyyy-MM-dd_hh-mm-ss-fff.zip**), mit Protokolldateien
*   Ein HTML-Bericht

Nach dem Upload wird ein neuer Diagnosebericht im Azure-Portal aufgelistet.

![Screenshot: Liste der Diagnoseberichte auf dem Blatt „Leistungsdiagnose“](media/performance-diagnostics/diagnostics-report-list.png)

## <a name="how-to-change-performance-diagnostics-settings"></a>Ändern der Einstellungen der Leistungsdiagnose
Verwenden Sie die Symbolleistenschaltfläche **Einstellungen**, um das Speicherkonto zu ändern, in dem die Erkenntnisse und Ausgaben der Diagnose gespeichert werden können. Sie können das gleiche Speicherkonto für mehrere VMs verwenden, für die eine Leistungsdiagnose ausgeführt wird. Wenn Sie das Speicherkonto ändern, werden die vorhandenen Berichte und Erkenntnisse nicht gelöscht. Sie werden aber nicht mehr in der Liste der Diagnoseberichte angezeigt. 

## <a name="review-insights-and-performance-diagnostics-report"></a>Überprüfen von Erkenntnissen und Leistungsdiagnosebericht
Jede Diagnoseausführung enthält eine Liste mit Erkenntnissen und Empfehlungen, betroffene Ressourcen, Protokolldateien, weitere relevante Diagnoseinformationen, die gesammelt wurden, und einen Bericht für die Offlineanzeige. Eine vollständige Liste aller gesammelten Diagnosedaten finden Sie unter [Welche Informationen werden von PerfInsights gesammelt?](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-use-perfinsights#what-kind-of-information-is-collected-by-perfinsights). 

### <a name="select-a-performance-diagnostics-report"></a>Auswählen eines Leistungsdiagnoseberichts
Sie können die Liste der Diagnoseberichte verwenden, um alle ausgeführten Diagnoseberichte zu finden. Die Liste enthält Details zu der verwendeten Analyse, gefundenen Erkenntnisse sowie deren Schweregrade. Markieren Sie eine Zeile, um weitere Details anzuzeigen.

![Screenshot: Auswahl eines Diagnoseberichts auf dem Blatt „Leistungsdiagnose“](media/performance-diagnostics/select-report.png)

### <a name="review-a-performance-diagnostics-report"></a>Überprüfen eines Leistungsdiagnoseberichts
Jeder Leistungsdiagnosebericht enthält verschiedene Erkenntnisse sowie den entsprechenden Schweregrad: hoch, mittel oder niedrig. Jede Erkenntnis enthält zudem Empfehlungen, um das Problem zu verringern. Erkenntnisse sind zur besseren Filterung gruppiert. 

Schweregrade geben das Potenzial für Leistungsprobleme an, basierend auf Faktoren wie Fehlkonfigurationen, bekannten Problemen oder Problemen, die von anderen Benutzern gemeldet wurden. Sie selbst sind von den aufgeführten Problemen möglicherweise noch nicht betroffen. Möglicherweise haben Sie SQL-Protokolldateien und Datenbankdateien auf demselben Datenträger gespeichert. Diese Konfiguration birgt ein hohes Risiko für Engpässe und andere Leistungsprobleme, wenn die Datenbanknutzung hoch ist. Bei geringer Auslastung bemerken Sie wahrscheinlich keine Probleme.

![Screenshot: Blatt mit der Übersicht über den Bericht der Leistungsdiagnose](media/performance-diagnostics/performance-diagnostics-report-overview.png)

### <a name="reviewing-performance-diagnostics-insights-and-recommendations"></a>Überprüfen von Erkenntnissen der Leistungsdiagnose und Empfehlungen
Sie können eine Erkenntnis auswählen, um weitere Details zu den betroffenen Ressourcen und vorgeschlagenen Lösungen sowie Referenzlinks anzuzeigen. 

![Screenshot: Details zu einer Erkenntnis der Leistungsdiagnose](media/performance-diagnostics/insight-detail.png)

### <a name="download-and-review-the-full-performance-diagnostics-report"></a>Herunterladen und Überprüfen des vollständigen Leistungsdiagnoseberichts
Sie können die Schaltfläche **Bericht herunterladen** verwenden, um einen HTML-Bericht herunterzuladen, der weitere relevante Diagnoseinformationen enthält, z.B. Speicher- und Netzwerkkonfiguration, Leistungsindikatoren, Ablaufverfolgungen, Prozesslisten und Protokolle. Der Inhalt richtet sich nach der ausgewählten Analyse. Zur erweiterten Problembehandlung kann der Bericht auch weitere Informationen und interaktive Diagramme enthalten, die sich auf eine hohe CPU- und Datenträgerauslastung sowie auf Prozesse beziehen, die übermäßig viel Arbeitsspeicher belegen. Weitere Informationen zum Leistungsdiagnosebericht finden Sie unter [Überprüfen eines Diagnoseberichts](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-use-perfinsights#review-the-diagnostics-report).

## <a name="manage-performance-diagnostics-reports"></a>Verwalten von Leistungsdiagnoseberichten
Sie können Leistungsdiagnoseberichte löschen, indem Sie die Schaltfläche **Bericht löschen** verwenden.

## <a name="how-to-uninstall-performance-diagnostics"></a>Deinstallieren der Leistungsdiagnose
Sie können die Leistungsdiagnose von einem virtuellen Computer deinstallieren. Diese Aktion entfernt die VM-Erweiterung, wirkt sich aber nicht auf die Diagnosedaten aus, die sich im Speicherkonto befinden. 

![Screenshot: Symbolleiste auf dem Blatt „Leistungsdiagnose“, Schaltfläche „Deinstallieren“ hervorgehoben](media/performance-diagnostics/uninstal-button.png)

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="where-is-the-diagnostics-data-from-my-vm-stored"></a>Wo werden die Diagnosedaten für meinen virtuellen Computer gespeichert? 
Alle Erkenntnisse und Berichte der Leistungsdiagnose werden in Ihrem eigenen Speicherkonto gespeichert. Erkenntnisse werden in Azure-Tabellen gespeichert. Die komprimierte Berichtdatei wird in einem Blobcontainer namens „azdiagextnresults“ gespeichert.

Sie können die Informationen zum Speicherkonto über die Schaltfläche „Einstellungen“ auf der Symbolleiste anzeigen. 

### <a name="how-do-i-share-this-data-with-microsoft-customer-support"></a>Wie gebe ich diese Daten für den Microsoft-Kundendienst frei? 
Es gibt verschiedene Möglichkeiten, den Diagnosebericht für Microsoft freizugeben.

**Option 1:** Den neuesten Bericht automatisch freigeben  
Wenn Sie ein Supportticket bei Microsoft eröffnen, ist es wichtig, den Leistungsdiagnosebericht freizugeben. Wenn Sie angegeben haben, dass diese Informationen nach dem Ausführen der Diagnose für Microsoft freigegeben werden (indem Sie das Kontrollkästchen **Ich stimme der Übermittlung von Diagnoseinformationen an Microsoft zu**, kann Microsoft bis zu 30 Tage nach dem Datum der Ausführung über einen SAS-Link zur ZIP-Ausgabedatei auf den Bericht in Ihrem Speicherkonto zugreifen. Dem Supporttechniker steht nur der neueste Bericht zur Verfügung. 

**Option 2:** Generieren einer Shared Access Signature (SAS) für die komprimierte Datei mit dem Diagnosebericht  
Sie können mithilfe von Shared Access Signatures einen Link zur komprimierten Datei mit dem Bericht an andere Personen weitergeben. Gehen Sie dazu folgendermaßen vor: 
1.  Navigieren Sie im Azure-Portal zu dem Speicherkonto, in dem die Diagnosedaten gespeichert sind.
1.  Wählen Sie im Abschnitt **Blobdienst** die Option **Blobs** aus. 
1.  Wählen Sie den Container **azdiagextnresults** aus.
1.  Wählen Sie die komprimierte Ausgabedatei mit einem Leistungsdiagnosebericht aus, die Sie freigeben möchten.
1.  Wählen Sie auf der Registerkarte **SAS generieren** die Kriterien für die Freigabe aus. 
1.  Klicken Sie auf **Blob-SAS-Token und URL generieren**.
1.  Kopieren Sie die **Blob-SAS-URL**, und geben Sie sie an den Supporttechniker weiter. 

**Option 3:** Herunterladen des Berichts aus dem Speicherkonto

Sie können eine komprimierte Ausgabedatei mit einem Leistungsdiagnosebericht auch ermitteln, indem Sie die Schritte 1–4 aus Option 2 durchführen. Laden Sie die Datei herunter. Senden Sie sie dann per E-Mail an den Supporttechniker, oder fragen Sie den Techniker nach einem Speicherort, in den Sie die Datei hochladen können.  

### <a name="how-do-i-capture-the-diagnostics-data-at-the-correct-time"></a>Wie erfasse ich die Diagnosedaten zum richtigen Zeitpunkt?
Jede Leistungsdiagnose wird in zwei Phasen ausgeführt: 
1.  Installieren oder aktualisieren der VM-Erweiterung für die Leistungsdiagnose
1.  Ausführen der Diagnose über den angegebenen Zeitraum

Zurzeit gibt es keine einfache Möglichkeit, herauszufinden, wann genau die Installation der VM-Erweiterung abgeschlossen ist. Im Allgemeinen dauert es etwa 45 Sekunden bis zu einer Minute, um die Erweiterung zu installieren. Nach der Installation der VM-Erweiterung können Sie die Schritte zur Reproduktion des Problems ausführen, damit die Leistungsdiagnose den richtigen Satz an Daten für die Problembehandlung erfasst. 

## <a name="next-steps"></a>Nächste Schritte
Wenn Sie nach Überprüfung der Erkenntnisse und des Berichts der Leistungsdiagnose das Problem noch nicht ermitteln können und weitere Hilfe benötigen, können Sie ein Supportticket beim Microsoft-Kundendienst eröffnen. 

Sollten Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie sich über das [MSDN Azure-Forum oder über das Stack Overflow-Forum](https://azure.microsoft.com/support/forums/) mit Azure-Experten in Verbindung setzen. Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/) auf, und wählen Sie die Option **Support erhalten**. Informationen zur Nutzung von Azure-Support finden Sie unter [Häufig gestellte Fragen zum Azure-Support](https://azure.microsoft.com/support/faq/).
