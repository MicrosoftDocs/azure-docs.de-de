---
title: Reliable Collection-Objektserialisierung in Azure Service Fabric | Microsoft-Dokumentation
description: Reliable Collection-Objektserialisierung in Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: masnider,rajak
ms.assetid: 9d35374c-2d75-4856-b776-e59284641956
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/8/2017
ms.author: aljo
ms.openlocfilehash: ee19be45915b3ff1253ec721f4334fead19647b8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60723593"
---
# <a name="reliable-collection-object-serialization-in-azure-service-fabric"></a>Reliable Collection-Objektserialisierung in Azure Service Fabric
Reliable Collection repliziert und speichert seine Elemente, um sicherzustellen, dass sie über Computer- und Stromausfälle hinaus erhalten bleiben.
Sowohl zum Replizieren als auch zum Speichern von Elementen müssen diese von Reliable Collections serialisiert werden.

Reliable Collection ruft das geeignete Serialisierungsprogramm für einen bestimmten Typ von Reliable State Manager ab.
Reliable State Manager enthält integrierte Serialisierungsprogramme und lässt die Registrierung benutzerdefinierter Serialisierungsprogramme für einen bestimmten Typ zu.

## <a name="built-in-serializers"></a>Integrierte Serialisierungsprogramme

Reliable State Manager enthält integrierte Serialisierungsprogramme für einige allgemeine Typen, sodass diese standardmäßig effizient serialisiert werden können. Für andere Typen weicht Reliable State Manager auf die Verwendung von [DataContractSerializer](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer(v=vs.110).aspx) aus.
Integrierte Serialisierungsprogramme sind effizienter, da sich ihre Typen nicht ändern und daher keine Informationen über den Typ (z.B. den Typnamen) hinzugefügt werden müssen.

Reliable State Manager verfügt über integrierte Serialisierungsprogramme für die folgenden Typen: 
- Guid
- bool
- byte
- sbyte
- Byte[]
- char
- Zeichenfolge
- decimal
- double
- float
- int
- uint
- lang
- ulong
- short
- ushort

## <a name="custom-serialization"></a>Benutzerdefinierte Serialisierung

Benutzerdefinierte Serialisierungsprogramme werden häufig zum Steigern der Leistung oder zum Verschlüsseln der Daten über das Netzwerk und auf dem Datenträger verwendet. Neben anderen Gründen sind benutzerdefinierte Serialisierungsprogramme häufig effizienter als generische Serialisierungsprogramme, da sie Informationen über den Typ nicht serialisieren müssen. 

[IReliableStateManager.TryAddStateSerializer<T>](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.ireliablestatemanager.tryaddstateserializer) wird zum Registrieren eines benutzerdefinierten Serialisierungsprogramms für den angegebenen Typ T verwendet. Diese Registrierung sollte bei der Erstellung der StatefulServiceBase erfolgen, um sicherzustellen, dass vor Beginn der Wiederherstellung alle Reliable Collections über Zugriff auf die relevanten Serialisierungsprogramme zum Lesen ihrer persistenten Daten verfügen.

```csharp
public StatefulBackendService(StatefulServiceContext context)
  : base(context)
  {
    if (!this.StateManager.TryAddStateSerializer(new OrderKeySerializer()))
    {
      throw new InvalidOperationException("Failed to set OrderKey custom serializer");
    }
  }
```

> [!NOTE]
> Benutzerdefinierte Serialisierungsprogramme erhalten Vorrang vor integrierten Serialisierungsprogrammen. Wenn z.B. ein benutzerdefiniertes Serialisierungsprogramm für „int“ registriert ist, wird es anstelle des integrierten Serialisierungsprogramm für „int“ zum Serialisieren von ganzen Zahlen verwendet.

### <a name="how-to-implement-a-custom-serializer"></a>Implementieren eines benutzerdefinierten Serialisierungsprogramms

Ein benutzerdefiniertes Serialisierungsprogramm muss die [IStateSerializer<T>](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.istateserializer-1)-Schnittstelle implementieren.

> [!NOTE]
> IStateSerializer<T> umfasst Schreib- und Leseüberladungen, die einen zusätzlichen T-Wert verwenden, den Basiswert. Diese API ist für die differenzielle Serialisierung bestimmt. Aktuell ist das Feature der differenziellen Serialisierung nicht verfügbar. Daher werden diese beiden Überladungen nicht aufgerufen, solange die differenzielle Serialisierung nicht verfügbar gemacht und aktiviert wurde.

Es folgt ein Beispiel für einen benutzerdefinierten Typ „OrderKey“, der vier Eigenschaften enthält.

```csharp
public class OrderKey : IComparable<OrderKey>, IEquatable<OrderKey>
{
    public byte Warehouse { get; set; }

    public short District { get; set; }

    public int Customer { get; set; }

    public long Order { get; set; }

    #region Object Overrides for GetHashCode, CompareTo and Equals
    #endregion
}
```

