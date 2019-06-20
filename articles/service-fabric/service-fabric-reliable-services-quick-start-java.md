---
title: Erstellen Ihres ersten zuverlässigen Azure Service Fabric-Diensts in Java | Microsoft-Dokumentation
description: Einführung in das Erstellen einer Microsoft Azure Service Fabric-Anwendung mit zustandslosen und zustandsbehafteten Diensten.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: chackdan
editor: ''
ms.assetid: 7831886f-7ec4-4aef-95c5-b2469a5b7b5d
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: suhuruli
ms.openlocfilehash: 6bf8c632a7513d018745bc74aa0a1db95a39af8b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62130125"
---
# <a name="get-started-with-reliable-services"></a>Erste Schritte mit Reliable Services
> [!div class="op_single_selector"]
> * [C# unter Windows](service-fabric-reliable-services-quick-start.md)
> * [Java unter Linux](service-fabric-reliable-services-quick-start-java.md)
>
>

Dieser Artikel erläutert die Grundlagen von Azure Service Fabric Reliable Services und führt Sie durch das Erstellen und Bereitstellen einer einfachen in Java geschriebenen Reliable Services-Anwendung. 

## <a name="installation-and-setup"></a>Installation und Setup
Bevor Sie beginnen, vergewissern Sie sich, dass die Entwicklungsumgebung von Service Fabric auf Ihrem Computer eingerichtet wurde.
Wenn Sie sie einrichten müssen, wechseln Sie zu [Einrichten Ihrer Entwicklungsumgebung unter Mac OS X](service-fabric-get-started-mac.md) bzw. [Vorbereiten Ihrer Entwicklungsumgebung unter Linux](service-fabric-get-started-linux.md).

## <a name="basic-concepts"></a>Grundlegende Konzepte
Für den Einstieg in Reliable Services müssen Sie sich nur mit einigen grundlegenden Konzepten vertraut machen:

* **Diensttyp**: Dies ist Ihre Dienstimplementierung. Sie wird durch die von Ihnen geschriebene Klasse definiert, mit der `StatelessService` und alle anderen darin verwendeten Codeelemente oder Abhängigkeiten erweitert werden, einschließlich eines Namens und der Versionsnummer.
* **Benannte Dienstinstanz**: Zum Ausführen des Diensts erstellen Sie benannte Instanzen Ihres Diensttyps. Dies ähnelt der Erstellung von Objektinstanzen eines Klassentyps. Bei Dienstinstanzen handelt es sich eigentlich um Objektinstanziierungen der von Ihnen geschriebenen Dienstklasse.
* **Diensthost**: Die benannten Dienstinstanzen, die Sie erstellen, müssen in einem Host ausgeführt werden. Der Diensthost ist nur ein Prozess, für den Instanzen Ihres Diensts ausgeführt werden können.
* **Dienstregistrierung**: Bei der Registrierung werden alle Elemente zusammengeführt. Der Diensttyp muss bei der Service Fabric-Laufzeit in einem Diensthost registriert werden, damit von Service Fabric Instanzen davon für die Ausführung erstellt werden können.  

## <a name="create-a-stateless-service"></a>Erstellen eines zustandslosen Diensts
Beginnen Sie, indem Sie eine Service Fabric-Anwendung erstellen. Das Service Fabric SDK für Linux enthält einen Yeoman-Generator, um das Gerüst für eine Service Fabric-Anwendung mit einem zustandslosen Dienst bereitzustellen. Beginnen Sie, indem Sie den folgenden Yeoman Befehl ausführen:

```bash
$ yo azuresfjava
```

Befolgen Sie die Anweisungen zum Erstellen eines **zustandslosen Reliable Service**. Für dieses Tutorial nennen Sie die Anwendung „HelloWorldApplication“ und den Dienst „HelloWorld“. Das Ergebnis enthält Verzeichnisse für `HelloWorldApplication` und `HelloWorld`.

```bash
HelloWorldApplication/
├── build.gradle
├── HelloWorld
│   ├── build.gradle
│   └── src
│       └── statelessservice
│           ├── HelloWorldServiceHost.java
│           └── HelloWorldService.java
├── HelloWorldApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   └── _readme.txt
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── install.sh
├── settings.gradle
└── uninstall.sh
```
### <a name="service-registration"></a>Dienstregistrierung
Diensttypen müssen bei der Service Fabric-Laufzeit registriert sein. Der Diensttyp wird in der Datei `ServiceManifest.xml` und in der Dienstklasse definiert, die `StatelessService` implementiert. Die Dienstregistrierung erfolgt im Haupteinstiegspunkt des Prozesses. In diesem Beispiel lautet der Haupteinstiegspunkt des Prozesses `HelloWorldServiceHost.java`:

```java
public static void main(String[] args) throws Exception {
    try {
        ServiceRuntime.registerStatelessServiceAsync("HelloWorldType", (context) -> new HelloWorldService(), Duration.ofSeconds(10));
        logger.log(Level.INFO, "Registered stateless service type HelloWorldType.");
        Thread.sleep(Long.MAX_VALUE);
    }
    catch (Exception ex) {
        logger.log(Level.SEVERE, "Exception in registration:", ex);
        throw ex;
    }
}
```

## <a name="implement-the-service"></a>Implementieren des Diensts

Öffnen Sie **HelloWorldApplication/HelloWorld/src/statelessservice/HelloWorldService.java**. Diese Klasse definiert den Diensttyp und kann jeden Code ausführen. Die Dienst-API bietet zwei Einstiegspunkte für den Code:

* Eine Einstiegspunktmethode mit offenem Ende namens `runAsync()`, mit der Sie die Ausführung beliebiger Workloads starten können, inklusive Computeworkloads mit langer Ausführungsdauer.

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    ...
}
```

* Einen Einstiegspunkt für die Kommunikation, mit dem Sie mit einem beliebigen Kommunikationsstapel verbinden können. Dies ist der Punkt, an dem Sie mit dem Empfangen der Anforderungen von Benutzern und anderen Diensten beginnen können.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {
    ...
}
```

