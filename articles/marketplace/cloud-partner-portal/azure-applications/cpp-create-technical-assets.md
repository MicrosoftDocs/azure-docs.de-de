---
title: Erstellen der technischen Ressourcen einer Azure-Anwendung | Azure Marketplace
description: Erstellen der technischen Ressourcen für ein Azure-Anwendungsangebot.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 12/13/2018
ms.author: pabutler
ms.openlocfilehash: cbe1b8c8f1159d90fbf97eeae272c1c50ec9b9bb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64942981"
---
# <a name="prepare-your-azure-application-technical-assets"></a>Vorbereiten der technischen Ressourcen einer Azure-Anwendung

In diesem Artikel sind die Ressourcen für ein Vorbereiten der technischen Ressourcen für ein Azure-Anwendungsangebot beschrieben.

## <a name="before-you-begin"></a>Voraussetzungen

Sehen Sie sich das folgende Video, [Building Solution Templates, and Managed Applications for the Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603) (Erstellen von Lösungsvorlagen und verwalteten Anwendungen für den Azure Marketplace) an, das erläutert, wie Sie eine Azure Resource Manager-Vorlage erstellen, um eine Azure-Anwendungslösung zu definieren, und das Anwendungsangebot danach im Azure Marketplace veröffentlichen.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


Lesen Sie die folgende Dokumentation zu Azure-Anwendungen. Diese Dokumentation enthält Schnellstarts, Tutorials und Beispiele.

- [Verstehen von Azure Resource Manager-Vorlagen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
- Schnellstartanleitungen:

  - [Azure-Schnellstartvorlagen](https://azure.microsoft.com/documentation/templates/)
  - [Azure-Schnellstartvorlagen auf GitHub](https://github.com/azure/azure-quickstart-templates)
  - [Veröffentlichen der Anwendungsdefinition](https://docs.microsoft.com/azure/managed-applications/publish-managed-app-definition-quickstart)
  - [Bereitstellen einer Dienstkatalog-App](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

  
- Tutorials:

  - [Erstellen von Definitionsdateien](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
  - [Veröffentlichen der Marketplace-Anwendung](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

  - Beispiele:

    - [Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    - [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    - [Verwaltete Anwendungslösungen](https://docs.microsoft.com/azure/managed-applications/sample-projects)

## <a name="fundamental-technical-knowledge"></a>Grundlegende technische Kenntnisse

Das Entwerfen, Erstellen und Testen dieser Ressourcen dauert lange, und es sind technische Kenntnisse in Bezug auf die Azure Platform und die Technologien erforderlich, um das Angebot zu erstellen.

Ihr Entwicklerteam sollte sich mit den folgenden Microsoft-Technologien auskennen:

- Grundkenntnisse in Bezug auf [Azure-Dienste](https://azure.microsoft.com/services/)
- [Entwerfen und Erstellen der Architektur für Azure-Anwendungen](https://azure.microsoft.com/solutions/architecture/)
- Erweiterte Grundkenntnisse in Bezug auf [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage) und [Azure-Netzwerk](https://azure.microsoft.com/services/?filter=networking)
- Erweiterte Grundkenntnisse in Bezug auf [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
- Fundierte Kenntnisse zu [JSON](https://www.json.org/)

## <a name="suggested-tools"></a>Vorgeschlagene Tools

Wählen Sie eine oder beide der folgenden Skriptumgebungen als Unterstützung bei der Verwaltung Ihrer Azure-Anwendung aus:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
- [Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/cli/azure)

Es empfiehlt sich, dass Sie Ihrer Entwicklungsumgebung die folgenden Tools hinzufügen:

- [Azure Storage-Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
- [Visual Studio Code](https://code.visualstudio.com/) mit den folgenden Erweiterungen:

  - Erweiterung: [Azure Resource Manager Tools](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Erweiterung: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Erweiterung: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Außerdem empfehlen wir Ihnen, sich die verfügbaren Tools auf der Seite [Azure-Entwicklungstools](https://azure.microsoft.com/tools/) und – bei Verwendung von Visual Studio – den [Visual Studio Marketplace](https://marketplace.visualstudio.com/) anzusehen.

## <a name="next-steps"></a>Nächste Schritte

[Erstellen eines Azure-Anwendungsangebots](./cpp-create-offer.md)