Im Folgenden finden Sie ein Beispiel für das Implementieren von IStateSerializer\<OrderKey>.
Beachten Sie, dass die Lese-/Schreibüberladungen, die den BaseValue erfassen, zur Aufwärtskompatibilität die entsprechende Überladung aufrufen.

```csharp
public class OrderKeySerializer : IStateSerializer<OrderKey>
{
  OrderKey IStateSerializer<OrderKey>.Read(BinaryReader reader)
  {
      var value = new OrderKey();
      value.Warehouse = reader.ReadByte();
      value.District = reader.ReadInt16();
      value.Customer = reader.ReadInt32();
      value.Order = reader.ReadInt64();

      return value;
  }

  void IStateSerializer<OrderKey>.Write(OrderKey value, BinaryWriter writer)
  {
      writer.Write(value.Warehouse);
      writer.Write(value.District);
      writer.Write(value.Customer);
      writer.Write(value.Order);
  }
  
  // Read overload for differential de-serialization
  OrderKey IStateSerializer<OrderKey>.Read(OrderKey baseValue, BinaryReader reader)
  {
      return ((IStateSerializer<OrderKey>)this).Read(reader);
  }

  // Write overload for differential serialization
  void IStateSerializer<OrderKey>.Write(OrderKey baseValue, OrderKey newValue, BinaryWriter writer)
  {
      ((IStateSerializer<OrderKey>)this).Write(newValue, writer);
  }
}
```

## <a name="upgradability"></a>Upgradefähigkeit
Bei einem [parallelen Anwendungsupgrade](service-fabric-application-upgrade.md) wird das Upgrade auf eine Teilmenge von Knoten angewendet. Dabei werden die einzelnen Upgradedomänen nacheinander abgearbeitet. Während dieses Vorgangs weisen einige Upgradedomänen die neuere Version Ihrer Anwendung auf und andere Upgradedomänen die ältere Version Ihrer Anwendung. Während der Einführung muss die neue Version der Anwendung die alte Version Ihrer Daten sowie die alte Version der Anwendung die neue Version der Daten lesen können. Wenn das Datenformat nicht aufwärts- und abwärtskompatibel ist, kann das Upgrade nicht erfolgreich durchgeführt werden, oder es gehen möglicherweise sogar Daten verloren oder werden beschädigt.

Wenn Sie das integrierte Serialisierungsprogramm verwenden, brauchen Sie sich nicht um die Kompatibilität zu kümmern.
Wenn Sie jedoch ein benutzerdefiniertes Serialisierungsprogramm oder „DataContractSerializer“ verwenden, müssen die Daten uneingeschränkt abwärts- und aufwärtskompatibel sein.
Das heißt, jede Version des Serialisierungsprogramms muss in der Lage sein, jede beliebige Version des Typs zu serialisieren und zu deserialisieren.

Data Contract-Benutzer sollten die klar definierten Versionsregeln zum Hinzufügen, Entfernen und Ändern von Feldern befolgen. Data Contract unterstützt außerdem die Behandlung von unbekannten Feldern, die Hookfunktion für den Serialisierungs- und Deserialisierungsvorgang und den Umgang mit der Klassenvererbung. Weitere Informationen finden Sie unter [Verwenden von Datenverträgen](https://msdn.microsoft.com/library/ms733127.aspx).

Benutzer von benutzerdefinierten Serialisierungsprogrammen sollten die Richtlinien des verwendeten Serialisierungsprogramms befolgen, um Abwärts- und Aufwärtskompatibilität zu gewährleisten.
Eine übliche Methode, um sicherzustellen, dass sämtliche Versionen unterstützt werden, besteht darin, Größeninformationen am Anfang hinzuzufügen und nur optionale Eigenschaften hinzuzufügen.
Auf diese Weise kann jede Version so viel lesen wie möglich und den Rest des Streams überspringen.

## <a name="next-steps"></a>Nächste Schritte
  * [Serialisierung und Upgrade](service-fabric-application-upgrade-data-serialization.md)
  * [Entwicklerreferenz für zuverlässige Auflistungen](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  * [Ihre Anwendung mit Visual Studio upgraden](service-fabric-application-upgrade-tutorial.md) beschreibt das Upgraden von Anwendungen mit Visual Studio.
  * [Upgrade Ihrer Anwendung mithilfe von PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) werden Sie schrittweise durch das Upgrade der Anwendung mithilfe von PowerShell geführt.
  * Steuern Sie die Upgrades von Anwendungen mithilfe von [Upgradeparametern](service-fabric-application-upgrade-parameters.md).
  * Erfahren Sie, wie Sie erweiterte Funktionen beim Upgrade Ihrer Anwendung nutzen, indem Sie sich mit den [weiterführenden Themen](service-fabric-application-upgrade-advanced.md)beschäftigen.
  * Informationen zum Beheben gängiger Probleme bei Anwendungsupgrades finden Sie in den Anweisungen unter [Problembehandlung bei Anwendungsupgrades](service-fabric-application-upgrade-troubleshooting.md).
