---
title: Ausführen des Bereitstellungsplaners für die VMware-Notfallwiederherstellung mit Azure Site Recovery
description: In diesem Artikel wird beschrieben, wie Sie den Azure Site Recovery-Bereitstellungsplaner für die VMware-Notfallwiederherstellung in Azure ausführen.
author: rajeswari-mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/15/2019
ms.author: ramamill
ms.openlocfilehash: 05d260de726c62c130a58938c2a2c9fa2440a96d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004720"
---
# <a name="run-the-deployment-planner-for-vmware-disaster-recovery"></a>Ausführen des Bereitstellungsplaners für die VMware-Notfallwiederherstellung
Dieser Artikel ist der Leitfaden zum Deployment Planner (Bereitstellungsplaner) von Azure Site Recovery für Bereitstellungen von „VMware zu Azure“ in der Produktion.


## <a name="modes-of-running-deployment-planner"></a>Modi der Bereitstellungsplaner-Ausführung
Sie können das Befehlszeilentool (ASRDeploymentPlanner.exe) in einem der folgenden drei Modi ausführen:

1.  [Profilerstellung](#profile-vmware-vms)
2.  [Berichterstellung](#generate-report)
3.  [Durchsatzberechnung](#get-throughput)

Führen Sie das Tool zuerst im Modus für die Profilerstellung aus, um für die VM die Datenänderung und den IOPS-Wert zu erfassen. Führen Sie im Tool als Nächstes die Berichterstellung durch, um die Netzwerkbandbreite, die Speicheranforderungen und die Kosten für die Notfallwiederherstellung zu ermitteln.

## <a name="profile-vmware-vms"></a>Durchführen der Profilerstellung für VMware-VMs
Im Profilerstellungsmodus stellt das Deployment Planner-Tool eine Verbindung mit dem vCenter Server bzw. vSphere ESXi-Host her, um Leistungsdaten zur VM zu sammeln.

* Die Profilerstellung wirkt sich nicht negativ auf die Leistung der Produktions-VMs aus, weil keine direkte Verbindung damit hergestellt wird. Alle Leistungsdaten werden über den vCenter-Server/vSphere ESXi-Host ermittelt.
* Um sicherzustellen, dass die Auswirkungen der Profilerstellung auf den Server vernachlässigbar sind, fragt das Tool den vCenter-Server/vSphere ESXi-Host alle 15 Minuten ab. Die Genauigkeit der Profilerstellung wird durch dieses Abfrageintervall nicht beeinträchtigt, weil die Leistungsindikatordaten vom Tool minutengenau gespeichert werden.

### <a name="create-a-list-of-vms-to-profile"></a>Erstellen einer Liste mit VMs für die Profilerstellung
Zuerst benötigen Sie eine Liste mit den VMs, für die die Profilerstellung durchgeführt werden soll. Sie können alle Namen von VMs auf einem vCenter-Server/vSphere ESXi-Host abrufen, indem Sie im Rahmen des folgenden Verfahrens die VMware vSphere PowerCLI-Befehle verwenden. Alternativ dazu können Sie die Anzeigenamen oder IP-Adressen der VMs, für die die Profilerstellung durchgeführt werden soll, auch manuell in einer Datei auflisten.

1. Melden Sie sich an der VM an, auf der VMware vSphere PowerCLI installiert ist.
2. Öffnen Sie die VMware vSphere PowerCLI-Konsole.
3. Stellen Sie sicher, dass die Ausführungsrichtlinie für das Skript aktiviert ist. Wenn sie deaktiviert ist, können Sie die VMware vSphere PowerCLI-Konsole im Administratormodus starten und dann aktivieren, indem Sie den folgenden Befehl ausführen:

    ```powershell
    Set-ExecutionPolicy –ExecutionPolicy AllSigned
    ```

4. Sie müssen möglicherweise den folgenden Befehl ausführen, falls Connect-VIServer nicht als Cmdlet-Name erkannt wird.

    ```powershell
    Add-PSSnapin VMware.VimAutomation.Core
    ```

5. Zum Abrufen aller Namen von VMs eines vCenter-Servers/vSphere ESXi-Hosts und Speichern der Liste in einer TXT-Datei führen Sie die beiden hier angegebenen Befehle aus.
Ersetzen Sie &lsaquo;server name&rsaquo;, &lsaquo;user name&rsaquo;, &lsaquo;password&rsaquo; und &lsaquo;outputfile.txt&rsaquo; durch Ihre Angaben:

    ```powershell
    Connect-VIServer -Server <server name> -User <user name> -Password <password>

    Get-VM |  Select Name | Sort-Object -Property Name >  <outputfile.txt>
    ```

6. Öffnen Sie die Ausgabedatei im Editor, und kopieren Sie die Namen aller VMs, für die Profile erstellt werden sollen, in eine andere Datei (z.B. „ProfileVMList.txt“). Fügen Sie einen VM-Namen pro Zeile ein. Diese Datei wird als Eingabe für den Parameter *-VMListFile* des Befehlszeilentools verwendet.

    ![Liste mit VM-Namen im Deployment Planner
](media/site-recovery-vmware-deployment-planner-run/profile-vm-list-v2a.png)

### <a name="start-profiling"></a>Starten der Profilerstellung
Nachdem Sie die Liste mit den VMs für die Profilerstellung vorliegen haben, können Sie für das Tool den Modus für die Profilerstellung ausführen. Unten ist die Liste mit den obligatorischen und optionalen Parametern des Tools für die Ausführung im Modus für die Profilerstellung angegeben.

```
ASRDeploymentPlanner.exe -Operation StartProfiling /?
```

| Parametername | BESCHREIBUNG |
|---|---|
| -Operation | StartProfiling |
| -Server | Der vollständig qualifizierte Domänenname oder die IP-Adresse des vCenter-Servers/vSphere ESXi-Hosts, für dessen VMs die Profilerstellung durchgeführt werden soll.|
| -User | Der Benutzername für die Herstellung der Verbindung mit dem vCenter-Server/vSphere ESXi-Host. Der Benutzer muss mindestens über Lesezugriff verfügen.|
| -VMListFile | Die Datei mit der Liste der VMs, für die Profile erstellt werden sollen. Der Dateipfad kann absolut oder relativ sein. Die Datei sollte einen VM-Namen bzw. eine IP-Adresse pro Zeile enthalten. Der in der Datei angegebene Name des virtuellen Computers sollte mit dem VM-Namen auf dem vCenter-Server/vSphere ESXi-Host identisch sein.<br>Die Datei „VMList.txt“ enthält beispielsweise die folgenden VMs:<ul><li>virtual_machine_A</li><li>10.150.29.110</li><li>virtual_machine_B</li><ul> |
|-NoOfMinutesToProfile|Gibt an, wie lange die Profilerstellung durchgeführt werden soll (in Minuten). Der Mindestwert lautet 30 Minuten.|
|-NoOfHoursToProfile|Gibt an, wie lange die Profilerstellung durchgeführt werden soll (in Stunden).|
| -NoOfDaysToProfile | Gibt an, wie lange die Profilerstellung durchgeführt werden soll (in Tagen). Es wird empfohlen, die Profilerstellung länger als 7 Tage durchzuführen. So kann sichergestellt werden, dass das Workloadmuster in Ihrer Umgebung im angegebenen Zeitraum eingehalten und verwendet wird, um eine genaue Empfehlung zu erhalten. |
|-Virtualization|Geben Sie den Virtualisierungstyp an (VMware oder Hyper-V).|
| -Directory | (Optional) Der UNC-Pfad (Universal Naming Convention) oder lokale Verzeichnispfad zum Speichern der Daten, die während des Profilerstellungsvorgangs generiert werden. Wenn kein Verzeichnisname angegeben ist, wird das Verzeichnis mit dem Namen „ProfiledData“ unter dem aktuellen Pfad als Standardverzeichnis verwendet. |
| -Password | (Optional) Das Kennwort zum Herstellen der Verbindung mit dem vCenter-Server/vSphere ESXi-Host. Wenn Sie hier kein Kennwort angeben, werden Sie bei der Ausführung des Befehls zur Eingabe aufgefordert.|
|-Port|(Optional) Portnummer zum Herstellen der Verbindung mit dem vCenter/ESXi-Host. Der Standardport ist 443.|
|-Protocol| (Optional) Geben Sie als Protokoll entweder „http“ oder „https“ für die Verbindungsherstellung mit vCenter an. Das Standardprotokoll ist „https“.|
| -StorageAccountName | (Optional) Der Name des Speicherkontos zur Ermittlung des Durchsatzes, der für die Replikation von Daten aus der lokalen Umgebung in Azure erreichbar ist. Mit dem Tool werden Testdaten in dieses Speicherkonto hochgeladen, um den Durchsatz zu berechnen. Das Speicherkonto muss vom Typ „Allgemein v1 (GPv1)“ sein. |
| -StorageAccountKey | (Optional) Der Schlüssel des Speicherkontos, der zum Zugreifen auf das Speicherkonto verwendet wird. Navigieren Sie im Azure-Portal zu „Speicherkonten“ > <*Name des Speicherkontos*> > Einstellungen > Zugriffsschlüssel > Key1. |
| -Environment | (optional) Dies ist Ihre Azure Storage-Zielkontoumgebung. Diese drei Werte sind möglich: „AzureCloud“, „AzureUSGovernment“ und „AzureChinaCloud“. Der Standardwert ist „AzureCloud“. Verwenden Sie den Parameter, wenn Sie als Zielregion „Azure US Government“ oder „Azure China 21Vianet“ nutzen. |


Es wird empfohlen, die Profilerstellung für Ihre VMs länger als 7 Tage durchzuführen. Wenn sich das Muster der Datenänderung innerhalb eines Monats ändert, ist es ratsam, die Profilerstellung in die Woche mit der höchsten Datenänderung zu legen. Die beste Möglichkeit besteht darin, die Profilerstellung 31 Tage lang durchzuführen, um eine bessere Empfehlung zu erhalten. Während des Zeitraums der Profilerstellung wird „ASRDeploymentPlanner.exe“ weiter ausgeführt. Im Tool wird der Zeitraum für die Profilerstellung in Tagen eingegeben. Sie können die Profilerstellung einige Stunden oder Minuten lang durchführen, um kurz das Tool bzw. den Ablauf (Proof of Concept) zu testen. Die kürzeste zulässige Dauer für die Profilerstellung beträgt 30 Minuten.

Während der Profilerstellung können Sie optional den Namen eines Speicherkontos und den dazugehörigen Schlüssel übergeben, um den Durchsatz zu ermitteln, der für Site Recovery bei der Replikation vom Konfigurationsserver oder Prozessserver zu Azure erreicht werden kann. Wenn der Name des Speicherkontos und der Schlüssel während der Profilerstellung nicht übergeben werden, wird der erreichbare Durchsatz vom Tool nicht berechnet.

Sie können mehrere Instanzen des Tools für verschiedene Gruppen von VMs ausführen. Stellen Sie sicher, dass die VM-Namen in den Gruppen für die Profilerstellung nicht mehr als einmal vorkommen. Wenn Sie beispielsweise Profile für zehn VMs (VM1 bis VM10) erstellt haben und nach einigen Tagen Profile für fünf weitere VMs (VM11 bis VM15) erstellen möchten, können Sie das Tool für die zweite Gruppe von VMs (VM11 bis VM15) über eine andere Befehlszeilenkonsole ausführen. Stellen Sie hierbei sicher, dass die zweite Gruppe von VMs keine Namen der VMs aus der ersten Profilerstellungsinstanz enthält, oder verwenden Sie für die zweite Ausführung ein anderes Ausgabeverzeichnis. Wenn zwei Instanzen des Tools für die Profilerstellung derselben VMs verwendet werden und dabei dasselbe Ausgabeverzeichnis genutzt wird, ist der generierte Bericht fehlerhaft.

Standardmäßig ist das Tool so konfiguriert, dass für bis zu 1000 VMs Profile und Berichte erstellt werden. Sie können diesen Grenzwert ändern, indem Sie den Schlüsselwert MaxVMsSupported in der Datei *ASRDeploymentPlanner.exe.config* ändern.
```
<!-- Maximum number of vms supported-->
<add key="MaxVmsSupported" value="1000"/>
```
Wenn Sie mit den Standardeinstellungen beispielsweise Profile für 1.500 VMs erstellen möchten, können Sie zwei Dateien vom Typ „VMList.txt“ verwenden. Eine mit 1.000 VMs und eine weitere mit 500 VMs. Führen Sie die beiden Instanzen des Azure Site Recovery-Bereitstellungsplaners aus, eine mit „VMList1.txt“ und eine mit „VMList2.txt“. Sie können zum Speichern der Profildaten beider VMList-VMs den gleichen Verzeichnispfad verwenden.

Es wurde verdeutlicht, dass basierend auf der Hardwarekonfiguration – vor allem der RAM-Größe des Servers, auf dem das Tool für die Berichtserstellung ausgeführt wird – der Vorgang aufgrund von unzureichendem Arbeitsspeicher fehlschlagen kann. Wenn Sie über gute Hardware verfügen, können Sie MaxVMsSupported auch auf einen höheren Wert festlegen.  

Bei Verwendung von mehreren vCenter-Servern müssen Sie für die Profilerstellung eine Instanz von ASRDeploymentPlanner für jeden vCenter-Server ausführen.

VM-Konfigurationen werden zu Beginn des Profilerstellungsvorgangs einmal erfasst und in einer Datei mit dem Namen „VMDetailList.xml“ gespeichert. Diese Informationen werden für die Berichterstellung verwendet. Alle Änderungen der VM-Konfiguration (z.B. erhöhte Anzahl von Kernen, Datenträgern oder NICs) vom Anfang bis zum Ende der Profilerstellung werden nicht erfasst. Wenn sich eine betroffene VM-Konfiguration während der Profilerstellung geändert hat, können Sie dieses Problem für die öffentliche Vorschauversion wie folgt umgehen, um bei der Berichterstellung die aktuellen VM-Details zu erhalten:

* Sichern Sie die Datei „VMdetailList.xml“, und löschen Sie die Datei an ihrem aktuellen Speicherort.
* Übergeben Sie die Argumente „-User“ und „-Password“ während der Berichterstellung.

Mit dem Befehl für die Profilerstellung werden im Verzeichnis der Profilerstellung mehrere Dateien generiert. Löschen Sie keine Dateien, weil sich dies auf die Berichterstellung auswirkt.

#### <a name="example-1-profile-vms-for-30-days-and-find-the-throughput-from-on-premises-to-azure"></a>Beispiel 1: VM-Profilerstellung für 30 Tage, Ermittlung des Durchsatzes von lokal zu Azure
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile  30  -User vCenterUser1 -StorageAccountName  asrspfarm1 -StorageAccountKey Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

#### <a name="example-2-profile-vms-for-15-days"></a>Beispiel 2: VM-Profilerstellung für 15 Tage

```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -NoOfDaysToProfile  15  -User vCenterUser1
```

#### <a name="example-3-profile-vms-for-60-minutes-for-a-quick-test-of-the-tool"></a>Beispiel 3: VM-Profilerstellung für 60 Minuten zum schnellen Testen des Tools
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfMinutesToProfile 60  -User vCenterUser1
```

#### <a name="example-4-profile-vms-for-2-hours-for-a-proof-of-concept"></a>Beispiel 4: VM-Profilerstellung für 2 Stunden zu Proof of Concept-Zwecken
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -NoOfHoursToProfile 2 -User vCenterUser1
```

>[!NOTE]
>
>* Wenn der Server, auf dem das Tool ausgeführt wird, neu gestartet wird oder abstürzt oder wenn Sie das Tool mit STRG+C schließen, werden die Profilerstellungsdaten beibehalten. Es kann aber sein, dass die letzten 15 Minuten der Daten für die Profilerstellung fehlen. Führen Sie das Tool in diesem Fall im Profilerstellungsmodus aus, nachdem der Server neu gestartet wurde.
>* Wenn der Name des Speicherkontos und der dazugehörige Schlüssel übergeben werden, misst das Tool den Durchsatz im letzten Schritt der Profilerstellung. Falls das Tool geschlossen wird, bevor die Profilerstellung abgeschlossen ist, wird der Durchsatz nicht berechnet. Zur Ermittlung des Durchsatzes vor dem Generieren des Berichts können Sie den GetThroughput-Vorgang über die Befehlszeilenkonsole ausführen. Andernfalls sind die Informationen zum Durchsatz nicht im Bericht enthalten.


## <a name="generate-report"></a>Erstellen des Berichts
Das Tool generiert eine makrofähige Microsoft Excel-Datei (XLSM-Datei) als Berichtsausgabe, in der alle Empfehlungen für die Bereitstellung zusammengefasst sind. Der Bericht hat den Namen `DeploymentPlannerReport_<unique numeric identifier>.xlsm` und wird im angegebenen Verzeichnis gespeichert.

>[!NOTE]
>Damit der Bericht erstellt werden kann, ist ein Windows-PC oder Windows-Server mit Excel 2013 oder höher erforderlich. Das Dezimalzeichen auf diesem Computer sollte als „.“ festgelegt sein, damit die Kostenschätzungen erstellt werden können. Falls Sie „,“ als Dezimalzeichen eingerichtet haben, navigieren Sie bitte in der Systemsteuerung zu „Ändern von Datums-, Uhrzeit- oder Zahlenformaten“ und navigieren Sie zu „Weitere Einstellungen“, um das Dezimalzeichen in „.“ zu ändern.

Nach Abschluss der Profilerstellung können Sie das Tool im Berichterstellungsmodus ausführen. Die folgende Tabelle enthält eine Liste mit den erforderlichen und optionalen Toolparametern für die Ausführung im Modus für die Berichterstellung.

`ASRDeploymentPlanner.exe -Operation GenerateReport /?`

|Parametername | BESCHREIBUNG |
|-|-|
| -Operation | GenerateReport |
| -Server |  Der vollqualifizierte Name oder die IP-Adresse (der gleiche Name bzw. die gleiche IP-Adresse wie bei der Profilerstellung) des vCenter-/vSphere-Servers, auf dem sich die VMs für die Profilerstellung befinden, für die der Bericht erstellt werden soll. Beachten Sie Folgendes: Wenn Sie bei der Profilerstellung einen vCenter-Server verwendet haben, können Sie keinen vSphere-Server für die Berichterstellung verwenden (und umgekehrt).|
| -VMListFile | Die Datei mit der Liste mit den VMs für die Profilerstellung, für die der Bericht erstellt werden soll. Der Dateipfad kann absolut oder relativ sein. Die Datei sollte einen VM-Namen bzw. eine IP-Adresse pro Zeile enthalten. Die VM-Namen, die in der Datei angegeben werden, sollten den VM-Namen auf dem vCenter-Server/vSphere ESXi-Host entsprechen und mit den Angaben für die Profilerstellung übereinstimmen.|
|-Virtualization|Geben Sie den Virtualisierungstyp an (VMware oder Hyper-V).|
| -Directory | (Optional) Der UNC- oder lokale Verzeichnispfad des Speicherorts, an dem die Daten der Profilerstellung (während der Profilerstellung generierte Dateien) gespeichert werden. Diese Daten werden für die Erstellung des Berichts benötigt. Wenn kein Name vorhanden ist, wird das Verzeichnis „ProfiledData“ verwendet. |
| -GoalToCompleteIR | (Optional) Die Anzahl von Stunden, in denen die erste Replikation der VMs für die Profilerstellung abgeschlossen werden muss. Im erstellten Bericht ist die Anzahl von VMs enthalten, für die die erste Replikation im angegebenen Zeitraum abgeschlossen werden kann. Die Standardeinstellung beträgt 72 Stunden. |
| -User | (Optional) Der Benutzername für die Herstellung der Verbindung mit dem vCenter-/vSphere-Server. Der Name dient zum Abrufen der aktuellen Konfigurationsinformationen der VMs, z.B. Anzahl von Datenträgern, Anzahl von Kernen und Anzahl von NICs, für die Verwendung im Bericht. Wenn der Name nicht angegeben wird, werden die Konfigurationsinformationen verwendet, die zu Beginn der Profilerstellung erfasst wurden. |
| -Password | (Optional) Das Kennwort zum Herstellen der Verbindung mit dem vCenter-Server/vSphere ESXi-Host. Wenn das Kennwort nicht als Parameter angegeben ist, werden Sie später bei der Ausführung des Befehls zum Eingeben aufgefordert. |
|-Port|(Optional) Portnummer zum Herstellen der Verbindung mit dem vCenter/ESXi-Host. Der Standardport ist 443.|
|-Protocol|(Optional) Geben Sie als Protokoll entweder „http“ oder „https“ für die Verbindungsherstellung mit vCenter an. Das Standardprotokoll ist „https“.|
| -DesiredRPO | (Optional) Der gewünschte RPO-Wert (Recovery Point Objective) in Minuten. Der Standardwert ist 15 Minuten.|
| -Bandwidth | Bandbreite in MBit/s. Der Parameter wird zum Berechnen des RPO-Werts verwendet, der für die angegebene Bandbreite erzielt werden kann. |
| -StartDate | (Optional) Das Startdatum und die Uhrzeit im Format MM-TT-JJJJ:HH:MM (24-Stunden-Format). *StartDate* muss zusammen mit *EndDate* angegeben werden. Wenn „StartDate“ angegeben ist, wird der Bericht für die Profilerstellungsdaten erstellt, die zwischen „StartDate“ und „EndDate“ erfasst wurden. |
| -EndDate | (Optional) Das Enddatum und die Uhrzeit im Format MM-TT-JJJJ:HH:MM (24-Stunden-Format). *EndDate* muss zusammen mit *StartDate* angegeben werden. Wenn „EndDate“ angegeben ist, wird der Bericht für die Profilerstellungsdaten erstellt, die zwischen „StartDate“ und „EndDate“ erfasst wurden. |
| -GrowthFactor | (Optional) Der Zuwachsfaktor als Prozentsatz. Der Standardwert ist 30 Prozent. |
| -UseManagedDisks | (Optional) UseManagedDisks – Ja/Nein. Die Standardeinstellung ist „Ja“. Für Anzahl der virtuellen Computer in einer einzelnen Speicherkontoplatzierung wird bei der Berechnung berücksichtigt, dass das Failover bzw. Testfailover von virtuellen Computern auf einem verwalteten Datenträger statt auf einem nicht verwalteten Datenträger erfolgt. |
|-SubscriptionId |(Optional) Die GUID für das Abonnement. Beachten Sie, dass dieser Parameter erforderlich ist, wenn Sie den Bericht zur Kostenvorkalkulation mit dem aktuellen Preis generieren, der auf Ihrem Abonnement, dem Angebot zu Ihrem Abonnement und Ihrer jeweiligen Azure-Zielregion in der **gewählten Währung** basiert.|
|-TargetRegion|(Optional) Die Azure-Zielregion für die Replikation. Verwenden Sie diesen Parameter, um einen Bericht mit der jeweiligen Azure-Zielregion zu erstellen, da Azure über unterschiedliche Kosten pro Region verfügt.<br>Die Standardeinstellung ist „USA, Westen 2“ bzw. die zuletzt verwendete Zielregion.<br>Weitere Informationen finden Sie in der Liste mit den [unterstützten Zielregionen](site-recovery-vmware-deployment-planner-cost-estimation.md#supported-target-regions).|
|-OfferId|(Optional) Das Angebot, das dem jeweiligen Abonnement zugeordnet ist. Die Standardeinstellung ist MS-AZR-0003P (nutzungsbasierte Bezahlung).|
|-Currency|(Optional) Die Währung, in der Kosten im generierten Bericht angezeigt werden. Die Standardeinstellung ist „US-Dollar ($)“ bzw. die zuletzt verwendete Währung.<br>Weitere Informationen finden Sie in der Liste mit den [unterstützten Währungen](site-recovery-vmware-deployment-planner-cost-estimation.md#supported-currencies).|

Standardmäßig ist das Tool so konfiguriert, dass für bis zu 1000 VMs Profile und Berichte erstellt werden. Sie können diesen Grenzwert ändern, indem Sie den Schlüsselwert MaxVMsSupported in der Datei *ASRDeploymentPlanner.exe.config* ändern.
```xml
<!-- Maximum number of vms supported-->
<add key="MaxVmsSupported" value="1000"/>
```

#### <a name="example-1-generate-a-report-with-default-values-when-the-profiled-data-is-on-the-local-drive"></a>Beispiel 1: Berichterstellung mit Standardwerten, wenn sich die Profilerstellungsdaten auf dem lokalen Laufwerk befinden
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-2-generate-a-report-when-the-profiled-data-is-on-a-remote-server"></a>Beispiel 2: Berichterstellung, wenn sich die Profilerstellungsdaten auf einem Remoteserver befinden
Sie sollten über Lese-/Schreibzugriff für das Remoteverzeichnis verfügen.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “\\PS1-W2K12R2\vCenter1_ProfiledData” -VMListFile “\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-3-generate-a-report-with-a-specific-bandwidth-and-goal-to-complete-ir-within-specified-time"></a>Beispiel 3: Berichterstellung mit spezifischer Bandbreite und der Vorgabe, die erste Replikation innerhalb der angegebenen Zeit abzuschließen
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -Bandwidth 100 -GoalToCompleteIR 24
```

#### <a name="example-4-generate-a-report-with-a-5-percent-growth-factor-instead-of-the-default-30-percent"></a>Beispiel 4: Berichterstellung mit Zuwachsfaktor von 5 Prozent (anstelle der Standardeinstellung von 30 Prozent)
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -GrowthFactor 5
```

#### <a name="example-5-generate-a-report-with-a-subset-of-profiled-data"></a>Beispiel 5: Berichterstellung mit einer Teilmenge der Profilerstellungsdaten
Es kann beispielsweise sein, dass Sie über Profilerstellungsdaten für 30 Tage verfügen und den Bericht nur für 20 Tage erstellen möchten.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -StartDate  01-10-2017:12:30 -EndDate 01-19-2017:12:30
```

#### <a name="example-6-generate-a-report-for-5-minute-rpo"></a>Beispiel 6: Berichterstellung für RPO von 5 Minuten
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -DesiredRPO 5
```

#### <a name="example-7-generate-a-report-for-south-india-azure-region-with-indian-rupee-and-specific-offer-id"></a>Beispiel 7: Berichterstellung für die Azure-Region „Indien, Süden“ mit indischer Rupie und spezifischer Angebots-ID

Beachten Sie, dass die Abonnement-ID erforderlich ist, um Kostenberichte in einer bestimmten Währung zu generieren.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware  -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -SubscriptionID 4d19f16b-3e00-4b89-a2ba-8645edf42fe5 -OfferID MS-AZR-0148P -TargetRegion southindia -Currency INR
```

## <a name="percentile-value-used-for-the-calculation"></a>Für die Berechnung verwendeter Perzentilwert
**Welcher Perzentilstandardwert der Leistungsmetriken, die während der Profilerstellung gesammelt wurden, wird vom Tool für die Berichterstellung verwendet?**

Für das Tool werden standardmäßig die Werte des 95. Perzentils für Lese/Schreib-IOPS, Schreib-IOPS und die Datenänderung verwendet, die während der Profilerstellung aller VMs gesammelt wurden. Mit dieser Metrik wird sichergestellt, dass Spitzen (100. Perzentil), die für Ihre VMs aufgrund von temporären Ereignissen auftreten können, nicht zur Ermittlung der Bandbreitenanforderungen für Ihr Zielspeicherkonto und Ihre Quelle herangezogen werden. Ein temporäres Ereignis kann beispielsweise ein Sicherungsauftrag sein, der einmal pro Tag ausgeführt wird, oder eine regelmäßige Datenbankindizierung, eine Aktivität zur Erstellung eines Analyseberichts oder ähnliche Point-in-Time-Ereignisse mit kurzer Lebensdauer.

Bei Verwendung der Werte des 95. Perzentils erhalten Sie ein genaues Bild der echten Workloadmerkmale und erzielen die beste Leistung, wenn die Workloads in Azure ausgeführt werden. Dieser Wert muss normalerweise nicht geändert werden. Falls Sie den Wert doch ändern möchten (z.B. in das 90. Perzentil), können Sie die Konfigurationsdatei *ASRDeploymentPlanner.exe.config* im Standardordner aktualisieren und speichern, um einen neuen Bericht für die vorhandenen Profilerstellungsdaten zu erstellen.
```xml
<add key="WriteIOPSPercentile" value="95" />      
<add key="ReadWriteIOPSPercentile" value="95" />      
<add key="DataChurnPercentile" value="95" />
```

## <a name="growth-factor-considerations"></a>Informationen zum Zuwachsfaktor
**Warum sollte ich den Zuwachsfaktor beim Planen von Bereitstellungen berücksichtigen?**

Es ist wichtig, dass Sie in Bezug auf Ihre Workloadmerkmale den Zuwachs und eine potenzielle vermehrte Nutzung im Laufe der Zeit berücksichtigen. Wenn Sich Ihre Workloadmerkmale nach der Einrichtung des Schutzes ändern, können Sie in Bezug auf den Schutz nicht zu einem anderen Speicherkonto wechseln, ohne den Schutz zu deaktivieren und anschließend wieder zu aktivieren.

Angenommen, Ihre VM ist derzeit für ein Standardspeicherkonto für die Replikation eingerichtet. Die Wahrscheinlichkeit ist hoch, dass es im Laufe der nächsten drei Monate zu mehreren Änderungen kommt:

* Die Anzahl von Benutzern der Anwendung, die auf der VM ausgeführt wird, erhöht sich.
* Aufgrund der sich daraus ergebenden erhöhten Datenänderungsrate auf der VM muss die VM auf Storage Premium umgestellt werden, damit die Site Recovery-Replikation Schritt halten kann.
* Sie müssen also den Schutz deaktivieren und für ein Storage Premium-Konto wieder aktivieren.

Es wird dringend empfohlen, beim Planen der Bereitstellung den Zuwachs zu berücksichtigen und den Standardwert von 30 Prozent zu verwenden. Sie sind der Experte in Bezug auf das Nutzungsmuster Ihrer Anwendungen und den projizierten Zuwachs und können diese Angabe beim Erstellen des Berichts entsprechend ändern. Außerdem haben Sie die Möglichkeit, mehrere Berichte mit unterschiedlichen Zuwachsfaktoren für dieselben Profilerstellungsdaten zu erstellen, um zu ermitteln, welche Empfehlungen für die Speicherzielbandbreite und die Quellbandbreite für Sie am besten geeignet sind.

Der erstellte Microsoft Excel-Bericht enthält die folgenden Informationen:

* [On-premises Summary](site-recovery-vmware-deployment-planner-analyze-report.md#on-premises-summary) (Lokale Zusammenfassung)
* [Empfehlungen](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations)
* [VM&lt;-&gt;Storage Placement](site-recovery-vmware-deployment-planner-analyze-report.md#vm-storage-placement) (VM/Speicher-Anordnung)
* [Compatible VMs](site-recovery-vmware-deployment-planner-analyze-report.md#compatible-vms) (Kompatible VMs)
* [Incompatible VMs](site-recovery-vmware-deployment-planner-analyze-report.md#incompatible-vms) (Inkompatible VMs)
* [Cost Estimation](site-recovery-vmware-deployment-planner-cost-estimation.md) (Kostenvorkalkulation)

![Deployment Planner](media/site-recovery-vmware-deployment-planner-analyze-report/Recommendations-v2a.png)

## <a name="get-throughput"></a>Durchsatzberechnung

Führen Sie das Tool im GetThroughput-Modus aus, um den Durchsatz zu schätzen, der von Site Recovery während der Replikation von der lokalen Umgebung zu Azure erreicht werden kann. Das Tool berechnet den Durchsatz für den Server, auf dem das Tool ausgeführt wird. Idealerweise basiert dieser Server auf den Vorgaben des Leitfadens zur Größe des Konfigurationsservers. Führen Sie das Tool auf dem Konfigurationsserver aus, falls Sie Site Recovery-Infrastrukturkomponenten bereits lokal bereitgestellt haben.

Öffnen Sie eine Befehlszeilenkonsole, und wechseln Sie in den Ordner mit dem Site Recovery-Tool für die Bereitstellungsplanung. Führen Sie die Datei „ASRDeploymentPlanner.exe“ mit den unten angegebenen Parametern aus.

`ASRDeploymentPlanner.exe -Operation GetThroughput /?`

|Parametername | BESCHREIBUNG |
|-|-|
| -Operation | GetThroughput |
|-Virtualization|Geben Sie den Virtualisierungstyp an (VMware oder Hyper-V).|
| -Directory | (Optional) Der UNC- oder lokale Verzeichnispfad des Speicherorts, an dem die Daten der Profilerstellung (während der Profilerstellung generierte Dateien) gespeichert werden. Diese Daten werden für die Erstellung des Berichts benötigt. Wenn kein Verzeichnisname angegeben ist, wird das Verzeichnis „ProfiledData“ verwendet. |
| -StorageAccountName | Der Name des Speicherkontos zur Ermittlung der Bandbreite, die für die Replikation von Daten aus der lokalen Umgebung in Azure verbraucht wird. Mit dem Tool werden Testdaten in dieses Speicherkonto hochgeladen, um die verbrauchte Bandbreite zu ermitteln. Das Speicherkonto muss vom Typ „Allgemein v1 (GPv1)“ sein.|
| -StorageAccountKey | Der Schlüssel des Speicherkontos, der zum Zugreifen auf das Speicherkonto verwendet wird. Navigieren Sie im Azure-Portal zu „Speicherkonten“ > <*Name des Speicherkontos*> > „Einstellungen“ > „Zugriffsschlüssel“ > „Key1“ (oder primärer Zugriffsschlüssel für ein klassisches Speicherkonto). |
| -VMListFile | Die Datei mit der Liste der VMs, für die Profile erstellt werden sollen, um die verbrauchte Bandbreite zu berechnen. Der Dateipfad kann absolut oder relativ sein. Die Datei sollte einen VM-Namen bzw. eine IP-Adresse pro Zeile enthalten. Die in der Datei angegebenen VM-Namen sollten mit den VM-Namen auf dem vCenter-Server/vSphere ESXi-Host identisch sein.<br>Die Datei „VMList.txt“ enthält beispielsweise die folgenden VMs:<ul><li>VM_A</li><li>10.150.29.110</li><li>VM_B</li></ul>|
| -Environment | (optional) Dies ist Ihre Azure Storage-Zielkontoumgebung. Diese drei Werte sind möglich: „AzureCloud“, „AzureUSGovernment“ und „AzureChinaCloud“. Der Standardwert ist „AzureCloud“. Verwenden Sie den Parameter, wenn Sie als Zielregion „Azure US Government“ oder „Azure China 21Vianet“ nutzen. |

Mit dem Tool werden im angegebenen Verzeichnis mehrere Dateien vom Typ „asrvhdfile<#>.vhd“ (wobei „#“ für eine Zahl steht) mit 64 MB erstellt. Mit dem Tool werden diese Dateien in das Speicherkonto hochgeladen, um den Durchsatz zu ermitteln. Nach dem Messen des Durchsatzes löscht das Tool diese Dateien aus dem Speicherkonto und vom lokalen Server. Wenn das Tool aus irgendeinem Grund beendet wird, während der Durchsatz berechnet wird, werden die Dateien nicht aus dem Speicher oder vom lokalen Server gelöscht. Sie müssen sie manuell löschen.

Der Durchsatz wird zu einem angegebenen Zeitpunkt gemessen. Hierbei handelt es sich um den maximalen Durchsatz, der von Site Recovery während der Replikation erreicht werden kann, wenn alle anderen Faktoren gleich bleiben. Falls eine Anwendung beispielsweise in demselben Netzwerk auf einmal mehr Bandbreite verbraucht, variiert der tatsächliche Durchsatz während der Replikation. Wenn Sie den GetThroughput-Befehl über einen Konfigurationsserver ausführen, hat das Tool keinerlei Informationen zu geschützten VMs und zur laufenden Replikation. Das Ergebnis des gemessenen Durchsatzes ist anders, wenn der GetThroughput-Vorgang ausgeführt wird, während die geschützten VMs über eine hohe Datenänderungsrate verfügen. Wir empfehlen Ihnen, das Tool zu unterschiedlichen Zeiten während der Profilerstellung auszuführen, um zu verstehen, welche Durchsatzstufen jeweils erreicht werden können. Im Bericht wird vom Tool der zuletzt gemessene Durchsatz angezeigt.

### <a name="example"></a>Beispiel
```
ASRDeploymentPlanner.exe -Operation GetThroughput -Directory  E:\vCenter1_ProfiledData -Virtualization VMware -VMListFile E:\vCenter1_ProfiledData\ProfileVMList1.txt  -StorageAccountName  asrspfarm1 -StorageAccountKey by8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

>[!NOTE]
>
> Führen Sie das Tool auf einem Server aus, der über die gleichen Speicher- und CPU-Merkmale wie der Konfigurationsserver verfügt.
>
> Legen Sie die empfohlene Bandbreite für die Replikation so fest, dass der RPO-Wert in 100 Prozent der Fälle erreicht wird. Nach dem Festlegen der passenden Bandbreite sollten Sie wie folgt vorgehen, falls vom Tool kein Anstieg des erzielten Durchsatzes gemeldet wird:
>
>  1. Überprüfen Sie, ob der Site Recovery-Durchsatz durch die Dienstqualität (Quality of Service, QoS) des Netzwerks eingeschränkt wird.
>
>  2. Überprüfen Sie, ob sich Ihr Site Recovery-Tresor in der nächstgelegenen physisch unterstützten Microsoft Azure-Region befindet, um die Netzwerkwartezeit zu verringern.
>
>  3. Überprüfen Sie Ihre lokalen Speichermerkmale, um zu ermitteln, ob Sie die Hardware (z.B. Wechsel von HDD auf SSD) verbessern können.
>
>  4. Ändern Sie die Site Recovery-Einstellungen auf dem Prozessserver, um [die für die Replikation verwendete Menge an Netzwerkbandbreite zu erhöhen](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth).

## <a name="next-steps"></a>Nächste Schritte
* [Analysieren des generierten Berichts](site-recovery-vmware-deployment-planner-analyze-report.md)
