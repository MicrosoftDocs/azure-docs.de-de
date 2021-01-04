---
title: Behandeln von Problemen bei der Zertifizierung von virtuellen Computern (VMs) für Azure Marketplace
description: Es wird beschrieben, wie Sie häufige Probleme beim Testen und Zertifizieren von VM-Images für Azure Marketplace behandeln.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: troubleshooting
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/19/2020
ms.openlocfilehash: bc1ae4bc2cf64c3e2f996709c086eb23cb8b8385
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96602596"
---
# <a name="troubleshoot-virtual-machine-certification"></a>Problembehandlung bei der Zertifizierung von virtuellen Computern

Beim Veröffentlichen Ihres Images des virtuellen Computers (VM) in Azure Marketplace wird es vom Azure-Team überprüft, um dafür die Startfähigkeit, Sicherheit und Kompatibilität mit Azure sicherzustellen. Falls Ihr VM-Image einen der Tests zur Überprüfung der hohen Qualitätsanforderungen nicht besteht, wird es nicht veröffentlicht. Sie erhalten eine Fehlermeldung, in der das Problem beschrieben ist.

In diesem Artikel werden Fehlermeldungen, die häufig bei der Veröffentlichung von VM-Images angezeigt werden, sowie die zugehörigen Lösungen erläutert.