In diesem Tutorial geht es um die Einstiegspunktmethode `runAsync()`. Hiermit können Sie sofort mit der Ausführung des Codes beginnen.

### <a name="runasync"></a>RunAsync
Die Plattform ruft diese Methode auf, wenn eine Instanz des Diensts platziert wurde und zur Ausführung bereit ist. Bei zustandslosen Diensten ist dies der Fall, wenn die Dienstinstanz geöffnet wird. Ein Abbruchtoken koordiniert, wann die Dienstinstanz geschlossen werden muss. In Service Fabric kann dieser Offen/Geschlossen-Zyklus einer Dienstinstanz über die gesamte Lebensdauer des Diensts häufig auftreten. Dies kann aus unterschiedlichen Gründen geschehen, z. B.:

* Das System verschiebt Ihre Dienstinstanzen, um einen Lastenausgleich für Ressourcen durchzuführen.
* In Ihrem Code treten Fehler auf.
* Die Anwendung oder das System werden aktualisiert.
* Die zugrunde liegende Hardware fällt aus.

Diese Orchestrierung wird von Service Fabric verwaltet, um sicherzustellen, dass der Dienst hoch verfügbar bleibt und die Lasten richtig verteilt sind.

Mit `runAsync()` sollte nicht synchron blockiert werden. Ihre Implementierung von runAsync sollte ein CompletableFuture-Element zurückgeben, damit die Laufzeit fortgesetzt werden kann. Wenn Ihre Workload eine Aufgabe mit langer Ausführungsdauer implementieren muss, sollte dies innerhalb des CompletableFuture-Elements erfolgen.

