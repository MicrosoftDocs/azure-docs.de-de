---
author: mgoedtel
ms.service: log-analytics
ms.topic: include
ms.date: 11/09/2018
ms.author: magoedte
ms.openlocfilehash: 6890c71ac7c265d46cc77751786fea4d0b228588
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178484"
---
### <a name="troubleshoot-azure-diagnostics"></a>Behandeln von Problemen mit Azure Diagnostics

Wenn Sie die folgende Fehlermeldung erhalten, ist der Ressourcenanbieter „Microsoft.insights“ nicht registriert:

`Failed to update diagnostics for 'resource'. {"code":"Forbidden","message":"Please register the subscription 'subscription id' with Microsoft.Insights."}`

Führen Sie im Azure-Portal die folgenden Schritte aus, um den Ressourcenanbieter zu registrieren:

1.  Klicken Sie im linken Navigationsbereich auf *Abonnements*.
2.  Wählen Sie das in der Fehlermeldung angegebene Abonnement aus.
3.  Klicken Sie auf *Ressourcenanbieter*.
4.  Suchen Sie nach dem Anbieter *Microsoft.insights*.
5.  Klicken Sie auf den Link *Registrieren*.

![Registrierung des Ressourcenanbieters „Microsoft.insights“](./media/log-analytics-troubleshoot-azure-diagnostics/log-analytics-register-microsoft-diagnostics-resource-provider.png)

Wiederholen Sie nach der Registrierung des Ressourcenanbieters *Microsoft.insights* die Diagnosekonfiguration.


Wenn Sie in PowerShell die folgende Fehlermeldung erhalten, müssen Sie Ihre Version von PowerShell aktualisieren:

`Set-AzDiagnosticSetting : A parameter cannot be found that matches parameter name 'WorkspaceId'.`

Aktualisieren Sie Ihre Version von Azure PowerShell, indem Sie die Anweisungen im Artikel [Installieren von Azure PowerShell](/powershell/azure/install-az-ps) befolgen.
