---
title: Installieren lizenzierter Komponenten für Azure SSIS Integration Runtime | Microsoft-Dokumentation
description: Erfahren Sie, wie ein ISV kostenpflichtige oder lizenzierte benutzerdefinierte Komponenten für die Azure SSIS Integration Runtime entwickeln und installieren kann.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/13/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 80d4fff03422beacccd3aff3cdd8cb1047d5f5af
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61344645"
---
# <a name="install-paid-or-licensed-custom-components-for-the-azure-ssis-integration-runtime"></a>Installieren kostenpflichtiger oder lizenzierter benutzerdefinierter Komponenten für Azure SSIS Integration Runtime

In diesem Artikel wird erläutert, wie ein ISV kostenpflichtige oder lizenzierte benutzerdefinierte Komponenten für SSIS-Pakete (SQL Server Integration Services) entwickeln und installieren kann, die in Azure in der Azure SSIS Integration Runtime ausgeführt werden.

## <a name="the-problem"></a>Die Problematik

Die Eigenart der Azure SSIS Integration Runtime bringt verschiedene Herausforderungen mit sich, aufgrund derer die typischen Lizenzierungsmethoden für die lokale Installation benutzerdefinierter Komponenten unzureichend sind. Daher erfordert die Azure SSIS IR einen anderen Ansatz.

-   Die Knoten der Azure SSIS IR sind flüchtig und können jederzeit zugeordnet oder freigegeben werden. Beispielsweise können Sie Knoten starten oder beenden, um die Kosten im Griff zu behalten, oder durch verschiedene Knotengrößen zentral hoch- und herunterskalieren. Dadurch ist es nicht mehr möglich, eine Komponentenlizenz eines Drittanbieters über computerspezifische Informationen wie MAC-Adresse oder CPU-ID an einen bestimmten Knoten zu binden.

-   Sie können die Azure SSIS IR auch horizontal herunter- oder hochskalieren, sodass die Anzahl der Knoten jederzeit verkleinert oder vergrößert werden kann.

## <a name="the-solution"></a>Die Lösung

Aufgrund der im vorherigen Abschnitt beschriebenen Einschränkungen herkömmlicher Lizenzierungsmethoden bietet die Azure SSIS IR eine neue Lösung. Bei dieser Lösung werden Windows-Umgebungsvariablen und SSIS-Systemvariablen für die Lizenzbindung und -validierung von Drittanbieterkomponenten verwendet. ISVs können diese Variablen nutzen, um eindeutige und persistente Informationen für eine Azure SSIS IR zu erhalten, wie z.B. Cluster-ID und Anzahl der Clusterknoten. Anhand dieser Informationen können ISVs dann die Lizenz für ihre Komponente an eine Azure SSIS IR *als ein Cluster* binden. Diese Bindung verwendet eine ID, die sich nicht ändert, wenn Kunden die Azure SSIS IR in jeglicher Form starten oder beenden, horizontal oder vertikal hoch- oder herunterskalieren oder neu konfigurieren.

Die folgende Abbildung zeigt die typischen Installations-, Aktivierungs- und Lizenzbindungsabläufe für Komponenten von Drittanbietern, die diese neuen Variablen verwenden:

![Installation lizenzierter Komponenten](media/how-to-configure-azure-ssis-ir-licensed-components/licensed-component-installation.png)

## <a name="instructions"></a>Anleitung
1. ISVs können ihre lizenzierten Komponenten in verschiedenen SKUs oder Stufen anbieten (z.B. Einzelknoten, bis zu 5 Knoten, bis zu 10 Knoten usw.). Der ISV stellt beim Kauf eines Produkts den entsprechenden Product Key zur Verfügung. Der ISV kann auch einen Azure Storage-Blobcontainer bereitstellen, der ein ISV-Setupskript und zugehörige Dateien enthält. Kunden können diese Dateien in ihren eigenen Speichercontainer kopieren und mit ihrem eigenen Product Key modifizieren (z.B. durch Ausführen von `IsvSetup.exe -pid xxxx-xxxx-xxxx`). Kunden können dann die Azure SSIS IR mit dem SAS-URI ihres Containers als Parameter bereitstellen oder neu konfigurieren. Weitere Informationen finden Sie unter [Custom setup for the Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md) (Benutzerdefinierte Einrichtung der Azure SSIS Integration Runtime).

2. Wenn die Azure SSIS IR bereitgestellt oder neu konfiguriert wurde, wird das ISV-Setup auf jedem Knoten ausgeführt, um die Windows-Umgebungsvariablen `SSIS_CLUSTERID` und `SSIS_CLUSTERNODECOUNT` abzufragen. Anschließend sendet die Azure SSIS IR ihre Cluster-ID und den Product Key für das lizenzierte Produkt an den Aktivierungsserver des ISV, um einen Aktivierungsschlüssel zu generieren.

3. Nach Empfang des Aktivierungsschlüssels kann das ISV-Setup den Schlüssel lokal auf jedem Knoten (z.B. in der Registrierung) speichern.

4. Wenn Kunden ein Paket ausführen, das die lizenzierte Komponente des ISV auf einem Knoten der Azure SSIS IR verwendet, liest das Paket den lokal gespeicherten Aktivierungsschlüssel und validiert ihn im Abgleich mit der Cluster-ID des Knotens. Das Paket kann dem ISV-Aktivierungsserver optional auch die Anzahl der Clusterknoten melden.

    Hier ein Codebeispiel, das den Aktivierungsschlüssel validiert und die Anzahl der Clusterknoten meldet:

    ```csharp
    public override DTSExecResult Validate(Connections, VariableDispenser, IDTSComponentEvents componentEvents, IDTSLogging log) 
                                                                                                                               
    {                                                                                                                             
                                                                                                                               
    Variables vars = null;                                                                                                        
                                                                                                                               
    variableDispenser.LockForRead("System::ClusterID");                                                                           
                                                                                                                               
    variableDispenser.LockForRead("System::ClusterNodeCount");                                                                    
                                                                                                                               
    variableDispenser.GetVariables(ref vars);                                                                                     
                                                                                                                               
    // Validate Activation Key with ClusterID                                                                                     
                                                                                                                               
    // Report on ClusterNodeCount                                                                                                 
                                                                                                                               
    vars.Unlock();                                                                                                                
                                                                                                                               
    return base.Validate(connections, variableDispenser, componentEvents, log);                                                   
                                                                                                                               
    }
    ```

## <a name="isv-partners"></a>ISV-Partner

Eine Liste von ISV-Partnern, die ihre Komponenten und Erweiterungen für die Azure SSIS IR angepasst haben, finden Sie am Ende des Blogbeitrags [Enterprise Edition, Benutzerdefiniertes Setup und Erweiterbarkeit durch Drittanbieter für SSIS in ADF](https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/).

## <a name="next-steps"></a>Nächste Schritte

-   [Benutzerdefiniertes Setup von Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Enterprise Edition von Azure SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)
