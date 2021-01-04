---
title: Aufskalieren einer Datenbank
description: Erfahren Sie, wie Sie "ShardMapManager" und die Clienbtbibliothek für elastische Datenbanken verwenden.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: e23b94c850c6ec326c2f4ad034e1fefc158087a5
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92793448"
---
# <a name="scale-out-databases-with-the-shard-map-manager"></a>Aufskalieren von Datenbanken mit dem Shardzuordnungs-Manager
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Verwenden Sie einen Shardzuordnungs-Manager, um Datenbanken in Azure SQL-Datenbank problemlos aufzuskalieren. Der Shardzuordnungs-Manager ist eine spezielle Datenbank, die globale Zuordnungsinformationen zu allen Shards (Datenbanken) in einer Shardgruppe verwaltet. Die Metadaten ermöglichen einer Anwendung die Verbindung mit der richtigen Datenbank basierend auf dem Wert des **Sharding-Schlüssels** . Darüber hinaus enthält jeder Shard in der Gruppe Zuordnungen, die die lokalen Sharddaten (als **Shardlets** bezeichnet) nachverfolgen.

![Shard-Zuordnungsverwaltung](./media/elastic-scale-shard-map-management/glossary.png)

Für die Shard-Zuordnungsverwaltung ist es unabdingbar, dass Sie die Grundlagen des Aufbaus dieser Zuordnungen verstehen. Dies erfolgt durch die ShardMapManager-Klasse ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)) in der [Clientbibliothek für elastische Datenbanken](elastic-database-client-library.md) für die Verwaltung von Shardzuordnungen.  

## <a name="shard-maps-and-shard-mappings"></a>Shard-Karten und Shard-Zuordnungen

Für jeden Shard müssen Sie den Typ der zu erstellenden Shardzuordnung auswählen. Die Auswahl hängt von der Architektur der Datenbank ab:

1. Ein Mandant pro Datenbank  
2. Mehrere Mandanten pro Datenbank (zwei Typen):
   1. Listenzuordnung
   2. Bereichszuordnung

Erstellen Sie für ein Modell mit einem einzelnen Mandanten eine **Listenzuordnungs** -Shardzuordnung. Beim Modell mit einem Mandanten wird eine Datenbank pro Mandant zugewiesen. Dies ist ein effektives Modell für SaaS-Entwickler, da es die Verwaltung vereinfacht.

![Listenzuordnung][1]

Beim Modell mit mehreren Mandanten werden einer einzelnen Datenbank mehrere Mandanten zugewiesen. Außerdem können Sie Mandantengruppen auf verschiedene Datenbanken verteilen. Verwenden Sie dieses Modell, wenn Sie erwarten, dass die einzelnen Mandanten geringe Datenanforderungen haben. In diesem Modell wird einer Datenbank mithilfe der **Bereichszuordnung** ein Bereich von Mandanten zugewiesen.

![Bereichszuordnung][2]

Wenn Sie einer einzelnen Datenbank mehrere Mandanten zuweisen möchten, kann das Datenbankmodell mit mehreren Mandanten auch unter Verwendung einer *Listenzuordnung* implementiert werden. Beispiel: DB1 wird zum Speichern von Informationen zu den Mandanten-IDs 1 und 5, DB2 zum Speichern von Daten für die Mandanten 7 und 10 verwendet.

![Einzeldatenbank mit mehreren Mandanten][3]

### <a name="supported-types-for-sharding-keys"></a>Unterstützte Typen für Shardingschlüssel

Die elastische Skalierung unterstützt die folgenden Typen als Shardingschlüssel:

| .NET | Java |
| --- | --- |
| integer |integer |
| long |long |
| guid |uuid |
| byte[]  |byte[] |
| datetime | timestamp |
| Zeitraum | duration|
| datetimeoffset |offsetdatetime |

### <a name="list-and-range-shard-maps"></a>Listen- und Bereichs-Shard-Zuordnungen

Shardzuordnungen können mit **Listen von einzelnen Shardingschlüsselwerten** oder mit **Bereichen von Shardingschlüsselwerten** erstellt werden.

### <a name="list-shard-maps"></a>Listenshardzuordnungen

