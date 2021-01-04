---
title: Speicherarchitektur von SAP HANA in Azure (große Instanzen) | Microsoft-Dokumentation
description: Speicherarchitektur für die Bereitstellung von SAP HANA in Azure (große Instanzen)
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/10/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9254b3d19bd840b62d2f5f7023eba9a91e605d1e
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967515"
---
# <a name="sap-hana-large-instances-storage-architecture"></a>SAP HANA-Speicherarchitektur (große Instanzen)

Das Speicherlayout für SAP HANA in Azure (große Instanzen) wird von SAP HANA nach dem klassischen Bereitstellungsmodell anhand der von SAP empfohlenen Richtlinien konfiguriert. Die Richtlinien sind im Whitepaper [SAP HANA storage requirements](https://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) (SAP HANA-Speicheranforderungen) dokumentiert.

Bei HANA (große Instanz) der Typ-I-Klasse ist das Arbeitsspeichervolume viermal so groß wie das Speichervolume. Bei Einheiten von HANA (große Instanz) der Typ-II-Klasse ist dies nicht der Fall. Die Einheiten verfügen über ein Volume zum Speichern von HANA-Transaktionsprotokollsicherungen. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von SAP HANA in Azure (große Instanzen)](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Informationen zur Speicherzuordnung finden Sie in der folgenden Tabelle. Die Tabelle enthält die ungefähre Kapazität für die verschiedenen Volumes, die mit den unterschiedlichen Einheiten von HANA (große Instanz) bereitgestellt werden.

| HANA-SKU (große Instanzen) | hana/data | hana/log | hana/shared | hana/logbackups |
| --- | --- | --- | --- | --- |
| S72 | 1\.280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3\.328 GB | 768 GB |1\.280 GB | 768 GB |
| S96 | 1\.280 GB | 512 GB | 768 GB | 512 GB |
| S192 | 4\.608 GB | 1\.024 GB | 1\.536 GB | 1\.024 GB |
| S192m | 11.520 GB | 1\.536 GB | 1\.792 GB | 1\.536 GB |
| S192xm |  11.520 GB |  1\.536 GB |  1\.792 GB |  1\.536 GB |
| S384 | 11.520 GB | 1\.536 GB | 1\.792 GB | 1\.536 GB |
| S384m | 12.000 GB | 2\.050 GB | 2\.050 GB | 2\.040 GB |
| S384xm | 16.000 GB | 2\.050 GB | 2\.050 GB | 2\.040 GB |
| S384xxm |  20.000 GB | 3\.100 GB | 2\.050 GB | 3\.100 GB |
| S576m | 20.000 GB | 3\.100 GB | 2\.050 GB | 3\.100 GB |
| S576xm | 31744 GB | 4096 GB | 2048 GB | 4096 GB |
| S768m | 28.000 GB | 3\.100 GB | 2\.050 GB | 3\.100 GB |
| S768xm | 40960 GB | 6144 GB | 4096 GB | 6144 GB |
| S960m | 36.000 GB | 4\.100 GB | 2\.050 GB | 4\.100 GB |
| S896m | 33.792 GB | 512 GB | 1\.024 GB | 512 GB |

Neuere SKUs für große HANA-Instanzen werden mit folgenden Speicherkonfigurationen geliefert:

| HANA-SKU (große Instanzen) | hana/data | hana/log | hana/shared | hana/logbackups |
| --- | --- | --- | --- | --- |
| S224 | 4\.224 GB | 512 GB | 1\.024 GB | 512 GB |
| S224oo | 6\.336 GB | 512 GB | 1\.024 GB | 512 GB |
| S224m | 8\.448 GB | 512 GB | 1\.024 GB | 512 GB |
| S224om | 8\.448 GB | 512 GB | 1\.024 GB | 512 GB |
| S224ooo | 10.560 GB | 512 GB | 1\.024 GB | 512 GB |
| S224oom | 12.672 GB | 512 GB | 1\.024 GB | 512 GB |
| S448 | 8\.448 GB | 512 GB | 1\.024 GB | 512 GB |
| S448oo | 12.672 GB | 512 GB | 1\.024 GB | 512 GB |
| S448m | 16.896 GB | 512 GB | 1\.024 GB | 512 GB |
| S448om | 16.896 GB | 512 GB | 1\.024 GB | 512 GB |
| S448ooo | 21.120 GB | 512 GB | 1\.024 GB | 512 GB |
| S448oom | 25.344 GB | 512 GB | 1\.024 GB | 512 GB |
| S672 | 12.672 GB | 512 GB | 1\.024 GB | 512 GB |
| S672oo | 19.008 GB | 512 GB | 1\.024 GB | 512 GB |
| S672m | 25.344 GB | 512 GB | 1\.024 GB | 512 GB |
| S672om | 25.344 GB | 512 GB | 1\.024 GB | 512 GB |
| S672ooo | 31.680 GB | 512 GB | 1\.024 GB | 512 GB |
| S672oom | 38.016 GB | 512 GB | 1\.024 GB | 512 GB |
| S896 | 16.896 GB | 512 GB | 1\.024 GB | 512 GB |
| S896oo | 25.344 GB | 512 GB | 1\.024 GB | 512 GB |
| S896om | 33.792 GB | 512 GB | 1\.024 GB | 512 GB |
| S896ooo | 42.240 GB | 512 GB | 1\.024 GB | 512 GB |
| S896oom | 50.688 GB | 512 GB | 1\.024 GB | 512 GB |


Die tatsächlich bereitgestellten Volumes können je nach Bereitstellung und Tool, das zum Anzeigen der Volumegrößen verwendet wird, variieren.

Wenn Sie eine SKU von HANA (große Instanz) unterteilen, könnten die Unterteilungen beispielsweise wie folgt aussehen:

| Speicherpartition in GB | hana/data | hana/log | hana/shared | hana/log/backup |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1\.280 GB | 512 GB | 768 GB | 512 GB |
| 1\.024 | 1\.792 GB | 640 GB | 1\.024 GB | 640 GB |
| 1\.536 | 3\.328 GB | 768 GB | 1\.280 GB | 768 GB |


Diese Größen sind ungefähre Zahlen, die je nach Bereitstellung und den zum Anzeigen der Volumes verwendeten Tools geringfügig variieren können. Es sind auch weitere Partitionsgrößen wie 2,5 TB verfügbar. Diese Speichergrößen werden mit einer Formel berechnet, die der für die vorherigen Partitionen verwendeten Formel ähnelt. Der Begriff „Partitionen“ bedeutet nicht, dass das Betriebssystem, der Arbeitsspeicher oder die CPU-Ressourcen in irgendeiner Weise partitioniert werden. Er verweist nur auf Speicherpartitionen für die verschiedenen HANA-Instanzen, die Sie auf einer einzelnen Einheit von HANA (große Instanz) bereitstellen können. 

Möglicherweise benötigen Sie mehr Speicher. Sie können Speicher hinzufügen, indem Sie zusätzlichen Speicher in Einheiten von 1 TB erwerben. Dieser zusätzliche Speicher kann als zusätzliches Volume hinzugefügt werden. Er kann auch verwendet werden, um vorhandene Volumes zu erweitern. Es ist nicht möglich, die Größe der ursprünglich bereitgestellten Volumes (größtenteils in den obigen Tabellen aufgeführt) zu verringern. Ebenso ist es nicht möglich, die Namen der Volumes oder Bereitstellungsnamen zu ändern. Die oben beschriebenen Speichervolumes werden als NFS4-Volumes an die Einheiten von HANA (große Instanz) angefügt.

Sie können Speichermomentaufnahmen zur Sicherung und Wiederherstellung sowie zur Notfallwiederherstellung verwenden. Weitere Informationen finden Sie unter [Hochverfügbarkeit und Notfallwiederherstellung für SAP HANA in Azure (große Instanzen)](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Speicherlayoutdetails für Ihr Szenario finden Sie unter [Unterstützte HLI-Szenarien](hana-supported-scenario.md).

## <a name="run-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>Ausführen mehrerer SAP HANA-Instanzen auf einer Einheit von HANA (große Instanz)

Es ist möglich, mehrere aktive SAP HANA-Instanzen auf Einheiten von HANA (große Instanz) zu hosten. Um Funktionen für Speichermomentaufnahmen und die Notfallwiederherstellung bereitzustellen, erfordert eine solche Konfiguration ein pro Instanz festgelegtes Volume. Zurzeit können Einheiten von HANA (große Instanz) wie folgt unterteilt werden:

- **S72, S72m, S96, S144, S192:** In Inkrementen von 256 GB, wobei 256 GB die kleinste Einheit ist. Andere Inkremente wie 256 GB und 512 GB können bis zum Maximum des Arbeitsspeichers der Einheit kombiniert werden.
- **S144m und S192m:** In Inkrementen von 256 GB, wobei 512 GB die kleinste Einheit ist. Andere Inkremente wie 512 GB und 768 GB können bis zum Maximum des Arbeitsspeichers der Einheit kombiniert werden.
- **Typ II-Klasse**: In Inkrementen von 512 GB, wobei 2 TB die kleinste Einheit ist. Andere Inkremente wie 512 GB, 1 TB und 1,5 TB können bis zum Maximum des Arbeitsspeichers der Einheit kombiniert werden.

Einige Beispiele für die Ausführung mehrerer SAP HANA-Instanzen können etwa wie folgt aussehen:

| SKU | Arbeitsspeichergröße | Speichergröße | Größen mit mehreren Datenbanken |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | 1 x 768 GB HANA-Instanz<br /> oder 1 x 512 GB-Instanz + 1 x 256 GB-Instanz<br /> oder 3 x 256 GB-Instanzen | 
| S72m | 1,5 TB | 6 TB | 3 x 512 GB HANA-Instanzen<br />oder 1 x 512 GB-Instanz + 1 x 1 TB-Instanz<br />oder 6 x 256 GB-Instanzen<br />oder 1 x 1,5 TB-Instanzen | 
| S192m | 4 TB | 16 TB | 8 x 512 GB-Instanzen<br />oder 4 x 1 TB-Instanzen<br />oder 4 x 512 GB-Instanzen + 2 x 1 TB-Instanzen<br />oder 4 x 768 GB-Instanzen + 2 x 512 TB-Instanzen<br />oder 1 x 4 TB-Instanz |
| S384xm | 8 TB | 22 TB | 4 x 2 TB-Instanzen<br />oder 2 x 4 TB-Instanzen<br />oder 2 x 3 TB-Instanzen + 1 x 2 TB-Instanz<br />oder 2 x 2,5 TB-Instanzen + 1 x 3 TB-Instanz<br />oder 1 x 8 TB-Instanz |


Andere Variationen sind ebenfalls möglich. 

## <a name="encryption-of-data-at-rest"></a>Verschlüsselung für ruhende Daten
Der für HANA (große Instanz) genutzte Speicher verwendet seit Ende 2018 eine transparente Verschlüsselung für die Daten, die auf den Datenträgern gespeichert werden. In früheren Bereitstellungen konnten Sie entscheiden, ob die Volumes verschlüsselt werden sollten. Wenn Sie sich dagegen entschieden haben, konnten Sie die Verschlüsselung der Volumes online anfordern. Der Wechsel von unverschlüsselten zu verschlüsselten Volumes erfolgt transparent und erfordert keine Downtime. 

Bei SKUs der Typ-I-Klasse wird das Volume, auf dem die Start-LUN gespeichert ist, verschlüsselt. In Stempeln der Revision 3 großer HANA-Instanzen müssen Sie die Boot-LUN bei Verwendung von SKUs von großen HANA-Instanzen der Typ II-Klasse mithilfe von Methoden des Betriebssystems verschlüsseln. In Stempeln der Revision 4 großer HANA-Instanzen wird die Boot-LUN bei Verwendung von Typ II-Einheiten standardmäßig auch im Ruhezustand verschlüsselt. 

## <a name="required-settings-for-larger-hana-instances-on-hana-large-instances"></a>Erforderliche Einstellungen für größere HANA-Instanzen in HANA (große Instanzen)
Der in HANA (große Instanzen) genutzte Speicher weist eine Dateigrößenbeschränkung auf. Die [Größenbeschränkung beträgt 16 TB](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html) pro Datei. Anders als bei den Einschränkungen der Dateigröße in EXT3-Dateisystemen wird die durch HANA (große Instanzen) erzwungene Speichereinschränkung in HANA nicht implizit behandelt. Daher erstellt HANA nicht automatisch eine neue Datendatei, wenn die maximale Dateigröße von 16 TB erreicht ist. Während HANA versucht, die Datei über 16 TB hinaus zu vergrößern, werden Fehler gemeldet, und der Indexserver stürzt am Ende ab.

> [!IMPORTANT]
> Um zu verhindern, dass HANA versucht, Datendateien über die Dateigrößenbeschränkung von 16 TB für den Speicher in HANA (große Instanzen) hinaus zu vergrößern, müssen Sie in der Konfigurationsdatei „Global.ini“ von HANA die folgenden Parameter festlegen
> 
> - datavolume_striping=true
> - datavolume_striping_size_gb = 15000
> - Siehe auch SAP-Hinweis [2400005](https://launchpad.support.sap.com/#/notes/2400005).
> - Achten Sie auf SAP-Hinweis [2631285](https://launchpad.support.sap.com/#/notes/2631285).




**Nächste Schritte**
- Lesen Sie [Unterstützte Szenarien für HANA (große Instanzen)](hana-supported-scenario.md).