---
title: Unterstützungsmöglichkeiten für das Hinzufügen virtueller Azure-Computer zu einer vorhandenen Verfügbarkeitsgruppe | Microsoft-Dokumentation
description: Unterstützungsmöglichkeiten für das Hinzufügen virtueller Azure-Computer zu einer vorhandenen Verfügbarkeitsgruppe.
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: 7a5e97b66fec040b4ec32caa8d58cf9b50169a33
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60443703"
---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>Unterstützungsmöglichkeiten für das Hinzufügen virtueller Azure-Computer zu einer vorhandenen Verfügbarkeitsgruppe

Beim Hinzufügen von neuen virtuellen Computern zu einer vorhandenen Verfügbarkeitsgruppe stoßen Sie gelegentlich an Grenzen. Im folgenden Diagramm ist aufgeführt, welche VM-Serien in der gleichen Verfügbarkeitsgruppe verwendet werden können.

Hier sehen Sie die Matrix für die Unterstützungsmöglichkeiten beim Kombinieren verschiedener VM-Typen:

Serie und Verfügbarkeitsgruppe|Zweiter virtueller Computer|Eine Datei|Av2|D|Dv2|Dv3|
|---|---|---|---|---|---|---|
|Erster virtueller Computer|||||||
|Eine Datei||OK|OK|OK|OK|OK|
|Av2||OK|OK|OK|OK|OK|
|D||OK|OK|OK|OK|OK|
|Dv2||OK|OK|OK|OK|OK|
|Dv3||OK|OK|OK|OK|OK|

Alle anderen Serien können nicht in der gleichen Verfügbarkeitsgruppe enthalten sein, da sie spezielle Hardware erfordern.

Die VM-Größen A8 und A9 können aufgrund der Anforderung an dedizierte RDMA-Back-End-Netzwerke nicht kombiniert werden.