**Shards** enthalten **Shardlets** , und die Zuordnung von Shardlets zu Shards wird über eine Shardzuordnung verwaltet. Eine **Listenshardzuordnung** ist eine Zuordnung zwischen den einzelnen Schlüsselwerten, mit der die Shardlets und die Datenbanken identifiziert werden, die als Shards dienen.  **Listenzuordnungen** sind explizit. Es können der gleichen Datenbank andere Schlüsselwerte zugeordnet werden. Beispielsweise kann Schlüsselwert 1 Datenbank A zugeordnet sein, während die Schlüsselwerte 3 und 6 Datenbank B zugeordnet sind.

| Schlüssel | Shard-Speicherort |
| --- | --- |
| 1 |Datenbank_A |
| 3 |Datenbank_B |
| 4 |Datenbank_C |
| 6 |Datenbank_B |
| ... |... |

### <a name="range-shard-maps"></a>Bereichs-Shard-Zuordnungen

In einer **Bereichsshardzuordnung** wird der Schlüsselbereich durch ein Paar **[niedriger Wert, hoher Wert)** beschrieben, wobei der *niedrige Wert* den minimalen Schlüssel in dem Bereich und der *hohe Wert* den ersten Wert darstellt, der höher ist als die Werte in diesem Bereich.

**[0, 100)** enthält z.B. alle ganzen Zahlen größer oder gleich 0 und kleiner 100. Mehrere Bereiche können auf dieselbe Datenbank verweisen, und nicht zusammenhängende Bereiche werden unterstützt (beispielsweise verweisen „[100,200)“ und „[400,600)“ im folgenden Beispiel auf Database_C).

| Schlüssel | Shard-Speicherort |
| --- | --- |
| [1,50) |Datenbank_A |
| [50,100) |Datenbank_B |
| [100,200) |Datenbank_C |
| [400,600) |Datenbank_C |
| ... |... |

Jede der obigen Tabellen ist ein grundlegendes Beispiel für ein **ShardMap** -Objekt. Jede Zeile ist ein vereinfachtes Beispiel für ein einzelnes **PointMapping** -Objekt (für die Listenshardzuordnung) oder für das **RangeMapping** -Objekt (für die Bereichsshardzuordnung).

## <a name="shard-map-manager"></a>Shard-Zuordnungs-Manager

In der Clientbibliothek stellt der Shardzuordnungs-Manager eine Sammlung von Shardzuordnungen dar. Die von einer **ShardMapManager** -Instanz verwalteten Daten werden an drei Orten gespeichert:

1. **Globale Shardzuordnung (GSM):** Sie geben eine Datenbank als Repository für alle Shardzuordnungen und -mappings an. Spezielle Tabellen und gespeicherte Prozeduren werden automatisch zur Verwaltung der Informationen erstellt. Hierfür wird üblicherweise eine kleine Datenbank verwendet, auf die einfach zugegriffen werden kann. Diese sollte jedoch nicht für andere Anforderungen der Anwendung verwendet werden. Die Tabellen befinden sich in einem speziellen Schema namens **__ShardManagement** .
2. **Lokale Shardzuordnung (LSM):** Jede Datenbank, die Sie als Shard angeben, wird dahin gehend geändert, dass sie mehrere kleine Tabellen und spezielle gespeicherte Prozeduren enthält, die Shardzuordnungsinformationen speziell für diesen Shard enthalten und verwalten. Diese Informationen sind für die Informationen in der GSM redundant, jedoch kann die Anwendung die zwischengespeicherten Shardzuordnungsinformationen überprüfen, ohne dass Last auf der GSM anfällt. Die Anwendung verwendet die LSM, um festzustellen, ob eine zwischengespeicherte Zuordnung noch gültig ist. Die Tabellen für die LSM zu jedem Shard sind auch im Schema **__ShardManagement** enthalten.
3. **Anwendungscache:** Jede Anwendungsinstanz, die Zugriff auf ein **ShardMapManager** -Objekt hat, verwaltet lokal einen speicherinternen Cache ihrer Zuordnungen. Sie speichert die Routinginformationen, die zuletzt abgerufen wurden.

## <a name="constructing-a-shardmapmanager"></a>Erstellen eines ShardMapManager