#### <a name="cancellation"></a>Abbruch
Zum Abbrechen der Arbeitsauslastung ist das Zusammenspiel verschiedener Aktionen erforderlich, die vom bereitgestellten Abbruchtoken orchestriert werden. Das System wartet, bis Ihre Aufgabe beendet wurde (erfolgreicher Abschluss, Abbruch oder Fehler), bevor der Vorgang fortgesetzt wird. Es ist wichtig, das Abbruchtoken zu berücksichtigen, etwaige Arbeiten abzuschließen und `runAsync()` so schnell wie möglich zu beenden, wenn vom System ein Abbruch angefordert wird. Das folgende Beispiel zeigt, wie ein Abbruchereignis verarbeitet wird:

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {

    // TODO: Replace the following sample code with your own logic
    // or remove this runAsync override if it's not needed in your service.

    return CompletableFuture.runAsync(() -> {
        long iterations = 0;
        while(true)
        {
        cancellationToken.throwIfCancellationRequested();
        logger.log(Level.INFO, "Working-{0}", ++iterations);

        try {
            Thread.sleep(1000);
        } catch (InterruptedException ex){}
        }
    });
}
```

In diesem Beispiel eines zustandslosen Diensts wird die Anzahl in einer lokalen Variablen gespeichert. Da es sich aber um einen zustandslosen Dienst handelt, existiert der gespeicherte Wert nur für den aktuellen Lebenszyklus der Dienstinstanz. Wenn der Dienst verschoben oder neu gestartet wird, geht der Wert verloren.

## <a name="create-a-stateful-service"></a>Erstellen eines zustandsbehafteten Diensts
Mit Service Fabric wird eine neue Art von zustandsbehaftetem Dienst eingeführt. Bei einem zustandsbehafteten Dienst kann der Zustand zuverlässig innerhalb des Diensts selbst verwaltet und dem Code zugeordnet werden, in dem er verwendet wird. Service Fabric stellt die hohe Verfügbarkeit des Zustands sicher, ohne dass dieser extern gespeichert werden muss.

Um einen Zählerwert selbst bei einer Verschiebung oder einem Neustart des Diensts von zustandslos zu hoch verfügbar und persistent zu konvertieren, benötigen Sie einen zustandsbehafteten Dienst.

Sie können im gleichen Verzeichnis wie die Anwendung „HelloWorld“ einen neuen Dienst durch Ausführen des Befehls `yo azuresfjava:AddService` hinzufügen. Wählen Sie den „zuverlässigen zustandsbehafteten Dienst“ für Ihr Framework, und nennen Sie den Dienst „HelloWorldStateful“. 

Ihre Anwendung sollte nun über zwei Dienste verfügen: den zustandslosen Dienst „HelloWorld“ und den zustandsbehafteten Dienst „HelloWorldStateful“.

Ein zustandsbehafteter Dienst hat die gleichen Einstiegspunkte wie ein zustandsloser Dienst. Der Hauptunterschied liegt in der Verfügbarkeit eines Zustandsanbieters, der den Zustand zuverlässig speichern kann. Service Fabric beinhaltet eine Zustandsanbieterimplementierung namens „Reliable Collections“, die es Ihnen ermöglicht, replizierte Datenstrukturen mittels Reliable State Manager zu erstellen. Ein zustandsbehafteter Reliable Service verwendet standardmäßig diesen Zustandsanbieter.

Öffnen Sie „HelloWorldStateful.java“ in **HelloWorldStateful -> src**. Darin ist die folgende RunAsync-Methode enthalten:

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    Transaction tx = stateManager.createTransaction();
    return this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap").thenCompose((map) -> {
        return map.computeAsync(tx, "counter", (k, v) -> {
            if (v == null)
                return 1L;
            else
                return ++v;
            }, Duration.ofSeconds(4), cancellationToken)
                .thenCompose((r) -> tx.commitAsync())
                .whenComplete((r, e) -> {
            try {
                tx.close();
            } catch (Exception e) {
                logger.log(Level.SEVERE, e.getMessage());
            }
        });
    });
}
```

