---
title: Runbook und Modulkataloge für Azure Automation
description: Ihnen stehen Runbooks und Module von Microsoft und der Community zur Verfügung, die Sie in Ihrer Azure Automation-Umgebung installieren und verwenden können.  Dieser Artikel beschreibt, wie Sie auf diese Ressourcen zugreifen und Ihre Runbooks im Katalog bereitstellen können.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 20aafc117ad8b6bd625894180fdfe79bd86192bd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60737414"
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Runbook und Modulkataloge für Azure Automation

Statt eigene Runbooks und Module in Azure Automation zu erstellen, können Sie Szenarien nutzen, die bereits von Microsoft und der Community entwickelt wurden.

Sie können PowerShell-Runbooks und -[Module](#modules-in-powershell-gallery) aus dem PowerShell-Katalog sowie [Python-Runbooks](#python-runbooks) aus dem Script Center-Katalog abrufen. Sie können auch etwas zur Community beitragen, indem Sie von Ihnen entwickelte Szenarios zur Verfügung stellen. Weitere Informationen finden Sie unter „Hinzufügen eines Runbooks zum Runbook-Katalog“.

## <a name="runbooks-in-powershell-gallery"></a>Runbooks im PowerShell-Katalog

Der [PowerShell-Katalog](https://www.powershellgallery.com/packages) bietet zahlreiche Runbooks von Microsoft und der Community, die Sie in Azure Automation importieren können. Um eins zu verwenden, können Sie ein Runbook aus dem Katalog herunterladen, oder Sie können Runbooks direkt aus dem Katalog oder aus Ihrem Automatisierungskonto im Azure-Portal importieren.

Das direkte Importieren aus dem PowerShell-Katalog ist nur über das Azure-Portal möglich. Diese Funktion kann nicht mithilfe von PowerShell ausgeführt werden.

> [!NOTE]
> Überprüfen Sie unbedingt den Inhalt der aus dem PowerShell-Katalog heruntergeladenen Runbooks, und seien Sie äußerst vorsichtig, wenn Sie sie in einer Produktionsumgebung installieren und ausführen.

### <a name="to-import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>So importieren Sie mit dem Azure-Portal ein PowerShell-Runbook aus dem Runbook-Katalog

1. Öffnen Sie im Azure-Portal Ihr Automation-Konto.
2. Klicken Sie unter **Prozessautomatisierung** auf **Runbook-Katalog**.
3. Wählen Sie **Quelle: PowerShell-Katalog** aus.
4. Suchen Sie das gewünschte Katalogelement, und wählen Sie es zum Anzeigen der Details aus. Auf den linken Seite können Sie weitere Suchparameter für Herausgeber und Typ eingeben.

   ![Katalog durchsuchen](media/automation-runbook-gallery/browse-gallery.png)

5. Klicken Sie auf **Quellprojekt anzeigen** , um den Artikel im [TechNet Script Center](https://gallery.technet.microsoft.com/)anzuzeigen.
6. Klicken Sie zum Importieren eines Artikels auf den Artikel, um seine Details anzuzeigen, und klicken Sie anschließend auf die Schaltfläche **Importieren** .

   ![Schaltfläche „Importieren“](media/automation-runbook-gallery/gallery-item-detail.png)

7. Ändern Sie optional den Namen des Runbooks, und klicken Sie zum Importieren des Runbooks auf **OK** .
8. Das Runbook wird auf der Registerkarte **Runbooks** des Automation-Kontos angezeigt.

### <a name="adding-a-powershell-runbook-to-the-gallery"></a>Hinzufügen eines PowerShell-Runbooks zum Katalog

Microsoft empfiehlt, Runbooks aus dem PowerShell-Katalog hinzuzufügen, die für andere Kunden nützlich sein könnten. Der PowerShell-Katalog akzeptiert PowerShell-Module und PowerShell-Skripts. Sie können ein Runbook hinzufügen, indem Sie [es in den PowerShell-Katalog hochladen](/powershell/gallery/how-to/publishing-packages/publishing-a-package).

> [!NOTE]
> Grafische Runbooks werden im PowerShell-Katalog nicht unterstützt.

## <a name="modules-in-powershell-gallery"></a>Module im PowerShell-Katalog

PowerShell-Module enthalten Cmdlets, die Sie in Ihren Runbooks verwenden können. Vorhandene Module, die Sie in Azure Automation installieren können, sind im [PowerShell-Katalog](https://www.powershellgallery.com) verfügbar. Sie starten diesen Katalog über das Azure-Portal und installieren die Module direkt in Azure Automation. Sie können sie auch herunterladen und manuell installieren.  

### <a name="to-import-a-module-from-the-automation-module-gallery-with-the-azure-portal"></a>So importieren Sie mit dem Azure-Portal ein Modul aus dem Automation-Modulkatalog

1. Öffnen Sie im Azure-Portal Ihr Automation-Konto.
2. Wählen Sie **Module** unter **Freigegebene Ressourcen** aus, um die Liste der Module zu öffnen.
3. Klicken Sie oben auf der Seite auf **Katalog durchsuchen**.

   ![Modulkatalog](media/automation-runbook-gallery/modules-blade.png)

4. Auf der Seite **Katalog durchsuchen** können Sie nach den folgenden Feldern suchen:

   * Modulname
   * Tags
   * Autor
   * Cmdlet-/DSC-Ressourcenname

5. Suchen Sie das gewünschte Modul, und wählen Sie es aus, um seine Details anzuzeigen.  

   Wenn Sie einen Drilldown in einem bestimmten Modul ausführen, können Sie weitere Informationen anzeigen. Diese Informationen umfassen einen Link zurück zum PowerShell-Katalog, alle erforderlichen Abhängigkeiten und alle Cmdlets oder DSC-Ressourcen, die das Modul enthält.

   ![PowerShell-Moduldetails](media/automation-runbook-gallery/gallery-item-details-blade.png)

6. Um das Modul direkt in Azure Automation zu installieren, klicken Sie auf die Schaltfläche **Importieren** .
7. Wenn Sie auf die Schaltfläche **Importieren** klicken, wird der Name des Moduls angezeigt, das Sie importieren möchten. Wenn alle Abhängigkeiten installiert sind, ist die Schaltfläche **OK** aktiv. Falls Abhängigkeiten fehlen, müssen diese Abhängigkeiten importiert werden, bevor dieses Modul importiert werden kann.
8. Klicken Sie auf der Seite **Importieren** auf **OK**, um das Modul zu importieren. Wenn Azure Automation ein Modul in Ihr Konto importiert, werden Metadaten zum Modul und den Cmdlets extrahiert. Dieser Vorgang kann einige Minuten dauern, da jede Aktivität extrahiert werden muss.
9. Sie erhalten jeweils eine Benachrichtigung, wenn das Modul bereitgestellt wird und wenn der Vorgang abgeschlossen ist.
10. Nachdem das Modul importiert wurde, können Sie die verfügbaren Aktivitäten anzeigen. Sie können ihre Ressourcen in Ihren Runbooks und der Desired State Configuration verwenden.

> [!NOTE]
> Module, die nur PowerShell Core unterstützen, werden in Azure Automation nicht unterstützt und können nicht in das Azure-Portal importiert werden oder direkt über den PowerShell-Katalog bereitgestellt werden.

## <a name="python-runbooks"></a>Python-Runbooks

Python-Runbooks finden Sie im [Script Center-Katalog](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=). Sie haben die Möglichkeit, Python-Runbooks dem Script Center-Katalog hinzuzufügen, indem Sie auf **Upload a contribution** (Beitrag hochladen) klicken. Wenn Sie das tun, achten Sie darauf, dass Sie beim Hochladen Ihres Beitrags das Tag **Python** hinzufügen.

> [!NOTE]
> Um Inhalte in das [Script Center](https://gallery.technet.microsoft.com/scriptcenter) hochladen zu können, sind mindestens 100 Punkte erforderlich. 

## <a name="requesting-a-runbook-or-module"></a>Anfordern eines Runbooks oder eines Moduls

Sie können Anforderungen an [User Voice](https://feedback.azure.com/forums/246290-azure-automation/)senden.  Wenn Sie Hilfe beim Schreiben eines Runbooks benötigen oder eine Frage zu PowerShell haben, stellen Sie eine entsprechende Anfrage in unserem [Forum](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Einstieg in die Arbeit mit Runbooks finden Sie unter [Verwalten eines Runbooks in Azure Automation](manage-runbooks.md).
* Informationen zu den Unterschieden zwischen PowerShell- und PowerShell-Workflow bei der Arbeit mit Runbooks finden Sie unter [Grundlagen des PowerShell-Workflows](automation-powershell-workflow.md)