Ein **ShardMapManager** -Objekt wird mit einem Factorymuster ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory)) erstellt. Die **ShardMapManagerFactory.GetSqlShardMapManager** -Methode ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)) nimmt Anmeldeinformationen (einschließlich Server- und Datenbankname mit der GSM) in Form einer **ConnectionString** entgegen und gibt eine Instanz eines **ShardMapManager** zurück.  

**Hinweis:** Das **ShardMapManager** -Objekt sollte innerhalb des Initialisierungscodes für eine Anwendung nur einmal pro Anwendungsdomäne instanziiert werden. Das Erstellen zusätzlicher Instanzen von ShardMapManager in derselben Anwendungsdomäne führt zu mehr Arbeitsspeicher- und CPU-Auslastung der Anwendung. Ein **ShardMapManager** -Element kann eine beliebige Anzahl von Shardzuordnungen enthalten. Während eine einzelne Shardzuordnung für viele Anwendungen ausreichend sein kann, werden in einigen Fällen unterschiedliche Sätze von Datenbanken für ein anderes Schema oder einen eindeutigen Zweck verwendet. In diesen Fällen sind möglicherweise mehrfache Shardzuordnungen vorzuziehen.

In diesem Code versucht eine Anwendung, ein bestehendes **ShardMapManager** -Element mit der TryGetSqlShardMapManager-Methode ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.trygetsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)) zu öffnen. Wenn Objekte, die ein globales **ShardMapManager** -Element darstellen, in der Datenbank noch nicht vorhanden sind, erstellt die Clientbibliothek sie dort mithilfe der CreateSqlShardMapManager-Methode ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.createsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager)).

```Java
// Try to get a reference to the Shard Map Manager in the shardMapManager database.
// If it doesn't already exist, then create it.
ShardMapManager shardMapManager = null;
boolean shardMapManagerExists = ShardMapManagerFactory.tryGetSqlShardMapManager(shardMapManagerConnectionString,ShardMapManagerLoadPolicy.Lazy, refShardMapManager);
shardMapManager = refShardMapManager.argValue;

if (shardMapManagerExists) {
    ConsoleUtils.writeInfo("Shard Map %s already exists", shardMapManager);
}
else {
    // The Shard Map Manager does not exist, so create it
    shardMapManager = ShardMapManagerFactory.createSqlShardMapManager(shardMapManagerConnectionString);
    ConsoleUtils.writeInfo("Created Shard Map %s", shardMapManager);
}
```

```csharp
// Try to get a reference to the Shard Map Manager via the Shard Map Manager database.  
// If it doesn't already exist, then create it.
ShardMapManager shardMapManager;
bool shardMapManagerExists = ShardMapManagerFactory.TryGetSqlShardMapManager(
                                        connectionString,
                                        ShardMapManagerLoadPolicy.Lazy,
                                        out shardMapManager);

if (shardMapManagerExists)
{
    Console.WriteLine("Shard Map Manager already exists");
}
else
{
    // Create the Shard Map Manager.
    ShardMapManagerFactory.CreateSqlShardMapManager(connectionString);
    Console.WriteLine("Created SqlShardMapManager");

    shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(
            connectionString,
            ShardMapManagerLoadPolicy.Lazy);

// The connectionString contains server name, database name, and admin credentials for privileges on both the GSM and the shards themselves.
}
```

Für die .NET-Version können Sie mit PowerShell einen neuen Shardzuordnungs-Manager erstellen. Ein Beispiel ist [hier](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db)verfügbar.

## <a name="get-a-rangeshardmap-or-listshardmap"></a>Abrufen einer RangeShardMap oder ListShardMap

Nach dem Erstellen eines Shardzuordnungs-Managers können Sie RangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) oder ListShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) mithilfe der Methoden TryGetRangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetrangeshardmap), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap)), TryGetListShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetlistshardmap), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap)) oder GetShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getshardmap), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap)) abrufen.

