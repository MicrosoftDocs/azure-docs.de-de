---
title: Azure Cache for Redis-Beispiele | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Azure Cache for Redis verwenden.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 1f8d210c-ee09-4fe2-b63f-1e69246a27d8
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: multiple
ms.topic: article
ms.date: 01/23/2017
ms.author: yegu
ms.openlocfilehash: 73c771ab18d1cc2944298818c1cab90eb2f277ff
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60829802"
---
# <a name="azure-cache-for-redis-samples"></a>Beispiele für Azure Cache for Redis
Dieses Thema enthält eine Liste mit Azure Cache for Redis-Beispielen für Szenarien wie das Herstellen einer Verbindung mit einem Cache, das Lesen und Schreiben von Cachedaten und das Verwenden der ASP.NET Azure Cache for Redis-Anbieter. Einige der Beispiele sind herunterladbare Projekte und einige bieten eine schrittweise Anleitung und Codeausschnitte, ohne mit einem herunterladbaren Projekt verknüpft zu sein.

## <a name="hello-world-samples"></a>Hello World-Beispiele
Die Beispiele in diesem Abschnitt veranschaulichen die Grundlagen des Herstellens einer Verbindung mit einer Azure Cache for Redis-Instanz sowie das Lesen und Schreiben von Cachedaten für eine Vielzahl von Sprachen und Redis-Clients.

Das [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld)-Beispiel zeigt, wie verschiedene Cachevorgänge mithilfe des [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis)-.NET-Clients ausgeführt werden.

Dieses Beispiel zeigt Folgendes:

* Verwenden von verschiedenen Verbindungsoptionen
* Lesen und Schreiben von Objekten aus dem und in den Cache mithilfe von synchronen und asynchronen Vorgängen
* Verwenden der Redis-MGET/MSET-Befehle zum Zurückgeben von Werten aus angegebenen Schlüsseln
* Ausführen von Redis-Transaktionsvorgängen
* Arbeiten mit Listen und sortierten Sätzen von Redis
* Speichern von .NET-Objekten mithilfe von JsonConvert-Serialisierungsprogrammen
* Verwenden von Redis-Sätzen zum Implementieren des Taggings
* Arbeiten mit dem Redis-Cluster

Weitere Informationen finden Sie in der [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis)-Dokumentation auf GitHub. Weitere Verwendungsszenarien finden Sie in den [StackExchange.Redis.Tests](https://github.com/StackExchange/StackExchange.Redis/tree/master/tests)-Einheitentests.

[Schnellstart: Verwenden von Azure Cache for Redis mit Python](cache-python-get-started.md) zeigt die ersten Schritte mit Azure Cache for Redis mit Python und dem Client [redis-py](https://github.com/andymccurdy/redis-py).

[Arbeiten mit .NET-Objekten im Cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache) zeigt eine Möglichkeit zum Serialisieren von .NET-Objekten, um sie in eine Azure Cache for Redis-Instanz zu schreiben und daraus zu lesen. 

## <a name="use-azure-cache-for-redis-as-a-scale-out-backplane-for-aspnet-signalr"></a>Verwenden von Azure Cache for Redis als horizontal skalierbare Backplane für ASP.NET SignalR
Das Beispiel [Use Redis Cache as a Scale out Backplane for ASP.NET SignalR](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) (Verwenden von Azure Cache for Redis als horizontal skalierbare Backplane für ASP.NET SignalR) veranschaulicht die Verwendung von Azure Cache for Redis als SignalR-Backplane. Weitere Informationen zur Backplane finden Sie unter [Horizontales Hochskalieren von SignalR mit Redis](https://www.asp.net/signalr/overview/performance/scaleout-with-redis).

## <a name="azure-cache-for-redis-customer-query-sample"></a>Azure Cache for Redis: Kundenabfragebeispiel
Dieses Beispiel veranschaulicht Leistungsvergleiche zwischen den Zugriff auf Daten aus einem Cache und dem Zugriff auf Daten in persistentem Speicher. Dieses Beispiel enthält zwei Projekte.

* [Demo how Redis Cache can improve performance by Caching data](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample) (Demo: Verbessern der Leistung durch Zwischenspeichern von Daten in Redis Cache)
* [Seeding der Datenbank und des Caches für die Demo](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>ASP.NET-Sitzungszustand und Ausgabezwischenspeicherung
Das Beispiel [Use Azure Redis Cache to store ASP.NET SessionState and OutputCache](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) (Verwenden von Azure Redis Cache zum Speichern von „SessionState“ und „OutputCache“ von ASP.NET) veranschaulicht die Verwendung von Azure Cache for Redis zum Speichern des ASP.NET-Sitzungs- und Ausgabecaches mit den SessionState- und OutputCache-Anbietern für Redis.

## <a name="manage-azure-cache-for-redis-with-maml"></a>Verwalten von Azure Cache for Redis mit MAML
Das Beispiel [Manage Azure Redis Cache using Azure Management Libraries](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) (Verwalten von Azure Redis Cache mithilfe von Azure-Verwaltungsbibliotheken) veranschaulicht, wie Sie Azure-Verwaltungsbibliotheken zum Verwalten (Erstellen/Aktualisieren/Löschen) Ihres Caches verwenden. 

## <a name="custom-monitoring-sample"></a>Beispiel einer benutzerdefinierten Überwachung
Das Beispiel [Access Redis Cache Monitoring data](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) (Zugreifen auf Überwachungsdaten von Redis Cache) zeigt, wie Sie außerhalb des Azure-Portals auf Überwachungsdaten für Ihre Azure Cache for Redis-Instanz zugreifen.

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>Ein Klon im Stil von Twitter, geschrieben mit PHP und Redis
Das Beispiel [Retwis](https://github.com/SyntaxC4-MSFT/retwis) ist die Hello-World-Variante von Redis. Es handelt sich um den minimalen Klon eines sozialen Netzwerks im Twitter-Stil, der mithilfe von Redis und PHP mit dem [Predis](https://github.com/nrk/predis) -Client geschrieben wurde. Der Quellcode ist sehr einfach gehalten und zeigt dennoch verschiedene Redis-Datenstrukturen.

## <a name="bandwidth-monitor"></a>Bandbreitenüberwachung
Das Beispiel [Bandbreitenüberwachung](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) ermöglicht die Überwachung der auf dem Client genutzten Bandbreite. Um die Bandbreite zu messen, führen Sie das Beispiel auf dem Cacheclientcomputer aus, rufen Sie den Cache auf, und beobachten Sie die durch das Beispiel gemeldete Bandbreite.