> [!NOTE]
> Wenden Sie sich an den [Partner Center-Support](https://aka.ms/marketplacepublishersupport), falls Sie Fragen zu diesem Artikel oder Verbesserungsvorschläge haben.

## <a name="approved-base-image"></a>Genehmigtes Basisimage

Wenn Sie eine Anforderung zur erneuten Veröffentlichung des Images mit Updates einreichen, schlägt der Testfall für die Überprüfung der Teilenummer möglicherweise fehl. In diesem Fall wird das Image nicht genehmigt.

Dieser Fehler tritt auf, wenn Sie ein Basisimage verwenden, das einem anderen Herausgeber gehört, und Sie das Image aktualisiert haben. In dieser Situation können Sie das Image nicht veröffentlichen.

Rufen Sie zum Beheben des Problems das Image aus Azure Marketplace ab, und nehmen Sie Änderungen daran vor. Weitere Informationen finden Sie in den folgenden Artikeln:

- [Linux-Images](../virtual-machines/linux/endorsed-distros.md?toc=/azure/virtual-machines/linux/toc.json)
- [Windows-Images](azure-vm-create-using-approved-base.md)

> [!Note]
> Wenn Sie ein Linux-Basisimage verwenden, das nicht aus dem Azure Marketplace stammt, können Sie die erste Partition um 2048 KB versetzen. Der nicht formatierte Speicherplatz kann so zum Hinzufügen neuer Abrechnungsinformationen verwendet werden, und Azure kann mit der Veröffentlichung Ihres virtuellen Computers im Azure Marketplace fortfahren.  

## <a name="vm-extension-failure"></a>Fehler bei einer VM-Erweiterung

Überprüfen Sie, ob Ihr Image VM-Erweiterungen unterstützt.

Aktivieren Sie VM-Erweiterungen wie folgt:

1. Wählen Sie Ihren virtuellen Linux-Computer aus.
1. Navigieren Sie zu **Diagnoseeinstellungen**.
1. Aktivieren Sie Basismatrizen, indem Sie das **Speicherkonto** aktualisieren.
1. Wählen Sie **Speichern** aus.

   ![Screenshot: Aktivieren der Überwachung auf Gastebene](./media/create-vm/vm-certification-issues-solutions-1.png)

Überprüfen Sie wie folgt, ob die VM-Erweiterungen richtig aktiviert wurden:

1. Wählen Sie auf dem virtuellen Computer die Registerkarte **VM-Erweiterungen** aus, und überprüfen Sie den Status der **Linux-Diagnoseerweiterung**.
1. Überprüfen Sie den Bereitstellungsstatus.

   - Ist der Status *Bereitstellung erfolgreich*, wurde der Testfall für Erweiterungen erfolgreich bestanden.  
   - Lautet der Status *Fehler beim Bereitstellen*, wurde der Testfall für Erweiterungen nicht bestanden, und Sie müssen das Festgeschrieben-Flag festlegen.

   ![Screenshot: Erfolgreiche Bereitstellung](./media/create-vm/vm-certification-issues-solutions-2.png)

   Liegt ein Fehler bei der VM-Erweiterung vor, lesen Sie die Informationen unter [Verwenden der Linux-Diagnoseerweiterung zum Überwachen von Metriken und Protokollen](../virtual-machines/extensions/diagnostics-linux.md), um sie zu aktivieren. Wenn die VM-Erweiterung nicht aktiviert sein soll, wenden Sie sich an das Supportteam, und lassen Sie die Erweiterung deaktivieren.

## <a name="vm-provisioning-issue"></a>Problem bei der VM-Bereitstellung

Vergewissern Sie sich vor der Übermittlung Ihres Angebots, dass Sie den VM-Bereitstellungsprozess strikt befolgt haben. Unter dem Thema zum [Testen von VM-Images](azure-vm-image-test.md) finden Sie das JSON-Format zum Bereitstellen der VM.

Bereitstellungsprobleme können auf folgende Fehler zurückzuführen sein:

|Szenario|Fehler|`Reason`|Lösung|
|---|---|---|---|
|1|Ungültige virtuelle Festplatte (VHD)|Wenn der angegebene Cookie-Wert in der VHD-Fußzeile nicht korrekt ist, wird die VHD als ungültig eingestuft.|Erstellen Sie das Image neu, und senden Sie die Anforderung.|
|2|Ungültiger Blobtyp|Fehler bei der VM-Bereitstellung, weil der verwendete Block ein Blobtyp und kein Seitentyp ist.|Erstellen Sie das Image neu, und senden Sie die Anforderung.|
|3|Timeout bei der Bereitstellung oder nicht ordnungsgemäß generalisiert|Es liegt ein Problem mit der VM-Generalisierung vor.|Erstellen Sie das Image mit Generalisierung neu, und senden Sie die Anforderung.|

> [!NOTE]
> Weitere Informationen zur VM-Generalisierung finden Sie in den folgenden Ressourcen:
> - [Generalisieren des Images](azure-vm-create-using-approved-base.md#generalize-the-image)
> - [Generalisieren der Windows-VM mithilfe von Sysprep](../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep)


## <a name="vhd-specifications"></a>VHD-Spezifikationen

### <a name="conectix-cookie-and-other-vhd-specifications"></a>Conectix-Cookie und andere VHD-Spezifikationen

Die Zeichenfolge „conectix“ ist Teil der VHD-Spezifikation. Sie wird als 8-Byte-Cookie in der VHD-Fußzeile für die Identifizierung des Dateierstellers definiert. Alle von Microsoft erstellten VHD-Dateien verfügen über dieses Cookie. 

Ein Blob mit VHD-Formatierung sollte eine 512-Byte-Fußzeile mit dem folgenden Format aufweisen:

|Felder in Festplatten-Fußzeilen|Größe (Byte)|
|---|---|
Cookie|8
Features|4
Dateiformatversion|4
Datenoffset|8
Zeitstempel|4
Anwendung des Erstellers|4
Version des Erstellers|4
Hostbetriebssystem des Erstellers|4
Ursprüngliche Größe|8
Aktuelle Größe|8
Datenträgergeometrie|4
Datenträgertyp|4
Checksum|4
Eindeutige ID|16
Gespeicherter Zustand|1
Reserviert|427


### <a name="vhd-specifications"></a>VHD-Spezifikationen

Stellen Sie sicher, dass Ihre VHD die folgenden Kriterien erfüllt, um eine reibungslose Veröffentlichung zu ermöglichen:

- Das Cookie enthält die Zeichenfolge „conectix“.
- Der Datenträgertyp lautet „Fixed“ (Feststehend).
- Die virtuelle Größe der VHD beträgt mindestens 20 MB.
- Die VHD wurde ausgerichtet. Die virtuelle Größe muss ein Vielfaches von 1 MB sein.
- Die Länge des VHD-Blobs entspricht der virtuellen Größe plus der Länge der VHD-Fußzeile (512).

Laden Sie die [VHD-Spezifikation](https://www.microsoft.com/download/details.aspx?id=23850) herunter.

## <a name="software-compliance-for-windows"></a>Softwarekompatibilität für Windows

Wenn die Anforderung für Ihr Windows-Image abgelehnt wird, weil ein Problem mit der Softwarekompatibilität besteht, haben Sie unter Umständen ein Windows-Image mit einer installierten SQL Server-Instanz erstellt. Stattdessen müssen Sie das relevante Basisimage der SQL Server-Version aus dem Azure Marketplace verwenden.

Erstellen Sie kein eigenes Windows-Image, auf dem SQL Server installiert ist. Verwenden Sie die genehmigten SQL Server-Basisimages (Enterprise/Standard/Web) aus dem Azure Marketplace.

Wenn Sie Visual Studio oder ein lizenziertes Office-Produkt installieren möchten, holen Sie vorab die Genehmigung des Supportteams ein.

Weitere Informationen zum Auswählen einer genehmigten Basis finden Sie im Thema zum [Erstellen eines virtuellen Computers aus einer genehmigten Basis](azure-vm-create-using-approved-base.md).

## <a name="toolkit-test-case-execution-failed"></a>Fehler beim Ausführen des Toolkit-Testfalls

Mit dem Microsoft Certification-Toolkit können Sie Testfälle ausführen und überprüfen, ob Ihre VHD oder Ihr Image mit der Azure-Umgebung kompatibel ist.

Laden Sie das [Microsoft Certification-Toolkit](azure-vm-image-test.md) herunter.

### <a name="linux-test-cases"></a>Linux-Testfälle

In der folgenden Liste sind die Linux-Testfälle aufgelistet, die vom Toolkit ausgeführt werden. Die Testvalidierung wird in der Beschreibung angegeben.

|Szenario|Testfall|BESCHREIBUNG|
|---|---|---|
|1|Bashverlauf|Bashverlaufsdateien sollten vor dem Erstellen des VM-Images gelöscht werden.|
|2|Linux-Agent-Version|Azure Linux Agent 2.2.41 oder höher muss installiert sein.|
|3|Erforderliche Kernelparameter|Überprüft, ob die folgenden Kernelparameter festgelegt sind: <br>console=ttyS0<br>earlyprintk=ttyS0<br>rootdelay=300 |
|4|Swap-Partition auf dem Betriebssystemdatenträger|Stellt sicher, dass Swap-Partitionen nicht auf dem Betriebssystemdatenträger erstellt werden.|
|5|Stammpartition auf dem Betriebssystemdatenträger|Erstellen Sie eine einzelne Stammpartition für den Betriebssystemdatenträger.|
|6|OpenSSL-Version|Sie müssen mindestens Version v0.9.8 von OpenSSL verwenden.|
|7|Python-Version|Dringend empfohlen wird Python-Version 2.6 oder höher.|
|8|Client-Alive-Intervall|Legen Sie das ClientAliveInterval auf 180 fest. Je nach Anforderung der Anwendung kann es auf einen Wert zwischen 30 und 235 festgelegt werden. Wenn Sie SSH für Ihre Endbenutzer aktivieren, muss dieser Wert gemäß der Beschreibung festgelegt werden.|
|9|Betriebssystemarchitektur|Es werden nur 64-Bit-Betriebssysteme unterstützt.|
|10|Automatische Aktualisierung|Gibt an, ob die automatische Aktualisierung des Linux-Agents aktiviert ist.|

### <a name="common-test-case-errors"></a>Häufige Testfallfehler

In der folgenden Tabelle sind die häufigen Fehler angegeben, die beim Ausführen von Testfällen auftreten können:

| Szenario | Testfall | Fehler | Lösung |
| --- | --- | --- | --- |
| 1 | Testfall zur Überprüfung der Linux-Agent-Version | Die mindestens erforderliche Linux-Agent-Version ist 2.2.41 oder höher. Diese Anforderung gilt seit dem 1. Mai 2020. | Aktualisieren Sie die Version des Linux-Agents. Sie sollte 2.241 oder höher lauten. Weitere Informationen finden Sie auf der [Seite mit Updates für die Linux-Agent-Version](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support). |
| 2 | Bashverlauf-Testfall | Es tritt ein Fehler auf, wenn die Größe des Bashverlaufs in Ihrem gesendeten Image 1 Kilobyte (KB) überschreitet. Die Größe ist auf 1 KB beschränkt, um sicherzustellen, dass in Ihrer Datei mit dem Bashverlauf keine potenziell vertraulichen Informationen erfasst werden. | Stellen Sie die VHD hierfür auf einer anderen funktionierenden VM bereit, und nehmen Sie Änderungen vor, um die Größe auf maximal 1 KB zu reduzieren. Löschen Sie beispielsweise die `.bash`-Verlaufsdateien. |
| 3 | Testfall für erforderliche Kernelparameter | Dieser Fehler wird angezeigt, wenn der Wert für `console` nicht auf `ttyS0` festgelegt ist. Überprüfen Sie die Änderung, indem Sie den folgenden Befehl ausführen: <br /> `cat /proc/cmdline` | Legen Sie den Wert für `console` auf `ttyS0` fest, und senden Sie die Anforderung noch einmal. |
| 4 | Testfall für das Client-Alive-Intervall | Falls im Toolkit-Ergebnis für diesen Testfall ein Fehler zurückgegeben wird, ist für `ClientAliveInterval` ein unzulässiger Wert vorhanden. | Legen Sie den Wert für `ClientAliveInterval` auf maximal 235 fest, und senden Sie die Anforderung noch einmal. |


### <a name="windows-test-cases"></a>Windows-Testfälle

Die folgende Tabelle enthält die Windows-Testfälle, die vom Toolkit ausgeführt werden, sowie eine Beschreibung der Testüberprüfung:

|Szenario |Testfälle|BESCHREIBUNG|
|---|---|---|---|
|1|Betriebssystemarchitektur|Azure unterstützt nur 64-Bit-Betriebssysteme.|
|2|Abhängigkeit von Benutzerkonten|Die Ausführung der Anwendung sollte nicht vom Administratorkonto abhängen.|
|3|Failovercluster|Das Feature „Windows Server-Failoverclustering“ wird noch nicht unterstützt. Die Anwendung sollte nicht von diesem Feature abhängen.|
|4|IPV6|IPv6 wird in der Azure-Umgebung noch nicht unterstützt. Die Anwendung sollte nicht von diesem Feature abhängen.|
|5|DHCP|Die Serverrolle „Dynamic Host Configuration-Protokoll“ wird noch nicht unterstützt. Die Anwendung sollte nicht von diesem Feature abhängen.|
|6|Hyper-V|Die Serverrolle „Hyper-V“ wird noch nicht unterstützt. Die Anwendung sollte nicht von diesem Feature abhängen.|
|7|Remotezugriff|Die Serverrolle „Remotezugriff (Direktzugriff)“ wird noch nicht unterstützt. Die Anwendung sollte nicht von diesem Feature abhängen.|
|8|Rights Management Services|Rights Management Services. Die Serverrolle wird noch nicht unterstützt. Die Anwendung sollte nicht von diesem Feature abhängen.|
|9|Windows-Bereitstellungsdienste|Windows-Bereitstellungsdienste. Die Serverrolle wird noch nicht unterstützt. Die Anwendung sollte nicht von diesem Feature abhängen.|
|10|BitLocker-Laufwerkverschlüsselung|Die BitLocker-Laufwerkverschlüsselung wird auf der Betriebssystem-Festplatte nicht unterstützt, aber möglicherweise auf Datenträgern verwendet.|
|11|Internet Storage Name Server|Das Feature „Internet Storage Name Server“ wird noch nicht unterstützt. Die Anwendung sollte nicht von diesem Feature abhängen.|
|12|Multipfad-E/A|Multipfad-E/A. Diese Serverfunktion wird noch nicht unterstützt. Die Anwendung sollte nicht von diesem Feature abhängen.|
|13|Netzwerklastenausgleich|Netzwerklastenausgleich. Diese Serverfunktion wird noch nicht unterstützt. Die Anwendung sollte nicht von diesem Feature abhängen.|
|14|Peer Name Resolution-Protokoll|Peer Name Resolution-Protokoll. Diese Serverfunktion wird noch nicht unterstützt. Die Anwendung sollte nicht von diesem Feature abhängen.|
|15|SNMP-Dienste|Das Feature „SNMP-Dienste“ (Simple Network Management-Protokoll) wird noch nicht unterstützt. Die Anwendung sollte nicht von diesem Feature abhängen.|
|16|WINS (Windows Internet Name Service)|WINS (Windows Internet Name Service). Diese Serverfunktion wird noch nicht unterstützt. Die Anwendung sollte nicht von diesem Feature abhängen.|
|17|WLAN-Dienst|WLAN-Dienst. Diese Serverfunktion wird noch nicht unterstützt. Die Anwendung sollte nicht von diesem Feature abhängen.|

Wenn im Zusammenhang mit den obigen Testfällen Fehler auftreten, finden Sie in der Tabelle in der Spalte **Beschreibung** Hinweise zur Fehlerbehebung. Wenden Sie sich an das Supportteam, falls Sie weitere Informationen benötigen. 

## <a name="data-disk-size-verification"></a>Überprüfung der Datenträgergröße

Datenträgeranforderungen mit einer Größe von mehr als 1.023 Gigabyte (GB) werden nicht genehmigt. Diese Regel gilt sowohl für Linux als auch für Windows.

Senden Sie die Anforderung mit einer Größe von maximal 1.023 GB noch einmal.

## <a name="os-disk-size-validation"></a>Validierung der Betriebssystem-Datenträgergröße

In den folgenden Regeln finden Sie Einschränkungen hinsichtlich der Betriebssystem-Datenträgergröße. Überprüfen Sie beim Senden von Anforderungen, ob die Betriebssystem-Datenträgergröße innerhalb der Grenzen für Linux bzw. Windows liegt.

|OS|Empfohlene VHD-Größe|
|---|---|
|Linux|1 GB bis 1.023 GB|
|Windows|30 GB bis 250 GB|

Da VMs den Zugriff auf das zugrunde liegende Betriebssystem zulassen, sollten Sie sich vergewissern, dass die Größe der VHD ausreicht. Datenträger können erweitert werden, ohne dass es zu Ausfallzeiten kommt. Verwenden Sie eine Datenträgergröße zwischen 30 und 50 GB.

|VHD-Größe|Tatsächlich belegte Größe|Lösung|
|---|---|---|
|Mehr als 500 Tebibyte (TiB)|–|Wenden Sie sich wegen einer Ausnahmegenehmigung an das Supportteam.|
|250-500 TiB|Differenz von mehr als 200 Gibibyte (GiB) zur Blobgröße|Wenden Sie sich wegen einer Ausnahmegenehmigung an das Supportteam.|

> [!NOTE]
> Größere Datenträger führen zu höheren Kosten und während der Einrichtung und Replikation zu einer Verzögerung. Aufgrund dieser Verzögerung und Kosten kann das Supportteam eine begründete Ausnahmegenehmigung erteilen.

## <a name="wannacry-patch-verification-test-for-windows"></a>Test zur Überprüfung auf WannaCry-Patch für Windows

Um einen potenziellen Angriff im Zusammenhang mit dem WannaCry-Virus zu verhindern, stellen Sie sicher, dass alle Windows-Image-Anforderungen mit dem neuesten Patch aktualisiert sind.

Sie können die Version der Imagedatei unter `C:\windows\system32\drivers\srv.sys` oder `srv2.sys` überprüfen.

In der folgenden Tabelle ist die gepatchte Mindestversion von Windows Server angegeben: 

|OS|Version|
|---|---|
|Windows Server 2008 R2|6.1.7601.23689|
|Windows Server 2012|6.2.9200.22099|
|Windows Server 2012 R2|6.3.9600.18604|
|Windows Server 2016|10.0.14393.953|
|Windows Server 2019|Nicht verfügbar|

> [!NOTE]
> Für WindowsServer2019 gelten keine obligatorischen Versionsanforderungen.

## <a name="sack-vulnerability-patch-verification"></a>Überprüfung auf Patch für SACK-Sicherheitsrisiken

Wenn Sie ein Linux-Image senden, wird Ihre Anforderung möglicherweise aufgrund von Problemen mit der Kernelversion abgelehnt.

Aktualisieren Sie den Kernel mit einer genehmigten Version, und senden Sie die Anforderung noch einmal. Die genehmigte Kernelversion finden Sie in der folgenden Tabelle. Die Versionsnummer muss mindestens der hier aufgeführten entsprechen.

Wenn das Image nicht mit einer der folgenden Kernelversionen installiert ist, aktualisieren Sie es mit den richtigen Patches. Fordern Sie die nötige Genehmigung vom Supportteam an, nachdem das Image mit diesen erforderlichen Patches aktualisiert wurde:

- CVE-2019-11477 
- CVE-2019-11478 
- CVE-2019-11479

|Betriebssystemfamilie|Version|Kernel|
|---|---|---|
|Ubuntu|14.04 LTS|4.4.0-151| 
||14.04 LTS|4.15.0-1049-*-azure|
||16.04 LTS|4.15.0-1049|
||18.04 LTS|4.18.0-1023|
||18.04 LTS|5.0.0-1025|
||18.10|4.18.0-1023|
||19.04|5.0.0-1010|
||19.04|5.3.0-1004|
|RHEL und CentOS|6.10|2.6.32-754.15.3|
||7.2|3.10.0-327.79.2|
||7.3|3.10.0-514.66.2|
||7.4|3.10.0-693.50.3|
||7,5|3.10.0-862.34.2|
||7.6|3.10.0-957.21.3|
||7,7|3.10.0-1062.1.1|
||8.0|4.18.0-80.4.2|
||8.1|4.18.0-147|
||„7-RAW“ (7.6)||
||„7-LVM“ (7.6)|3.10.0-957.21.3|
||RHEL-SAP 7.4|TBD|
||RHEL-SAP 7.5|TBD|
|SLES|SLES11SP4 (einschließlich SAP)|3.0.101-108.95.2|
||SLES12SP1 für SAP|3.12.74-60.64.115.1|
||SLES12SP2 für SAP|4.4.121-92.114.1|
||SLES12SP3|4.4180-4.31.1 (Kernel-Azure)|
||SLES12SP3 für SAP|4.4.180-94.97.1|
||SLES12SP4|4.12.14-6.15.2 (Kernel-Azure)|
||SLES12SP4 für SAP|4.12.14-95.19.1|
||SLES15|4.12.14-5.30.1 (Kernel-Azure)|
||SLES15 für SAP|4.12.14-5.30.1 (Kernel-Azure)|
||SLES15SP1|4.12.14-5.30.1 (Kernel-Azure)|
|Oracle|6.10|UEK2 2.6.39-400.312.2<br>UEK3 3.8.13-118.35.2<br>RHCK 2.6.32-754.15.3 
||7.0-7.5|UEK3 3.8.13-118.35.2<br>UEK4 4.1.12-124.28.3<br>RHCK folgt auf obiges RHEL|
||7.6|RHCK 3.10.0-957.21.3<br>UEK5 4.14.35-1902.2.0|
|CoreOS Stable 2079.6.0|4.19.43*|
||Beta 2135.3.1|4.19.50*|
||Alpha 2163.2.1|4.19.50*|
|Debian|Jessie (Sicherheit)|3.16.68-2|
||Jessie-Backports|4.9.168-1+deb9u3|
||Stretch (Sicherheit)|4.9.168-1+deb9u3|
||Debian GNU/Linux 10 (Buster)|Debian 6.3.0-18+deb9u1|
||Buster, SID (Stretch-Backports)|4.19.37-5|

## <a name="image-size-should-be-in-multiples-of-megabytes"></a>Die Imagegröße muss ein Megabyte-Vielfaches sein.

Alle VHDs in Azure müssen eine virtuelle Größe aufweisen, die sich an einem Vielfachen von 1 Megabyte (MB) orientiert. Wenn Ihre VHD nicht empfohlene virtuelle Größe aufweist, wird Ihre Anforderung möglicherweise zurückgewiesen.

Halten Sie sich beim Konvertieren von einem Rohdatenträger in eine VHD an die Richtlinien. Stellen Sie sicher, dass die Größe des Rohdatenträgers ein Vielfaches von 1 MB ist. Weitere Informationen finden Sie unter [Informationen zu nicht unterstützten Verteilungen](../virtual-machines/linux/create-upload-generic.md).

## <a name="vm-access-denied"></a>VM-Zugriff verweigert

Ein Problem vom Typ _Zugriff verweigert_ beim Ausführen eines Testfalls auf der VM wird ggf. durch unzureichende Berechtigungen verursacht.

Überprüfen Sie, ob Sie für das Konto, unter dem die Testfälle ausgeführt werden, den richtigen Zugriff aktiviert haben. Aktivieren Sie den Zugriff zum Ausführen von Testfällen, falls dies noch nicht durchgeführt wurde. Wenn Sie den Zugriff nicht aktivieren möchten, können Sie die Ergebnisse des Selbsttestfalls mit dem Supportteam teilen.

Übermitteln Sie Ihre Anforderung mit einem Image mit deaktiviertem SSH für den Zertifizierungsprozess wie folgt:

1. Führen Sie das [aktuelle Tool zum Testen der Zertifizierung für Azure-VMs](https://aka.ms/AzureCertificationTestTool) für Ihr Image aus.

2. Erstellen Sie ein [Supportticket](https://aka.ms/marketplacepublishersupport). Achten Sie darauf, dass Sie den Toolkit-Bericht anfügen und die Angebotsdetails angeben:
   - Angebotsname
   - Herausgebername
   - Plan-ID/SKU und Version

3. Übermitteln Sie Ihre Zertifizierungsanforderung erneut.

## <a name="download-failure"></a>Fehler beim Herunterladen
    
In der Tabelle unten finden Sie alle Probleme, die auftreten können, wenn Sie das VM-Image mit einer SAS-URL (Shared Access Signature) herunterladen.

|Szenario|Fehler|`Reason`|Lösung|
|---|---|---|---|
|1|Nicht gefundenes Blob|Die VHD wurde entweder gelöscht oder vom angegebenen Speicherort verschoben.|| 
|2|Blob wird verwendet|Die VHD wird von einem anderen internen Prozess verwendet.|Die VHD muss sich beim Herunterladen über eine SAS-URL im Status „Verwendet“ befinden.|
|3|Ungültige SAS-URL|Die zugehörige SAS-URL für die VHD ist falsch.|Rufen Sie die richtige SAS-URL ab.|
|4|Ungültige Signatur|Die zugehörige SAS-URL für die VHD ist falsch.|Rufen Sie die richtige SAS-URL ab.|
|6|Bedingter HTTP-Header|Die SAS-URL ist ungültig.|Rufen Sie die richtige SAS-URL ab.|
|7|Ungültiger VHD-Name|Überprüfen Sie, ob Sonderzeichen wie das Prozentzeichen (`%`) oder Anführungszeichen (`"`) im VHD-Namen enthalten sind.|Benennen Sie die VHD-Datei um, und entfernen Sie die Sonderzeichen.|

## <a name="first-mb-2048-kb-partition-linux-only"></a>„Erste MB“-Partition (2.048 KB) (nur Linux)

Stellen Sie beim Senden der VHD sicher, dass die ersten 2.048 KB der VHD leer sind. Andernfalls schlägt die Anforderung fehl.

>[!NOTE]
>Für bestimmte besondere Images, z. B. die zusätzlich zu Azure-Windows-Basisimages aus Azure Marketplace erstellt werden, wird nach einem Abrechnungstag gesucht. Die MB-Partition wird ignoriert, wenn das Abrechnungstag vorhanden ist und unseren internen verfügbaren Werten entspricht.

### <a name="create-a-first-mb-2048-kb-partition-on-an-empty-vhd"></a>Erstellen einer „Erste MB“-Partition (2.048 KB) auf einer leeren VHD

Diese Schritte gelten nur für Linux.

1. Erstellen Sie eine beliebige Art von Linux-VM, z. B. Ubuntu, Cent OS usw. Füllen Sie die erforderlichen Felder aus, und wählen Sie dann die folgende Option aus: **Weiter: Datenträger >** .

   ![Screenshot: Seite „Virtuellen Computer erstellen“ mit hervorgehobener Schaltfläche „Weiter: Datenträger“](./media/create-vm/vm-certification-issues-solutions-15.png)

1. Erstellen Sie einen nicht verwalteten Datenträger für Ihren virtuellen Computer.

   Verwenden Sie entweder die Standardwerte, oder geben Sie einen beliebigen Wert für Felder an, z. B. NIC, NSG und öffentliche IP-Adresse.

   ![Screenshot: Seite „Datenträger“ unter „Virtuellen Computer erstellen“](./media/create-vm/vm-certification-issues-solutions-16.png)

1. Wählen Sie nach dem Erstellen der VM im linken Bereich die Option **Datenträger** aus.

   ![Screenshot: Auswählen von Datenträgern für eine VM](./media/create-vm/vm-certification-issues-solutions-17.png)

1. Fügen Sie Ihre VHD als Datenträger an Ihre VM an, um eine Partitionstabelle zu erstellen.

   1. Wählen Sie **Datenträger hinzufügen** > **Vorhandenes Blob** aus.

      ![Screenshot: Hinzufügen eines Datenträgers zu Ihrer VHD](./media/create-vm/vm-certification-issues-solutions-18.png)

   1. Suchen Sie nach Ihrem VHD-Speicherkonto.
   1. Wählen Sie die Option **Container** und dann Ihre VHD aus.
   1. Klicken Sie auf **OK**.

      ![Screenshot: Seite „Nicht verwalteten Datenträger anfügen“](./media/create-vm/vm-certification-issues-solutions-19.png)

      Ihre VHD wird als Datenträger „LUN 0“ hinzugefügt.

   1. Starten Sie den virtuellen Computer neu.

1. Melden Sie sich nach dem Neustart der VM bei der VM an, indem Sie PuTTY oder einen anderen Client verwenden, und führen Sie den Befehl `sudo  -i` aus, um root-Zugriff zu erhalten.

   ![Screenshot: Befehlszeile im PuTTY-Client mit dem Befehl „sudo -i“](./media/create-vm/vm-certification-issues-solutions-20.png)

1. Erstellen Sie auf Ihrer VHD eine Partition.

   1. Geben Sie den Befehl `fdisk /dev/sdb` ein.
   1. Geben Sie `p` ein, um die vorhandene Partitionsliste Ihrer VHD anzuzeigen.
   1. Geben Sie `d` ein, um alle vorhandenen Partitionen zu löschen, die auf Ihrer VHD verfügbar sind. Sie können diesen Schritt überspringen, falls er für Sie nicht erforderlich ist.

      ![Screenshot: Befehlszeile im PuTTY-Client mit den Befehlen zum Löschen vorhandener Partitionen](./media/create-vm/vm-certification-issues-solutions-21.png)

   1. Geben Sie `n` ein, um eine neue Partition zu erstellen, und wählen Sie als Typ `p` (primäre Partition) aus.

   1. Geben Sie „2048“ als Wert für den ersten Sektor (_First sector_) ein. Sie können _Last sector_ (Letzter Sektor) als Standardwert beibehalten.

      >[!IMPORTANT]
      >Alle vorhandenen Daten bis 2.048 KB werden gelöscht. Erstellen Sie eine Sicherung der VHD, bevor Sie eine neue Partition erstellen.

      ![Screenshot: Befehlszeile im PuTTY-Client mit den Befehlen und der Ausgabe für gelöschte Daten](./media/create-vm/vm-certification-issues-solutions-22.png)

   1. Geben Sie `w` ein, um die Erstellung der Partition zu bestätigen. 

      ![Screenshot: Befehlszeile im PuTTY-Client mit den Befehlen zum Erstellen einer Partition](./media/create-vm/vm-certification-issues-solutions-23.png)

   1. Sie können die Partitionstabelle überprüfen, indem Sie den Befehl `n fdisk /dev/sdb` ausführen und `p` eingeben. Sie sehen, dass die Partition mit dem Offsetwert „2048“ erstellt wurde. 

      ![Screenshot: Befehlszeile im PuTTY-Client mit den Befehlen zum Erstellen des Offsets von 2048](./media/create-vm/vm-certification-issues-solutions-24.png)

1. Trennen Sie die VHD vom virtuellen Computer, und löschen Sie den virtuellen Computer.

### <a name="create-a-first-mb-2048-kb-partition-by-moving-existing-data-on-vhd"></a>Erstellen einer „Erste MB“-Partition (2.048 KB) durch das Verschieben von vorhandenen Daten auf der VHD

Diese Schritte gelten nur für Linux.

1. Erstellen Sie eine beliebige Art von Linux-VM, z. B. Ubuntu, Cent OS usw. Füllen Sie die erforderlichen Felder aus, und wählen Sie dann die folgende Option aus: **Weiter: Datenträger >** .

   ![Screenshot: Seite „Virtuellen Computer erstellen“ mit hervorgehobener Schaltfläche „Weiter: Datenträger“](./media/create-vm/vm-certification-issues-solutions-15.png)

1. Erstellen Sie einen nicht verwalteten Datenträger für Ihren virtuellen Computer.

   ![Screenshot: Seite „Datenträger“ unter „Virtuellen Computer erstellen“](./media/create-vm/vm-certification-issues-solutions-16.png)

   Verwenden Sie entweder die Standardwerte, oder geben Sie einen beliebigen Wert für Felder an, z. B. NIC, NSG und öffentliche IP-Adresse.

1. Wählen Sie nach dem Erstellen der VM im linken Bereich die Option **Datenträger** aus.

   ![Screenshot: Auswählen von Datenträgern für eine VM](./media/create-vm/vm-certification-issues-solutions-17.png)

1. Fügen Sie Ihre VHD als Datenträger an Ihre VM an, um eine Partitionstabelle zu erstellen.

   1. Fügen Sie Ihre VHD als Datenträger an Ihre VM an, um eine Partitionstabelle zu erstellen.

   1. Wählen Sie **Datenträger hinzufügen** > **Vorhandenes Blob** aus.

      ![Screenshot: Hinzufügen eines Datenträgers zu Ihrer VHD](./media/create-vm/vm-certification-issues-solutions-18.png)

   1. Suchen Sie nach Ihrem VHD-Speicherkonto.
   1. Wählen Sie die Option **Container** und dann Ihre VHD aus.
   1. Klicken Sie auf **OK**.

      ![Screenshot: Seite „Nicht verwalteten Datenträger anfügen“](./media/create-vm/vm-certification-issues-solutions-19.png)

      Ihre VHD wird als Datenträger „LUN 0“ hinzugefügt.

   1. Starten Sie den virtuellen Computer neu.

1. Melden Sie sich mit PuTTY oder einem anderen Client bei der VM an, und führen Sie den Befehl `sudo  -i` aus, um root-Zugriff zu erhalten.

   ![Screenshot: Befehlszeile im PuTTY-Client mit der Anmeldung und dem Befehl „sudo -i“](./media/create-vm/vm-certification-issues-solutions-20.png)

1. Führen Sie den Befehl `echo '+1M,' | sfdisk --move-data /dev/sdc -N 1` aus.

   ![Screenshot: Befehlszeile im PuTTY-Client mit der Ausführung der Befehle](./media/create-vm/vm-certification-issues-solutions-25.png)

   >[!NOTE]
   >Es kann eine Weile dauern, bis dieser Befehl abgeschlossen wurde. Der Vorgang hängt von der Größe des Datenträgers ab.

1. Trennen Sie die VHD vom virtuellen Computer, und löschen Sie den virtuellen Computer.


## <a name="default-credentials"></a>Standardanmeldeinformationen

Senden Sie zusammen mit der übermittelten VHD niemals Standardanmeldeinformationen. Das Hinzufügen von Standardanmeldeinformationen macht die VHD anfälliger gegenüber Sicherheitsbedrohungen. Erstellen Sie stattdessen beim Senden der VHD eigene Anmeldeinformationen.
  
## <a name="datadisk-mapped-incorrectly"></a>DataDisk nicht ordnungsgemäß zugeordnet

Ein Zuordnungsproblem kann auftreten, wenn eine Anforderung mit mehreren Datenträgern übermittelt wird, die nicht als entsprechende Sequenz vorliegen. Die Nummerierungsreihenfolge für die drei Datenträger muss beispielsweise *0, 1, 2* lauten. Alle anderen Reihenfolgen werden als Zuordnungsproblem behandelt.

Senden Sie die Anforderung mit ordnungsgemäßer Sequenzierung der Datenträger noch einmal.

## <a name="incorrect-os-mapping"></a>Falsche Betriebssystemzuordnung

Beim Erstellen eines Images kann dieses der falschen Betriebssystembezeichnung zugeordnet oder zugewiesen werden. Wenn Sie beim Erstellen des Images beispielsweise **Windows** als Teil des Betriebssystemnamens auswählen, darf der Betriebssystem-Datenträger nur mit Windows installiert werden. Die gleiche Anforderung gilt für Linux.

## <a name="vm-not-generalized"></a>VM ist nicht generalisiert

Wenn alle Images aus Azure Marketplace wiederverwendet werden sollen, muss die Betriebssystem-VHD generalisiert werden.

* Unter **Linux** wird mit dem folgenden Prozess eine Linux-VM generalisiert und als separate VM erneut bereitgestellt.

  Geben Sie im SSH-Fenster den folgenden Befehl ein: `sudo waagent -deprovision+user`

* Unter **Windows** generalisieren Sie Windows-Images mithilfe von `sysreptool`.

  Weitere Informationen zum Tool `sysreptool` finden Sie unter [Übersicht über Sysprep (Systemvorbereitung)](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

## <a name="datadisk-errors"></a>DataDisk-Fehler

In der folgenden Tabelle sind Fehlerbehebungsoptionen für Fehler in Verbindung mit dem Datenträger aufgeführt:

|Fehler|`Reason`|Lösung|
|---|---|---|
|`DataDisk- InvalidUrl:`|Dieser Fehler kann darauf zurückzuführen sein, dass beim Übermitteln des Angebots eine ungültige logische Gerätenummer (Logical Unit Number, LUN) angegeben wurde.|Vergewissern Sie sich, dass sich die Sequenz der LUN-Nummern für den Datenträger in Partner Center befindet.|
|`DataDisk- NotFound:`|Dieser Fehler kann darauf zurückzuführen sein, dass sich ein Datenträger nicht unter einer angegebenen SAS-URL befindet.|Vergewissern Sie sich, dass der Datenträger unter der angegebenen SAS-URL vorhanden ist.|

## <a name="remote-access-issue"></a>Problem mit dem Remotezugriff

Sie erhalten diesen Fehler, wenn für das Windows-Image nicht die Remotedesktopprotokoll-Option (RDP) aktiviert ist.

Aktivieren Sie vor dem Senden den RDP-Zugriff für Windows-Images.

## <a name="bash-history-failed"></a>Fehler beim Bashverlauf

Dieser Fehler tritt auf, wenn die Größe des Bashverlaufs in Ihrem gesendeten Image 1 Kilobyte (KB) überschreitet. Die Größe ist auf 1 KB beschränkt, um zu verhindern, dass die Datei potenziell vertrauliche Informationen enthält.

Löschen Sie den Bashverlauf wie folgt:

1. Stellen Sie den virtuellen Computer bereit, und wählen Sie im Azure-Portal die Option **Befehl ausführen** aus.

   ![Screenshot: Option „Befehl ausführen“ im linken Bereich des Azure-Portals](./media/create-vm/vm-certification-issues-solutions-3.png)

1. Wählen Sie die erste Option **RunShellScript** aus, und führen Sie dann den folgenden Befehl aus: `cat /dev/null > ~/.bash_history && history -c`.

   ![Screenshot: Seite für Ausführung des Befehlsskripts im Azure-Portal](./media/create-vm/vm-certification-issues-solutions-4.png)

1. Starten Sie den virtuellen Computer neu, nachdem der Befehl erfolgreich ausgeführt wurde.

1. Generalisieren Sie den virtuellen Computer, erstellen Sie die Image-VHD, und beenden Sie den virtuellen Computer.

1. Übermitteln Sie das generalisierte Image erneut.

## <a name="request-an-exception-on-vm-images-for-select-tests"></a>Anfordern einer Ausnahme für VM-Images für ausgewählte Tests

Herausgeber können für einige Tests, die während der VM-Zertifizierung durchgeführt werden, Ausnahmen anfordern. Ausnahmen werden in seltenen Fällen gewährt, wenn der Herausgeber die Anforderung fundiert begründet. Das Zertifizierungsteam behält sich das Recht vor, Ausnahmen jederzeit abzulehnen oder zu genehmigen.

In diesem Abschnitt werden allgemeine Szenarien, in denen Herausgeber eine Ausnahme anfordern, und die entsprechenden Vorgehensweisen beschrieben.

### <a name="scenarios-for-exception"></a>Szenarien für eine Ausnahme

Herausgeber fordern Ausnahmen normalerweise in den folgenden Fällen an:

- **Ausnahme für einen oder mehrere Testfälle**: Wenden Sie sich an den [Partner Center-Support](https://aka.ms/marketplacepublishersupport), um Ausnahmen für Testfälle anzufordern.

- **Gesperrte VMs/kein Root-Zugriff**: Bei einigen Herausgebern müssen VMs gesperrt werden, weil Software (z. B. Firewalls) auf der VM installiert ist. Laden Sie in diesem Fall das [Certified-Testtool](https://aka.ms/AzureCertificationTestTool) herunter, und übermitteln Sie den Bericht an den [Partner Center-Support](https://aka.ms/marketplacepublishersupport).

- **Benutzerdefinierte Vorlagen**: Einige Herausgeber veröffentlichen VM-Images, für die eine benutzerdefinierte ARM-Vorlage (Azure Resource Manager) zum Bereitstellen der VMs erforderlich ist. Reichen Sie in diesem Fall die benutzerdefinierten Vorlagen beim [Partner Center-Support](https://aka.ms/marketplacepublishersupport) ein, damit diese vom Zertifizierungsteam für die Überprüfung verwendet werden können.

### <a name="information-to-provide-for-exception-scenarios"></a>Zu übermittelnde Informationen für Ausnahmeszenarien

Wenden Sie sich an den [Partner Center-Support](https://aka.ms/marketplacepublishersupport), um eine Ausnahme für eines der Szenarien anzufordern, und geben Sie die folgenden Informationen an:

- **Herausgeber-ID**: Geben Sie die Herausgeber-ID aus Ihrem Partner Center-Portal ein.
- **Angebots-ID/Name**: Geben Sie die Angebots-ID oder den Namen ein.
- **SKU/Plan-ID**: Geben Sie die Plan-ID oder SKU des VM-Angebots ein.
- **Version**: Geben Sie die Version des VM-Angebots ein, für das eine Ausnahme erforderlich ist.
- **Ausnahmetyp**: Wählen Sie zwischen Tests, gesperrten virtuellen Computern und benutzerdefinierten Vorlagen.
- **Grund für die Anforderung**: Geben Sie den Grund für die Ausnahmeanforderung sowie Informationen zu den auszuschließenden Tests an.
- **Zeitachse**: Geben Sie das Enddatum für Ihre Ausnahme ein.
- **Anlage**: Angefügte Dokumente mit wichtigen Nachweisinformationen:

  - Fügen Sie für gesperrte virtuelle Computer den Testbericht an.
  - Fügen Sie für benutzerdefinierte Vorlagen die benutzerdefinierte ARM-Vorlage als Anlage an.

  Wenn Sie diese Anlagen nicht anfügen, wird die Anforderung abgelehnt.

## <a name="address-a-vulnerability-or-an-exploit-in-a-vm-offer"></a>Beheben eines Sicherheitsrisikos oder Exploits in einem VM-Angebot

In diesem Abschnitt wird beschrieben, wie Sie ein neues VM-Image bereitstellen, wenn ein Sicherheitsrisiko oder Exploit bei einem Ihrer VM-Images erkannt wird. Dies gilt nur für Azure-VM-Angebote, die auf Azure Marketplace veröffentlicht werden.

> [!NOTE]
> Sie können weder das letzte VM-Image aus einem Plan entfernen noch den Verkauf des letzten Plans für ein Angebot einstellen.

Führen Sie eine der folgenden Aktionen aus:

- Falls Sie über ein neues VM-Image als Ersatz für das gefährdete VM-Image verfügen, helfen Ihnen die Informationen unter [Bereitstellen eines korrigierten VM-Images](#provide-a-fixed-vm-image) weiter.
- Wenn Sie kein neues VM-Image als Ersatz für das einzige VM-Image in einem Plan besitzen und den Plan beenden möchten, können Sie [den Verkauf des Plans einstellen](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan).
- Wenn das einzige VM-Image im Angebot nicht ersetzt werden soll, empfiehlt es sich, [den Verkauf des Angebots einzustellen](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan).

### <a name="provide-a-fixed-vm-image"></a>Bereitstellen eines korrigierten VM-Images

Gehen Sie wie folgt vor, um ein korrigiertes VM-Image als Ersatz für ein VM-Image mit Sicherheitsrisiko oder Exploit bereitzustellen:

1. Stellen Sie ein neues VM-Image bereit, um das Sicherheitsrisiko oder den Exploit zu beheben.
1. Entfernen Sie das VM-Image mit dem Sicherheitsrisiko oder Exploit.
1. Veröffentlichen Sie das Angebot erneut.

#### <a name="provide-a-new-vm-image-to-address-the-security-vulnerability-or-exploit"></a>Bereitstellen eines neuen VM-Images zur Behebung des Sicherheitsrisikos oder Exploits

Bereiten Sie die technischen Ressourcen für das hinzuzufügende VM-Image vor, um diese Schritte ausführen zu können. Weitere Informationen finden Sie unter [Erstellen eines virtuellen Computers mit einer genehmigten Basis](azure-vm-create-using-approved-base.md) oder [Erstellen eines virtuellen Computers mit einem eigenen Image](azure-vm-create-using-own-image.md) und [Generieren eines SAS-URIs für Ihr VM-Image](azure-vm-get-sas-uri.md).

1. Melden Sie sich bei [Partner Center](https://partner.microsoft.com/dashboard/home) an.
1. Wählen Sie im linken Bereich **Kommerzieller Marketplace** > **Übersicht** aus.
1. Wählen Sie in der Spalte **Angebotsalias** das Angebot aus.
1. Wählen Sie auf der Registerkarte **Planübersicht** in der Spalte **Name** den entsprechenden Plan aus.
1. Wählen Sie auf der Registerkarte **Technische Konfiguration** unter **VM-Images** die Option **+ VM-Image hinzufügen** aus.

   > [!NOTE]
   > Sie können einem Plan jeweils nur ein VM-Image hinzufügen. Wenn Sie mehrere VM-Images hinzufügen möchten, sollten Sie das erste VM-Image veröffentlichen, bevor Sie das nächste hinzufügen.

1. Geben Sie in den angezeigten Feldern eine neue Datenträgerversion und das VM-Image an.
1. Wähen Sie **Entwurf speichern** aus.

Entfernen Sie als Nächstes das VM-Image mit dem Sicherheitsrisiko.

#### <a name="remove-the-vm-image-with-the-security-vulnerability-or-exploit"></a>Entfernen des VM-Images mit dem Sicherheitsrisiko oder Exploit

1. Melden Sie sich bei [Partner Center](https://partner.microsoft.com/dashboard/home) an.
2. Wählen Sie im linken Bereich **Kommerzieller Marketplace** > **Übersicht** aus.
3. Wählen Sie in der Spalte **Angebotsalias** das Angebot aus.
4. Wählen Sie auf der Registerkarte **Planübersicht** in der Spalte **Name** den entsprechenden Plan aus.
5. Wählen Sie auf der Registerkarte **Technische Konfiguration** unter **VM-Images** die Option **VM-Image entfernen** neben dem zu entfernenden VM-Image aus.
6. Wählen Sie im Dialogfeld die Option **Weiter** aus.
7. Wähen Sie **Entwurf speichern** aus.

Veröffentlichen Sie das Angebot anschließend erneut.

#### <a name="republish-the-offer"></a>Erneutes Veröffentlichen des Angebots

1. Wählen Sie **Überprüfen und veröffentlichen** aus.
2. Wenn Sie Informationen für das Zertifizierungsteam bereitstellen müssen, fügen Sie diese dem Feld **Hinweise zur Zertifizierung** hinzu.
3. Wählen Sie **Veröffentlichen**.

Unter [Überprüfen und Veröffentlichen von Angeboten](review-publish-offer.md) erfahren Sie, wie Sie die Veröffentlichung abschließen.

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren von VM-Angebotseigenschaften](azure-vm-create-properties.md)
- [Aktive Marketplace-Prämien](partner-center-portal/marketplace-rewards.md)
- Wenden Sie sich an den [Partner Center-Support](https://aka.ms/marketplacepublishersupport), wenn Sie Fragen oder Verbesserungsvorschläge haben.