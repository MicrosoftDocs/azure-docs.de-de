---
title: Ausführen des Azure Site Recovery-Bereitstellungsplaners für die Hyper-V-Notfallwiederherstellung in Azure | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie den Azure Site Recovery-Bereitstellungsplaner für die Hyper-V-Notfallwiederherstellung in Azure ausführen.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: mayg
ms.openlocfilehash: 6528b683ec9464c2b1982d631455718e6fe6f3b7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60748957"
---
# <a name="run-the-azure-site-recovery-deployment-planner-for-hyper-v-disaster-recovery-to-azure"></a>Ausführen des Azure Site Recovery-Bereitstellungsplaners für die Hyper-V-Notfallwiederherstellung in Azure

Sie können das Befehlszeilentool Site Recovery-Bereitstellungsplaner (ASRDeploymentPlanner.exe) in einem der folgenden vier Modi ausführen: 
-   Abrufen der Liste mit virtuellen Computern (VMs)
-   [Profil](#profile-hyper-v-vms)
-   Generieren eines Berichts
-   [Durchsatzberechnung](#get-throughput)

Führen Sie zuerst das Tool aus, um die Liste mit den VMs eines einzelnen oder mehrerer Hyper-V-Hosts abzurufen. Führen Sie das Tool anschließend im Modus für die Profilerstellung aus, um für die VM die Datenänderung und den IOPS-Wert zu erfassen. Führen Sie im Tool anschließend die Berichterstellung durch, um die Netzwerkbandbreite und die Speicheranforderungen zu ermitteln.

## <a name="get-the-vm-list-for-profiling-hyper-v-vms"></a>Abrufen der VM-Liste für die Profilerstellung für Hyper-V-VMs
Zuerst benötigen Sie eine Liste mit den VMs, für die die Profilerstellung durchgeführt werden soll. Verwenden Sie den Modus GetVMList des Bereitstellungsplaner-Tools, um die Liste mit den VMs, die auf mehreren Hyper-V-Hosts vorhanden sind, mit einem einzelnen Befehl zu generieren. Nachdem Sie die vollständige Liste generiert haben, können Sie VMs, für die keine Profilerstellung durchgeführt werden soll, aus der Ausgabedatei entfernen. Nutzen Sie die Ausgabedatei anschließend für alle anderen Vorgänge: Profilerstellung, Berichterstellung und Durchsatzberechnung.

Sie können die VM-Liste generieren, indem Sie im Tool auf einen Hyper-V-Cluster oder einen eigenständigen Hyper-V-Host (oder eine Kombination) verweisen.

### <a name="command-line-parameters"></a>Befehlszeilenparameter
Die folgende Tabelle enthält eine Liste mit den erforderlichen und optionalen Parametern des Tools für die Ausführung im Modus GetVMList. 
```
ASRDeploymentPlanner.exe -Operation GetVMList /?
```

| Parametername | BESCHREIBUNG |
|---|---|
| -Operation | GetVMList |
| -User | Der Benutzername zum Herstellen der Verbindung mit dem Hyper-V-Host oder Hyper-V-Cluster. Der Benutzer muss über Administratorzugriff verfügen.|
| -ServerListFile | Die Datei, in der die Liste mit den Servern enthalten ist, auf denen sich die VMs für die Profilerstellung befinden. Der Dateipfad kann absolut oder relativ sein. Die Datei sollte in jeder Zeile eine der folgenden Angaben enthalten:<ul><li>Hyper-V-Hostname oder -IP-Adresse</li><li>Hyper-V-Clustername oder -IP-Adresse</li></ul><br>**Beispiel:** „ServerList.txt“ enthält die folgenden Server:<ul><li>Host_1</li><li>10.8.59.27</li><li>Cluster_1</li><li>Host_2</li>|
| -Directory|(Optional) Der UNC-Pfad (Universal Naming Convention) oder lokale Verzeichnispfad zum Speichern der Daten, die während dieses Vorgangs generiert werden. Wenn kein Name angegeben ist, wird das Verzeichnis mit dem Namen „ProfiledData“ unter dem aktuellen Pfad als Standardverzeichnis verwendet.|
|-OutputFile| (Optional) Die Datei mit der Liste der von den Hyper-V-Servern abgerufenen VMs wird gespeichert. Wenn kein Name angegeben wird, werden die Details in „VMList.txt“ gespeichert.  Verwenden Sie die Datei zum Starten der Profilerstellung, nachdem Sie die VMs entfernt haben, für die der Vorgang nicht durchgeführt werden soll.|
|-Password|(Optional) Das Kennwort zum Herstellen der Verbindung mit dem Hyper-V-Host. Wenn Sie es nicht als Parameter angeben, werden Sie beim Ausführen des Befehls zum Eingeben aufgefordert.|

### <a name="getvmlist-discovery"></a>GetVMList-Ermittlung

- **Hyper-V-Cluster:** Wenn in der Datei mit der Serverliste der Name des Hyper-V-Clusters angegeben wird, ermittelt das Tool alle Hyper-V-Knoten des Clusters und ruft die VMs der einzelnen Hyper-V-Hosts ab.
**Hyper-V-Host:** Wenn der Name des Hyper-V-Hosts angegeben wird, überprüft das Tool zuerst, ob dieser zu einem Cluster gehört. Wenn ja, ruft das Tool die Knoten ab, die zum jeweiligen Cluster gehören. Anschließend werden die VMs jedes Hyper-V-Hosts abgerufen. 

Sie können in einer Datei die Anzeigenamen oder IP-Adressen der VMs, für die die Profilerstellung durchgeführt werden soll, auch manuell auflisten.

Öffnen Sie die Ausgabedatei im Editor, und kopieren Sie die Namen aller VMs, für die Profile erstellt werden sollen, in eine andere Datei (z.B. „ProfileVMList.txt“). Fügen Sie einen VM-Namen pro Zeile ein. Diese Datei wird als Eingabe für den Parameter „-VMListFile“ des Tools für alle anderen Vorgänge verwendet: Profilerstellung, Berichterstellung und Durchsatzberechnung.

### <a name="examples"></a>Beispiele

#### <a name="store-the-list-of-vms-in-a-file"></a>Speichern der Liste mit den VMs in einer Datei
```
ASRDeploymentPlanner.exe -Operation GetVMlist -ServerListFile "E:\Hyper-V_ProfiledData\ServerList.txt" -User Hyper-VUser1 -OutputFile "E:\Hyper-V_ProfiledData\VMListFile.txt"
```

#### <a name="store-the-list-of-vms-at-the-default-location--directory-path"></a>Speichern der Liste mit den VMs am Standardspeicherort (-Directory-Pfad)
```
ASRDeploymentPlanner.exe -Operation GetVMList -Directory "E:\Hyper-V_ProfiledData" -ServerListFile "E:\Hyper-V_ProfiledData\ServerList.txt" -User Hyper-VUser1
```

## <a name="profile-hyper-v-vms"></a>Durchführen der Profilerstellung für Hyper-V-VMs
Im Profilerstellungsmodus stellt das Bereitstellungsplaner-Tool jeweils eine Verbindung mit den einzelnen Hyper-V-Hosts her, um Leistungsdaten zu den VMs zu sammeln. 

Die Profilerstellung wirkt sich nicht negativ auf die Leistung der Produktions-VMs aus, weil keine direkte Verbindung damit hergestellt wird. Alle Leistungsdaten werden für den Hyper-V-Host erfasst.

Das Tool fragt den Hyper-V-Host alle 15 Sekunden ab, um die Genauigkeit der Profilerstellung sicherzustellen. Für jede Minute wird der Mittelwert der Leistungsindikatordaten gespeichert.

Das Tool führt die VM-Migration von Knoten zu Knoten im Cluster und die Speichermigration auf einem Host nahtlos durch.

### <a name="getting-the-vm-list-to-profile"></a>Abrufen der Liste mit den VMs für die Profilerstellung
In den Informationen zum GetVMList-Vorgang erfahren Sie, wie Sie eine Liste mit VMs für die Profilerstellung erstellen.

Nachdem Sie die Liste mit den VMs für die Profilerstellung vorliegen haben, können Sie für das Tool den Modus für die Profilerstellung ausführen. 

### <a name="command-line-parameters"></a>Befehlszeilenparameter
In der folgenden Tabelle sind die erforderlichen und optionalen Parameter des Tools für die Ausführung im Profilerstellungsmodus aufgeführt. Das Tool ist für Szenarien zum Umstellen von VMware auf Azure und von Hyper-V auf Azure geeignet. Die hier angegebenen Parameter gelten für Hyper-V. 
```
ASRDeploymentPlanner.exe -Operation StartProfiling /?
```

| Parametername | BESCHREIBUNG |
|---|---|
| -Operation | StartProfiling |
| -User | Der Benutzername zum Herstellen der Verbindung mit dem Hyper-V-Host oder Hyper-V-Cluster. Der Benutzer muss über Administratorzugriff verfügen.|
| -VMListFile | Die Datei mit der Liste der VMs, für die die Profilerstellung durchgeführt werden soll. Der Dateipfad kann absolut oder relativ sein. Für Hyper-V ist diese Datei die Ausgabedatei des Vorgangs GetVMList. Falls Sie die Vorbereitung manuell durchführen, sollte die Datei einen Servernamen oder eine IP-Adresse gefolgt vom VM-Namen (mit dem Trennzeichen „\“ pro Zeile) enthalten. Der in der Datei angegebene VM-Name sollte dem VM-Namen auf dem Hyper-V-Host entsprechen.<br><br>**Beispiel:** „VMList.txt“ enthält die folgenden VMs:<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-NoOfMinutesToProfile|Gibt an, wie lange die Profilerstellung durchgeführt werden soll (in Minuten). Der Mindestwert beträgt 30 Minuten.|
|-NoOfHoursToProfile|Gibt an, wie lange die Profilerstellung durchgeführt werden soll (in Stunden).|
|-NoOfDaysToProfile |Gibt an, wie lange die Profilerstellung durchgeführt werden soll (in Tagen). Es wird empfohlen, die Profilerstellung länger als sieben Tage durchzuführen. Bei dieser Dauer können Sie sicherstellen, dass das Workloadmuster in Ihrer Umgebung für den angegebenen Zeitraum eingehalten und zum Bereitstellen einer präzisen Empfehlung verwendet wird.|
|-Virtualization|Der Virtualisierungstyp (VMware oder Hyper-V).|
|-Directory|(Optional) Der UNC- oder lokale Verzeichnispfad zum Speichern von Daten, die während der Profilerstellung generiert wurden. Wenn kein Name angegeben wird, wird das Verzeichnis mit dem Namen „ProfiledData“ unter dem aktuellen Pfad als Standardverzeichnis verwendet.|
|-Password|(Optional) Das Kennwort zum Herstellen der Verbindung mit dem Hyper-V-Host. Wenn Sie es nicht als Parameter angeben, werden Sie beim Ausführen des Befehls zum Eingeben aufgefordert.|
|-StorageAccountName|(Optional) Der Name des Speicherkontos zur Ermittlung des Durchsatzes, der für die Replikation von Daten aus der lokalen Umgebung in Azure erreichbar ist. Mit dem Tool werden Testdaten in dieses Speicherkonto hochgeladen, um den Durchsatz zu berechnen. Das Speicherkonto muss vom Typ „Allgemein v1 (GPv1)“ sein.|
|-StorageAccountKey|(Optional) Der Schlüssel, der zum Zugreifen auf das Speicherkonto verwendet wird. Navigieren Sie im Azure-Portal zu **Speicherkonten** >  *<Name des Speicherkontos>*  > **Einstellungen** > **Zugriffsschlüssel** > **Key1** (oder der primäre Zugriffsschlüssel für ein klassisches Speicherkonto).|
|-Environment|(Optional) Ihre Zielumgebung für das Azure-Speicherkonto. Dies kann einer von drei Werten sein: „AzureCloud“, „AzureUSGovernment“ oder „AzureChinaCloud“. Der Standardwert ist „AzureCloud“. Verwenden Sie den Parameter, wenn Sie als Zielregion „Azure US Government“ oder „Azure China 21Vianet“ nutzen.|

Es wird empfohlen, die Profilerstellung für Ihre VMs länger als 7 Tage durchzuführen. Wenn sich das Muster der Datenänderung innerhalb eines Monats ändert, ist es ratsam, die Profilerstellung in die Woche mit der höchsten Datenänderung zu legen. Die beste Möglichkeit besteht darin, die Profilerstellung 31 Tage lang durchzuführen, um eine bessere Empfehlung zu erhalten. 

Während des Zeitraums der Profilerstellung wird „ASRDeploymentPlanner.exe“ weiter ausgeführt. Im Tool wird der Zeitraum für die Profilerstellung in Tagen eingegeben. Sie können die Profilerstellung einige Stunden oder Minuten lang durchführen, um das Tool bzw. den Ablauf (Proof of Concept) kurz zu testen. Die kürzeste zulässige Dauer für die Profilerstellung beträgt 30 Minuten. 

Während der Profilerstellung können Sie optional den Namen eines Speicherkontos und den dazugehörigen Schlüssel übergeben, um den Durchsatz zu ermitteln, der für Azure Site Recovery bei der Replikation vom Hyper-V-Server zu Azure erreicht werden kann. Wenn der Name des Speicherkontos und der Schlüssel während der Profilerstellung nicht übergeben werden, wird der erreichbare Durchsatz vom Tool nicht berechnet.

Sie können mehrere Instanzen des Tools für verschiedene Gruppen von VMs ausführen. Stellen Sie sicher, dass die VM-Namen in den Gruppen für die Profilerstellung nicht mehr als einmal vorkommen. Angenommen, Sie haben die Profilerstellung für zehn VMs durchgeführt (VM1 bis VM10). Nach Ablauf von einigen Tagen möchten Sie für weitere fünf VMs Profile erstellen (VM11 bis VM15). Sie können das Tool über eine andere Befehlszeilenkonsole für die zweite VM-Gruppe ausführen (VM11 bis VM15). 

Stellen Sie hierbei sicher, dass die zweite Gruppe von VMs keine Namen der VMs aus der ersten Profilerstellungsinstanz aufweist, oder verwenden Sie für die zweite Ausführung ein anderes Ausgabeverzeichnis. Wenn zwei Instanzen des Tools für die Profilerstellung derselben VMs verwendet werden und dabei dasselbe Ausgabeverzeichnis genutzt wird, ist der generierte Bericht fehlerhaft. 

Standardmäßig ist das Tool so konfiguriert, dass für bis zu 1.000 VMs Profile und Berichte erstellt werden. Sie können den Grenzwert ändern, indem Sie den Schlüsselwert „MaxVMsSupported“ in der Datei „ASRDeploymentPlanner.exe.config“ ändern.
```
<!-- Maximum number of VMs supported-->
<add key="MaxVmsSupported" value="1000"/>
```
Wenn Sie mit den Standardeinstellungen (beispielsweise) Profile für 1.500 VMs erstellen möchten, können Sie zwei Dateien vom Typ „VMList.txt“ verwenden. Eine enthält 1.000 VMs, und die andere enthält 500 VMs. Führen Sie die beiden Instanzen des Azure Site Recovery-Bereitstellungsplaners aus, eine mit „VMList1.txt“ und eine mit „VMList2.txt“. Sie können zum Speichern der Profildaten beider VMList-VMs den gleichen Verzeichnispfad verwenden. 

Basierend auf der Hardwarekonfiguration – vor allem der RAM-Größe des Servers, auf dem das Tool für die Berichterstellung ausgeführt wird – kann der Vorgang aufgrund von unzureichendem Arbeitsspeicher unter Umständen fehlschlagen. Wenn Sie über gute Hardware verfügen, können Sie „MaxVMsSupported“ auch auf einen höheren Wert festlegen.  

VM-Konfigurationen werden zu Beginn des Profilerstellungsvorgangs einmal erfasst und in einer Datei mit dem Namen „VMDetailList.xml“ gespeichert. Diese Informationen werden für die Berichterstellung verwendet. Alle Änderungen der VM-Konfiguration (z.B. erhöhte Anzahl von Kernen, Datenträgern oder NICs) vom Anfang bis zum Ende der Profilerstellung werden nicht erfasst. Wenn sich die Konfiguration einer VM während der Profilerstellung geändert hat, können Sie die folgende Problemumgehung nutzen, um bei der Berichterstellung die aktuellen VM-Details abzurufen:

* Sichern Sie die Datei „VMdetailList.xml“, und löschen Sie die Datei an ihrem aktuellen Speicherort.
* Übergeben Sie die Argumente „-User“ und „-Password“ während der Berichterstellung.

Mit dem Befehl für die Profilerstellung werden im Verzeichnis der Profilerstellung mehrere Dateien generiert. Löschen Sie keine Dateien, weil sich dies auf die Berichterstellung auswirkt.

### <a name="examples"></a>Beispiele

#### <a name="profile-vms-for-30-days-and-find-the-throughput-from-on-premises-to-azure"></a>VM-Profilerstellung für 30 Tage, Ermittlung des Durchsatzes von lokal zu Azure
```
ASRDeploymentPlanner.exe -Operation StartProfiling -virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -NoOfDaysToProfile 30 -User Contoso\HyperVUser1 -StorageAccountName  asrspfarm1 -StorageAccountKey Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

#### <a name="profile-vms-for-15-days"></a>VM-Profilerstellung für 15 Tage
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\vCenter1_ProfiledData\ProfileVMList1.txt"  -NoOfDaysToProfile  15  -User contoso\HypreVUser1
```

#### <a name="profile-vms-for-60-minutes-for-a-quick-test-of-the-tool"></a>VM-Profilerstellung für 60 Minuten zum schnellen Testen des Tools
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -NoOfMinutesToProfile 60 -User Contoso\HyperVUser1
```

#### <a name="profile-vms-for-2-hours-for-a-proof-of-concept"></a>VM-Profilerstellung für 2 Stunden zu Proof of Concept-Zwecken
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -NoOfHoursToProfile 2 -User Contoso\HyperVUser1
```

### <a name="considerations-for-profiling"></a>Aspekte der Profilerstellung

Wenn der Server, auf dem das Tool ausgeführt wird, neu gestartet wird oder abstürzt oder wenn Sie das Tool mit STRG+C schließen, werden die Profilerstellungsdaten beibehalten. Es kann aber sein, dass die letzten 15 Minuten der Daten für die Profilerstellung fehlen. Führen Sie das Tool in diesem Fall im Profilerstellungsmodus aus, nachdem der Server neu gestartet wurde.

Wenn der Name des Speicherkontos und der dazugehörige Schlüssel übergeben werden, misst das Tool den Durchsatz im letzten Schritt der Profilerstellung. Falls das Tool geschlossen wird, bevor die Profilerstellung abgeschlossen ist, wird der Durchsatz nicht berechnet. Zur Ermittlung des Durchsatzes vor dem Generieren des Berichts können Sie den GetThroughput-Vorgang über die Befehlszeilenkonsole ausführen. Andernfalls sind im Bericht keine Informationen zum Durchsatz enthalten.

Azure Site Recovery unterstützt keine VMs, die über iSCSI- und Pass-Through-Datenträger verfügen. Das Tool kann keine iSCSI- und Pass-Through-Datenträger erkennen, die an VMs angefügt sind, und auch keine Profilerstellung dafür durchführen.

## <a name="generate-a-report"></a>Generieren eines Berichts
Das Tool generiert eine makrofähige Microsoft Excel-Datei (XLSM) als Berichtsausgabe. Darin werden alle Empfehlungen zur Bereitstellung zusammengefasst. Der Bericht hat den Namen „DeploymentPlannerReport_*eindeutiger numerischer Bezeichner*.xlsm“ und wird im angegebenen Verzeichnis gespeichert.

Nach Abschluss der Profilerstellung können Sie das Tool im Berichterstellungsmodus ausführen. 

### <a name="command-line-parameters"></a>Befehlszeilenparameter
Die folgende Tabelle enthält eine Liste mit den erforderlichen und optionalen Toolparametern für die Ausführung im Modus für die Berichterstellung. Das Tool ist für die Umstellung von VMware auf Azure und von Hyper-V auf Azure geeignet. Die folgenden Parameter gelten für Hyper-V:
```
ASRDeploymentPlanner.exe -Operation GenerateReport /?
```

| Parametername | BESCHREIBUNG |
|---|---|
| -Operation | GenerateReport |
|-VMListFile | Die Datei mit der Liste der VMs für die Profilerstellung, für die der Bericht erstellt werden soll. Der Dateipfad kann absolut oder relativ sein. Für Hyper-V ist diese Datei die Ausgabedatei des Vorgangs GetVMList. Falls Sie die Vorbereitung manuell durchführen, sollte die Datei einen Servernamen oder eine IP-Adresse gefolgt vom VM-Namen (mit dem Trennzeichen „\“ pro Zeile) enthalten. Der in der Datei angegebene VM-Name sollte dem VM-Namen auf dem Hyper-V-Host entsprechen.<br><br>**Beispiel:** „VMList.txt“ enthält die folgenden VMs:<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-Virtualization|Der Virtualisierungstyp (VMware oder Hyper-V).|
|-Directory|(Optional) Der UNC- oder lokale Verzeichnispfad des Speicherorts, an dem die Daten der Profilerstellung (während der Profilerstellung generierte Dateien) gespeichert werden. Diese Daten werden für die Erstellung des Berichts benötigt. Wenn kein Name angegeben wird, wird das Verzeichnis mit dem Namen „ProfiledData“ unter dem aktuellen Pfad als Standardverzeichnis verwendet.|
| -User | (Optional) Der Benutzername zum Herstellen der Verbindung mit dem Hyper-V-Host oder Hyper-V-Cluster. Der Benutzer muss über Administratorzugriff verfügen. Der Benutzername und das Kennwort dienen zum Abrufen der aktuellen Konfigurationsinformationen von VMs (z.B. Anzahl von Datenträgern, Anzahl von Kernen und Anzahl von NICs) für die Verwendung im Bericht. Wenn dieser Wert nicht angegeben ist, werden die Konfigurationsinformationen verwendet, die während der Profilerstellung gesammelt wurden.|
|-Password|(Optional) Das Kennwort zum Herstellen der Verbindung mit dem Hyper-V-Host. Wenn Sie es nicht als Parameter angeben, werden Sie beim Ausführen des Befehls zum Eingeben aufgefordert.|
| -DesiredRPO | (Optional) Der gewünschte RPO-Wert (Recovery Point Objective) in Minuten. Der Standardwert ist 15 Minuten.|
| -Bandwidth | (Optional) Die Bandbreite in MBit/s. Verwenden Sie diesen Parameter zum Berechnen des RPO-Werts, der für die angegebene Bandbreite erzielt werden kann. |
| -StartDate | (Optional) Das Startdatum und die Uhrzeit im Format MM-TT-JJJJ:HH:MM (24-Stunden-Format). „StartDate“ muss zusammen mit „EndDate“ angegeben werden. Wenn „StartDate“ angegeben ist, wird der Bericht für die Profilerstellungsdaten erstellt, die zwischen „StartDate“ und „EndDate“ erfasst wurden. |
| -EndDate | (Optional) Das Enddatum und die Uhrzeit im Format MM-TT-JJJJ:HH:MM (24-Stunden-Format). „EndDate“ muss zusammen mit „StartDate“ angegeben werden. Wenn „EndDate“ angegeben ist, wird der Bericht für die Profilerstellungsdaten erstellt, die zwischen „StartDate“ und „EndDate“ erfasst wurden. |
| -GrowthFactor | (Optional) Der Zuwachsfaktor als Prozentsatz. Der Standardwert ist 30 Prozent. |
| -UseManagedDisks | (Optional:) UseManagedDisks: Ja/Nein. Die Standardeinstellung ist „Yes“ (Ja). Die Anzahl von virtuellen Computern, die in einem einzelnen Speicherkonto angeordnet werden können, wird basierend darauf berechnet, ob das Failover bzw. Testfailover von virtuellen Computern auf einem verwalteten Datenträger anstatt auf einem nicht verwalteten Datenträger erfolgt. |
|-SubscriptionId |(Optional) Die GUID für das Abonnement. Verwenden Sie diesen Parameter, um den Bericht zur Kostenvorkalkulation mit dem aktuellen Preis zu generieren, der auf Ihrem Abonnement, dem Angebot zu Ihrem Abonnement und Ihrer Azure-Zielregion in der gewählten Währung basiert.|
|-TargetRegion|(Optional) Die Azure-Zielregion für die Replikation. Verwenden Sie diesen Parameter, um einen Bericht mit der jeweiligen Azure-Zielregion zu erstellen, da Azure über unterschiedliche Kosten pro Region verfügt. Die Standardeinstellung ist „USA, Westen 2“ bzw. die zuletzt verwendete Zielregion. Weitere Informationen finden Sie in der Liste mit den [unterstützten Zielregionen](hyper-v-deployment-planner-cost-estimation.md#supported-target-regions).|
|-OfferId|(Optional) Das Angebot, das dem Abonnement zugeordnet ist. Die Standardeinstellung ist MS-AZR-0003P (nutzungsbasierte Bezahlung).|
|-Currency|(Optional) Die Währung, in der Kosten im generierten Bericht angezeigt werden. Die Standardeinstellung ist „US-Dollar ($)“ bzw. die zuletzt verwendete Währung. Weitere Informationen finden Sie in der Liste mit den [unterstützten Währungen](hyper-v-deployment-planner-cost-estimation.md#supported-currencies).|

Standardmäßig ist das Tool so konfiguriert, dass für bis zu 1.000 VMs Profile und Berichte erstellt werden. Sie können den Grenzwert ändern, indem Sie den Schlüsselwert „MaxVMsSupported“ in der Datei „ASRDeploymentPlanner.exe.config“ ändern.
```
<!-- Maximum number of VMs supported-->
<add key="MaxVmsSupported" value="1000"/>
```

### <a name="examples"></a>Beispiele
#### <a name="generate-a-report-with-default-values-when-the-profiled-data-is-on-the-local-drive"></a>Berichterstellung mit Standardwerten, wenn sich die Profilerstellungsdaten auf dem lokalen Laufwerk befinden
```
ASRDeploymentPlanner.exe -Operation GenerateReport -virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"
```

#### <a name="generate-a-report-when-the-profiled-data-is-on-a-remote-server"></a>Berichterstellung, wenn sich die Profilerstellungsdaten auf einem Remoteserver befinden
Sie sollten über Lese-/Schreibzugriff für das Remoteverzeichnis verfügen.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory "\\PS1-W2K12R2\Hyper-V_ProfiledData" -VMListFile "\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt"
```

#### <a name="generate-a-report-with-a-specific-bandwidth-that-you-will-provision-for-the-replication"></a>Berichterstellung mit einer bestimmten Bandbreite, die Sie für die Replikation bereitstellen
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt" -Bandwidth 100
```

#### <a name="generate-a-report-with-a-5-percent-growth-factor-instead-of-the-default-30-percent"></a>Berichterstellung mit Zuwachsfaktor von 5 Prozent (anstelle der Standardeinstellung von 30 Prozent) 
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt" -GrowthFactor 5
```

#### <a name="generate-a-report-with-a-subset-of-profiled-data"></a>Berichterstellung mit einer Teilmenge der Profilerstellungsdaten
Es kann beispielsweise sein, dass Sie über Profilerstellungsdaten für 30 Tage verfügen und den Bericht nur für 20 Tage erstellen möchten.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt" -StartDate  01-10-2017:12:30 -EndDate 01-19-2017:12:30
```

#### <a name="generate-a-report-for-a-5-minute-rpo"></a>Berichterstellung mit einem RPO-Wert von 5 Minuten
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -DesiredRPO 5
```

#### <a name="generate-a-report-for-the-south-india-azure-region-with-indian-rupee-and-a-specific-offer-id"></a>Berichterstellung für die Azure-Region „Indien, Süden“ mit Indischer Rupie und einer spezifischen Angebots-ID
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -SubscriptionID 4d19f16b-3e00-4b89-a2ba-8645edf42fe5 -OfferID MS-AZR-0148P -TargetRegion southindia -Currency INR
```


### <a name="percentile-value-used-for-the-calculation"></a>Für die Berechnung verwendeter Perzentilwert
Wenn mit dem Tool ein Bericht erstellt wird, wird für den Lese/Schreib-IOPS-Wert, den Schreib-IOPS-Wert und die Datenänderungsrate standardmäßig der 95. Perzentilwert verwendet. Diese Werte werden während der Profilerstellung für alle VMs gesammelt. Mit dieser Metrik wird sichergestellt, dass Spitzen (100. Perzentil), die für Ihre VMs aufgrund von temporären Ereignissen auftreten können, nicht zur Ermittlung des Bandbreitenbedarfs für Ihr Zielspeicherkonto und Ihre Quelle herangezogen werden. Ein temporäres Ereignis kann beispielsweise ein Sicherungsauftrag sein, der einmal pro Tag ausgeführt wird, oder eine regelmäßige Datenbankindizierung, eine Aktivität zur Erstellung eines Analyseberichts oder ein anderes Point-in-Time-Ereignis mit kurzer Lebensdauer.

Bei Verwendung des 95. Perzentilwerts erhalten Sie ein genaues Bild der echten Workloadmerkmale und erzielen die beste Leistung, wenn die Workloads in Azure ausgeführt werden. Dieser Wert muss normalerweise nicht geändert werden. Falls Sie den Wert doch ändern möchten (z.B. in das 90. Perzentil), können Sie die Konfigurationsdatei „ASRDeploymentPlanner.exe.config“ im Standardordner aktualisieren und speichern, um einen neuen Bericht für die vorhandenen Profilerstellungsdaten zu erstellen.
```
<add key="WriteIOPSPercentile" value="95" />      
<add key="ReadWriteIOPSPercentile" value="95" />      
<add key="DataChurnPercentile" value="95" />
```

### <a name="considerations-for-growth-factor"></a>Aspekte des Zuwachsfaktors
Es ist wichtig, dass Sie in Bezug auf Ihre Workloadmerkmale den Zuwachs und eine potenzielle vermehrte Nutzung im Laufe der Zeit berücksichtigen. Wenn Sich Ihre Workloadmerkmale nach der Einrichtung des Schutzes ändern, können Sie in Bezug auf den Schutz nicht zu einem anderen Speicherkonto wechseln, ohne den Schutz zu deaktivieren und anschließend wieder zu aktivieren.

Angenommen, Ihre VM ist derzeit für ein Standardspeicherkonto für die Replikation eingerichtet. Die Wahrscheinlichkeit ist hoch, dass es im Laufe der nächsten drei Monate zu diesen Änderungen kommt:

1. Die Anzahl von Benutzern der Anwendung, die auf der VM ausgeführt wird, erhöht sich.
2. Aufgrund der erhöhten Datenänderungsrate auf der VM muss die VM auf Storage Premium umgestellt werden, damit die Azure Site Recovery-Replikation Schritt halten kann.
3. Sie müssen den Schutz deaktivieren und für ein Storage Premium-Konto wieder aktivieren.

Es wird dringend empfohlen, bei der Bereitstellungsplanung den künftigen Zuwachs zu berücksichtigen. Der Standardwert beträgt zwar 30 Prozent, aber Sie sind der Experte in Bezug auf das Nutzungsmuster Ihrer Anwendungen und den projizierten Zuwachs. Sie können diese Angabe beim Erstellen eines Berichts entsprechend ändern. Außerdem können Sie mehrere Berichte mit unterschiedlichen Zuwachsfaktoren für dieselben Profilerstellungsdaten erstellen. Anschließend können Sie ermitteln, welche Empfehlungen für die Speicherzielbandbreite und die Quellbandbreite für Sie am besten geeignet sind. 

Der erstellte Microsoft Excel-Bericht enthält die folgenden Informationen:

* [On-premises Summary](hyper-v-deployment-planner-analyze-report.md#on-premises-summary) (Lokale Zusammenfassung)
* [Empfehlungen](hyper-v-deployment-planner-analyze-report.md#recommendations)
* [VM-storage placement](hyper-v-deployment-planner-analyze-report.md#vm-storage-placement-recommendation) (VM/Speicher-Anordnung)
* [Compatible VMs](hyper-v-deployment-planner-analyze-report.md#compatible-vms) (Kompatible VMs)
* [Incompatible VMs](hyper-v-deployment-planner-analyze-report.md#incompatible-vms) (Inkompatible VMs)
* [On-premises Storage Requirement](hyper-v-deployment-planner-analyze-report.md#on-premises-storage-requirement) (Bedarf an lokalem Speicher)
* [IR batching](hyper-v-deployment-planner-analyze-report.md#initial-replication-batching) (IR-Batchverarbeitung)
* [Cost Estimation](hyper-v-deployment-planner-cost-estimation.md) (Kostenvorkalkulation)

![Bereitstellungsplaner-Bericht](media/hyper-v-deployment-planner-run/deployment-planner-report-h2a.png)


## <a name="get-throughput"></a>Durchsatzberechnung
Führen Sie das Tool im GetThroughput-Modus aus, um den Durchsatz zu schätzen, der von Azure Site Recovery während der Replikation von der lokalen Umgebung zu Azure erreicht werden kann. Das Tool berechnet den Durchsatz für den Server, auf dem das Tool ausgeführt wird. Im Idealfall ist dieser Server der Hyper-V-Server, dessen VMs geschützt werden sollen. 

### <a name="command-line-parameters"></a>Befehlszeilenparameter 
Öffnen Sie eine Befehlszeilenkonsole, und wechseln Sie in den Ordner mit dem Azure Site Recovery-Tool für die Bereitstellungsplanung. Führen Sie die Datei „ASRDeploymentPlanner.exe“ mit den unten angegebenen Parametern aus.
```
ASRDeploymentPlanner.exe -Operation GetThroughput /?
```

 Parametername | BESCHREIBUNG |
|---|---|
| -Operation | GetThroughput |
|-Virtualization|Der Virtualisierungstyp (VMware oder Hyper-V).|
|-Directory|(Optional) Der UNC- oder lokale Verzeichnispfad des Speicherorts, an dem die Daten der Profilerstellung (während der Profilerstellung generierte Dateien) gespeichert werden. Diese Daten werden für die Erstellung des Berichts benötigt. Wenn kein Name angegeben wird, wird das Verzeichnis mit dem Namen „ProfiledData“ unter dem aktuellen Pfad als Standardverzeichnis verwendet.|
| -StorageAccountName | Der Name des Speicherkontos zur Ermittlung der Bandbreite, die für die Replikation von Daten aus der lokalen Umgebung in Azure verbraucht wird. Mit dem Tool werden Testdaten in dieses Speicherkonto hochgeladen, um die verbrauchte Bandbreite zu ermitteln. Das Speicherkonto muss vom Typ „Allgemein v1 (GPv1)“ sein.|
| -StorageAccountKey | Der Schlüssel des Speicherkontos, der zum Zugreifen auf das Speicherkonto verwendet wird. Navigieren Sie zum Azure-Portal und dann zu **Speicherkonten** > *Name des Speicherkontos* > **Einstellungen** > **Zugriffsschlüssel** > **Key1**.|
| -VMListFile | Die Datei mit der Liste der VMs, für die Profile erstellt werden sollen, um die verbrauchte Bandbreite zu berechnen. Der Dateipfad kann absolut oder relativ sein. Für Hyper-V ist diese Datei die Ausgabedatei des Vorgangs GetVMList. Falls Sie die Vorbereitung manuell durchführen, sollte die Datei einen Servernamen oder eine IP-Adresse gefolgt vom VM-Namen (mit dem Trennzeichen „\“ pro Zeile) enthalten. Der in der Datei angegebene VM-Name sollte dem VM-Namen auf dem Hyper-V-Host entsprechen.<br><br>**Beispiel:** „VMList.txt“ enthält die folgenden VMs:<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-Environment|(Optional) Ihre Zielumgebung für das Azure-Speicherkonto. Dies kann einer von drei Werten sein: „AzureCloud“, „AzureUSGovernment“ oder „AzureChinaCloud“. Der Standardwert ist „AzureCloud“. Verwenden Sie den Parameter, wenn Sie als Zielregion „Azure US Government“ oder „Azure China 21Vianet“ nutzen.|

### <a name="example"></a>Beispiel
```
ASRDeploymentPlanner.exe -Operation GetThroughput -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -StorageAccountName  asrspfarm1 -StorageAccountKey by8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

### <a name="throughput-considerations"></a>Aspekte des Durchsatzes

Mit dem Tool werden im angegebenen Verzeichnis mehrere Dateien vom Typ „asrvhdfile*Anzahl*.vhd“ (wobei *Anzahl* für die Anzahl von Dateien steht) mit einer Größe von 64 MB erstellt. Mit dem Tool werden diese Dateien in das Speicherkonto hochgeladen, um den Durchsatz zu ermitteln. Nach dem Messen des Durchsatzes löscht das Tool diese Dateien aus dem Speicherkonto und vom lokalen Server. Wenn das Tool während der Berechnung des Durchsatzes aus irgendeinem Grund beendet wird, werden die Dateien nicht aus dem Speicherkonto oder vom lokalen Server gelöscht. Sie müssen sie manuell löschen.

Der Durchsatz wird für einen bestimmten Zeitpunkt gemessen. Dies ist der maximale Durchsatz, der von Azure Site Recovery während der Replikation erreicht werden kann, sofern alle anderen Faktoren gleich bleiben. Falls eine Anwendung beispielsweise in demselben Netzwerk auf einmal mehr Bandbreite verbraucht, variiert der tatsächliche Durchsatz während der Replikation. Das Ergebnis des gemessenen Durchsatzes ist anders, wenn der GetThroughput-Vorgang ausgeführt wird, während die geschützten VMs über eine hohe Datenänderungsrate verfügen. 

Wir empfehlen Ihnen, das Tool zu unterschiedlichen Zeiten während der Profilerstellung auszuführen, um zu verstehen, welche Durchsatzstufen jeweils erreicht werden können. Im Bericht wird vom Tool der zuletzt gemessene Durchsatz angezeigt.

> [!NOTE]
> Führen Sie das Tool auf einem Server aus, der über die gleichen Speicher- und CPU-Merkmale wie ein Hyper-V-Server verfügt.

Legen Sie die empfohlene Bandbreite für die Replikation so fest, dass der RPO-Wert in 100 Prozent der Fälle erreicht wird. Nach dem Festlegen der passenden Bandbreite sollten Sie wie folgt vorgehen, falls vom Tool kein Anstieg des erzielten Durchsatzes gemeldet wird:

1. Überprüfen Sie, ob der Azure Site Recovery-Durchsatz durch ein Problem mit der Dienstqualität (Quality of Service, QoS) des Netzwerks eingeschränkt wird.
2. Überprüfen Sie, ob sich Ihr Azure Site Recovery-Tresor in der nächstgelegenen physisch unterstützten Microsoft Azure-Region befindet, um die Netzwerkwartezeit zu verringern.
3. Überprüfen Sie Ihre lokalen Speichermerkmale, um zu ermitteln, ob Sie die Hardware (z.B. Wechsel von HDD auf SSD) verbessern können.

    
## <a name="next-steps"></a>Nächste Schritte
* [Analysieren des generierten Berichts](hyper-v-deployment-planner-analyze-report.md)
