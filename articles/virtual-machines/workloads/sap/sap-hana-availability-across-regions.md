---
title: Verfügbarkeit von SAP HANA in verschiedenen Azure-Regionen | Microsoft-Dokumentation
description: Hier finden Sie eine Übersicht über Überlegungen zur Verfügbarkeit beim Ausführen von SAP HANA auf Azure-VMs in mehreren Azure-Regionen.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/12/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 799034d9060e8afe101e86e7b98ac7e01c28d191
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019309"
---
# <a name="sap-hana-availability-across-azure-regions"></a>SAP HANA-Verfügbarkeit in verschiedenen Azure-Regionen

Dieser Artikel beschreibt Szenarien in Zusammenhang mit der Verfügbarkeit von SAP HANA in verschiedenen Azure-Regionen. Aufgrund der Entfernung zwischen den Azure-Regionen müssen für die Einrichtung der Verfügbarkeit von SAP HANA in mehreren Azure-Regionen besondere Überlegungen angestellt werden.

## <a name="why-deploy-across-multiple-azure-regions"></a>Gründe für die Bereitstellung in mehreren Azure-Regionen

Azure-Regionen sind oft durch große Entfernungen voneinander getrennt. Abhängig von der geopolitischen Region kann die Entfernung zwischen Azure-Regionen Hunderte von Kilometern oder – wie etwa in den USA – sogar mehrere Tausend Kilometer betragen. Aufgrund der Entfernung treten beim Netzwerkdatenverkehr zwischen Ressourcen, die in zwei verschiedenen Azure-Regionen bereitgestellt sind, eine erhebliche Roundtrip-Netzwerklatenz auf. Diese Latenz ist so erheblich, dass ein synchroner Datenaustausch zwischen zwei SAP HANA-Instanzen bei typischen SAP-Workloads unmöglich ist. 

