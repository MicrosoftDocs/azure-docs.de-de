---
title: Verwenden von Azure DevTest Labs durch Entwickler | Microsoft-Dokumentation
description: Erfahren Sie mehr über die Verwendung von Azure DevTest Labs für Entwicklerszenarien.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 22e070e5-3d1a-49fe-9d4c-5e07cb0b7fe2
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 5a293946e4672e7737f912f42511ad0907ba4a81
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61293950"
---
# <a name="use-azure-devtest-labs-for-developers"></a>Verwenden von Azure DevTest Labs durch Entwickler
Azure DevTest Labs kann zum Implementieren vieler wichtiger Szenarien verwendet werden. Eines der primären Szenarien stellt aber die Verwendung von DevTest Labs zum Hosten von Entwicklungscomputern für Entwickler dar. In diesem Szenario bietet DevTest Labs folgende Vorteile:

- Entwickler können ihre Entwicklungscomputer bei Bedarf schnell bereitstellen.
- Entwickler können ihre Entwicklungscomputer bei Bedarf einfach anpassen.
- Administratoren können die Kosten steuern, indem sie Folgendes sicherstellen:
  - Entwickler können nicht mehr virtuelle Computer erhalten, als sie für die Entwicklung benötigen.
  - Virtuelle Computer werden heruntergefahren, wenn sie nicht in Gebrauch sind. 

![Verwenden von DevTest Labs zu Schulungszwecken](./media/devtest-lab-developer-lab/devtest-lab-developer-lab.png)

In diesem Artikel erfahren Sie mehr über verschiedene Azure DevTest Labs-Features, die zum Erfüllen der Entwickleranforderungen verwendet werden können. Zudem enthält er ausführliche Schritte, die Sie ausführen können, um ein Lab einzurichten.

## <a name="implementing-developer-environments-with-azure-devtest-labs"></a>Implementieren von Entwicklerumgebungen mit Azure DevTest Labs
1. **Erstellen des Labs** 
   
    Labs sind der Ausgangspunkt in Azure DevTest Labs. Nach dem Erstellen eines Labs können Sie z.B. Benutzer (Entwickler) hinzufügen, Richtlinien für die Kostenkontrolle festlegen, VM-Images definieren, die für die schnelle Erstellung verwendet werden können, und vieles mehr.  
   
    Klicken Sie auf die Links in der folgenden Tabelle, um weitere Informationen zu erhalten:
   
   | Aufgabe | Lerninhalt |
   | --- | --- |
   | [Erstellen eines Labs in Azure DevTest Labs](devtest-lab-create-lab.md) |Erfahren Sie, wie im Azure-Portal ein Lab in Azure DevTest Labs erstellt wird. |
2. **Erstellen von virtuellen Computern innerhalb weniger Minuten mithilfe von vordefinierten Marketplace-Images und benutzerdefinierten Images** 
   
    Aus einer Vielzahl von Images in Azure Marketplace können Sie vorgefertigte Images auswählen und im Lab zur Verfügung stellen. Wenn die vorgefertigten Images Ihren Anforderungen nicht entsprechen, können Sie ein benutzerdefiniertes Image erstellen. Dazu erstellen Sie mithilfe eines vorgefertigten Images aus Azure Marketplace einen virtuellen Lab-Computer, installieren die erforderliche Software und speichern den virtuellen Computer als benutzerdefiniertes Image im Lab.

    Wenn Sie benutzerdefinierte Images verwenden, sollten Sie eine Imagefactory für das Erstellen und Verteilen der Images in Erwägung ziehen. Eine Imagefactory ist eine Konfiguration-als-Code-Lösung, die regelmäßig und automatisch die konfigurierten Images erstellt und verteilt. Dies spart den Zeitaufwand für das manuelle Konfigurieren des Systems ein, nachdem ein virtueller Computer mit dem Basisbetriebssystem erstellt wurde.
  
    Klicken Sie auf die Links in der folgenden Tabelle, um weitere Informationen zu erhalten:
   
   | Aufgabe | Lerninhalt |
   | --- | --- |
   | [Konfigurieren von Azure Marketplace-Images](devtest-lab-configure-marketplace-images.md) |Erfahren Sie, wie Sie Azure Marketplace-Images der Positivliste hinzufügen. Dabei werden nur die Images zur Auswahl zur Verfügung gestellt, die Sie für Entwickler verwenden möchten.|
   | [Erstellen eines benutzerdefinierten Images](devtest-lab-create-template.md) |Erstellen Sie ein benutzerdefiniertes Image, indem Sie die für Entwickler erforderliche Software vorinstallieren, sodass Lab-Benutzer mithilfe des benutzerdefinierten Images schnell einen virtuellen Computer erstellen können.|
   | [Erfahren Sie mehr über Imagefactorys.](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/) |Sehen Sie sich ein Video an, in dem beschrieben wird, wie Sie eine Imagefactory einrichten und verwenden.|