```Java
// Creates a new Range Shard Map with the specified name, or gets the Range Shard Map if it already exists.
static <T> RangeShardMap<T> createOrGetRangeShardMap(ShardMapManager shardMapManager,
            String shardMapName,
            ShardKeyType keyType) {
    // Try to get a reference to the Shard Map.
    ReferenceObjectHelper<RangeShardMap<T>> refRangeShardMap = new ReferenceObjectHelper<>(null);
    boolean isGetSuccess = shardMapManager.tryGetRangeShardMap(shardMapName, keyType, refRangeShardMap);
    RangeShardMap<T> shardMap = refRangeShardMap.argValue;

    if (isGetSuccess && shardMap != null) {
        ConsoleUtils.writeInfo("Shard Map %1$s already exists", shardMap.getName());
    }
    else {
        // The Shard Map does not exist, so create it
        try {
            shardMap = shardMapManager.createRangeShardMap(shardMapName, keyType);
        }
        catch (Exception e) {
            e.printStackTrace();
        }
        ConsoleUtils.writeInfo("Created Shard Map %1$s", shardMap.getName());
    }

    return shardMap;
}
```

```csharp
// Creates a new Range Shard Map with the specified name, or gets the Range Shard Map if it already exists.
public static RangeShardMap<T> CreateOrGetRangeShardMap<T>(ShardMapManager shardMapManager, string shardMapName)
{
    // Try to get a reference to the Shard Map.
    RangeShardMap<T> shardMap;
    bool shardMapExists = shardMapManager.TryGetRangeShardMap(shardMapName, out shardMap);

    if (shardMapExists)
    {
        ConsoleUtils.WriteInfo("Shard Map {0} already exists", shardMap.Name);
    }
    else
    {
        // The Shard Map does not exist, so create it
        shardMap = shardMapManager.CreateRangeShardMap<T>(shardMapName);
        ConsoleUtils.WriteInfo("Created Shard Map {0}", shardMap.Name);
    }

    return shardMap;
}
```

### <a name="shard-map-administration-credentials"></a>Administratoranmeldeinformationen für Shard-Zuordnungen

Anwendungen, die Shard-Zuordnungen verwalten und bearbeiten, unterscheiden sich von jenen Anwendungen, die Shard-Zuordnungen zum Weiterleiten von Verbindungen verwenden.

Zum Verwalten von Shardzuordnungen (Hinzufügen oder Ändern von Shards, Shardzuordnungen usw.) müssen Sie das **ShardMapManager** -Element mithilfe von **Anmeldeinformationen instanziieren, die über Lese-/Schreibzugriff sowohl auf die GSM-Datenbank als auch auf die Datenbank verfügen, die als Shard fungiert** . Die Anmeldeinformationen müssen Schreibvorgänge in Bezug auf die Tabellen sowohl in der GSM als auch in der LSM ermöglichen, wenn Shard Map-Informationen eingegeben oder geändert werden, und sie müssen außerdem das Erstellen von LSM-Tabellen auf neuen Shards erlauben.  

Lesen Sie dazu [Anmeldeinformationen für den Zugriff auf die Clientbibliothek für elastische Datenbanken](elastic-scale-manage-credentials.md).

### <a name="only-metadata-affected"></a>Nur Metadaten betroffen

Methoden zum Auffüllen oder Ändern der **ShardMapManager** -Daten ändern nicht die Benutzerdaten, die in den Shards selbst gespeichert sind. Beispielsweise wirken sich Methoden wie etwa **CreateShard** , **DeleteShard** , **UpdateMapping** usw. nur auf die Shardzuordnungsmetadaten aus. nur auf die Shard-Zuordnungsmetadaten aus. Sie entfernen keine Benutzerdaten in den Shards, fügen diese hinzu oder ändern sie.  (Das **Split-Merge-Tool** , das Teil der Tools für elastische Datenbanken ist, nutzt diese APIs zusammen mit der Orchestrierung der tatsächlichen Datenverschiebung zwischen den Shards.) Siehe [Skalierung mit dem Split-Merge-Tool für elastische Datenbanken](elastic-scale-overview-split-and-merge.md).

## <a name="data-dependent-routing"></a>Datenabhängiges Routing

Der Shardzuordnungs-Manager wird in Anwendungen verwendet, die Verbindungen mit der Datenbank für App-spezifische Datenvorgänge erfordern. Diese Verbindungen müssen mit der richtigen Datenbank verknüpft sein. Dies wird als **datenabhängiges Routing** bezeichnet. Instanziieren Sie für diese Anwendungen ein Shard Map-Managerobjekt ab Werk mit den Anmeldeinformationen, die über schreibgeschützten Zugriff auf die GSM-Datenbank verfügen. Bei einzelnen Anforderungen für Verbindungen werden später die für die Verbindung mit der entsprechenden Sharddatenbank erforderlichen Anmeldeinformationen zur Verfügung gestellt.

