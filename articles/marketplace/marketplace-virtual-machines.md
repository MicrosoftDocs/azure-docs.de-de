---
title: Planen eines VM-Angebots – Kommerzieller Microsoft-Marketplace
description: In diesem Artikel werden die Anforderungen für das Veröffentlichen eines VM-Angebots im Azure Marketplace beschrieben.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/19/2020
ms.openlocfilehash: 433875e52e77a8992197b47fe0424cd0b7e9f50c
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2020
ms.locfileid: "94628287"
---
# <a name="how-to-plan-a-virtual-machine-offer"></a>Planen eines VM-Angebots

In diesem Artikel werden die verschiedenen Möglichkeiten und Anforderungen für das Veröffentlichen eines VM-Angebots (Virtual Machine, virtueller Computer) im kommerziellen Marketplace erläutert. VM-Angebote sind transaktionsfähige Angebote, die über den Azure Marketplace bereitgestellt und abgerechnet werden.

Erstellen Sie, bevor Sie beginnen, [ein Konto für den kommerziellen Marketplace in Partner Center](./partner-center-portal/create-account.md), und stellen Sie sicher, dass Ihr Konto für das kommerzielle Marketplace-Programm registriert ist.

### <a name="technical-fundamentals"></a>Technische Grundlagen

Das Entwerfen, Erstellen und Testen von Angeboten nimmt einiges an Zeit in Anspruch und erfordert Erfahrung im Umgang mit der Azure-Plattform und den Technologien, die zum Erstellen des Angebots verwendet werden. Ihr Entwicklungsteam sollte über Grundkenntnisse im Umgang mit [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage) und [Azure-Netzwerk](https://azure.microsoft.com/services/?filter=networking#networking) sowie über fundierte Kenntnisse in Bezug auf das [Entwerfen und die Architektur von Azure-Anwendungen](https://azure.microsoft.com/solutions/architecture/) verfügen. Weitere Informationen finden Sie in den folgenden technischen Ressourcen: 

- Tutorials
  - [Virtuelle Linux-Computer](../virtual-machines/linux/tutorial-manage-vm.md)
  - [Virtuelle Windows-Computer](../virtual-machines/windows/tutorial-manage-vm.md)

- Beispiele
  - [Azure CLI-Beispiele für Linux-VMs](../virtual-machines/linux/cli-samples.md)
  - [Azure PowerShell für Linux-VMs](../virtual-machines/linux/powershell-samples.md)
  - [Azure CLI-Beispiele für Windows-VMs](../virtual-machines/windows/cli-samples.md)
  - [Azure PowerShell für Windows-VMs](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md)

## <a name="technical-requirements"></a>Technische Anforderungen

Für VM-Angebote gelten die folgenden technischen Anforderungen:

- Sie müssen eine virtuelle Festplatte (Virtual Hard Disk, VHD) für das Betriebssystem vorbereiten. Datenträger-VHDs sind optional. Dies wird weiter unten in diesem Artikel näher erläutert.
- Der Kunde kann Ihr Angebot jederzeit stornieren.
- Sie müssen mindestens einen Plan für Ihr Angebot erstellen. Der Preis Ihres Plans basiert auf der von Ihnen gewählten [Lizenzierungsoption](#licensing-options).
   > [!IMPORTANT]
   > Jedes VM-Image in einem Plan muss die gleiche Anzahl von Datenträgern aufweisen.

Eine VM verfügt über zwei Komponenten:

- **Betriebs-VHD**: Enthält das Betriebssystem und die Lösung, die mit Ihrem Angebot bereitgestellt wird. Der Prozess zur Vorbereitung der VHD unterscheidet sich abhängig davon, ob es sich um eine Linux-VM, eine Windows-VM oder eine benutzerdefinierte VM handelt.
- **Datenträger-VHDs** (optional): Hierbei handelt es sich um dedizierten beständigen Speicher für eine VM. Verwenden Sie nicht die Betriebssystem-VHD (z. B. das Laufwerk C), um beständige Informationen zu speichern. 
    - Sie können bis zu 16 Datenträger hierfür verwenden.
    - Verwenden Sie eine VHD pro Datenträger, auch wenn der Datenträger leer ist.

    > [!NOTE]
    > Fügen Sie unabhängig vom verwendeten Betriebssystem immer nur die von der Lösung benötigte Mindestanzahl von Datenträgern hinzu. Kunden können Datenträger, die Teil eines Images sind, zum Zeitpunkt der Bereitstellung nicht entfernen. Sie haben aber immer die Möglichkeit, während oder nach der Bereitstellung Datenträger hinzuzufügen.

Detaillierte Anleitungen für das Vorbereiten Ihrer technischen Ressourcen finden Sie unter [Erstellen eines virtuellen Computers mit einer genehmigten Basis](azure-vm-create-using-approved-base.md) oder [Erstellen eines virtuellen Computers mit einem eigenen Image](azure-vm-create-using-own-image.md).

## <a name="preview-audience"></a>Vorschauzielgruppe

Eine Vorschauzielgruppe kann vor der Liveveröffentlichung im Azure Marketplace auf Ihr VM-Angebot zugreifen, um die gesamte Funktionalität vorab zu testen. Auf der Seite **Vorschauzielgruppe** können Sie eine eingeschränkte Vorschauzielgruppe definieren. 

> [!NOTE]
> Eine Vorschauzielgruppe unterscheidet sich von einem privaten Plan. Ein privater Plan ist ein Angebot, das nur einem bestimmten Publikum zur Verfügung steht. Darüber hinaus können Sie einen benutzerdefinierten Plan mit bestimmten Kunden aushandeln. Weitere Informationen finden Sie im nächsten Abschnitt: zu Plänen.

Sie können Einladungen an E-Mail-Adressen mit Microsoft-Konto (MSA) oder in Azure Active Directory (Azure AD) senden. Fügen Sie bis zu 10 E-Mail-Adressen manuell hinzu, oder importieren Sie über eine CSV-Datei bis zu 20 E-Mail-Adressen. Wenn Ihr Angebot bereits live geschaltet ist, können Sie dennoch eine Vorschauzielgruppe für das Testen von Änderungen oder für Aktualisierungen Ihres Angebots definieren.

## <a name="plans-and-pricing"></a>Pläne und Preise

Für VM-Angebote ist mindestens ein Plan erforderlich. Ein Plan definiert den Umfang und die Einschränkungen einer Lösung sowie ggf. die zugehörigen Preise. Sie können mehrere Pläne für Ihr Angebot erstellen, um verschiedene technische Optionen und Lizenzierungsoptionen sowie kostenlose Testversionen für Ihre Kunden bereitzustellen. Eine allgemeine Anleitung zu Plänen, einschließlich Preismodellen, kostenlosen Testversionen und privaten Plänen, finden Sie unter [Pläne und Preise für Angebote im kommerziellen Marketplace](plans-pricing.md). 

VMs können vollständig kommerziell genutzt werden, wobei dafür das Modell der nutzungsbasierten Bezahlung oder das Bring-Your-Own-Licence-Lizenzierungsmodell (BYOL) verwendet wird. Microsoft hostet die kommerzielle Transaktion und führt für Sie die Abrechnung mit Ihrem Kunden durch. Sie haben den Vorteil, dass Sie die bevorzugte Zahlungsmethoden zwischen Ihrem Kunden und Microsoft, einschließlich des Enterprise Agreements, nutzen können. Weitere Informationen finden Sie unter [Transaktionsfunktionen im kommerziellen Marketplace](./marketplace-commercial-transaction-capabilities-and-considerations.md).

> [!NOTE]
> Der mit einem Enterprise Agreement verbundene Mindestverbrauch kann auf die Azure-Nutzung Ihrer VM angewendet werden, aber nicht auf Ihre Softwarelizenzgebühren.

### <a name="licensing-options"></a>Lizenzierungsoptionen

Wenn Sie die Veröffentlichung eines neuen VM-Angebots vorbereiten, müssen Sie sich für eine Lizenzierungsoption entscheiden. Davon hängt ab, welche zusätzlichen Informationen Sie später bei der Erstellung Ihres Angebots in Partner Center bereitstellen müssen.

Für VM-Angebote sind die folgenden Lizenzierungsoptionen verfügbar:

| Lizenzierungsoption | Transaktionsprozess |
| --- | --- |
| Kostenlose Testversion | Bieten Sie Ihren Kunden eine kostenlose ein-, drei- oder sechsmonatige Testversion an. |
| Testversion | Mit dieser Option können Ihre Kunden VMs ohne zusätzliche Kosten bewerten. Sie müssen keine bestehenden Azure-Kunden sein, um die Testversion nutzen zu können. Weitere Informationen finden Sie unter [Worum handelt es sich bei einer Testversion?](./what-is-test-drive.md) |
| BYOL | Die Option „Bring Your Own License“ ermöglicht es Ihren Kunden, bereits vorhandene Softwarelizenzen in Azure zu übernehmen.\* |
| Nutzungsbasiert | Die nutzungsbasierte Bezahlung ermöglicht Ihren Kunden eine Zahlung auf Stundenbasis. |
| Interaktive Demo  | Bieten Sie Ihren Kunden mit einer interaktiven Demo eine Einführung in Ihre Lösung. Der Vorteil dieser Option besteht darin, dass Sie eine Testversion anbieten können, ohne ein kompliziertes Setup für Ihre komplexe Lösung bereitstellen zu müssen. |
|

\* Als Herausgeber sind Sie für alle Aspekte der Softwarelizenztransaktion verantwortlich, einschließlich (unter anderem) Auftrag, Auftragserfüllung, Messung, Abrechnung, Rechnungsstellung, Zahlung und Eintreibung.

Das folgende Beispiel zeigt ein VM-Angebot im Azure Marketplace mit nutzungsbasierter Bezahlung.

:::image type="content" source="media/vm/sample-offer-screen.png" alt-text="Bildschirm mit Beispiel-VM-Angebot":::

### <a name="private-plans"></a>Private Pläne

Sie können die Auffindbarkeit und Bereitstellung Ihrer VM auf eine bestimmte Gruppe von Kunden beschränken, indem Sie das Image und die Preise als privaten Plan veröffentlichen. Mit privaten Plänen können Sie exklusive Angebote für Ihre wertvollsten Kunden erstellen und benutzerdefinierte Software und Bedingungen anbieten. Benutzerdefinierte Bestimmungen ermöglichen Ihnen die Schaffung vielfältiger Szenarios, wie etwa vom Vertrieb erzielte Geschäftsabschlüsse mit Sonderpreisen und -bestimmungen sowie ein frühzeitiger Zugang zu Software, die in limitierter Anzahl veröffentlicht wird. Private Pläne ermöglichen es Ihnen, einer begrenzten Gruppe von Kunden bestimmte Preise oder Produkte anzubieten.

Weitere Informationen finden Sie unter [Pläne und Preise für Angebote im kommerziellen Marketplace](plans-pricing.md) und [Private Angebote im kommerziellen Microsoft-Marketplace](private-offers.md).

## <a name="test-drives"></a>Testversionen

Sie können eine Testversion für Ihre VM aktivieren. Über Testversionen können Kunden für eine feste Anzahl von Stunden auf eine vorkonfigurierte Umgebung zugreifen. Sie können Testversionen für beliebige Veröffentlichungsoptionen aktivieren, allerdings gelten für dieses Feature zusätzliche Anforderungen. Weitere Informationen zu Testversionen finden Sie unter [Was ist eine Testversion?](what-is-test-drive.md) Weitere Informationen zum Konfigurieren verschiedener Arten von Testversionen finden Sie unter [Technische Konfiguration der Testversion](test-drive-technical-configuration.md).

> [!TIP]
> Eine Testversion unterscheidet sich von einer [kostenlosen Testversion](plans-pricing.md#free-trials). Sie können entweder eine Testversion, eine kostenlose Testversion oder beides anbieten. Mit beiden Optionen können Kunden Ihre Lösung für einen festen Zeitraum nutzen. Eine Testversion umfasst jedoch auch eine praktische, selbstgesteuerte Tour durch die wichtigsten Features und Vorteile Ihres Produkts, die in einem praxisnahen Implementierungsszenario veranschaulicht werden.

## <a name="customer-leads"></a>Kundenleads

Um Kundeninformationen zu sammeln, müssen Sie Ihr Angebot mit Ihrem CRM-System (Customer Relationship Management) verbinden. Der Kunde wird gebeten, die Berechtigung zur Freigabe seiner Informationen zu erteilen. Diese Kundeninformationen sowie der Angebotsname, die Angebots-ID und der Onlineshop, auf dem der Kunde Ihr Angebot gefunden hat, werden an das von Ihnen konfigurierte CRM-System gesendet. Der kommerzielle Marketplace unterstützt eine Vielzahl von CRM-Systemen. Außerdem können Sie optional eine Azure-Tabelle verwenden oder einen HTTPS-Endpunkt mithilfe von Power Automate konfigurieren.

Eine CRM-Verbindung kann während der Angebotserstellung zu einem beliebigen Zeitpunkt hinzugefügt oder geändert werden. Eine detaillierte Anleitung finden Sie unter [Kundenleads aus Ihrem Angebot im kommerziellen Marketplace](partner-center-portal/commercial-marketplace-get-customer-leads.md).

## <a name="legal-contracts"></a>Verträge

Zur Vereinfachung des Beschaffungsprozesses für Kunden und zur Reduzierung der rechtlichen Komplexität für Softwareanbieter stellt Microsoft eine Vorlage für Standardverträge bereit, die Sie für Ihre Angebote im kommerziellen Marketplace verwenden können. Wenn Sie Ihre Software unter dem Standardvertrag anbieten, müssen Kunden ihn nur einmal lesen und akzeptieren, und Sie müssen keine benutzerdefinierten Geschäftsbedingungen erstellen.

Wenn Sie sich für die Verwendung des Standardvertrags entscheiden, können Sie dem Standardvertrag universelle Zusatzbedingungen und bis zu 10 benutzerdefinierte Ergänzungen hinzufügen. Sie können anstelle des Standardvertrags Ihre eigenen Geschäftsbedingungen verwenden. Diese Details werden auf der Seite **Eigenschaften** verwaltet. Ausführliche Informationen finden Sie unter [Standardvertrag für den kommerziellen Microsoft-Marketplace](standard-contract.md).

> [!NOTE]
> Nachdem Sie ein Angebot mit dem Standardvertrag für den kommerziellen Marketplace veröffentlicht haben, können Sie keine eigenen benutzerdefinierten Geschäftsbedingungen mehr verwenden. Es handelt sich um ein „oder“-Szenario. Entweder bieten Sie Ihre Lösung im Rahmen des Standardvertrags oder Ihrer eigenen Geschäftsbedingungen an. Wenn Sie die Bedingungen des Standardvertrags ändern möchten, können Sie dies über Zusatzvereinbarungen für den Standardvertrag erreichen.

## <a name="cloud-solution-providers"></a>Cloud Solution Provider (CSP)

Wenn Sie Ihr Angebot in Partner Center erstellen, wird die Registerkarte **Weiterverkauf über CSPs** angezeigt. Diese Option ermöglicht es Partnern, die am Microsoft Cloud Solution Provider-Programm (CSP) teilnehmen, Ihre VM als Teil eines Bundleangebots weiterzuverkaufen. Alle BYOL-Pläne (Bring-Your-Own-License) werden im Programm automatisch aktiviert. Sie können auch Ihre Nicht-BYOL-Pläne aktivieren. Weitere Informationen finden Sie unter [Cloud-Lösungsanbieter-Programm](cloud-solution-providers.md). 

> [!NOTE]
> Die Option zur Nutzung des CSP-Partnerkanals (Cloud Solution Provider) ist jetzt verfügbar. Weitere Informationen zur Vermarktung Ihres Angebots über Microsoft CSP-Partnerkanäle finden Sie unter [**Cloud Solution Provider-Programm**](./cloud-solution-providers.md).

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen eines VM-Angebots im Azure Marketplace](azure-vm-create.md)
- [Erstellen eines virtuellen Computers mit einer genehmigten Basis](azure-vm-create-using-approved-base.md) oder [Erstellen eines virtuellen Computers mit einem eigenen Image](azure-vm-create-using-own-image.md)
- [Bewährte Methoden für Angebotslistung](gtm-offer-listing-best-practices.md)