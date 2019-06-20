---
title: Grundlegendes zu freigegebenen IP-Adressen in Azure DevTest Labs | Microsoft-Dokumentation
description: Erfahren Sie, wie Azure DevTest Labs freigegebene IP-Adressen verwendet, um die Anzahl von öffentlichen IP-Adressen zu minimieren, die für den Zugriff auf die virtuellen Computer Ihres Labs erforderlich sind.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/12/2019
ms.author: spelluru
ms.openlocfilehash: f7c9feedddab1aea031cb3a8879e868aae04df00
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65236863"
---
# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>Grundlegendes zu freigegebenen IP-Adressen in Azure DevTest Labs

Azure DevTest Labs ermöglicht virtuellen Lab-Computern die Verwendung der gleichen öffentlichen IP-Adresse, um die Anzahl von öffentlichen IP-Adressen zu minimieren, die für den Zugriff auf die einzelnen virtuellen Lab-Computer erforderlich sind.  Dieser Artikel beschreibt die Funktionsweise von freigegebenen IP-Adressen und die zugehörigen Konfigurationsoptionen.

## <a name="shared-ip-setting"></a>Einstellung für freigegebene IP-Adressen

Wenn Sie ein Lab erstellen, wird dieses in einem Subnetz eines virtuellen Netzwerks erstellt.  Beim Erstellen dieses Subnetzes ist die Einstellung **Freigegeben öffentliche IP-Adresse aktivieren** standardmäßig auf *Ja* festgelegt.  Diese Konfiguration erstellt eine einzige öffentliche IP-Adresse für das gesamte Subnetz.  Weitere Informationen zum Konfigurieren virtueller Netzwerke und Subnetze finden Sie unter [Konfigurieren eines virtuellen Netzwerks in Azure DevTest Labs](devtest-lab-configure-vnet.md).

![Neues Labsubnetz](media/devtest-lab-shared-ip/lab-subnet.png)

Für bereits vorhandene Labs können Sie diese Option durch Auswählen von **Konfiguration und Richtlinien > Virtuelle Netzwerke** aktivieren. Wählen Sie in der Liste ein virtuelles Netzwerk und anschließend für ein bestimmtes Subnetz die Option **FREIGEGEBENE ÖFFENTLICHE IP-ADRESSE AKTIVIEREN** aus. Falls Sie keine öffentliche IP-Adresse für mehrere virtuelle Lab-Computer freigeben möchten, können Sie diese Option auch in jedem beliebigen Lab deaktivieren.

Alle in diesem Lab erstellten virtuellen Computer verwenden standardmäßig eine freigegebene IP-Adresse.  Beim Erstellen des virtuellen Computers können Sie diese Einstellung auf der Seite **Erweiterte Einstellungen** unter **IP-Adresskonfiguration** anzeigen.

![Neuer virtueller Computer](media/devtest-lab-shared-ip/new-vm.png)

- **Freigegeben**: Alle als **Freigegeben** erstellten virtuellen Computer werden in einer einzelnen Ressourcengruppe (RG) platziert. Dieser RG wird eine einzelne IP-Adresse zugewiesen, und diese IP-Adresse wird von allen virtuellen Computern in der RG verwendet.
- **Öffentlich:** Jeder virtuelle Computer, den Sie erstellen, besitzt eine eigene IP-Adresse und wird in seiner eigenen Ressourcengruppe erstellt.
- **Privat:** Jeder virtuelle Computer, den Sie erstellen, verwendet eine private IP-Adresse. Mit diesem virtuellen Computer kann keine direkte Remotedesktopverbindung über das Internet hergestellt werden.

Wenn dem Subnetz ein virtueller Computer mit aktivierter IP-Adressfreigabe hinzugefügt wird, fügt DevTest Labs den virtuellen Computer automatisch einem Load Balancer hinzu und weist eine TCP-Portnummer für die öffentliche IP-Adresse zu, um eine Weiterleitung an den RDP-Port des virtuellen Computers einzurichten.  

## <a name="using-the-shared-ip"></a>Verwenden der freigegebenen IP-Adresse

- **Linux-Benutzer:** Verwenden Sie die IP-Adresse oder den vollqualifizierten Domänennamen, gefolgt von einem Doppelpunkt und dem Port, um eine SSH-Verbindung mit dem virtuellen Computer herzustellen. In der folgenden Abbildung lautet die RDP-Adresse für die Verbindung mit dem virtuellen Computer `mydevtestlab597975021002.eastus.cloudapp.azure.com:50661`.

  ![Beispiel für einen virtuellen Computer](media/devtest-lab-shared-ip/vm-info.png)

- **Windows-Benutzer:** Verwenden Sie im Azure-Portal die Schaltfläche **Verbinden**, um eine vorkonfigurierte RDP-Datei herunterzuladen und auf den virtuellen Computer zuzugreifen.

## <a name="next-steps"></a>Nächste Schritte

* [Definieren von Labrichtlinien in Azure DevTest Labs](devtest-lab-set-lab-policy.md)
* [Konfigurieren eines virtuellen Netzwerks in Azure DevTest Labs](devtest-lab-configure-vnet.md)





