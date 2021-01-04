---
title: Data Tiering und Extension Nodes für SAP HANA in Azure (große Instanzen) | Microsoft-Dokumentation
description: Data Tiering und Extension Nodes für SAP HANA in Azure (große Instanzen)
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
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9a1fc1c3b332d033e453db11ce4451cd626c4217
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967804"
---
# <a name="use-sap-hana-data-tiering-and-extension-nodes"></a>Verwenden von SAP HANA Data Tiering und Extension Nodes

SAP unterstützt ein Data Tiering-Modell für SAP BW für verschiedene Releases von SAP NetWeaver und SAP BW/4HANA. Weitere Informationen zum Data Tiering-Modell finden Sie im SAP-Dokument [SAP BW/4HANA and SAP BW on HANA with SAP HANA extension nodes](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#) (SAP BW/4HANA und SAP BW unter HANA mit SAP HANA Extension Nodes).
Mit HANA (große Instanz) können Sie die Option-1-Konfiguration von SAP HANA Extension Nodes wie in den häufig gestellten Fragen und Blogdokumenten von SAP beschrieben verwenden. Option-2-Konfigurationen können mit folgenden HANA-SKUs (große Instanzen) eingerichtet werden: S72m, S192, S192m, S384 und S384m. 

In der Dokumentation ist der Vorteil möglicherweise nicht sofort offensichtlich. Wenn Sie jedoch einen Blick in die SAP-Dimensionierungsrichtlinien werfen, werden Sie sehen, welche Vorteile die Verwendung von Option-1- und Option-2-SAP HANA Extension Nodes bieten. Im Folgenden sind einige Beispiele aufgeführt:

- SAP HANA-Dimensionierungsrichtlinien erfordern in der Regel das Doppelte des Datenvolumens als Arbeitsspeicher. Wenn Sie Ihre SAP HANA-Instanz mit den heißen Daten ausführen, sind höchstens 50 % des Arbeitsspeichers mit Daten gefüllt. Der restliche Arbeitsspeicher wird im Idealfall für die Arbeit von SAP HANA reserviert.
- Das bedeutet, dass in einer HANA-S192-Einheit (große Instanz) mit 2 TB Arbeitsspeicher, in der eine SAP BW-Datenbank ausgeführt wird, nur 1 TB als Datenvolumen verfügbar ist.
- Wenn Sie einen zusätzlichen Option-1-SAP HANA Extension Node verwenden, würde eine S192-SKU von HANA (große Instanz) zusätzliche 2 TB Kapazität für das Datenvolumen bieten. Bei der Option-2-Konfiguration erhalten Sie ein zusätzliches Volumen von 4 TB für warme Daten. Gegenüber dem heißen Knoten kann die vollständige Speicherkapazität des „warmen“ Extension Node zur Datenspeicherung für die Option-1 verwendet werden. Bei der Option-2-SAP HANA Extension Node-Konfiguration kann die doppelte Menge an Arbeitsspeicher für das Datenvolumen genutzt werden.
- Bei Option-1 haben Sie letztlich eine Kapazität von 3 TB für Ihre Daten und ein Heiß/Warm-Verhältnis von 1:2. Bei der Option-2-Extension Node-Konfiguration verfügen Sie über 5 TB an Daten und ein Verhältnis von 1:4.

Je höher das Datenvolumen im Vergleich zum Arbeitsspeicher ist, desto wahrscheinlicher ist es, dass die von Ihnen angeforderten warmen Daten auf dem Datenträger gespeichert werden.

**Nächste Schritte**
- Lesen Sie [Architektur von SAP HANA in Azure (große Instanzen)](hana-architecture.md).