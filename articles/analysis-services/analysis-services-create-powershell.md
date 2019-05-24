---
title: 'Schnellstart: Erstellen eines Azure Analysis Services-Servers mithilfe von PowerShell | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie mithilfe von PowerShell einen Azure Analysis Services-Server erstellen.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b3b23509197dd1d91dbd74c57b3c732151c51a97
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66142986"
---
# <a name="quickstart-create-a-server---powershell"></a>Schnellstart: Erstellen eines Servers – PowerShell

In diesem Schnellstart erfahren Sie, wie Sie mithilfe von PowerShell über die Befehlszeile einen Azure Analysis Services-Server in Ihrem Azure-Abonnement erstellen.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-Abonnement:** Besuchen Sie die Webseite [Kostenlose Azure-Testversion](https://azure.microsoft.com/offers/ms-azr-0044p/), und erstellen Sie ein Konto.
- **Azure Active Directory:** Ihr Abonnement muss einem Azure Active Directory-Mandanten zugeordnet sein, und Sie müssen über ein Konto in diesem Verzeichnis verfügen. Weitere Informationen finden Sie unter [Authentifizierung und Benutzerberechtigungen](analysis-services-manage-users.md).
- **Azure PowerShell**. Führen Sie `Get-Module -ListAvailable Az` aus, um die installierte Version zu ermitteln. Informationen zum Installieren oder Upgraden finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/install-Az-ps).

## <a name="import-azanalysisservices-module"></a>Importieren des Moduls „Az.AnalysisServices“

Für die Servererstellung in Ihrem Abonnement verwenden Sie das Modul [Az.AnalysisServices](/powershell/module/az.analysisservices). Laden Sie das Modul „Az.AnalysisServices“ in Ihre PowerShell-Sitzung.

```powershell
Import-Module Az.AnalysisServices
```

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich mithilfe des Befehls [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) bei Ihrem Azure-Abonnement an. Befolgen Sie die Anweisungen auf dem Bildschirm.

```powershell
Connect-AzAccount
```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine [Azure-Ressourcengruppe](../azure-resource-manager/resource-group-overview.md) ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und als Gruppe verwaltet werden. Wenn Sie Ihren Server erstellen, müssen Sie eine Ressourcengruppe in Ihrem Abonnement angeben. Falls Sie noch nicht über eine Ressourcengruppe verfügen, können Sie mithilfe des Befehls [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine neue Ressourcengruppe erstellen. Im folgenden Beispiel wird eine Ressourcengruppe namens `myResourceGroup` in der Region „USA, Westen“ erstellt.

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "WestUS"
```

## <a name="create-a-server"></a>Erstellen eines Servers

Erstellen Sie mithilfe des Befehls [New-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver) einen neuen Server. Im folgenden Beispiel wird ein Server namens „myServer“ in „myResourceGroup“ in der Region „USA, Westen“ zum Tarif „D1“ (kostenlos) erstellt und philipc@adventureworks.com als Serveradministrator angegeben.

```powershell
New-AzAnalysisServicesServer -ResourceGroupName "myResourceGroup" -Name "myserver" -Location WestUS -Sku D1 -Administrator "philipc@adventure-works.com"
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Mithilfe des Befehls [Remove-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver) können Sie den Server aus Ihrem Abonnement entfernen. Falls Sie mit weiteren Schnellstartanleitungen und Tutorials aus dieser Sammlung fortfahren möchten, entfernen Sie den Server nicht. Im folgenden Beispiel wird der im vorherigen Schritt erstellte Server entfernt.


```powershell
Remove-AzAnalysisServicesServer -Name "myserver" -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie gelernt, wie Sie einen Server in Ihrem Azure-Abonnement mithilfe von PowerShell erstellen. Da Sie nun über einen Server verfügen, können Sie durch Konfigurieren einer (optionalen) Serverfirewall zur Sicherheit beitragen. Außerdem können Sie Ihrem Server direkt über das Portal ein einfaches Beispieldatenmodell hinzufügen. Ein Beispielmodell ist hilfreich, wenn Sie sich mit dem Konfigurieren von Modelldatenbankrollen und dem Testen von Clientverbindungen vertraut machen. Fahren Sie mit dem Tutorial zum Hinzufügen eines Beispielmodells fort, um mehr zu erfahren.

> [!div class="nextstepaction"]
> [Schnellstart: Konfigurieren der Serverfirewall – Portal](analysis-services-qs-firewall.md)      
> [!div class="nextstepaction"]
> [Tutorial: Hinzufügen eines Beispielmodells zu Ihrem Server](analysis-services-create-sample-model.md)