Beachten Sie, dass bei diesen Anwendungen (unter Verwendung eines **ShardMapManager** , der mit schreibgeschützten Anmeldeinformationen geöffnet wird) keine Änderungen an den Karten oder Zuordnungen vorgenommen werden können. Erstellen Sie für diese Anforderungen administrativ-spezifische Anwendungen oder PowerShell-Skripts, die Anmeldeinformationen mit höheren Berechtigungen, wie bereits erwähnt, zur Verfügung stellen. Lesen Sie dazu [Anmeldeinformationen für den Zugriff auf die Clientbibliothek für elastische Datenbanken](elastic-scale-manage-credentials.md).

Weitere Informationen finden Sie unter [Datenabhängiges Routing](elastic-scale-data-dependent-routing.md).

## <a name="modifying-a-shard-map"></a>Ändern von Shard-Zuordnungen

Eine Shard Map kann auf unterschiedliche Weise geändert werden. Sämtliche der folgenden Methoden ändern die Metadaten, die die Shards und ihre Mappings beschreiben. Sie ändern aber die Daten innerhalb der Shards weder physisch noch erstellen oder löschen sie die Datenbanken.  Einige der Vorgänge zur unten beschriebenen Shard Map müssen möglicherweise mit den administrativen Aktionen abgestimmt werden, über welche die Daten physisch verschoben oder über welche die als Shards fungierenden Datenbanken hinzugefügt oder entfernt werden.

Diese Methoden arbeiten zusammen als Bausteine für die Änderung der Gesamtverteilung von Daten in der Sharded-Datenbankumgebung.  

* Verwenden Sie zum Hinzufügen oder Entfernen von Shards **CreateShard** ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.createshard), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard)) und **DeleteShard** ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.deleteshard), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.deleteshard)) aus der Shardzuordnungsklasse ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap)).
  
    Der Server und die Datenbank, die das Ziel-Shard repräsentieren, müssen bereits für diese auszuführenden Vorgänge vorhanden sein. Diese Methoden haben keine Auswirkungen auf die Datenbanken selbst, lediglich auf die Metadaten in der Shard Map.
* Verwenden Sie zum Erstellen oder Entfernen von Punkten oder Bereichen, die den Shards zugeordnet sind, **CreateRangeMapping** ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.createrangemapping), [.NET](/previous-versions/azure/dn841993(v=azure.100))) und **DeleteMapping** ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.deletemapping), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) aus der RangeShardMapping-Klasse ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) und **CreatePointMapping** ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap.createpointmapping), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) aus der ListShardMap-Klasse ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)).
  
    Demselben Shard können viele verschiedene Punkte oder Bereiche zugeordnet werden. Diese Methoden wirken sich nur auf Metadaten aus – sie haben keine Auswirkungen auf Daten, die bereits in Shards vorhanden sein können. Wenn Daten aus der Datenbank entfernt werden müssen, damit diese mit **DeleteMapping** -Vorgängen konsistent ist, führen Sie diese Vorgänge separat (aber im Kontext dieser Methoden) aus.  
* Verwenden Sie zum Unterteilen vorhandener Bereiche in zwei Teile oder zum Zusammenführen benachbarter Bereiche **SplitMapping** ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.splitmapping), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) bzw. **MergeMappings** ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.mergemappings), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)).  
  
    Beachten Sie, dass Aufteilungs- und Zusammenführungsvorgänge **nicht den Shard ändern, dem Schlüsselwerte zugeordnet sind** . Eine Aufteilung teilt einen vorhandenen Bereich in zwei Teile, wobei beide jedoch demselben Shard zugeordnet bleiben. Bei einer Zusammenführung werden zwei benachbarte Bereiche, die bereits demselben Shard zugeordnet sind, zu einem einzigen Bereich zusammengefügt.  Das Verschieben von Punkten oder Bereichen zwischen den Shards selbst muss mit **UpdateMapping** in Verbindung mit der eigentlichen Datenverschiebung koordiniert werden.  Sie können den **Split-Merge** -Dienst verwenden, der Teil der Tools für elastische Datenbanken ist, um Änderungen an Shard-Zuordnungen bei Datenverschiebungen zu koordinieren, sofern eine Verschiebung erforderlich ist.
