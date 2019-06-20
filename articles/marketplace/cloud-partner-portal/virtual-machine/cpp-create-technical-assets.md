---
title: Erstellen von technischen Ressourcen für ein VM-Angebot für den Azure Marketplace
description: In diesem Artikel wird beschrieben, wie Sie die technischen Ressourcen für ein VM-Angebot im Azure Marketplace erstellen.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
ms.service: marketplace
ms.topic: article
ms.date: 08/20/2018
ms.author: pabutler
ms.openlocfilehash: 6113c10cd152a22bd31e7212d86925b0c2107e58
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938408"
---
# <a name="create-technical-assets-for-a-virtual-machine-offer"></a>Erstellen von technischen Ressourcen für ein VM-Angebot

In diesem Abschnitt wird die Erstellung und Konfiguration der technischen Ressourcen für ein VM-Angebot für den Azure Marketplace Schritt für Schritt beschrieben.  Eine VM enthält zwei Komponenten: die Lösungs-VHD und einen optional zugeordneten Datenträger.  

- *Virtuelle Festplatten (VHDs)* mit dem Betriebssystem und Ihrer Lösung, die Sie mit Ihrem Azure Marketplace-Angebot bereitstellen. Der Prozess zur Vorbereitung der VHD unterscheidet sich in Abhängigkeit davon, ob es sich um eine Linux-basierte, Windows-basierte oder benutzerdefinierte VM handelt.
- *Datenträger* sind der dedizierte, beständige Speicher für einen virtuellen Computer. Nutzen Sie *nicht* die VHD der Lösung (z.B. das Laufwerk `C:`), um persistente Informationen zu speichern.

Ein VM-Image enthält einen Betriebssystem-Datenträger und null oder mehr Datenträger. Pro Datenträger wird eine VHD benötigt. Auch für leere Datenträger wird für die Erstellung eine VHD benötigt.
Sie müssen das VM-Betriebssystem, die VM-Größe, die zu öffnenden Ports und bis zu 15 angefügte Datenträger konfigurieren.

> [!TIP] 
> Fügen Sie unabhängig vom verwendeten Betriebssystem immer nur die von der SKU benötigte Mindestanzahl an Datenträgern hinzu. Kunden können Datenträger, die Teil eines Images sind, zum Zeitpunkt der Bereitstellung nicht entfernen. Sie haben aber immer die Möglichkeit, während oder nach der Bereitstellung Datenträger hinzuzufügen. 

> [!IMPORTANT]
> *Ändern Sie nicht die Anzahl der Datenträger in einer neuen Imageversion.* Wenn Sie Datenträger im Image neu konfigurieren müssen, definieren Sie eine neue SKU. Das Veröffentlichen einer neuen Imageversion mit einer anderen Anzahl von Datenträgern birgt das Risiko des Fehlschlagens der neuen Bereitstellung basierend auf der neuen Imageversion bei automatischer Skalierung, automatischen Bereitstellungen von Lösungen mithilfe von Azure Resource Manager-Vorlagen und anderen Szenarien.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="fundamental-technical-knowledge"></a>Grundlegende technische Kenntnisse

