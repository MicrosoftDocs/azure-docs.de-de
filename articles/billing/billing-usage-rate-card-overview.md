---
title: Abrufen der Azure-Nutzung mit Azure-Abrechnungs-APIs | Microsoft-Dokumentation
description: Erfahren Sie mehr über die Azure-Abrechnungs-APIs für Nutzung und Gebührenkarte, mit denen Sie Einblicke in den Azure-Ressourcenverbrauch und damit verbundene Trends erlangen können.
services: ''
documentationcenter: ''
author: tonguyen
manager: mumami
editor: ''
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 5/10/2018
ms.author: erikre
ms.custom: seodec18
ms.openlocfilehash: 99772579fd28b0880d497abb038fb25fd2a6139e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60369266"
---
# <a name="use-azure-billing-apis-to-programmatically-get-insight-into-your-azure-usage"></a>Verwenden der Azure-Abrechnungs-APIs, um programmgesteuerte Einblicke in die Nutzung Ihrer Azure-Ressourcen zu erlangen
Verwenden Sie Azure-Abrechnungs-APIs, um Nutzungs- und Ressourcendaten mittels Pull in Ihre bevorzugten Datenanalysetools abzurufen. Mithilfe der Azure-Ressourcennutzungs- und -Gebührenkarten-APIs können Sie Ihre Kosten genau vorhersagen und verwalten. Die APIs werden als Ressourcenanbieter implementiert und sind Teil der API-Familie, die von Azure Resource Manager verfügbar gemacht wird.  

