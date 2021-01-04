---
title: Vorbereiten Ihrer technischen Ressourcen für Azure-Container
description: Technische Ressourcen und Anleitungen, die Ihnen bei der Konfiguration eines Containerangebots im Azure Marketplace helfen.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 11/30/2020
ms.openlocfilehash: 014bcd6fc519c267cdf17e9e98b850425c25ead6
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96459330"
---
# <a name="prepare-your-azure-container-technical-assets"></a>Vorbereiten Ihrer technischen Ressourcen für Azure-Container

Dieser Artikel enthält technische Ressourcen und Empfehlungen, die Ihnen helfen, ein Containerangebot im Azure Marketplace zu erstellen.

## <a name="before-you-begin"></a>Voraussetzungen

Schnellstarts, Tutorials und Beispiele finden Sie in der [Dokumentation zu Azure Container Instances](../container-instances/index.yml).

## <a name="fundamental-technical-knowledge"></a>Grundlegende technische Kenntnisse

Das Entwerfen, Erstellen und Testen dieser Ressourcen dauert lange, und es sind technische Kenntnisse in Bezug auf die Azure-Plattform und die Technologien erforderlich, die verwendet werden, um das Angebot zu erstellen.

Zusätzlich zur Vertrautheit mit Ihrer Lösungsdomäne sollte sich Ihr Engineeringteam mit den folgenden Microsoft-Technologien auskennen:

- Grundkenntnisse in Bezug auf [Azure-Dienste](https://azure.microsoft.com/services/)
- [Entwerfen und Erstellen der Architektur für Azure-Anwendungen](https://azure.microsoft.com/solutions/architecture/)
- Erweiterte Grundkenntnisse in Bezug auf [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage) und [Azure-Netzwerk](https://azure.microsoft.com/services/?filter=networking)
- Erweiterte Grundkenntnisse in Bezug auf [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
- Fundierte Kenntnisse zu [JSON](https://www.json.org/).

## <a name="suggested-tools"></a>Vorgeschlagene Tools

Wählen Sie eine oder beide der folgenden Skriptumgebungen als Unterstützung bei der Verwaltung Ihres Containerimages aus:

- [Azure PowerShell](/powershell/azure/)
- [Azure-Befehlszeilenschnittstelle](/cli/azure/).

Sie sollten Ihrer Entwicklungsumgebung diese Tools hinzufügen:

- [Azure Storage-Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows)
- [Visual Studio Code](https://code.visualstudio.com/)
  - Erweiterung: [Azure Resource Manager Tools](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Erweiterung: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Erweiterung: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json).

Machen Sie sich mit den verfügbaren Tools auf der Seite [Azure-Entwicklertools](https://azure.microsoft.com/) vertraut. Wenn Sie Visual Studio verwenden, machen Sie sich mit den im [Visual Studio Marketplace](https://marketplace.visualstudio.com/) verfügbaren Tools vertraut.

## <a name="create-the-container-image"></a>Erstellen des Containerimages

Sie können kein Image aus einer lokalen Registrierung in Azure Container Instances bereitstellen.

- Falls Sie bereits über einen funktionierenden Container in Ihrer lokalen Registrierung verfügen, erstellen Sie eine Azure-Registrierung, und laden Sie Ihr Containerimage in die Azure Container Registry-Instanz hoch. Weitere Informationen finden Sie unter [Tutorial: Erstellen und Bereitstellen von Containerimages in der Cloud mit Azure Container Registry Tasks](../container-registry/container-registry-tutorial-quick-task.md).

- Falls Sie noch nicht über ein Containerimage verfügen und Ihre vorhandene Anwendung containerisieren oder eine neue containerbasierte Anwendung erstellen möchten, klonen Sie den Quellcode der Anwendung von GitHub, erstellen Sie auf der Grundlage der Anwendungsquelle ein Containerimage, und testen Sie das Image in einer lokalen Docker-Umgebung. Weitere Informationen finden Sie unter [Tutorial: Erstellen eines Containerimages für die Bereitstellung in Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md).

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen Ihres Containerangebots](create-azure-container-offer.md)