Das Entwerfen, Erstellen und Testen dieser Ressourcen dauert lange, und es sind technische Kenntnisse in Bezug auf die Azure Platform und die Technologien erforderlich, um das Angebot zu erstellen. Zusätzlich zur Vertrautheit mit Ihrer Lösungsdomäne sollte sich Ihr Engineering Team mit den folgenden Microsoft-Technologien auskennen: 
-   Grundkenntnisse in Bezug auf [Azure-Dienste](https://azure.microsoft.com/services/) 
-   [Entwerfen und Erstellen der Architektur für Azure-Anwendungen](https://azure.microsoft.com/solutions/architecture/)
-   Erweiterte Grundkenntnisse in Bezug auf [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage) und das [Azure-Netzwerk](https://azure.microsoft.com/services/?filter=networking)
-   Erweiterte Grundkenntnisse in Bezug auf [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
-   Erweiterte [JSON](https://www.json.org/)-Grundkenntnisse


## <a name="suggested-tools"></a>Vorgeschlagene Tools 

Wählen Sie eine oder beide der folgenden Skriptumgebungen als Unterstützung bei der Verwaltung von VHDs und VMs aus:
-   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-   [Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/cli/azure)

Außerdem empfehlen wir Ihnen, Ihrer Entwicklungsumgebung die folgenden Tools hinzuzufügen: 

-   [Azure Storage-Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Visual Studio Code](https://code.visualstudio.com/)
    *   Erweiterung: [Azure Resource Manager Tools](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   Erweiterung: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   Erweiterung: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Außerdem empfehlen wir Ihnen, sich die verfügbaren Tools auf der Seite [Azure-Entwicklungstools](https://azure.microsoft.com/tools/) und – bei Verwendung von Visual Studio – den [Visual Studio Marketplace](https://marketplace.visualstudio.com/) anzusehen.


## <a name="next-steps"></a>Nächste Schritte

In den nachfolgenden Artikeln dieses Abschnitts werden die Schritte zum Erstellen und Registrieren dieser VM-Ressourcen beschrieben:

1. Unter [Create an Azure-compatible VHD](./cpp-create-vhd.md) (Erstellen einer mit Azure kompatiblen VHD) wird beschrieben, wie Sie eine Linux- oder Windows-basierte VHD erstellen, die mit Azure kompatibel ist.  Der Artikel enthält bewährte Methoden, z.B. zur Größenanpassung, zum Patchen und zur Vorbereitung der VM für den Upload.

2. Unter [Connect to your Azure-based virtual machine](./cpp-connect-vm.md) (Herstellen einer Verbindung mit Ihrem Azure-basierten virtuellen Computer) wird beschrieben, wie Sie für Ihre neu erstellte VM eine Remoteverbindung herstellen und sich daran anmelden.  In diesem Artikel wird auch beschrieben, wie Sie die VM beenden, um Nutzungskosten zu sparen.

3. Unter [Konfigurieren der in Azure gehosteten VM](./cpp-configure-vm.md) wird beschrieben, wie Sie die richtige VHD-Größe wählen, Ihr Image generalisieren, kürzlich veröffentlichte Updates (Patches) anwenden und benutzerdefinierte Konfigurationen planen.

4. Unter [Deploy a VM from your VHDs](./cpp-deploy-vm-vhd.md) (Bereitstellen einer VM über Ihre VHDs) wird beschrieben, wie Sie eine VM von einer in Azure bereitgestellten VHD registrieren.  Hier sind die erforderlichen Tools aufgeführt. Es wird beschrieben, wie Sie diese verwenden, um ein Benutzer-VM-Image zu erstellen und dieses dann entweder über das [Microsoft Azure-Portal](https://ms.portal.azure.com/) oder mithilfe von PowerShell-Skripts in Azure bereitzustellen. 

5. Unter [Certify your VM image](./cpp-certify-vm.md) (Zertifizieren Ihres VM-Images) wird beschrieben, wie Sie ein VM-Image für die Azure Marketplace-Zertifizierung testen und übermitteln. Der Artikel enthält Informationen dazu, wo Sie das *Certification Test Tool for Azure Certified* erhalten und zum Zertifizieren Ihres VM-Images verwenden. 

6. Unter [Get shared access signature URI for your VM image](./cpp-get-sas-uri.md) (Abrufen des Shared Access Signature-URI für Ihr VM-Image) wird beschrieben, wie Sie den Shared Access Signature-URI (SAS) für Ihre VM-Images abrufen.
 
Im unterstützenden Artikel [Common SAS URL issues and fixes](./cpp-common-sas-url-issues.md) (Häufige SAS-URL-Probleme und ihre Lösungen) sind einige häufige Probleme, die beim Nutzen von SAS-URIs auftreten können, und die möglichen Lösungen aufgeführt.

Nachdem Sie alle Schritte abgeschlossen haben, können Sie [Ihr VM-Angebot auf dem Azure Marketplace veröffentlichen](./cpp-publish-offer.md).
