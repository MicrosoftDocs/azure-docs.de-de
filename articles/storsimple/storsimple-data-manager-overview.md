---
title: Übersicht über Microsoft Azure StorSimple Data Manager | Microsoft Docs
description: Hier finden Sie eine Übersicht über den StorSimple Data Manager-Dienst.
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/21/2018
ms.author: vidarmsft
ms.openlocfilehash: c5ffe3ec2ec3cb06297df6be4ba7021f692633bf
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60630681"
---
# <a name="storsimple-data-manager-solution-overview"></a>Übersicht über die StorSimple Data Manager-Lösung

## <a name="overview"></a>Übersicht

Microsoft Azure StorSimple verwendet den Cloudspeicher als Erweiterung der lokalen Lösung und verteilt die Daten automatisch auf den lokalen Speicher und die Cloud. Daten werden in der Cloud in einem für maximale Effizienz und geringere Kosten deduplizierten und komprimierten Format gespeichert. Da die Daten im StorSimple-Format gespeichert werden, können sie nicht sofort von anderen Cloudanwendungen, die Sie verwenden möchten, verarbeitet werden.

Der StorSimple Data Manager ermöglicht Ihnen, nahtlos auf die Daten im StorSimple-Format in der Cloud zuzugreifen und diese zu verwenden. Dies geschieht durch das Transformieren des StorSimple-Formats in native Blobs und Dateien, die Sie mit anderen Diensten wie Azure Media Services, Azure HDInsights und Azure Machine Learning verwenden können.

Dieser Artikel bietet eine Übersicht über den StorSimple Data Manager-Dienst. Außerdem wird erklärt, wie Sie diesen Dienst nutzen können, um Anwendungen zu schreiben, die StorSimple-Daten und andere Azure-Dienste in der Cloud verwenden.

## <a name="how-it-works"></a>So funktioniert's

Der StorSimple Data Manager-Dienst identifiziert StorSimple-Daten in der Cloud von einem lokalen Gerät der StorSimple 8000-Serie. Die StorSimple-Daten in der Cloud sind im deduplizierten, komprimierten StorSimple-Format gespeichert. Der Data Manager-Dienst stellt APIs bereit, über die die Daten im StorSimple-Format extrahiert und in andere Formate transformiert werden können, z.B. Azure-Blobs und Azure Files. Diese transformierten Daten können dann unmittelbar von Azure HDInsight und Azure Media Services verwendet werden. Die Datentransformation ermöglicht es diesen Diensten somit, die transformierten StorSimple-Daten von lokalen Geräten der StorSimple 8000-Serie zu verarbeiten. Dieser Ablauf ist im folgenden Diagramm dargestellt.

![Übersichtsdiagramm](./media/storsimple-data-manager-overview/storsimple-data-manager-overview2.png)


## <a name="data-manager-use-cases"></a>Data Manager-Anwendungsfälle

Sie können Data Manager mit Azure Functions, Azure Automation und Azure Data Factory verwenden, sodass Workflows mit Ihren Daten ausgeführt werden, sobald diese StorSimple erreichen. Möglicherweise möchten Sie Ihre in StorSimple gespeicherten Medieninhalte mit Azure Media Services verarbeiten, einen Algorithmus für maschinelles Lernen auf diese Daten ausführen oder einen Hadoop-Cluster zum Analysieren der in StorSimple gespeicherten Daten einrichten. Mit den vielfältigen Diensten unter Azure und den in StorSimple verfügbaren Daten können Sie Ihre Daten optimal nutzen.


## <a name="region-availability"></a>Regionale Verfügbarkeit

Der StorSimple Manager ist in den folgenden sieben Regionen verfügbar:

 - Asien, Südosten
 - USA (Ost)
 - USA (Westen)
 - USA, Westen 2
 - USA, Westen-Mitte
 - Nordeuropa
 - Europa, Westen

Der StorSimple Manager kann hingegen zum Transformieren von Daten in den folgenden Regionen verwendet werden. 

![Für Daten verfügbare Regionen](./media/storsimple-data-manager-overview/data-manager-job-definition-different-regions-m.png)

Diese Gruppe ist größer, da die Ressourcenbereitstellung in allen oben genannten Regionen den Transformationsvorgang in den folgenden Regionen aufrufen kann. Sofern sich Ihre Daten in einer der 19 Regionen befinden, können Sie daher Ihre Daten mit diesem Dienst transformieren.


## <a name="choosing-a-region"></a>Auswählen einer Region