3. **Erstellen von wiederverwendbaren Vorlagen für Entwicklercomputer** 
   
    Eine Formel in Azure DevTest Labs ist eine Liste standardmäßiger Eigenschaftswerte, die zum Erstellen eines virtuellen Computers verwendet werden. Sie können im Lab eine Formel erstellen, indem Sie ein Image, eine Größe für den virtuellen Computer (eine Kombination aus CPU und Arbeitsspeicher) und ein virtuelles Netzwerk auswählen. Jeder Entwickler kann die Formel im Lab sehen und zum Erstellen eines virtuellen Computers verwenden. 
   
    Klicken Sie auf die Links in der folgenden Tabelle, um weitere Informationen zu erhalten:
   
   | Aufgabe | Lerninhalt |
   | --- | --- |
   | [Verwalten von DevTest Labs-Formeln zum Erstellen virtueller Computer](devtest-lab-manage-formulas.md) |Erfahren Sie, wie Sie eine Formel erstellen, indem Sie ein Image, eine Größe für den virtuellen Computer (Kombination aus CPU und Arbeitsspeicher) und ein virtuelles Netzwerk auswählen.|

4. **Erstellen von Artefakten für flexible VM-Anpassungen**

   Artefakte werden zum Bereitstellen und Konfigurieren Ihrer Anwendung nach der Bereitstellung eines virtuellen Computers verwendet. Bei Artefakten kann es sich um Folgendes handeln:

   - Tools, die Sie auf dem virtuellen Computer installieren möchten, z. B. Agents, Fiddler oder Visual Studio.
   - Aktionen, die auf dem virtuellen Computer ausgeführt werden sollen, z. B. das Klonen eines Repositorys.
   - Anwendungen, die Sie testen möchten.

   Viele Artefakte sind bereits vorkonfiguriert verfügbar. Sie können eigene benutzerdefinierte Artefakte erstellen, wenn Sie eine zusätzliche Anpassung an spezifische Anforderungen durchführen möchten.

   Klicken Sie auf die Links in der folgenden Tabelle, um weitere Informationen zu erhalten:
   
   | Aufgabe | Lerninhalt |
   | --- | --- |
   | [Erstellen benutzerdefinierter Artefakte für Ihre DevTest Labs-VM](devtest-lab-artifact-author.md) |Erstellen Sie eigene benutzerdefinierte Artefakte für die virtuellen Computer im Lab.|
   | [Hinzufügen eines Git-Repositorys zum Speichern von benutzerdefinierten Artefakten und Azure Resource Manager-Vorlagen für die Verwendung in Azure DevTest Labs](devtest-lab-add-artifact-repo.md) |Erfahren Sie, wie Sie Ihre benutzerdefinierten Artefakte in einem eigenen privaten Git-Repository speichern.|

