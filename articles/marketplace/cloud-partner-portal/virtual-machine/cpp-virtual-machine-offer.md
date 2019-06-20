---
title: VM-Angebots im Azure Marketplace
description: Übersicht über die Vorgehensweise zum Veröffentlichen eines VM-Angebots im Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 12/04/2018
ms.author: pabutler
ms.openlocfilehash: fed0f47c963edf40883c432f5476bd7fe5720abb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938058"
---
# <a name="virtual-machine-offer"></a>VM-Angebot

|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| In diesem Abschnitt wird erläutert, wie Sie ein neues VM-Angebot im [Azure Marketplace](https://azuremarketplace.microsoft.com) veröffentlichen. Es werden sowohl Windows- als auch Linux-basierte virtuelle Computer unterstützt, wobei jeder Computer eine virtuelle Festplatte (VHD) mit Betriebssystem und null oder mehr Daten-VHDs umfasst. | ![Symbol für virtuellen Computer](./media/virtual-machine-icon.png)  |


## <a name="publishing-overview"></a>Übersicht über die Veröffentlichung

Das folgende Video, [Optimize Your Azure Marketplace Offer](https://channel9.msdn.com/Events/Build/2017/P4026?ocid=player), gibt einen umfassenden Überblick über den Azure Marketplace. Dazu gehören Informationen, wie in diesem Marketplace veröffentlicht wird (über eine Lösung für virtuelle Computer), wie die Bedienbarkeit Ihrer Produktseite und, optional, die Testversionsoberfläche optimiert werden können, wie Benutzerleads generiert werden und wie Sie diese nutzen sowie die Kundenbindung optimieren können.

> [!VIDEO https://channel9.msdn.com/Events/Build/2017/P4026/player]


## <a name="vm-publishing-process-flow"></a>Ablauf des VM-Veröffentlichungsprozesses

Das folgende Diagramm veranschaulicht die allgemeinen Schritte zum Veröffentlichen eines VM-Angebots. 

![VM-Veröffentlichungsprozess](./media/publishvm_001.png)

1. Erstellen des Angebots: In diesem Schritt werden sämtliche Details und Informationen für das Angebot konfiguriert. Hierzu zählen die Angebotsbeschreibung, Marketingmaterial, rechtliche Informationen, Supportinformationen und Ressourcenspezifikationen.

2. Erstellen der geschäftlichen und technischen Ressourcen: In diesem Schritt werden die geschäftlichen Ressourcen (rechtlich relevante Dokumente und Marketingmaterial) und die technischen Ressourcen für die zugeordnete Lösung (in diesem Fall: die virtuellen Computer sowie angefügte Datenträger) erstellt. 

3. Erstellen der SKU: In diesem Schritt werden die dem Angebot zugeordneten SKUs erstellt und übermittelt.  Für jedes Image, das Sie veröffentlichen möchten, ist eine eindeutige SKU erforderlich. 
 
4. Zertifizieren und Veröffentlichen des Angebots: Nach Fertigstellung des Angebots und der technischen Ressourcen kann das Angebot übermittelt werden. Im Rahmen des daraufhin beginnenden Veröffentlichungsprozesses wird die Lösung getestet, überprüft, zertifiziert und anschließend im Marketplace live geschaltet.  

## <a name="next-steps"></a>Nächste Schritte

Bevor Sie diese Schritte ausführen, müssen die [technischen und geschäftlichen Anforderungen](./cpp-prerequisites.md) für die Veröffentlichung eines virtuellen Computers im Microsoft Azure Marketplace erfüllt sein. 
