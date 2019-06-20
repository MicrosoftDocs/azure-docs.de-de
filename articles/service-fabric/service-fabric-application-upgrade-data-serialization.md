---
title: 'Anwendungsupgrade: Datenserialisierung | Microsoft Docs'
description: Bewährte Methoden für die Datenserialisierung und ihre Auswirkung auf parallele Anwendungsupgrades.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: a5f36366-a2ab-4ae3-bb08-bc2f9533bc5a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 55cbd869e7434469ebddd7af493c91bfedafc594
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60614442"
---
# <a name="how-data-serialization-affects-an-application-upgrade"></a>Auswirkungen der Datenserialisierung auf Anwendungsupgrades
Bei einem [parallelen Anwendungsupgrade](service-fabric-application-upgrade.md) wird das Upgrade auf eine Teilmenge von Knoten angewendet. Dabei werden die einzelnen Upgradedomänen nacheinander abgearbeitet. Während dieses Vorgangs weisen einige Upgradedomänen die neuere Version Ihrer Anwendung auf und andere Upgradedomänen die ältere Version Ihrer Anwendung. Während der Einführung muss die neue Version der Anwendung die alte Version Ihrer Daten sowie die alte Version der Anwendung die neue Version der Daten lesen können. Wenn das Datenformat nicht aufwärts- und abwärtskompatibel ist, kann das Upgrade nicht erfolgreich durchgeführt werden, oder es gehen möglicherweise sogar Daten verloren oder werden beschädigt. In diesem Artikel wird die Zusammensetzung des Datenformats erörtert und es werden bewährte Methoden zum Sicherstellen der Aufwärts- und Abwärtskompatibilität der Daten vorgestellt.

## <a name="what-makes-up-your-data-format"></a>Woraus besteht das Datenformat?
In Azure Service Fabric stammen die Daten, die persistent gespeichert und repliziert werden, aus den C#-Klassen. Bei Anwendungen mit [Reliable Collections](service-fabric-reliable-services-reliable-collections.md)(zuverlässige Auflistungen) sind diese Daten die Objekte in den zuverlässigen Wörterbüchern und Warteschlangen. Bei Anwendungen mit [Reliable Actors](service-fabric-reliable-actors-introduction.md)(zuverlässige Akteure) ist dies der unterstützende Zustand für den Akteur. Diese C#-Klassen müssen serialisierbar sein, um persistent gespeichert und repliziert zu werden. Aus diesem Grund wird das Datenformat durch die Felder und Eigenschaften definiert, die serialisiert werden, sowie dadurch, wie sie serialisiert werden. In `IReliableDictionary<int, MyClass>` sind die Daten beispielsweise ein serialisiertes `int` und eine serialisierte `MyClass`.

### <a name="code-changes-that-result-in-a-data-format-change"></a>Codeänderungen, die zu einer Änderung des Datenformats führen
Da das Datenformat durch C#-Klassen bestimmt wird, ziehen Änderungen an den Klassen möglicherweise eine Änderung des Datenformats nach sich. Es muss sorgfältig sichergestellt werden, dass ein paralleles Upgrade die Änderung des Datenformats verarbeiten kann. Beispiele, die Änderungen des Datenformats zur Folge haben können:

* Hinzufügen oder Entfernen von Feldern oder Eigenschaften
* Umbenennen von Feldern oder Eigenschaften
* Ändern der Feld- oder Eigenschaftentypen
* Ändern des Klassennamens oder Namespaces