Wir empfehlen Folgendes:
 - Das Quellspeicherkonto (das Ihrem StorSimple-Gerät zugeordnet ist) und das Zielspeicherkonto (das die Daten im nativen Format enthalten soll) sollten sich in derselben Azure-Region befinden.
 - Data Manager und die Auftragsdefinition sollten sich in der Region mit dem StorSimple-Speicherkonto befinden. Wenn dies nicht möglich ist, richten Sie Data Manager in der nächstgelegenen Azure-Region ein, und erstellen Sie dann die Auftragsdefinition in derselben Region wie Ihr StorSimple-Speicherkonto. 

    Wenn sich Ihr StorSimple-Speicherkonto nicht in den 26 Regionen befindet, die die Erstellung von Auftragsdefinitionen unterstützen, wird empfohlen, StorSimple Data Manager nicht auszuführen, da dies mit langen Wartezeiten und potenziellen Ausgabegebühren verbunden ist.
    
Microsoft möchte sicherstellen, dass Azure-Dienste in allen Regionen immer verfügbar sind. Allerdings kann es in einer bestimmten Region während kurzer Zeiträume zu ungeplanten Dienstunterbrechungen kommen. In solchen Fällen können Sie einen Data Manager und eine Auftragsdefinition in einer Region aufrufen, die von der Unterbrechung nicht betroffen ist, und den Transformationsauftrag ausführen. Möglicherweise kommt es in einem solchen Szenario zu einer zusätzlichen Wartezeit, doch kann dies Ihre Wiederherstellungsstrategie im seltenen Fall einer regionalen Unterbrechung sein.

## <a name="security-considerations"></a>Sicherheitshinweise

Für StorSimple Data Manager müssen die Verschlüsselungsschlüssel für Dienstdaten aus dem StorSimple-Format in das native Format transformiert werden. Der Verschlüsselungsschlüssel für Dienstdaten wird generiert, wenn das erste Gerät beim StorSimple-Dienst registriert wird. Weitere Informationen zu diesem Schlüssel finden Sie unter [StorSimple-Sicherheit](storsimple-8000-security.md).

Der als Eingabe bereitgestellte Verschlüsselungsschlüssel für Dienstdaten wird in einem Schlüsseltresor gespeichert, der beim Erstellen von Data Manager erstellt wird. Der Tresor befindet sich in derselben Azure-Region wie Ihre Instanz von StorSimple Data Manager. Dieser Schlüssel wird gelöscht, wenn Sie den Data Manager-Dienst löschen.

Mit dem Schlüssel führen die Serverressourcen die Transformation aus. Diese Computeressourcen befinden sich in derselben Azure-Region wie Ihre Auftragsdefinition. Diese Region kann mit der Region, in der Sie Data Manager einrichten, identisch sein, dies ist jedoch keine Bedingung.

Wenn Ihre Data Manager-Region sich von Ihrer Auftragsdefinitionsregion unterscheidet, müssen Sie wissen, welche Daten/Metadaten sich in den beiden Regionen befinden. Das folgende Diagramm veranschaulicht die Auswirkungen von unterschiedlichen Regionen für Data Manager und den Auftrag.

![Dienst und Auftragsdefinition in unterschiedlichen Regionen](./media/storsimple-data-manager-overview/data-manager-job-different-regions.png)

## <a name="managing-personal-information"></a>Verwalten personenbezogener Informationen

Personenbezogene Daten werden vom StorSimple Data Manager weder gesammelt noch angezeigt. Weitere Informationen finden Sie im [Trust Center](https://www.microsoft.com/trustcenter) in der Microsoft-Datenschutzrichtlinie.

## <a name="known-limitations"></a>Bekannte Einschränkungen

Für den Dienst gelten derzeit folgende Einschränkungen:
- Der StorSimple Data Manager funktioniert derzeit nicht mit Volumes, die BitLocker-verschlüsselt sind. Wenn Sie versuchen, den Dienst mit einem verschlüsselten Laufwerk auszuführen, werden Auftragsfehler angezeigt.
- Einige Metadaten von Dateien (z.B. ACLs) werden nicht in den transformierten Daten beibehalten.
- Dieser Dienst funktioniert nur mit NTFS-Volumes.
- Wenn Dateipfade 256 Zeichen oder mehr aufweisen, tritt bei dem Auftrag ein Fehler auf.

## <a name="next-steps"></a>Nächste Schritte

[Use StorSimple Data Manager UI to transform your data (Verwenden der StorSimple Data Manager-UI zum Transformieren von Daten) ](storsimple-data-manager-ui.md).
