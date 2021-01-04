---
title: Leitfaden für die Veröffentlichung von Containerangeboten im Azure Marketplace
description: In diesem Artikel werden die Anforderungen für die Veröffentlichung von Containerangeboten im Azure Marketplace beschrieben.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 11/30/2020
ms.openlocfilehash: 83c575aa40b80d9a8e39263e89a5e7860c8f8774
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95741660"
---
# <a name="publishing-guide-for-azure-container-offers"></a>Leitfaden für die Veröffentlichung von Azure-Containerangeboten

Azure-Containerangebote erleichtern Ihnen die Veröffentlichung Ihres Containerimages im Azure Marketplace. Informieren Sie sich in diesem Handbuch über die Anforderungen für diesen Angebotstyp.

Azure-Containerangebote sind Transaktionsangebote, die über den Azure Marketplace bereitgestellt und abgerechnet werden. Den Benutzern wird die Listenoption „Jetzt kaufen“ angezeigt.

Verwenden Sie den Angebotstyp Azure Container, wenn Ihre Lösung ein Docker-Container-Image ist, das als Kubernetes-basierte Azure Container-Instanz eingerichtet wird.

> [!NOTE]
> Eine Azure Container-Instanz ist eine Laufzeit-Docker-Instanz, mit der sich Container in Azure besonders schnell und einfach ausführen lassen, ohne dass Sie dazu virtuelle Computer verwalten oder einen übergeordneten Dienst einführen müssen. Container-Instanzen können direkt in Azure bereitgestellt oder von Azure Kubernetes Services oder Azure Kubernetes Service Engine orchestriert werden.  

Microsoft unterstützt derzeit kostenlose und Bring-Your-Own-License-Lizenzierungsmodelle (BYOL).

## <a name="container-offer-requirements"></a>Anforderungen für Containerangebote

| Anforderung | Details |  
|:--- |:--- |  
| Abrechnung und Messung | Es wird entweder das kostenlose oder BYOL-Abrechnungsmodell unterstützt.<br><br> |  
| Auf Grundlage einer Dockerfile erstelltes Image | Containerimages müssen auf der Docker-Imagespezifikation basieren und auf der Grundlage einer Dockerfile-Datei erstellt werden.<br> <br>Weitere Informationen zum Entwickeln von Docker-Images finden Sie im Abschnitt zur Verwendung in der [Dockerfile-Referenz](https://docs.docker.com/engine/reference/builder/#usage).<br><br> |  
| Hosten in einem Azure Container Registry-Repository | Containerimages müssen in einem Azure Container Registry-Repository gehostet werden.<br> <br>Weitere Informationen zum Arbeiten mit Azure Container Registry finden Sie unter [Schnellstart: Erstellen einer privaten Containerregistrierung über das Azure-Portal](../container-registry/container-registry-get-started-portal.md).<br><br> |  
| Imagemarkierung | Containerimages müssen mindestens ein Tag enthalten (maximale Taganzahl: 16).<br><br>Weitere Informationen zum Markieren eines Images finden Sie auf der Seite `docker tag` auf der Website zur [Docker-Dokumentation](https://docs.docker.com/engine/reference/commandline/tag).<br><br> |  

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Vorbereiten von technischen Ressourcen für ein Containerangebot finden Sie unter [Erstellen von technischen Ressourcen für Azure-Container](create-azure-container-technical-assets.md).

- Weitere Informationen zum Erstellen eines Azure-Containerangebots finden Sie unter [Erstellen eines Azure-Containerangebots in Azure Marketplace](create-azure-container-offer.md).
