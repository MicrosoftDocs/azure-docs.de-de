---
title: 'Verwalten von Azure Key Vault mit Azure Automation: Azure Key Vault | Microsoft-Dokumentation'
description: Erfahren Sie, wie der Azure Automation-Dienst zum Verwalten des Azure-Schlüsseltresors verwendet werden kann.
services: Key-Vault, automation
author: mgoedtel
manager: jwhit
editor: ''
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: magoedte
ms.openlocfilehash: 87fdb565f47e7d88342c82bd5940c1ddb9c137e6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64712121"
---
# <a name="managing-azure-key-vault-using-azure-automation"></a>Verwalten des Azure-Schlüsseltresors mit Azure Automation

Dieser Leitfaden bietet eine Einführung in den Azure Automation-Dienst und zeigt, wie Azure Automation zur einfacheren Verwaltung Ihrer Schlüssel und geheimen Schlüssel im Azure-Schlüsseltresor verwendet werden kann.

## <a name="what-is-azure-automation"></a>Was ist Azure Automation?

[Azure Automation](../automation/automation-intro.md) ist ein Azure-Dienst zur Vereinfachung der Cloudverwaltung durch Prozessautomatisierung und Konfiguration des gewünschten Zustands. Mithilfe von Azure Automation können manuelle, sich wiederholende, lang andauernde und fehleranfällige Aufgaben automatisiert werden, um die Zuverlässigkeit und Effizienz für Ihre Organisation zu erhöhen und die Amortisierungszeit zu verkürzen.

Azure Automation bietet eine äußerst zuverlässige, hoch verfügbare Workflow-Ausführungs-Engine, die sich mittels Skalierung an Ihre Anforderungen anpasst. In Azure Automation können Prozesse manuell, durch Drittanbietersysteme oder in geplanten Intervallen gestartet werden, sodass Aufgaben genau nach Bedarf ausgeführt werden.

Indem Sie die Aufgaben im Zusammenhang mit der Cloudverwaltung mit Azure Automation automatisieren, verringern Sie den Verwaltungsaufwand und ermöglichen es Ihren IT- und DevOps-Mitarbeitern, sich Aufgaben zu widmen, die einen Mehrwert für Ihr Unternehmen liefern.

## <a name="how-can-azure-automation-help-manage-azure-key-vault"></a>Wie kann Azure Automation beim Verwalten des Azure-Schlüsseltresors nützlich sein?

Key Vault kann in Azure Automation mithilfe der [AzureRM Key Vault-Cmdlets](https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) und der [klassischen Azure Key Vault-Cmdlets](https://docs.microsoft.com/powershell/module/servicemanagement/azure) verwaltet werden. Das Azure-Modul für die Verwaltung einer klassischen Key Vault-Instanz ist automatisch in Azure Automation verfügbar. Sie können das [AzureRM-KeyVault-Modul](https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) in Azure Automation importieren, sodass viele Key Vault-Verwaltungsaufgaben im Dienst ausgeführt werden können. Weitere Informationen zum Importieren Ihres Moduls in Azure Automation finden Sie unter [Verwalten von Modulen in Azure Automation](../automation/shared-resources/modules.md). Sie können diese Cmdlets in Azure Automation auch an die Cmdlets für andere Azure-Dienste koppeln, um komplexe Aufgaben über Azure-Dienste und Drittanbietersysteme hinweg zu automatisieren.

Mit den Azure Key Vault-Cmdlets können Sie unter anderem die folgenden Aufgaben ausführen: 

* Erstellen und Konfigurieren eines Schlüsseltresors
* Erstellen oder Importieren eines Schlüssels
* Erstellen oder Aktualisieren eines geheimen Schlüssels
* Aktualisieren der Attribute eines Schlüssels
* Abrufen eines Schlüssels oder geheimen Schlüssels
* Löschen eines Schlüssels oder geheimen Schlüssels

Hier sind einige Beispiele für die Verwendung von PowerShell zum Verwalten von Key Vault aufgeführt:  

* [Azure Key Vault - Step by Step (Azure Key Vault – Schritt für Schritt)](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step)
* [Setting Up and Configuring an Azure Key Vault (Einrichten und Konfigurieren eines Azure-Schlüsseltresors)](https://www.simple-talk.com/cloud/platform-as-a-service/setting-up-and-configuring-an-azure-key-vault)

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen von Azure Automation und dessen Einsatz zum Verwalten des Azure-Schlüsseltresors vertraut gemacht haben, folgen Sie diesen Links, um weitere Informationen zu Azure Automation zu erhalten.

* Weitere Informationen erhalten Sie im Lernprogramm [Erste Schritte](../automation/automation-first-runbook-graphical.md)zu Azure Automation.
* Weitere Informationen finden Sie in den [PowerShell-Skripts des Azure-Schlüsseltresors](https://gallery.technet.microsoft.com/scriptcenter/site/search?query=azure%20key%20vault&f%5B0%5D.Value=azure%20key%20vault&f%5B0%5D.Type=SearchText&ac=5).

