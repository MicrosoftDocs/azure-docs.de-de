---
title: Anwendungslebenszyklus in Service Fabric | Microsoft Docs
description: Beschreibt das Entwickeln, Bereitstellen, Testen, Aktualisieren, Verwalten und Entfernen von Service Fabric-Anwendungen.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 08837cca-5aa7-40da-b087-2b657224a097
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/19/2018
ms.author: atsenthi
ms.openlocfilehash: 53cab3591ea11721e36b48438f35df016e2a9f3a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60621485"
---
# <a name="service-fabric-application-lifecycle"></a>Lebenszyklus der Service Fabric-Anwendung
Ähnlich wie auf anderen Plattformen durchläuft eine Anwendung auf Azure Service Fabric normalerweise die folgenden Phasen: Entwurf, Entwicklung, Test, Bereitstellung, Update, Wartung und Deinstallation. Service Fabric bietet erstklassige Unterstützung für den gesamten Anwendungslebenszyklus von Cloudanwendungen: von der Entwicklung über die Bereitstellung, die tägliche Verwaltung und die Wartung bis zur endgültigen Außerbetriebnahme. Das Dienstmodell ermöglicht die unabhängige Beteiligung verschiedener Rollen am Anwendungslebenszyklus. Dieser Artikel bietet eine Übersicht über die APIs und wie sie von den verschiedenen Rollen während der Phasen des Service Fabric-Anwendungslebenszyklus verwendet werden.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-sfctl.md)]

## <a name="service-model-roles"></a>Rollen des Dienstmodells
Es gibt folgende Rollen im Dienstmodell:

* **Dienstentwickler**: Entwickelt modulare und generische Dienste, die für neue Zwecke in mehreren Anwendungen des gleichen Typs oder in anderen Anwendungstypen wiederverwendet werden können. Ein Warteschlangendienst kann beispielsweise zum Erstellen einer Ticketing-Anwendung (Helpdesk) oder einer E-Commerce-Anwendung (Warenkorb) verwendet werden.
* **Anwendungsentwickler**: Erstellt Anwendungen durch das Integrieren einer Sammlung von Diensten, um bestimmte Anforderungen zu erfüllen oder bestimmte Szenarien abzudecken. Beispielsweise können „Zustandsloser Front-End-Dienst für JSON“, „Zustandsbehafteter Dienst für Auktion“ und „Zustandsbehafteter Dienst für Warteschlange“ in einer E-Commerce-Website integriert werden, um eine Auktionslösung zu erstellen.
* **Anwendungsadministrator**: Trifft Entscheidungen zur Anwendungskonfiguration (Ausfüllen der Konfigurationsvorlagenparameter), zur Bereitstellung (Zuordnung zu den verfügbaren Ressourcen) und zur Servicequalität. Ein Anwendungsadministrator legt z. B. das Sprachgebietsschema (Englisch für die USA oder Japanisch für Japan) der Anwendung fest. Für eine andere bereitgestellte Anwendung können andere Einstellungen festgelegt werden.
* **Operator**: Stellt Anwendungen entsprechend der Anwendungskonfiguration und den vom Anwendungsadministrator angegebenen Anforderungen bereit. Ein Operator stellt z. B. die Anwendung bereit und stellt sicher, dass sie in Azure ausgeführt wird. Operatoren überwachen den Anwendungszustand und die Leistungsinformationen und verwalten bei Bedarf die physische Infrastruktur.

## <a name="develop"></a>Entwickeln
1. Ein *Dienstentwickler* entwickelt unter Verwendung der Programmiermodell [Reliable Actors](service-fabric-reliable-actors-introduction.md) oder [Reliable Services](service-fabric-reliable-services-introduction.md) verschiedene Diensttypen.
2. Ein *Dienstentwickler* beschreibt die entwickelten Diensttypen deklarativ in einer Dienstmanifestdatei, die aus einem oder mehreren Code-, Konfigurations- und Datenpaketen besteht.
3. Ein *Anwendungsentwickler* erstellt dann mit verschiedenen Diensttypen eine Anwendung.
4. Der *Anwendungsentwickler* beschreibt den Anwendungstyp deklarativ in einem Anwendungsmanifest, indem er auf die Dienstmanifeste der zugehörigen Dienste verweist und verschiedene Konfigurations- und Bereitstellungseinstellungen der zugehörigen Dienste entsprechend überschreibt und parametrisiert.

