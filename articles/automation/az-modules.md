---
title: Verwenden von Az-Modulen in Azure Automation
description: Dieser Artikel enthält Informationen zum Verwenden von Az-Modulen in Azure Automation.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 02/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a45a1fbe0d7a99c970d6f8f5626c1349f9d8b1ca
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67123697"
---
# <a name="az-module-support-in-azure-automation"></a>Unterstützung für Az-Module in Azure Automation

Azure Automation unterstützt die Möglichkeit zum Verwenden des [Azure PowerShell Az-Moduls](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) in Ihren Runbooks. Das Az-Modul wird nicht automatisch in neue oder vorhandenen Automation-Konten importiert. Dieser Artikel beschreibt, wie Sie Az-Module mit Azure Automation verwenden.

## <a name="considerations"></a>Überlegungen

Es gibt viele Dinge zu beachten, wenn Sie das Az-Modul in Azure Automation verwenden. Runbooks und Module können von übergeordneten Lösungen in Ihrem Automation-Konto verwendet werden. Das Bearbeiten von Runbooks oder das Aktualisieren von Modulen kann zu Problemen mit Ihren Runbooks führen. Sie sollten alle Runbooks und Lösungen sorgfältig in einem separaten Automation-Konto testen, bevor Sie die neuen `Az`-Module importieren. Modifikationen an Modulen können sich negativ auf übergeordnete Lösungen auswirken, wie z.B. die Updateverwaltung und das Starten/Stoppen von VMs außerhalb der Geschäftszeiten. Es wird empfohlen, Module und Runbooks in Automation-Konten, die Lösungen enthalten, nicht zu ändern. Dieses Verhalten ist nicht spezifisch für die Az-Module. Dieses Verhalten sollte bei der Einführung von Änderungen in Ihrem Automation-Konto berücksichtigt werden.

Beim Importieren eines `Az`-Moduls in Ihr Automation-Konto wird das Modul in der PowerShell-Sitzung, die die Runbooks verwenden, nicht automatisch importiert. Module werden in den folgenden Situationen in die PowerShell-Sitzung importiert:

* Wenn ein Cmdlet von einem Modul aus einem Runbook aufgerufen wird
* Wenn ein Runbook es explizit mit einem `Import-Module`-Cmdlet importiert
* Wenn ein anderes Modul, das vom Modul abhängig ist, in eine PowerShell-Sitzung importiert wird