## <a name="azure-invoice-download-api-preview"></a>Azure-API zum Herunterladen von Rechnungen (Vorschau)
Sobald das [Abonnieren erfolgt ist](billing-manage-access.md#opt-in), laden Sie Rechnungen mit der Vorschauversion der [Rechnungs-API](/rest/api/billing) herunter. Sie enthält folgende Features:

* **Rollenbasierte Zugriffssteuerung für Azure** – Sie können Ihre Zugriffsrichtlinien im [Azure-Portal](https://portal.azure.com) oder mit [Azure PowerShell-Cmdlets](/powershell/azure/overview) konfigurieren, um festzulegen, welche Benutzer oder Anwendungen Zugriff auf die Nutzungsdaten eines Abonnements erhalten. Aufrufer müssen zur Authentifizierung standardmäßige Azure Active Directory-Token verwenden. Ordnen Sie dem Aufrufer die Rolle „Abrechnungsleser“, „Leser“, „Besitzer“ oder „Mitwirkender“ zu, um Zugriff auf die Nutzungsdaten für ein bestimmtes Azure-Abonnement zu erhalten.
* **Datumsfilterung**: Nutzen Sie den `$filter`-Parameter, um alle Rechnungen in umgekehrter chronologischer Reihenfolge nach dem Enddatum des Rechnungszeitraums abzurufen.

> [!NOTE]
> Dieses Feature befindet sich in der ersten Vorschauversion und kann Änderungen aus Gründen der Abwärtskompatibilität unterliegen. Derzeit ist es für bestimmte Abonnementangebote (EA, CSP, AIO werden nicht unterstützt) und Azure Deutschland nicht verfügbar.

## <a name="azure-resource-usage-api-preview"></a>Azure-API zur Ressourcennutzung (Preview)
Mit der [Azure-Ressourcennutzungs-API](/previous-versions/azure/reference/mt219003(v=azure.100)) können Sie Ihre geschätzten Azure-Nutzungsdaten abrufen. Die API umfasst:

* **Rollenbasierte Zugriffssteuerung für Azure** – Sie können Ihre Zugriffsrichtlinien im [Azure-Portal](https://portal.azure.com) oder mit [Azure PowerShell-Cmdlets](/powershell/azure/overview) konfigurieren, um festzulegen, welche Benutzer oder Anwendungen Zugriff auf die Nutzungsdaten eines Abonnements erhalten. Aufrufer müssen zur Authentifizierung standardmäßige Azure Active Directory-Token verwenden. Ordnen Sie dem Aufrufer die Rolle „Abrechnungsleser“, „Leser“, „Besitzer“ oder „Mitwirkender“ zu, um Zugriff auf die Nutzungsdaten für ein bestimmtes Azure-Abonnement zu erhalten.
* **Stündliche oder tägliche Aggregationen** – Aufrufer können angeben, ob sie ihre Azure-Nutzungsdaten in stündlichen Buckets oder täglichen Buckets erhalten möchten. Die Standardeinstellung ist „Täglich“.
* **Instanzmetadaten (inkl. Ressourcentags)** – Details der Instanzebene, z.B. der vollqualifizierte Ressourcen-URI „(/subscriptions/{subscription-id}/..)“, Ressourcengruppeninformationen und Ressourcentags. Mit diesen Metadaten können Sie die Nutzung auf deterministische und programmgesteuerte Weise anhand der Tags zuordnen, z.B. bei der internen Verrechnung.
* **Ressourcenmetadaten** – Ressourcendetails (z.B. Meter Name, Meter Category, Meter Sub-Category, Unit und Region) lassen den Aufrufer besser verstehen, was genutzt wurde. Außerdem arbeiten wir an einer Vereinheitlichung der Terminologie für Ressourcenmetadaten im Azure-Portal, in der CSV-Datei zur Azure-Nutzung, in der CSV-Datei zur EA-Abrechnung und in anderen öffentlich zugänglichen Benutzeroberflächen, um Ihnen die Korrelation von Daten über verschiedene Umgebungen hinweg zu ermöglichen.
* **Nutzung für verschiedene Angebotstypen** – Nutzungsdaten sind für Angebotstypen verfügbar, z. B. „Nutzungsbasierte Bezahlung“, „MSDN“, „Verbindliche Zusage“, „Guthaben“ und „EA“, mit Ausnahme von [CSP](https://docs.microsoft.com/azure/cloud-solution-provider/billing/azure-csp-invoice#retrieve-usage-data-for-a-specific-subscription).

## <a name="azure-resource-ratecard-api-preview"></a>Azure-API für Ressourcengebührenkarte (Preview)
Mit der [Azure-Ressourcengebührenkarten-API](/previous-versions/azure/reference/mt219005(v=azure.100)) (Resource RateCard) können Sie die Liste der verfügbaren Azure-Ressourcen sowie jeweils die geschätzten Preise abrufen. Die API umfasst:

* **Rollenbasierte Zugriffssteuerung für Azure** – Sie können Ihre Zugriffsrichtlinien im [Azure-Portal](https://portal.azure.com) oder mit [Azure PowerShell-Cmdlets](/powershell/azure/overview) konfigurieren, um festzulegen, welche Benutzer oder Anwendungen Zugriff auf die Gebührenkartendaten erhalten. Aufrufer müssen zur Authentifizierung standardmäßige Azure Active Directory-Token verwenden. Ordnen Sie dem Aufrufer die Rolle „Leser“, „Besitzer“ oder „Mitwirkender“ zu, um Zugriff auf die Nutzungsdaten für ein bestimmtes Azure-Abonnement zu erhalten.
* **Unterstützung für die Angebote „Nutzungsbasierte Bezahlung“, „MSDN“, „Verbindliche Zusage“ und „Guthaben“ („EA“ und [CSP](https://docs.microsoft.com/azure/cloud-solution-provider/billing/azure-csp-pricelist#get-prices-by-using-the-azure-rate-card) werden nicht unterstützt)** – Mit dieser API werden Preisinformationen der Azure-Angebotsebene bereitgestellt.  Aufrufer dieser API müssen die Angebotsinformationen übergeben, um Ressourcendetails und -preise abzurufen. Wir können derzeit keine EA-Preise angeben, da EA-Angebote über angepasste Preise pro Registrierung verfügen.

## <a name="scenarios"></a>Szenarien
Hier sind einige Szenarios angegeben, die aufgrund der Kombination aus Nutzungs- und Gebührenkarten-APIs möglich sind:

* **Azure-Ausgaben während des Monats** – Setzen Sie die Nutzungs- und Gebührenkarten-APIs zusammen ein, um einen besseren Einblick in ihre Cloudausgaben während eines Monats zu erhalten. Hierzu analysieren sie die stündlichen und täglichen Buckets für die Nutzung und die Gebührenschätzungen.
* **Einrichten von Warnungen**  – Verwenden Sie die Nutzungs- und Gebührenkarten-APIs, um die Verbrauchs- und Gebührenschätzungen für die Cloud abzurufen und ressourcenbasierte oder kostenbasierte Warnungen einzurichten.
* **Vorhersagen des Rechnungsbetrags** – Rufen Sie Ihren geschätzten Verbrauch und die geschätzten Cloudausgaben ab, und wenden Sie Machine Learning-Algorithmen an, um vorherzusagen, welcher Betrag sich am Ende des Abrechnungszeitraums ergibt.
* **Kostenanalyse vor der Nutzung**  – Mit der Gebührenkarten-API können Sie vorhersagen, wie hoch ihre Rechnung bei der erwarteten Nutzung wäre, wenn sie ihre Workloads auf Azure verlagern. Wenn Sie über vorhandene Workloads in anderen Clouds bzw. Private Clouds verfügen, können Sie die Nutzung auch den Azure-Preisen zuordnen, um eine bessere Schätzung der Azure-Ausgaben zu erhalten. Diese Schätzungen bieten die Pivot-Funktion für ein Angebot an und ermöglichen Ihnen, verschiedene Angebotstypen über die nutzungsbasierte Bezahlung hinaus zu vergleichen und gegenüberzustellen, z.B. verbindliche Zusage und Guthaben. Außerdem können Sie mit den APIs Kostenunterschiede nach Region anzeigen und eine Was-wäre-wenn-Analyse durchführen, um Bereitstellungsentscheidungen zu erleichtern.
* **Was-wäre-wenn-Analyse** -

  * Sie können ermitteln, ob es kostengünstiger ist, Ihre Workloads in einer anderen Region oder unter einer anderen Konfiguration der Azure-Ressource auszuführen. Azure-Ressourcenkosten können je nach der Azure-Region variieren, in der sie ausgeführt werden.
  * Außerdem können Sie ermitteln, ob ein anderer Azure-Angebotstyp zu einem besseren Preis für eine Azure-Ressource führt.


## <a name="next-steps"></a>Nächste Schritte
* Sehen Sie sich die Codebeispiele auf GitHub an:
  * [Codebeispiel der Rechnungs-API](https://go.microsoft.com/fwlink/?linkid=845124)

  * [Codebeispiel der Nutzung-API](https://github.com/Azure-Samples/billing-dotnet-usage-api)

  * [Codebeispiel der RateCard-API](https://github.com/Azure-Samples/billing-dotnet-ratecard-api)

* Weitere Informationen zum Azure-Ressourcen-Manager finden Sie unter [Übersicht über den Azure-Ressourcen-Manager](../azure-resource-manager/resource-group-overview.md) .
