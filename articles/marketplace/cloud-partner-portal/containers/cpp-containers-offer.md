---
title: Azure-Containerimageangebot | Azure Marketplace
description: Übersicht über die Vorgehensweise zum Veröffentlichen eines Containerangebots im Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: pabutler
ms.openlocfilehash: 9617380822f5814d1cbd2bd2b69eac72722fe813
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64942755"
---
# <a name="containers"></a>Container

<table> <tr> <td>In diesem Abschnitt wird erläutert, wie Sie ein Containerimage im <a href="https://azuremarketplace.microsoft.com">Azure Marketplace</a> veröffentlichen.  
Der Containerangebotstyp unterstützt Docker-Containerimages, die als <a href="https://docs.microsoft.com/azure/aks/index">Azure Kubernetes Service</a>-Instanzen oder <a href="https://docs.microsoft.com/azure/container-instances/container-instances-overview">Azure Container Instances</a> bereitgestellt und in einem <a href="https://docs.microsoft.com/azure/container-registry">Azure Container Registry</a>-Repository gehostet werden. </td> <td><img src="./media/container-icon.png"  alt="Azure container icon" /></td> </tr> </table>

## <a name="offer-components"></a>Angebotskomponenten

Dieser Abschnitt enthält eine Übersicht über Elemente für die Veröffentlichung eines Containers und ist als Herausgeberleitfaden für den Azure Marketplace konzipiert. Eine Veröffentlichung ist in folgende Hauptbereiche unterteilt:

- [Voraussetzungen](./cpp-prerequisites.md) – listet die technischen und geschäftlichen Anforderungen auf, die zum Erstellen oder Veröffentlichen eines Containerangebots erfüllt sein müssen.
- [Erstellen eines Angebots](./cpp-create-offer.md) – listet die Schritte auf, die zum Erstellen eines neuen Eintrags für ein Containerangebot über das Cloud-Partnerportal ausgeführt werden müssen.
- [Vorbereiten der technischen Ressourcen](./cpp-create-technical-assets.md) – Schritte zum Erstellen der technischen Ressourcen für eine Containerlösung als Angebot im Azure Marketplace.
- [Veröffentlichen des Angebots](./cpp-publish-offer.md) – Informationen zum Übermitteln des Angebots zur Veröffentlichung im Azure Marketplace.

## <a name="container-publishing-process"></a>Prozess für die Containerveröffentlichung

Das folgende Diagramm veranschaulicht die allgemeinen Schritte zum Veröffentlichen eines VM-Angebots.
![Schritte zum Veröffentlichen eines Angebots](./media/containers-offer-process.png)

Die Veröffentlichung eines Containerangebots umfasst ganz allgemein folgende Schritte:

1. Erstellen des Angebots – Geben Sie detaillierte Angebotsinformationen an. Hierzu zählen unter anderem die Angebotsbeschreibung, Marketingmaterial, Supportinformationen und Ressourcenspezifikationen.
2. Erstellen der geschäftlichen und technischen Ressourcen – Erstellen Sie die geschäftlichen Ressourcen (rechtlich relevante Dokumente und Marketingmaterial) und die technischen Ressourcen für die zugeordnete Lösung (die in einer Azure Container Registry-Instanz gehosteten Containerimages).
3. Erstellen der SKU – Erstellen Sie die dem Angebot zugeordneten SKUs. Für jedes Image, das Sie veröffentlichen möchten, ist eine eindeutige SKU erforderlich.
4. Zertifizieren und Veröffentlichen des Angebots – Nach Fertigstellung des Angebots und der technischen Ressourcen kann das Angebot übermittelt werden. Durch diese Übermittlung wird der Veröffentlichungsprozess gestartet. Im Rahmen dieses Prozesses wird die Lösung getestet, überprüft, zertifiziert und anschließend im Azure Marketplace live geschaltet.

## <a name="next-steps"></a>Nächste Schritte

Bevor Sie diese Schritte ausführen, müssen die [technischen und geschäftlichen Anforderungen](./cpp-prerequisites.md) für die Veröffentlichung eines Containers im Microsoft Azure Marketplace erfüllt sein.