---
title: Erstellen der technischen Ressourcen eines Containerimages | Azure Marketplace
description: Erstellen Sie eine technische Ressourcen für einen Azure-Container.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pabutler
ms.openlocfilehash: c639389fdd0d4624152fcdfa4432be09a18a97bc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65794340"
---
# <a name="prepare-your-container-technical-assets"></a>Vorbereiten Ihrer technischen Containerressourcen

Dieser Artikel beschreibt die Schritte und Anforderungen für die Konfiguration eines Containerangebots im Azure Marketplace.

## <a name="before-you-begin"></a>Voraussetzungen

Lesen Sie die Dokumentation [Azure Container Instances](https://docs.microsoft.com/azure/container-instances). Darin finden Sie außerdem Schnellstarts, Tutorials und Beispiele.

## <a name="fundamental-technical-knowledge"></a>Grundlegende technische Kenntnisse

Das Entwerfen, Erstellen und Testen dieser Ressourcen dauert lange, und es sind technische Kenntnisse in Bezug auf die Azure Platform und die Technologien erforderlich, um das Angebot zu erstellen.
 
Zusätzlich zur Vertrautheit mit Ihrer Lösungsdomäne sollte sich Ihr Engineering Team mit den folgenden Microsoft-Technologien auskennen:

-   Grundkenntnisse in Bezug auf [Azure-Dienste](https://azure.microsoft.com/services/) 
-   [Entwerfen und Erstellen der Architektur für Azure-Anwendungen](https://azure.microsoft.com/solutions/architecture/)
-   Erweiterte Grundkenntnisse in Bezug auf [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage) und das [Azure-Netzwerk](https://azure.microsoft.com/services/?filter=networking)
-   Erweiterte Grundkenntnisse in Bezug auf [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
-   Erweiterte [JSON](https://www.json.org/)-Grundkenntnisse

## <a name="suggested-tools"></a>Vorgeschlagene Tools

Wählen Sie eine oder beide der folgenden Skriptumgebungen als Unterstützung bei der Verwaltung Ihres Containerimages aus:

-   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-   [Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/cli/azure)

Außerdem empfehlen wir Ihnen, Ihrer Entwicklungsumgebung die folgenden Tools hinzuzufügen:

-   [Azure Storage-Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Visual Studio Code](https://code.visualstudio.com/)
    *   Erweiterung: [Azure Resource Manager Tools](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   Erweiterung: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   Erweiterung: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Außerdem empfehlen wir Ihnen, sich die verfügbaren Tools auf der Seite [Azure-Entwicklungstools](https://azure.microsoft.com/tools/) und – bei Verwendung von Visual Studio – den [Visual Studio Marketplace](https://marketplace.visualstudio.com/) anzusehen.

## <a name="create-the-container-image"></a>Erstellen des Containerimages

Weitere Informationen finden Sie unter den folgenden Links:

* [Tutorial: Erstellen eines Containerimages für die Bereitstellung in Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
* [Tutorial: Erstellen und Bereitstellen von Containerimages in der Cloud mit Azure Container Registry Tasks](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task)

## <a name="next-steps"></a>Nächste Schritte

[Erstellen Ihres Containerangebots](./cpp-create-offer.md)