5. **Kostenkontrolle**
   
    Mit Azure DevTest Labs können Sie eine Richtlinie im Lab festlegen, um die Anzahl von virtuellen Computern zu begrenzen, die von einem Entwickler im Lab erstellt werden können. 
   
    Wenn Ihr Entwicklerteam einen Arbeitszeitplan aufgestellt hat und Sie alle virtuellen Computer zu einer bestimmten Tageszeit herunterfahren und am folgenden Tag neu starten möchten, können Sie im Lab Richtlinien zum automatischen Herunterfahren und Starten festlegen. 
   
    Nach Abschluss der App-Entwicklung können Sie alle virtuellen Computer durch Ausführen eines einzigen PowerShell-Skripts gleichzeitig löschen. 
   
    Klicken Sie auf die Links in der folgenden Tabelle, um weitere Informationen zu erhalten:
   
   | Aufgabe | Lerninhalt |
   | --- | --- |
   | [Definieren von Labrichtlinien.](devtest-lab-set-lab-policy.md) |Kontrollieren Sie Kosten durch Festlegen von Richtlinien im Lab. |
   | [Löschen aller virtuellen Computer mithilfe eines PowerShell-Skripts](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Löschen Sie alle Labs in einem Vorgang, wenn die Entwicklung abgeschlossen ist.|

1. **Hinzufügen eines virtuellen Netzwerks zu einem virtuellen Computer** 
   
    DevTest Labs erstellt jedes Mal ein neues virtuelles Netzwerk (VNET), wenn ein Lab erstellt wird. Wenn Sie ein eigenes VNET konfiguriert haben – beispielsweise mithilfe von ExpressRoute oder als Site-to-Site-VPN –, können Sie dieses virtuelle Netzwerk den VNET-Einstellungen Ihres Labs hinzufügen, damit es beim Erstellen virtueller Computer verfügbar ist.

    Darüber hinaus ist ein Artefakt zum Azure Active Directory-Domänenbeitritt verfügbar, mit dem ein virtueller Computer bei seiner Erstellung einer Domäne beitreten kann. 
   
    Klicken Sie auf die Links in der folgenden Tabelle, um weitere Informationen zu erhalten:
   
   | Aufgabe | Lerninhalt |
   | --- | --- |
   | [Konfigurieren eines virtuellen Netzwerks in Azure DevTest Labs](devtest-lab-configure-vnet.md) |Erfahren Sie, wie Sie mit dem Azure-Portal ein virtuelles Netzwerk in Azure DevTest Labs konfigurieren.|

6. **Freigeben des Labs für alle Entwickler**
   
    Labs sind direkt über einen Link zugänglich, den Sie für Ihre Entwickler freigeben. Die Benutzer benötigen nicht einmal ein Azure-Konto, sofern sie über ein [Microsoft-Konto](devtest-lab-faq.md#what-is-a-microsoft-account) verfügen. Entwickler können die von anderen Entwicklern erstellten virtuellen Computer nicht sehen.  
   
    Klicken Sie auf die Links in der folgenden Tabelle, um weitere Informationen zu erhalten:
   
   | Aufgabe | Lerninhalt |
   | --- | --- |
   | [Hinzufügen eines Entwicklers zu einem Lab in Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Verwenden Sie das Azure-Portal, um Ihrem Lab Entwickler hinzuzufügen.|
   | [Hinzufügen von Entwicklern zum Lab mithilfe eines PowerShell-Skripts](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Automatisieren Sie mithilfe von PowerShell das Hinzufügen von Entwicklern zu Ihrem Lab. |
   | [Abrufen eines Links zum Lab](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Erfahren Sie, wie Entwickler über einen Link direkt auf ein Lab zugreifen können.|

7. **Automatisieren der Lab-Erstellung für mehrere Teams** 
   
    Sie können die Lab-Erstellung, einschließlich der benutzerdefinierten Einstellungen, automatisieren, indem Sie eine Resource Manager-Vorlage erstellen und diese mehrfach zum Erstellen identischer Labs verwenden. 
   
    Klicken Sie auf die Links in der folgenden Tabelle, um weitere Informationen zu erhalten:
   
   | Aufgabe | Lerninhalt |
   | --- | --- |
   | [Erstellen eines Labs mithilfe einer Resource Manager-Vorlage](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Erstellen Sie Labs in Azure DevTest Labs mithilfe von Resource Manager-Vorlagen. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