* Um einzelne Punkte oder Bereiche erneut unterschiedlichen Shards zuzuordnen (oder sie zu verschieben), verwenden Sie **UpdateMapping** ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.updatemapping), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)).  
  
    Da Daten unter Umständen aus einem Shard in einen anderen verschoben werden, damit sie mit **UpdateMapping** -Vorgängen konsistent sind, müssen Sie diese Verschiebung separat (aber im Kontext dieser Methoden) ausführen.

* Verwenden Sie zum Online- und Offlineschalten von Zuordnungen **MarkMappingOffline** ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.markmappingoffline), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) bzw. **MarkMappingOnline** ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.markmappingonline), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)), um den Onlinestatus einer Zuordnung zu steuern.
  
    Bestimmte Vorgänge sind bei Shardzuordnungen nur dann zulässig, wenn sich die Zuordnung im Zustand „offline“ befindet, einschließlich **UpdateMapping** und **DeleteMapping** . Wenn eine Zuordnung offline ist, gibt eine datenabhängige Anforderung auf Grundlage eines in dieser Zuordnung enthaltenen Schlüssels einen Fehler zurück. Wenn darüber hinaus ein Bereich zuerst offline geschaltet wird, werden alle Verbindungen mit dem betroffenen Shard automatisch abgebrochen. So soll verhindert werden, dass inkonsistente oder unvollständige Ergebnisse bei Abfragen gegen jene Bereiche auftreten, die geändert werden.

Zuordnungen sind in .NET unveränderliche Objekte.  Alle oben genannten Methoden zum Ändern von Zuordnungen machen auch alle Verweise auf diese in Ihrem Code ungültig. Zur einfacheren Durchführung von Vorgangsabfolgen, die den Zustand einer Zuordnung ändern, geben alle Methoden, die eine Zuordnung ändern, einen neuen Zuordnungsverweis zurück, sodass die Vorgänge verkettet werden können. Um z. B. eine vorhandene Zuordnung in "shardmap sm" mit dem Schlüssel 25 zu löschen, können Sie Folgendes ausführen:

```
    sm.DeleteMapping(sm.MarkMappingOffline(sm.GetMappingForKey(25)));
```

## <a name="adding-a-shard"></a>Hinzufügen eines Shards

Anwendungen müssen häufig neue Shards hinzufügen, um Daten zu verwalten, die von neuen Schlüsseln oder Schlüsselbereichen für eine Shardzuordnung erwartet werden, die bereits vorhanden ist. Eine Anwendung beispielsweise, bei der Sharding über die Mandanten-ID durchgeführt wird, muss unter Umständen einen neuen Shard für einen neuen Mandanten bereitstellen, oder Daten, bei denen das Sharding monatlich durchgeführt wird, benötigen möglicherweise einen neuen Shard, der vor dem Start eines jeweils neuen Monats bereitgestellt wird.

Wenn der neue Bereich von Schlüsselwerten nicht bereits Teil einer vorhandenen Zuordnung ist und keine Datenverschiebung erforderlich ist, ist es einfach, den neuen Shard hinzuzufügen und dem Shard den neuen Schlüssel oder Bereich zuzuordnen. Weitere Informationen zum Hinzufügen neuer Shards finden Sie unter [Hinzufügen eines neuen Shards](elastic-scale-add-a-shard.md).

Bei Szenarios, die eine Datenverschiebung erforderlich machen, wird jedoch das Split-Merge-Tool benötigt, um die Datenverschiebung zwischen Shards in Kombination mit den erforderlichen Shard-Zuordnungsaktualisierungen zu koordinieren. Weitere Informationen zur Verwendung des Split-Merge-Tools finden Sie unter [Übersicht über Split-Merge](elastic-scale-overview-split-and-merge.md)

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/elastic-scale-shard-map-management/listmapping.png
[2]: ./media/elastic-scale-shard-map-management/rangemapping.png
[3]: ./media/elastic-scale-shard-map-management/multipleonsingledb.png