Andererseits besteht in vielen Organisationen die Anforderung, dass zwischen dem primären und dem sekundären Rechenzentrum eine bestimmte Mindestentfernung liegt. Eine solche Anforderung an eine bestimmte Entfernung hilft dabei, die Verfügbarkeit sicherzustellen, wenn in einem größeren geografischen Gebiet eine Naturkatastrophe auftritt. Beispiele hier sind die Orkane, die im September und Oktober 2017 in der Karibik und in Florida große Schäden anrichteten. In Ihrer Organisation ist möglicherweise eine Mindestanforderung an Entfernungen vorhanden. Bei den meisten Azure-Kunden erfordert eine solche definierte Mindestentfernung, dass die Verfügbarkeit über mehrere [Azure-Regionen](https://azure.microsoft.com/regions/) hinweg geplant werden muss. Da die Entfernung zwischen zwei Azure-Regionen zu groß ist, um den synchronen HANA-Replikationsmodus zu verwenden, sind Sie durch die Anforderungen an RTO und RPO möglicherweise gezwungen, Verfügbarkeitskonfigurationen in einer Region bereitzustellen und diese dann um weitere Bereitstellungen in einer zweiten Region zu ergänzen.

Ein weiterer Aspekt, der in einem solchen Szenario zu bedenken ist, betrifft das Failover und die Clientumleitung. Es kann angenommen werden, dass ein Failover zwischen SAP HANA-Instanzen in zwei verschiedenen Azure-Regionen immer manuell erfolgen. Da der Replikationsmodus für die SAP HANA-Systemreplikation auf „asynchron“ festgelegt ist, besteht die Möglichkeit, dass Daten, für die in der primären HANA-Instanz ein Commit ausgeführt wurde, in der sekundären HANA-Instanz noch nicht angekommen sind. Aus diesem Grund ist ein automatisches Failover in Konfigurationen mit asynchroner Replikation keine Option. Selbst bei einem manuell gesteuerten Failover – etwa bei einer Failoverübung – müssen Sie sicherstellen, dass alle Daten, für die im primären Standort ein Commit ausgeführt wurde, die sekundäre Instanz erreicht haben, bevor Sie manuell zur anderen Azure-Region wechseln.
 
Azure Virtual Network verwendet einen anderen IP-Adressbereich. Die IP-Adressen sind in der zweiten Azure-Region bereitgestellt. Sie müssen also entweder die SAP HANA-Clientkonfiguration ändern oder – vorzugsweise – Schritte einrichten, um die Namensauflösung zu ändern. Auf diese Weise werden die Clients an die IP-Adresse des Servers im neuen sekundären Standort umgeleitet. Weitere Informationen finden Sie im SAP-Artikel [Client Connection Recovery after Takeover](https://help.sap.com/doc/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c93a723ceedc45da9a66ff47672513d3.html) (Wiederherstellung der Clientverbindung nach einer Übernahme).   

## <a name="simple-availability-between-two-azure-regions"></a>Einfache Verfügbarkeit zwischen zwei Azure-Regionen

Möglicherweise möchten Sie keine Verfügbarkeitskonfiguration innerhalb einer einzelnen Region einrichten, müssen aber dennoch dafür sorgen, dass die Workloads verarbeitet werden, wenn ein Notfall eintritt. Typische Fälle für solche Szenarios sind Nicht-Produktionssysteme. Hier ist ein Systemausfall für einen halben oder sogar einen ganzen Tag möglicherweise tolerierbar, Sie können aber keinesfalls zulassen, dass das System 48 Stunden oder länger nicht verfügbar ist. Um das Setup kostengünstiger zu gestalten, führen Sie auf dem virtuellen Computer ein weiteres System aus, das weniger wichtig ist. Das andere System fungiert als Ziel. Sie können für den virtuellen Computer in der sekundären Region auch eine kleinere Größe festlegen und entscheiden, dass Daten nicht vorab geladen werden. Da das Failover manuell erfolgt und viele weitere Schritte umfasst, um ein Failover des vollständigen Anwendungsstapels auszuführen, ist die zusätzliche Zeit zum Herunterfahren des virtuellen Computers, zum Ändern der Größe und zum Neustarten des virtuellen Computers akzeptabel.

Wenn Sie das Szenario der Freigabe des DR-Ziels für ein QA-System auf einem virtuellen Computer verwenden, müssen Sie die folgenden Aspekte berücksichtigen:

- Mit „delta_datashipping“ und „logreplay“ stehen für ein solches Szenario zwei [Betriebsmodi](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html) zur Verfügung.
- Für beide Betriebsmodi gelten unterschiedliche Arbeitsspeicheranforderungen ohne das Vorabladen von Daten.
- „Delta_datashipping“ benötigt möglicherweise erheblich weniger Arbeitsspeicher ohne die Option zum Vorabladen als „logreplay“. Weitere Informationen hierzu finden Sie in Kapitel 4.3 des SAP-Dokuments [How To Perform System Replication for SAP HANA](https://archive.sap.com/kmuuid2/9049e009-b717-3110-ccbd-e14c277d84a3/How%20to%20Perform%20System%20Replication%20for%20SAP%20HANA.pdf) (Gewusst wie: Durchführen der Systemreplikation für SAP HANA).
- Die Arbeitsspeicheranforderungen des Betriebsmodus „logreplay“ ohne das Vorabladen sind nicht deterministisch und hängen von den geladenen Columnstore-Strukturen ab. In Extremfällen benötigen Sie möglicherweise 50 % des Arbeitsspeichers der primären Instanz. Für den Arbeitsspeicher für den Betriebsmodus „logreplay“ ist es irrelevant, ob Sie die Option zum Vorabladen von Daten ausgewählt haben oder nicht.


![Diagramm mit zwei virtuellen Computern in zwei Regionen](./media/sap-hana-availability-two-region/two_vm_HSR_async_2regions_nopreload.PNG)

> [!NOTE]
> In dieser Konfiguration können Sie keine RPO von 0 angeben, da Ihr HANA System im asynchronen Modus repliziert wird. Wenn Sie eine RPO von 0 bereitstellen müssen, ist dies nicht die Konfiguration Ihrer Wahl.

Sie können die Konfiguration geringfügig ändern, indem Sie Daten als vorab ladbar konfigurieren. Angesichts der Tatsache, dass das Failover manuell ausgeführt wird und dass Anwendungsschichten ebenfalls in die zweite Region verlagert werden müssen, ist es jedoch möglicherweise nicht sinnvoll, Daten vorab zu laden. 

## <a name="combine-availability-within-one-region-and-across-regions"></a>Kombinieren der Verfügbarkeit innerhalb einer Region und über mehrere Regionen hinweg 

Eine Kombination der regionsinternen und regionsübergreifenden Verfügbarkeit kann durch folgende Faktoren motiviert sein:

- Die RPO muss in einer Azure-Region 0 sein.
- In der Organisation ist es nicht gewünscht oder akzeptabel, dass der globale Betrieb durch eine Naturkatastrophe eingeschränkt wird, die eine größere Region betrifft. Dies war der Fall bei den Orkanen, die in den letzten Jahren in der Karibik auftraten.
- Gesetzliche Vorschriften fordern Entfernungen zwischen primären und sekundären Standorten, die eindeutig die Möglichkeiten der Azure-Verfügbarkeitszonen überschreiten.

In diesen Fällen können Sie mithilfe der HANA-Systemreplikation ein Setup konfigurieren, das in der Terminologie von SAP als [SAP HANA Multitier System Replication](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/ca6f4c62c45b4c85a109c7faf62881fc.html) (SAP HANA-Systemreplikation auf mehreren Ebenen) bezeichnet wird. Die Architektur sieht folgendermaßen aus:

![Diagramm mit drei virtuellen Computern in zwei Regionen](./media/sap-hana-availability-two-region/three_vm_HSR_async_2regions_ha_and_dr.PNG)

SAP hat mit HANA 2.0 SPS3 die [Multi-Target-Systemreplikation](https://help.sap.com/viewer/42668af650f84f9384a3337bcd373692/2.0.03/en-US/0b2c70836865414a8c65463180d18fec.html) eingeführt. Die Multi-Target-Systemreplikation hat einige Vorteile für Updateszenarios. So ist beispielsweise die DR-Site (Region 2) nicht betroffen, wenn die sekundäre HA-Site wegen Wartung oder Updates ausfällt. [Hier](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/ba457510958241889a459e606bbcf3d3.html) finden Sie weitere Informationen zur Multi-Target-Systemreplikation von HANA.
Eine mögliche Architektur mit Multi-Target-Replikation würde folgendermaßen aussehen:

![Diagramm mit drei virtuellen Computern in zwei Multi-Target-Regionen](./media/sap-hana-availability-two-region/saphanaavailability_hana_system_2region_HA_and_DR_multitarget_3VMs.PNG)

Wenn das Unternehmen Anforderungen an die Hochverfügbarkeit in der zweiten (DR) Azure-Region hat, dann würde die Architektur so aussehen:

![Diagramm: Eine Organisation erfordert Bereitschaft für Hochverfügbarkeit in der zweiten Azure-Region (DR).](./media/sap-hana-availability-two-region/saphanaavailability_hana_system_2region_HA_and_DR_multitarget_4VMs.PNG)


Verwenden von „logreplay“ als Betriebsmodus: Diese Konfiguration stellt in der primären Region einen RPO-Wert von 0 und einen niedrigen RTO-Wert bereit. Die Konfiguration sorgt auch für eine annehmbare RPO, wenn eine Verlagerung in die zweite Region erforderlich ist. Die RTO-Zeiten in der zweiten Region richten sich danach, ob Daten vorab geladen werden. Viele Kunden verwenden den virtuellen Computer in der sekundären Region zum Ausführen eines Testsystems. In diesem Fall können die Daten nicht vorab geladen werden.

> [!IMPORTANT]
> Die Betriebsmodi zwischen den verschiedenen Ebenen müssen homogen sein. Es ist **nicht** möglich, „logreply“ als Betriebsmodus zwischen Ebene 1 und Ebene 2 und „delta_datashipping“ für Ebene 3 zu verwenden. Sie können jeweils nur einen Betriebsmodus auswählen, der für alle Ebenen einheitlich sein muss. Da „delta_datashipping“ nicht für einen RPO-Wert von 0 geeignet ist, bleibt „logreplay“ der einzige angemessene Betriebsmodus für eine solche Konfiguration mit mehreren Ebenen. Details zu Betriebsmodi und einigen Einschränkungen finden Sie im SAP-Artikel [Operation Modes for SAP HANA System Replication](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html) (Betriebsmodi für die SAP HANA Systemreplikation). 

## <a name="next-steps"></a>Nächste Schritte

Eine ausführliche Anleitung zum Einrichten dieser Konfigurationen in Azure finden Sie in folgenden Artikeln:

- [Hochverfügbarkeit von SAP HANA auf virtuellen Azure-Computern (VMs)](sap-hana-high-availability.md)
- [Your SAP on Azure – Part 4 – High Availability for SAP HANA using System Replication](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/) (SAP in Azure – Teil 4: Hochverfügbarkeit für SAP HANA durch Systemreplikation)

 



 
  
