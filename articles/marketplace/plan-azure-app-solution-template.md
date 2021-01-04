---
title: Planen einer Lösungsvorlage für ein Azure-Anwendungsangebot
description: Erfahren Sie, was zum Erstellen eines Plans für eine Lösungsvorlage für ein neues Azure-Anwendungsangebot erforderlich ist, wenn Sie das Portal für den kommerziellen Marketplace in Microsoft Partner Center verwenden.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: 3e4d0513808cdc44fc71e182a07fa6b050d182ee
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452506"
---
# <a name="plan-a-solution-template-for-an-azure-application-offer"></a>Planen einer Lösungsvorlage für ein Azure-Anwendungsangebot

In diesem Artikel werden die Anforderungen zum Veröffentlichen eines Plans für eine Lösungsvorlage für ein Azure-Anwendungsangebot erläutert. Ein Plan für eine Lösungsvorlage ist einer der beiden für Azure-Anwendungsangebote unterstützten Plantypen. Weitere Informationen zu den Unterschieden zwischen diesen beiden Plantypen finden Sie unter [Plantypen](plan-azure-application-offer.md#plans). Wenn Sie den Artikel [Planen eines Azure-Anwendungsangebots](plan-azure-application-offer.md) noch nicht gelesen haben, holen Sie dies nach.

Für den Plantyp „Lösungsvorlage“ ist eine [Azure Resource Manager-Vorlage (ARM-Vorlage)](../azure-resource-manager/templates/overview.md) erforderlich, damit Ihre Lösungsinfrastruktur automatisch bereitgestellt werden kann.

## <a name="solution-template-requirements"></a>Anforderungen für Lösungsvorlagen

| Requirements (Anforderungen) | Details |
| ------------ | ------------- |
| Abrechnung und Messung | Pläne für Lösungsvorlagen sind keine Transaktionsangebote. Sie können jedoch zum Bereitstellen bezahlter VM-Angebote verwendet werden, die über den kommerziellen Microsoft-Marketplace abgerechnet werden. Die über die ARM-Vorlage der Lösung bereitgestellten Ressourcen werden im Azure-Abonnement des Kunden eingerichtet. Bei virtuellen Computern mit nutzungsbasierter Bezahlung erfolgt die Transaktion mit dem Kunden über Microsoft, und die Abrechnung wird über das Azure-Abonnement des Kunden abgewickelt. <br><br> Bei der Abrechnung nach dem Modell „Bring Your Own License“ (BYOL) rechnet Microsoft die angefallenen Infrastrukturkosten im Kundenabonnement ab, während Sie Ihre Softwarelizenzgebühren direkt mit dem Kunden abrechnen. |
| Azure-kompatible virtuelle Festplatte (VHD) | VMs müssen unter Windows oder Linux erstellt werden. Weitere Informationen finden Sie unter<ul><li>[Erstellen einer technischen Azure-VM-Ressource](./azure-vm-create-certification-faq.md#address-a-vulnerability-or-an-exploit-in-a-vm-offer) (für Windows-VHDs)</li><li>[Von Azure unterstützte Distributionen von Linux](../virtual-machines/linux/endorsed-distros.md) (für Linux-VHDs)</li></ul> |
| Zuordnen der Nutzung durch Kunden | Die Zuordnung der Nutzung durch Kunden muss für alle Lösungsvorlagen aktiviert werden, die im Azure Marketplace veröffentlicht werden. Weitere Informationen zur Zuordnung der Nutzung durch Kunden sowie zu deren Aktivierung finden Sie unter [Zuordnung der Nutzung durch Kunden von Azure-Partnern](azure-partner-customer-usage-attribution.md). |
| Verwenden von verwalteten Datenträgern | [Verwaltete Datenträger](../virtual-machines/managed-disks-overview.md) sind die Standardoption für persistente Datenträger von Infrastructure-as-a-Service-VMs (IaaS-VMs) in Azure. In Lösungsvorlagen müssen Sie verwaltete Datenträger verwenden.<ul><li>Wenn Sie Ihre Lösungsvorlagen aktualisieren möchten, folgen Sie den Anweisungen unter [Verwenden verwalteter Datenträger mit Resource Manager-Vorlagen](../virtual-machines/using-managed-disks-template-deployments.md), und verwenden Sie die bereitgestellten [Beispiele](https://github.com/Azure/azure-quickstart-templates).</li><li>Wenn Sie die VHD als Image im Azure Marketplace veröffentlichen möchten, importieren Sie die zugrunde liegende VHD der verwalteten Datenträger in ein Speicherkonto. Verwenden Sie dazu entweder [Azure PowerShell](../virtual-machines/scripts/virtual-machines-powershell-sample-copy-managed-disks-vhd.md) oder die [Azure-Befehlszeilenschnittstelle](../virtual-machines/scripts/virtual-machines-cli-sample-copy-managed-disks-vhd.md).</ul> |
| Bereitstellungspaket | Sie benötigen ein Bereitstellungspaket, mit dem Kunden Ihren Plan bereitstellen können. Wenn Sie mehrere Pläne erstellen, die dieselbe technische Konfiguration erfordern, können Sie auch dasselbe Planpaket verwenden. Weitere Details finden Sie im nächsten Abschnitt: „Bereitstellungspaket“. |
|||

## <a name="deployment-package"></a>Bereitstellungspaket

Das Bereitstellungspaket enthält alle für diesen Plan erforderlichen Vorlagendateien sowie alle zusätzlichen Ressourcen in einer ZIP-Datei.

Alle Azure-Anwendungen müssen diese beiden Dateien im Stammordner eines ZIP-Archivs enthalten:

- Eine Resource Manager-Vorlagendatei mit dem Namen [mainTemplate.json](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template). In dieser Vorlage werden die Ressourcen definiert, die im Azure-Abonnement des Kunden bereitgestellt werden sollen. Beispiele für Resource Manager-Vorlagen finden Sie im [Katalog der Azure-Schnellstartvorlagen](https://azure.microsoft.com/documentation/templates/) oder im entsprechenden Repository [GitHub: Azure Resource Manager Quickstart Templates](https://github.com/azure/azure-quickstart-templates) (Azure Resource Manager-Schnellstartvorlagen).
- Eine Benutzeroberflächendefinition für die Oberfläche zum Erstellen von Azure-Anwendungen mit dem Namen [createUiDefinition.json](../azure-resource-manager/managed-applications/create-uidefinition-overview.md). Auf der Benutzeroberfläche geben Sie Elemente an, die Kunden die Angabe von Parameterwerten ermöglichen.

Unterstützte maximale Dateigrößen:

- Bis zu 1 GB komprimierte Gesamtgröße des ZIP-Archivs
- Bis zu 1 GB für jede einzelne dekomprimierte Datei aus dem ZIP-Archiv

Alle neuen Azure-Anwendungsangebote müssen auch eine [GUID zum Zuordnen der Nutzung durch Kunden von Azure-Partnern](azure-partner-customer-usage-attribution.md) enthalten.

## <a name="azure-regions"></a>Azure-Regionen

Sie können Ihren Plan in öffentlichen Azure-Regionen und Azure Government-Regionen veröffentlichen. Testen und validieren Sie Ihren Plan vor dem Veröffentlichen für [Azure Government](../azure-government/documentation-government-manage-marketplace-partners.md) in der Umgebung, da sich bestimmte Endpunkte möglicherweise unterscheiden. Fordern Sie für Einrichten und Testen Ihres Plans ein Testkonto bei [Microsoft Azure Government-Test](https://azure.microsoft.com/global-infrastructure/government/request/) an.

Als Herausgeber sind Sie für alle Kompatibilitätskontrollen, Sicherheitsmaßnahmen und bewährten Methoden verantwortlich. Azure Government verwendet physisch isolierte Rechenzentren und Netzwerke (die sich ausschließlich in den USA befinden).

Eine Liste der Länder und Regionen, die vom kommerziellen Marketplace unterstützt werden, finden Sie unter [Geografische Verfügbarkeit und Unterstützung von Währungen](marketplace-geo-availability-currencies.md).

Azure Government-Dienste verarbeiten Daten, die bestimmten behördlichen Vorschriften und Anforderungen unterliegen. Beispielsweise FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 und CJIS. Um bei Ihren Zertifizierungen ein Bewusstsein für diese Programme zu schaffen, können Sie bis zu 100 Links bereitstellen, in denen sie beschrieben werden. Diese können entweder direkt mit ihrem Listing im Programm verknüpft sein oder zu Beschreibungen der Konformität mit diesen Programmen auf Ihren eigenen Websites weiterleiten. Diese Links sind nur für Azure Government-Kunden sichtbar.

## <a name="choose-who-can-see-your-plan"></a>Auswählen der Sichtbarkeit des Plans

Sie können festlegen, dass jeder Plan für alle Benutzer (öffentlich) oder nur für eine bestimmte Zielgruppe (privat) angezeigt werden soll. Sie können bis zu 100 Pläne erstellen, von denen bis zu 45 privat sein können. Möglicherweise möchten Sie einen privaten Plan erstellen, um bestimmten Kunden abweichende Preisoptionen oder technische Konfigurationen anzubieten.

Der Zugriff auf einen privaten Plan wird über Azure-Abonnement-IDs zugewiesen. Dabei kann eine Beschreibung jeder zugewiesenen Abonnement-ID angegeben werden. Sie können maximal 10 Abonnement-IDs manuell eingeben oder über eine CSV-Datei bis zu 10.000 Abonnement-IDs angeben. Azure-Abonnement-IDs werden als GUIDs dargestellt. Buchstaben müssen in Kleinbuchstaben angegeben werden.

> [!NOTE]
> Wenn Sie einen privaten Plan veröffentlichen, können Sie die Sichtbarkeit später in „Öffentlich“ ändern. Wenn Sie hingegen einen öffentlichen Plan veröffentlichen, können Sie dessen Sichtbarkeit nicht in „Privat“ ändern.

Bei Plänen für Lösungsvorlagen können Sie außerdem festlegen, dass der Plan nicht im Azure Marketplace angezeigt wird. Dies ist möglicherweise sinnvoll, wenn der Plan nur indirekt über eine andere Lösungsvorlage oder verwaltete Anwendung bereitgestellt wird.

> [!NOTE]
> Private Pläne werden bei Azure-Abonnements, die über einen Handelspartner des Cloud Solution Provider-Programms (CSP) eingerichtet wurden, nicht unterstützt.

Weitere Informationen finden Sie unter [Private Angebote im kommerziellen Microsoft-Marketplace](private-offers.md).

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen eines Azure-Anwendungsangebots im kommerziellen Marketplace](create-new-azure-apps-offer.md)