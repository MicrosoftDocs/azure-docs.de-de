---
title: Erstellen einer Image Factory in Azure DevTest Labs | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie in Azure DevTest Labs eine benutzerdefinierte Image Factory erstellen.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: cf1bb31614c04d6073bc40c510fc43b2f8e4e189
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60622628"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>Erstellen einer benutzerdefinierten Image Factory in Azure DevTest Labs
In diesem Artikel wird beschrieben, wie Sie eine benutzerdefinierte Image Factory einrichten, indem Sie Beispielskripts aus dem [Git-Repository](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory) verwenden.

## <a name="whats-an-image-factory"></a>Was ist eine Image Factory?
Eine Image Factory ist eine Konfiguration-als-Code-Lösung, die Images automatisch in regelmäßigen Abständen mit allen gewünschten Konfigurationen erstellt und verteilt. Die Images in der Image Factory sind stets auf dem neuesten Stand, und nach der Automatisierung des gesamten Prozesses fällt praktisch keinerlei Aufwand für die laufende Wartung mehr an. Da alle erforderlichen Konfigurationen bereits im Image enthalten sind, entfällt der Zeitaufwand für das manuelle Konfigurieren des Systems, nachdem ein virtueller Computer mit dem Basisbetriebssystem erstellt wurde.

Um einen Entwickler-Desktop in DevTest Labs schneller in einen betriebsbereiten Zustand zu versetzen, ist die Verwendung benutzerdefinierter Images entscheidend. Der Nachteil von benutzerdefinierten Images ist der, dass zusätzliche Daten im Lab verwaltet werden müssen. Testversionen von Produkten laufen beispielsweise im Laufe der Zeit ab oder neu veröffentlichte Sicherheitsupdates werden nicht angewendet, weshalb es nötig ist, das benutzerdefinierte Image in regelmäßigen Abständen zu aktualisieren. Mit einer Image Factory verfügen Sie über eine Definition des in die Quellcodeverwaltung eingecheckten Images und über einen automatisierten Prozess zum Erstellen benutzerdefinierter Images mithilfe dieser Definition.

Mit dieser Lösung wird das Erstellen virtueller Computer aus benutzerdefinierten Images beschleunigt, während zusätzliche Kosten für die laufende Wartung wegfallen. Mit dieser Lösung können Sie automatisch benutzerdefinierte Images erstellen, diese an andere DevTest Labs verteilen und die alten Images außer Betrieb nehmen. Im folgenden Video erfahren Sie mehr über die Image Factory und deren Implementierung mit DevTest Labs.  Alle Azure Powershell-Skripts stehen kostenlos hier zur Verfügung: [ https://aka.ms/dtlimagefactory ](https://aka.ms/dtlimagefactory).

<br/>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Custom-Image-Factory-with-Azure-DevTest-Labs/player]


## <a name="high-level-view-of-the-solution"></a>Allgemeiner Überblick über die Lösung
Mit dieser Lösung wird das Erstellen virtueller Computer aus benutzerdefinierten Images beschleunigt, während zusätzliche Kosten für die laufende Wartung entfallen. Mit dieser Lösung können Sie automatisch benutzerdefinierte Images erstellen und an andere DevTest Labs verteilen. Sie verwenden Azure DevOps (ehemals Visual Studio Team Services) als Orchestrierungs-Engine für die Automatisierung sämtlicher Vorgänge in den DevTest Labs.

![Allgemeiner Überblick über die Lösung](./media/create-image-factory/high-level-view-of-solution.png)

Es gibt eine [VSTS-Erweiterung für DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks), mit der Sie die folgenden Einzelschritte auszuführen können:

- Erstellen eines benutzerdefinierten Image
- Erstellen eines virtuellen Computers
- Löschen eines virtuellen Computers
- Erstellen der Umgebung
- Löschen der Umgebung
- Auffüllen der Umgebung

Mithilfe der DevTest Labs-Erweiterung können Sie auf einfache Weise mit der automatischen Erstellung benutzerdefinierter Images in DevTest Labs beginnen.

Für ein komplexeres Szenario gibt es eine alternative Implementierung mit einem PowerShell-Skript. Mit PowerShell können Sie eine auf DevTest Labs basierende Image Factory vollständig automatisieren, und diese kann in Ihrer Continuous Integration- und Continuous Delivery (CI/CD)-Toolkette verwendet werden. Diese alternative Lösung verwendet die folgenden Grundsätze:

- Allgemeine Aktualisierungen sollten keine Änderungen an der Image Factory erfordern. (Dies gilt z. B. für das Hinzufügen eines neuen Typs des benutzerdefinierten Images, die automatische Außerbetriebnahme alter Images, das Hinzufügen eines neuen „Endpunkt“-DevTest Labs zum Empfangen benutzerdefinierter Images usw.)
- Allgemeine Änderungen werden durch die Quellcodeverwaltung (Infrastruktur als Code) abgedeckt.
- DevTest Labs für den Empfang benutzerdefinierter Images fallen möglicherweise nicht unter dasselbe Azure-Abonnement (für Labs werden Abonnements verwendet).
- PowerShell-Skripts müssen wiederverwendbar sein, damit bei Bedarf zusätzliche Factorys eingerichtet werden können.

## <a name="next-steps"></a>Nächste Schritte
Fahren Sie mit dem nächsten Artikel in diesem Abschnitt fort: [Run an image factory from Azure DevOps (Ausführen einer Image Factory aus Azure DevOps)](image-factory-set-up-devops-lab.md)