> [!IMPORTANT]
> Es ist wichtig sicherzustellen, dass Runbooks in einem Automation-Konto entweder nur `Az`- oder `AzureRM`-Module in die von Runbooks verwendeten PowerShell-Sitzungen importieren und nicht beide. Wenn `Az` vor `AzureRM` in ein Runbook importiert wird, wird das Runbook abgeschlossen, aber es wird ein [Fehler mit Bezug auf die Methode get_SerializationSettings](troubleshoot/runbooks.md#get-serializationsettings) in den Auftragsdatenströmen angezeigt, und Cmdlets wurden möglicherweise nicht ordnungsgemäß ausgeführt. Wenn Sie `AzureRM` und dann `Az` importieren, wird Ihr Runbook immer noch vollständig sein, aber Sie werden einen Fehler in den Auftragsdatenströmen sehen, der besagt, dass sowohl `Az` als auch `AzureRM` nicht in der gleichen Sitzung importiert oder im gleichen Runbook verwendet werden können.

## <a name="migrating-to-az-modules"></a>Migrieren zu Az-Modulen

Es wird empfohlen, die Migration zur Verwendung von Az-Modulen anstelle von AzureRM-Modulen in einem Automation-Testkonto zu testen. Sobald dieses Automation-Konto erstellt wurde, können Sie anhand der folgenden Schritte sicherstellen, dass die Migration reibungslos verläuft:

### <a name="stop-and-unschedule-all-runbook-that-uses-azurerm-modules"></a>Alle Runbooks, die AzureRM-Module verwenden, anhalten und entfernen

Um sicherzustellen, dass Sie keine vorhandenen Runbooks ausführen, die `AzureRM`-Cmdlets verwenden, sollten Sie alle Runbooks, die `AzureRM`-Module verwenden, anhalten und entfernen. Sie können im folgenden Beispiel sehen, welche Zeitpläne vorhanden sind und welche Zeitpläne entfernt werden müssen:

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

Es ist wichtig, jeden Zeitplan separat zu überprüfen, um sicherzustellen, dass Sie ihn in Zukunft bei Bedarf für Ihre Runbooks neu planen können.

### <a name="import-the-az-modules"></a>Importieren der Az-Module

Importieren Sie nur die Az-Module, die für Ihre Runbooks erforderlich sind. Importieren Sie nicht das Rollup -`Az`-Modul, da es alle zu importierenden `Az.*`-Module enthält. Diese Anleitung gilt für alle Module.

Das [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0)-Modul ist eine Abhängigkeit für die anderen `Az.*`-Module. Aus diesem Grund muss dieses Modul in Ihr Automation-Konto importiert werden, bevor Sie andere Module importieren.

Wählen Sie in Ihrem Automation-Konto unter **Freigegebene Ressourcen** die Option **Module** aus. Klicken Sie auf **Katalog durchsuchen**, um die Seite **Katalog durchsuchen** zu öffnen.  Geben Sie in der Suchleiste den Namen des Moduls ein (z.B. `Az.Accounts`). Klicken Sie auf der Seite PowerShell-Module auf **Importieren**, um das Modul in Ihr Automation-Konto zu importieren.

![Importieren von Modulen aus einem Automation-Konto](media/az-modules/import-module.png)

Dieser Importvorgang kann auch über den [PowerShell-Katalog](https://www.powershellgallery.com) durchgeführt werden, indem nach dem Modul gesucht wird. Sobald Sie das Modul gefunden haben, wählen Sie es aus, und klicken Sie auf der Registerkarte **Azure Automation** auf **In Azure Automation bereitstellen**.

![Importieren von Modulen direkt aus dem Katalog](media/az-modules/import-gallery.png)

## <a name="test-your-runbooks"></a>Testen Ihres Runbooks

Sobald die `Az`-Module in Ihr Automation-Konto importiert wurden, können Sie nun mit der Bearbeitung Ihrer Runbooks beginnen, um stattdessen das Az-Modul zu verwenden. Die Mehrheit der Cmdlets haben den gleichen Namen, außer `AzureRM` wurde in `Az` geändert. Eine Liste der Module, die diesem Prozess nicht verwenden, finden Sie unter [Liste der Ausnahmen](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

Eine Möglichkeit, Ihre Runbooks zu testen, bevor Sie Ihr Runbook für die Verwendung der neuen Cmdlets ändern, besteht darin, `Enable-AzureRMAlias -Scope Process` am Anfang eines Runbooks zu verwenden. Wenn Sie dies zu Ihrem Runbook hinzufügen, kann Ihr Runbook ohne Änderungen ausgeführt werden.

## <a name="after-migration-details"></a>Details zu den Vorgängen nach der Migration

Starten Sie nach Abschluss der Migration keine Runbooks mehr, die `AzureRM`-Module für das Konto verwenden. Es wird auch empfohlen, keine `AzureRM`-Module in diesem Konto mehr zu importieren oder zu aktualisieren. Ab diesem Zeitpunkt sollten Sie dieses Konto als auf `Az` migriert betrachten und nur mit `Az`-Modulen arbeiten. Wenn ein neues Automation-Konto erstellt wird, werden die vorhandenen `AzureRM`-Module weiterhin installiert und die Tutorial-Runbooks werden weiterhin mit `AzureRM`-Cmdlets erstellt. Diese Runbooks sollten nicht ausgeführt werden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Verwendung von Az-Modulen finden Sie unter [Erste Schritte mit Az-Modulen](/powershell/azure/get-started-azureps?view=azps-1.1.0).