Beispiele finden Sie unter [Erste Schritte mit Reliable Actors](service-fabric-reliable-actors-get-started.md) und [Erste Schritte mit Reliable Services](service-fabric-reliable-services-quick-start.md).

## <a name="deploy"></a>Bereitstellen
1. Ein *Anwendungsadministrator* passt den Anwendungstyp in einer bestimmten Anwendung so an, dass sie in einem Service Fabric-Cluster bereitgestellt wird, indem er die entsprechenden Parameter des **ApplicationType** -Elements im Anwendungsmanifest angibt.
2. Ein *Operator* lädt das Anwendungspaket mit der [**CopyApplicationPackage**-Methode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) oder dem [**Copy-ServiceFabricApplicationPackage**-Cmdlet](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) in den Clusterimagespeicher hoch. Das Anwendungspaket enthält das Anwendungsmanifest und die Auflistung der Dienstpakete. Service Fabric stellt Anwendungen aus dem Anwendungspaket bereit, das im ImageStore gespeichert ist, bei dem es sich um einen Azure-Blobspeicher oder den Service Fabric-Systemdienst handeln kann.
3. Der *Operator* stellt dann mit der [**ProvisionApplicationAsync**-Methode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), dem [**Register-ServiceFabricApplicationType**-Cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/register-servicefabricapplicationtype) oder dem [REST-Vorgang **Anwendung bereitstellen**](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application) den Anwendungstyp aus dem hochgeladenen Anwendungspaket im Zielcluster bereit.
4. Nach der Bereitstellung der Anwendung startet ein *Operator* die Anwendung mit den vom *Anwendungsadministrator* angegebenen Parametern unter Verwendung der [**CreateApplicationAsync**-Methode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), des [**New-ServiceFabricApplication**-Cmdlets](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricapplication) oder des [REST-Vorgangs **Anwendung erstellen**](https://docs.microsoft.com/rest/api/servicefabric/create-an-application).
5. Nachdem die Anwendung bereitgestellt wurde, verwendet ein *Operator* die [**CreateServiceAsync**-Methode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient), das [**New-ServiceFabricService**-Cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice) oder den [REST-Vorgang **Dienst erstellen**](https://docs.microsoft.com/rest/api/servicefabric/create-a-service), um auf Grundlage verfügbarer Diensttypen neue Dienstinstanzen für die Anwendung zu erstellen.
6. Die Anwendung wird nun im Service Fabric-Cluster ausgeführt.

Beispiele finden Sie unter [Deploy an application](service-fabric-deploy-remove-applications.md) (in englischer Sprache).

## <a name="test"></a>Test
1. Nach der Bereitstellung im lokalen Entwicklungscluster oder in einem Testcluster führt ein *Dienstentwickler* das integrierte Failovertestszenario mithilfe der Klassen [**FailoverTestScenarioParameters**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenarioparameters) und [**FailoverTestScenario**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenario) oder des [**Invoke-ServiceFabricFailoverTestScenario**-Cmdlets](/powershell/module/servicefabric/invoke-servicefabricfailovertestscenario?view=azureservicefabricps) aus. Im Failovertestszenario wird ein bestimmter Dienst über wichtige Übergänge und Failover ausgeführt, um sicherzustellen, dass er weiterhin verfügbar und aktiv ist.
2. Der *Dienstentwickler* führt dann das integrierte Chaostestszenario mithilfe der Klassen [**ChaosTestScenarioParameters**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenarioparameters) und [**ChaosTestScenario**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenario) oder des [**Invoke-ServiceFabricChaosTestScenario-** Cmdlets](/powershell/module/servicefabric/invoke-servicefabricchaostestscenario?view=azureservicefabricps) aus. Im Chaostestszenario werden nach dem Zufallsprinzip mehrere Knoten-, Codepaket- und Replikatfehler im Cluster ausgelöst.
3. Der *Dienstentwickler* [testet die Dienst-zu-Dienst-Kommunikation](service-fabric-testability-scenarios-service-communication.md) durch das Erstellen von Testszenarien, die primäre Replikate im Cluster verschieben.

Weitere Informationen finden Sie unter [Testability – Übersicht](service-fabric-testability-overview.md) .

## <a name="upgrade"></a>Upgrade
1. Ein *Dienstentwickler* aktualisiert die zugehörigen Dienste der instanziierten Anwendung und/oder behebt Fehler und stellt eine neue Version des Dienstmanifests bereit.
2. Ein *Anwendungsentwickler* überschreibt und parametrisiert die Konfigurations- und Bereitstellungseinstellungen der zugehörigen Dienste und stellt eine neue Version des Anwendungsmanifests bereit. Der Anwendungsentwickler bindet dann die neuen Versionen der Dienstmanifeste in der Anwendung ein und stellt eine neue Version des Anwendungstyps in einem aktualisierten Anwendungspaket bereit.
3. Ein *Anwendungsadministrator* bindet durch Aktualisieren der entsprechenden Parameter die neue Version des Anwendungstyps in der Zielanwendung ein.
4. Ein *Operator* lädt das aktualisierte Anwendungspaket mit der [**CopyApplicationPackage**-Methode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) oder dem [**Copy-ServiceFabricApplicationPackage**-Cmdlet](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) in den Clusterimagespeicher hoch. Das Anwendungspaket enthält das Anwendungsmanifest und die Auflistung der Dienstpakete.
5. Ein *Operator* stellt die neue Version der Anwendung mit der [**ProvisionApplicationAsync**-Methode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), dem [**Register-ServiceFabricApplicationType**-Cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/register-servicefabricapplicationtype) oder dem [REST-Vorgang **Anwendung bereitstellen**](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application) im Zielcluster bereit.
6. Ein *Operator* führt mit der [**UpgradeApplicationAsync**-Methode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), dem [**Start-ServiceFabricApplicationUpgrade**-Cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade) oder dem [REST-Vorgang **Upgrade einer Anwendung ausführen**](https://docs.microsoft.com/rest/api/servicefabric/upgrade-an-application) ein Upgrade der Zielanwendung auf die neue Version aus.
7. Ein *Operator* überprüft den Status des Upgrades mit der [**GetApplicationUpgradeProgressAsync**-Methode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), dem [**Get-ServiceFabricApplicationUpgrade**-Cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricapplicationupgrade) oder dem [REST-Vorgang **Fortschritt des Anwendungsupgrades abrufen**](https://docs.microsoft.com/rest/api/servicefabric/get-the-progress-of-an-application-upgrade1).
8. Bei Bedarf ändert der *Operator* die Parameter des aktuellen Anwendungsupgrades mit der [**UpdateApplicationUpgradeAsync**-Methode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), dem [**Update-ServiceFabricApplicationUpgrade**-Cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade) oder dem [REST-Vorgang **Anwendungsupgrade aktualisieren**](https://docs.microsoft.com/rest/api/servicefabric/update-an-application-upgrade) und wendet sie erneut an.
9. Bei Bedarf führt der *Operator* mit der [**RollbackApplicationUpgradeAsync**-Methode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), dem [**Start-ServiceFabricApplicationRollback**-Cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback) oder dem [REST-Vorgang **Anwendungsupgrade zurücksetzen**](https://docs.microsoft.com/rest/api/servicefabric/rollback-an-application-upgrade) ein Rollback des aktuellen Anwendungsupgrades aus.
10. Service Fabric aktualisiert die im Cluster ausgeführte Zielanwendung ohne Verlust der Verfügbarkeit der einzelnen zugehörigen Dienste.

Beispiele finden Sie im [Tutorial für Anwendungsupgrades](service-fabric-application-upgrade-tutorial.md) .

## <a name="maintain"></a>Verwalten
1. Bei Upgrades und Patches des Betriebssystems wird Service Fabric mit der Azure-Infrastruktur verbunden, um die Verfügbarkeit aller im Cluster ausgeführten Anwendungen zu gewährleisten.
2. Bei Upgrades und Patches der Service Fabric-Plattform aktualisiert sich Service Fabric selbst ohne Verlust der Verfügbarkeit aller im Cluster ausgeführten Anwendungen.
3. Ein *Anwendungsadministrator* genehmigt das Hinzufügen oder Entfernen von Knoten in einem Cluster nach der Analyse der verlaufsbezogenen Kapazität der Verwendungsdaten und des voraussichtlichen zukünftigen Bedarfs.
4. Ein *Operator* fügt die vom *Anwendungsadministrator* angegebenen Knoten hinzu oder entfernt sie.
5. Wenn neue Knoten im Cluster hinzugefügt oder vorhandene Knoten aus dem Cluster entfernt werden, nimmt Service Fabric einen Lastenausgleich der in allen Knoten des Clusters ausgeführten Anwendungen vor, um eine optimale Leistung zu erzielen.

## <a name="remove"></a>Spalten
1. Ein *Operator* kann mithilfe der [**DeleteServiceAsync**-Methode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient), des [**Remove-ServiceFabricService**-Cmdlets](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice) oder des [REST-Vorgangs **Dienst löschen**](https://docs.microsoft.com/rest/api/servicefabric/delete-a-service) eine bestimmte Instanz eines ausgeführten Diensts im Cluster löschen, ohne die gesamte Anwendung zu entfernen.  
2. Ein *Operator* kann mithilfe der [**DeleteApplicationAsync**-Methode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), des [**Remove-ServiceFabricApplication**-Cmdlets](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricapplication) oder des [REST-Vorgangs **Anwendung löschen**](https://docs.microsoft.com/rest/api/servicefabric/delete-an-application) auch eine Anwendungsinstanz und all ihre Dienste löschen.
3. Sobald die Anwendung und die Dienste beendet wurden, kann der *Operator* mithilfe der [**UnprovisionApplicationAsync**-Methode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), des [**Unregister-ServiceFabricApplicationType**-Cmdlets](https://docs.microsoft.com/powershell/module/servicefabric/unregister-servicefabricapplicationtype) oder des [REST-Vorgangs **Bereitstellung einer Anwendung aufheben**](https://docs.microsoft.com/rest/api/servicefabric/unprovision-an-application) die Bereitstellung des Anwendungstyps aufheben. Beim Aufheben der Bereitstellung des Anwendungstyps wird das Anwendungspaket nicht aus dem ImageStore entfernt. Sie müssen das Anwendungspaket manuell entfernen.
4. Ein *Operator* entfernt das Anwendungspaket mit der [**RemoveApplicationPackage**-Methode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) oder dem [**Remove-ServiceFabricApplicationPackage**-Cmdlet](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) aus dem Imagespeicher.

Beispiele finden Sie unter [Deploy an application](service-fabric-deploy-remove-applications.md) (in englischer Sprache).

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum Entwickeln, Testen und Verwalten von Service Fabric-Anwendungen und Service Fabric-Diensten:

* [Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Reliable Services](service-fabric-reliable-services-introduction.md)
* [Bereitstellen von Anwendungen](service-fabric-deploy-remove-applications.md)
* [Anwendungsupgrade](service-fabric-application-upgrade.md)
* [Testability – Übersicht](service-fabric-testability-overview.md)