### <a name="data-contract-as-the-default-serializer"></a>„Datenvertrag“ als Standardserialisierungsprogramm
Das Serialisierungsprogramm liest im Allgemeinen die Daten und deserialisiert sie in die aktuelle Version, selbst wenn die Daten eine ältere oder *neuere* Version aufweisen. Das Standardserialisierungsprogramm ist der [Datenvertragsserialisierer](https://msdn.microsoft.com/library/ms733127.aspx), der klar definierte Versionsregeln aufweist. Mit Reliable Collections kann das Serialisierungsprogramm überschrieben werden, mit Reliable Actors derzeit jedoch nicht. Der Datenvertragsserialisierer spielt eine wichtige Rolle bei der Aktivierung von parallelen Upgrades. Der Datenvertragsserialisierer ist das für Service Fabric-Anwendungen empfohlene Serialisierungsprogramm.

## <a name="how-the-data-format-affects-a-rolling-upgrade"></a>Auswirkungen des Datenformats auf parallele Upgrades
Während eines parallelen Upgrades gibt es zwei Hauptszenarios, bei denen das Serialisierungsprogramm auf eine ältere oder *neuere* Version Ihrer Daten treffen kann:

1. Nachdem ein Knoten aktualisiert wurde und wieder gestartet wird, lädt das neue Serialisierungsprogramm die Daten, die persistent auf Festplatte gespeichert waren, mit der neuen Version.
2. Während des parallelen Upgrades enthält der Cluster eine Mischung aus der alten und neuen Version des Codes. Da Replikate in verschiedenen Upgradedomänen platziert werden können und Replikate sich untereinander Daten senden, kann die neue und/oder alte Version Ihres Serialisierungsprogramms auf die neue und/oder alte Version Ihrer Daten treffen.

> [!NOTE]
> Die "neue Version" und die "alte Version" beziehen sich hier auf die Version des ausgeführten Codes. Das "neue Serialisierungsprogramm" bezieht sich auf den Serialisierercode, der in der neuen Version der Anwendung ausgeführt wird. Die "neuen Daten" beziehen sich auf die serialisierte C#-Klasse aus der neuen Version der Anwendung.
> 
> 

Die beiden Versionen des Codes und Datenformats müssen aufwärts- und abwärtskompatibel sein. Wenn sie nicht kompatibel sind, kann das parallele Upgrade fehlschlagen, oder es gehen möglicherweise Daten verloren. Das parallele Upgrade kann fehlschlagen, weil der Code oder das Serialisierungsprogramm Ausnahmen oder Fehler ausgibt, wenn er oder es auf die andere Version stößt. Daten können verloren gehen, wenn beispielsweise eine neue Eigenschaft hinzugefügt wurde, das alte Serialisierungsprogramm sie jedoch während der Deserialisierung verwirft.

Datenverträge sind die empfohlene Lösung zum Sicherstellen der Kompatibilität Ihrer Daten. Sie verfügen über klar definierte Versionsregeln zum Hinzufügen, Entfernen und Ändern von Feldern. Sie unterstützen außerdem die Behandlung von unbekannten Feldern, die Hookfunktion für den Serialisierungs- und Deserialisierungsvorgang und den Umgang mit der Klassenvererbung. Weitere Informationen finden Sie unter [Verwenden von Datenverträgen](https://msdn.microsoft.com/library/ms733127.aspx).

## <a name="next-steps"></a>Nächste Schritte
[Ihre Anwendung mit Visual Studio upgraden](service-fabric-application-upgrade-tutorial.md) beschreibt das Upgraden von Anwendungen mit Visual Studio.

[Upgrade Ihrer Anwendung mithilfe von PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) werden Sie schrittweise durch das Upgrade der Anwendung mithilfe von PowerShell geführt.

Steuern Sie die Upgrades von Anwendungen mithilfe von [Upgradeparametern](service-fabric-application-upgrade-parameters.md).

Erfahren Sie, wie Sie erweiterte Funktionen beim Upgrade Ihrer Anwendung nutzen, indem Sie sich mit den [weiterführenden Themen](service-fabric-application-upgrade-advanced.md)beschäftigen.

Informationen zum Beheben gängiger Probleme bei Anwendungsupgrades finden Sie in den Anweisungen unter [Problembehandlung bei Anwendungsupgrades](service-fabric-application-upgrade-troubleshooting.md).