### <a name="runasync"></a>RunAsync
`RunAsync()` funktioniert in zustandsbehafteten und zustandslosen Diensten ähnlich. Bei einem zustandsbehafteten Dienst werden von der Plattform aber noch weitere Schritte in Ihrem Namen ausgeführt, bevor `RunAsync()`ausgeführt wird. Hierzu kann auch die Sicherstellung dessen gehören, dass der Reliable State Manager und Reliable Collections für die Verwendung bereit sind.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Reliable Collections und der Reliable State Manager
```java
ReliableHashMap<String,Long> map = this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap")
```

[ReliableHashMap](https://docs.microsoft.com/java/api/microsoft.servicefabric.data.collections.reliablehashmap) ist eine Wörterbuchimplementierung, die Sie nutzen können, um den Zustand im Dienst zuverlässig zu speichern. Mit Service Fabric und Reliable HashMaps können Sie Daten direkt in Ihrem Dienst speichern. Ein externer persistenter Speicher ist nicht erforderlich. Mit Reliable HashMaps kann die Hochverfügbarkeit Ihrer Daten ermöglicht werden. Service Fabric erreicht dies, indem mehrere *Replikate* Ihres Diensts für Sie erstellt und verwaltet werden. Außerdem wird eine API bereitgestellt, mit der die komplexen Verwaltungsanforderungen dieser Replikate und der damit verbundenen Zustandsübergänge beseitigt werden.

Reliable Collections können mit gewissen Einschränkungen beliebige Java-Typen – einschließlich benutzerdefinierten Typen – speichern:

* Service Fabric macht Ihren Zustand hoch verfügbar, indem der Zustand knotenübergreifend *repliziert* wird und Reliable HashMap Ihre Daten für jedes Replikat auf einem lokalen Datenträger speichert. Dies bedeutet, dass alle Elemente, die in Reliable HashMaps gespeichert werden, *serialisierbar* sein müssen. 
* Objekte werden zum Zweck der Hochverfügbarkeit repliziert, wenn Sie Transaktionen für Reliable HashMaps committen. In Reliable HashMaps gespeicherte Objekte werden in Ihrem Dienst im lokalen Speicher vorgehalten. Dies bedeutet, dass Sie über einen lokalen Verweis auf das Objekt verfügen.
  
   Es ist wichtig, dass Sie lokale Instanzen dieser Objekte nicht ändern, ohne ein Update für die Reliable Collection in einer Transaktion durchzuführen. Das liegt daran, dass Änderungen an lokalen Instanzen von Objekten nicht automatisch repliziert werden. Sie müssen das Objekt wieder ins Wörterbuch einfügen oder eine der Methoden zur *Aktualisierung* auf das Wörterbuch anwenden.

Reliable State Manager verwaltet Reliable HashMaps für Sie. Sie können über Reliable State Manager jederzeit und von jedem Ort in Ihrem Dienst aus anhand des Namens eine zuverlässige Auflistung anfordern. Der Reliable State Manager stellt sicher, dass Sie einen Verweis zurückerhalten. Es ist nicht ratsam, Verweise auf Instanzen zuverlässiger Sammlungen in Klassenmembervariablen oder -eigenschaften zu speichern. Achten Sie besonders darauf sicherzustellen, dass der Verweis während des Dienstlebenszyklus jederzeit auf eine Instanz festgelegt ist. Der Reliable State Manager übernimmt diesen Schritt für Sie. Er ist für wiederholte Besuche optimiert.


### <a name="transactional-and-asynchronous-operations"></a>Transaktionale und asynchrone Vorgänge
```java
return map.computeAsync(tx, "counter", (k, v) -> {
    if (v == null)
        return 1L;
    else
        return ++v;
    }, Duration.ofSeconds(4), cancellationToken)
        .thenCompose((r) -> tx.commitAsync())
        .whenComplete((r, e) -> {
    try {
        tx.close();
    } catch (Exception e) {
        logger.log(Level.SEVERE, e.getMessage());
    }
});
```

Vorgänge für Reliable HashMaps sind asynchron. Das liegt daran, dass Schreibvorgänge mit Reliable Collections E/A-Vorgänge ausführen, um Replikationen der Daten und ihre persistente Speicherung auf dem Datenträger vorzunehmen.

Reliable HashMaps-Vorgänge sind *transaktional*, damit Sie den Zustand über mehrere Reliable HashMaps und Vorgänge hinweg beibehalten können. Sie können beispielsweise ein Arbeitselement aus einem zuverlässigen Wörterbuch abrufen, einen Vorgang dafür ausführen und das Ergebnis in einer anderen Reliable HashMap-Instanz speichern – alles in einer Transaktion. Dies wird als atomischer Vorgang behandelt und es wird sichergestellt, dass entweder der gesamte Vorgang erfolgreich ist oder ein Rollback für den gesamten Vorgang ausgeführt wird. Wenn nach dem Entfernen des Elements aus der Warteschlange und vor dem Speichern des Ergebnisses ein Fehler auftritt, wird für die gesamte Transaktion ein Rollback ausgeführt, und das Element bleibt zur Verarbeitung in der Warteschlange enthalten.


## <a name="build-the-application"></a>Erstellen der Anwendung

Das Yeoman-Gerüst enthält ein Gradle-Skript zum Erstellen der Anwendung sowie Bash-Skripts zum Bereitstellen und Entfernen der Anwendung. Um die Anwendung auszuführen, erstellen Sie diese zunächst mit Gradle:

```bash
$ gradle
```

Dadurch wird ein Service Fabric-Anwendungspaket generiert, das mithilfe der Service Fabric CLI bereitgestellt werden kann.

## <a name="deploy-the-application"></a>Bereitstellen der Anwendung

Die erstellte Anwendung kann im lokalen Cluster bereitgestellt werden.

1. Stellen Sie eine Verbindung mit dem lokalen Service Fabric-Cluster her.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Führen Sie das in der Vorlage bereitgestellte Installationsskript aus, um das Anwendungspaket in den Imagespeicher des Clusters zu kopieren, den Anwendungstyp zu registrieren und eine Instanz der Anwendung zu erstellen.

    ```bash
    ./install.sh
    ```

Bei der Bereitstellung der erstellten Anwendung wird genau wie bei anderen Service Fabric-Anwendungen vorgegangen. Ausführliche Anweisungen finden Sie in der Dokumentation zum [Verwalten einer Service Fabric-Anwendung mit der Service Fabric-Befehlszeilenschnittstelle](service-fabric-application-lifecycle-sfctl.md).

Parameter für diesen Befehl finden Sie in den erstellten Manifesten im Anwendungspaket.

Öffnen Sie nach der Bereitstellung der Anwendung einen Browser, und navigieren Sie zu [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) unter [http://localhost:19080/Explorer](http://localhost:19080/Explorer). Erweitern Sie dann den Knoten **Anwendungen**. Hier finden Sie nun einen Eintrag für Ihren Anwendungstyp und einen weiteren für die erste Instanz dieses Typs.

> [!IMPORTANT]
> Wenn Sie die Anwendung in einem sicheren Linux-Cluster in Azure bereitstellen möchten, müssen Sie ein Zertifikat konfigurieren, um Ihre Anwendung mit der Service Fabric-Runtime zu überprüfen. Dadurch wird Ihren Reliable Services-Diensten die Kommunikation mit den zugrunde liegenden Service Fabric-Runtime-APIs ermöglicht. Weitere Informationen finden Sie unter [Konfigurieren einer Reliable Services-App zur Ausführung in Linux-Clustern](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters).  
>

## <a name="next-steps"></a>Nächste Schritte

* [Erste Schritte mit der Service Fabric-Befehlszeilenschnittstelle](service-fabric-cli.md)
