---
title: Konfigurieren Ihres eigenständigen Azure Service Fabric-Clusters | Microsoft-Dokumentation
description: Dieser Artikel beschreibt, wie Sie einen eigenständigen oder lokalen Azure Service Fabric-Cluster konfigurieren.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: 0c5ec720-8f70-40bd-9f86-cd07b84a219d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/12/2018
ms.author: dekapur
ms.openlocfilehash: ae7fbef864634e47866de13384871a98b8ce4675
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65209717"
---
# <a name="configuration-settings-for-a-standalone-windows-cluster"></a>Konfigurationseinstellungen für einen eigenständigen Windows-Cluster
In diesem Artikel werden die Konfigurationseinstellungen für einen eigenständigen Azure Service Fabric-Cluster beschrieben, die in der *ClusterConfig.json*-Datei festgelegt werden können. Sie verwenden diese Datei, um Informationen zu den Knoten des Clusters, den Sicherheitskonfigurationen sowie zur Netzwerktopologie in Bezug auf Fehler- und Upgradedomänen anzugeben.  Nach dem Ändern oder Hinzufügen von Konfigurationseinstellungen können Sie entweder [einen eigenständigen Cluster erstellen](service-fabric-cluster-creation-for-windows-server.md) oder [die Konfiguration eines eigenständigen Clusters aktualisieren](service-fabric-cluster-config-upgrade-windows-server.md).

Wenn Sie [das eigenständige Service Fabric-Paket](service-fabric-cluster-creation-for-windows-server.md#downloadpackage) herunterladen, sind auch ClusterConfig.json-Beispiele enthalten. Die Beispiele mit „DevCluster“ im Namen erstellen mithilfe logischer Knoten einen Cluster mit allen drei Knoten auf demselben Computer. Davon muss mindestens ein Knoten als primärer Knoten gekennzeichnet sein. Dieser Clustertyp eignet sich für Entwicklungs- oder Testumgebungen. Er wird nicht als Produktionscluster unterstützt. Die Beispiele mit „MultiMachine“ im Namen dienen Ihnen als Hilfe beim Erstellen von Clustern in Produktionsqualität, bei dem sich jeder Knoten auf einem anderen Computer befindet. Die Anzahl von primären Knoten für diese Cluster basiert jeweils auf der [Zuverlässigkeitsstufe](#reliability) des Clusters. Im Release 5.7, API-Version 05-2017 wurde die Eigenschaft für die Zuverlässigkeitsstufe entfernt. Stattdessen wird mit dem Code die optimale Zuverlässigkeitsstufe für Ihren Cluster berechnet. Versuchen Sie nicht, einen Wert für diese Eigenschaft in den Versionen ab 5.7 festzulegen.

* Mit „ClusterConfig.Unsecure.DevCluster.json“ und „ClusterConfig.Unsecure.MultiMachine.json“ wird veranschaulicht, wie Sie einen ungeschützten Test- bzw. Produktionscluster erstellen.

* Mit „ClusterConfig.Windows.DevCluster.json“ und „ClusterConfig.Windows.MultiMachine.json“ wird veranschaulicht, wie Sie einen Test- oder Produktionscluster erstellen, der per [Windows-Sicherheit](service-fabric-windows-cluster-windows-security.md) geschützt ist.

* Mit „ClusterConfig.X509.DevCluster.json“ und „ClusterConfig.X509.MultiMachine.json“ wird veranschaulicht, wie Sie einen Test- oder Produktionscluster erstellen, der per [Sicherheit auf Basis des X509-Zertifikats](service-fabric-windows-cluster-x509-security.md) geschützt ist.

Sehen wir uns nun die verschiedenen Abschnitte der Datei „ClusterConfig.json“ an.

## <a name="general-cluster-configurations"></a>Allgemeine Clusterkonfigurationen
Allgemeine Clusterkonfigurationen behandeln wie im folgenden JSON-Ausschnitt dargestellt die allgemeinen clusterspezifischen Konfigurationen:

```json
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "01-2017",
```

Sie können für Ihren Service Fabric-Cluster einen beliebigen Anzeigenamen festlegen, indem sie ihn der Variablen „name“ zuweisen. Die „clusterConfigurationVersion“ ist die Versionsnummer Ihres Clusters. Erhöhen Sie diese Nummer jedes Mal, wenn Sie Ihr Service Fabric-Cluster upgraden. Behalten Sie für „apiVersion“ den Standardwert bei.

## <a name="nodes-on-the-cluster"></a>Knoten im Cluster
Sie können die Knoten in Ihrem Service Fabric-Cluster, wie im folgenden Codeausschnitt gezeigt, mithilfe des „nodes“-Abschnitts konfigurieren:
```json
"nodes": [{
    "nodeName": "vm0",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD0"
}, {
    "nodeName": "vm1",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType1",
    "faultDomain": "fd:/dc1/r1",
    "upgradeDomain": "UD1"
}, {
    "nodeName": "vm2",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType2",
    "faultDomain": "fd:/dc1/r2",
    "upgradeDomain": "UD2"
}],
```

Ein Service Fabric-Cluster muss mindestens drei Knoten enthalten. Wenn es für Ihr Setup erforderlich ist, können Sie diesem Abschnitt weitere Knoten hinzufügen. In der folgenden Tabelle werden Konfigurationseinstellungen für die einzelnen Knoten erläutert:

| **Knotenkonfiguration** | **Beschreibung** |
| --- | --- |
| nodeName |Sie können einen beliebigen Anzeigenamen für den Knoten festlegen. |
| iPAddress |Ermitteln Sie die IP-Adresse Ihres Knotens, indem Sie in einem Befehlsfenster `ipconfig`eingeben. Notieren Sie sich die IPv4-Adresse, und weisen Sie sie der Variablen „iPAddress“ zu. |
| nodeTypeRef |Jedem Knoten kann ein anderer Knotentyp zugewiesen werden. Die [Knotentypen](#node-types) werden im folgenden Abschnitt definiert. |
| faultDomain |Mit Fehlerdomänen können Clusteradministratoren die physischen Knoten definieren, die unter Umständen gleichzeitig ausfallen können, weil gemeinsame physische Abhängigkeiten bestehen. |
| upgradeDomain |Upgradedomänen beschreiben Gruppen von Knoten, die zum Durchführen von Service Fabric-Upgrades zur gleichen Zeit heruntergefahren werden. Sie können selbst auswählen, welche Knoten den Upgradedomänen zugewiesen werden sollen, denn es bestehen hier keine physischen Einschränkungen. |

## <a name="cluster-properties"></a>Clustereigenschaften
Im Abschnitt „properties“ der Datei „ClusterConfig.json“ wird der Cluster wie folgt konfiguriert:

### <a name="reliability"></a>Zuverlässigkeit
Das Konzept „reliabilityLevel“ definiert die Anzahl von Replikaten oder Instanzen der Service Fabric-Systemdienste, die auf den primären Knoten des Clusters ausgeführt werden können. Es bestimmt die Zuverlässigkeit dieser Dienste und damit auch des Clusters. Der Wert wird vom System bei der Clustererstellung und beim Upgraden des Clusters berechnet.

### <a name="diagnostics"></a>Diagnose
Im Abschnitt „diagnosticsStore“ können Sie Parameter konfigurieren, um bei Knoten- und Clusterausfällen die Diagnose und Fehlerbehandlung zu ermöglichen. Dies wird im folgenden Codeausschnitt veranschaulicht: 

```json
"diagnosticsStore": {
    "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
    "dataDeletionAgeInDays": "7",
    "storeType": "FileShare",
    "IsEncrypted": "false",
    "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
}
```

„Metadata“ ist eine Beschreibung Ihres Clusters und kann gemäß Ihrem Setup festgelegt werden. Diese Variablen helfen Ihnen beim Sammeln von ETW-Ablaufprotokollen, Absturzabbildern und Leistungsindikatoren. Weitere Informationen über ETW-Ablaufprotokolle finden Sie unter [Tracelog](https://msdn.microsoft.com/library/windows/hardware/ff552994.aspx) und [ETW-Ablaufverfolgung](https://msdn.microsoft.com/library/ms751538.aspx). Alle Protokolle einschließlich [Absturzabbildern](https://blogs.technet.microsoft.com/askperf/2008/01/08/understanding-crash-dump-files/) und [Leistungsindikatoren](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) können zum Ordner „connectionString“ auf Ihrem Computer geleitet werden. Sie können AzureStorage auch zum Speichern von Diagnosen verwenden. Informationen hierzu finden Sie auch im folgenden Beispielcodeausschnitt:

```json
"diagnosticsStore": {
    "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
    "dataDeletionAgeInDays": "7",
    "storeType": "AzureStorage",
    "IsEncrypted": "false",
    "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
}
```

### <a name="security"></a>Sicherheit
Der Abschnitt „security“ wird für einen sicheren eigenständigen Service Fabric-Cluster benötigt. Der folgende Codeausschnitt zeigt einen Teil dieses Abschnitts:

```json
"security": {
    "metadata": "This cluster is secured using X509 certificates.",
    "ClusterCredentialType": "X509",
    "ServerCredentialType": "X509",
    . . .
}
```

„Metadata“ ist eine Beschreibung Ihres sicheren Clusters und kann gemäß Ihrem Setup festgelegt werden. „ClusterCredentialType“ und „ServerCredentialType“ bestimmen den Typ der Sicherheit, der im Cluster und in den Knoten implementiert wird. Sie können *X509* für eine auf Zertifikaten basierende Sicherheit oder *Windows* für eine auf Active Directory basierende Sicherheit festlegen. Die übrigen Festlegungen im Abschnitt „security“ hängen vom Typ der Sicherheit ab. Informationen darüber, wie Sie die restlichen Teile des Abschnitts „security“ ausfüllen, erhalten Sie in den Artikeln [Zertifikatbasierte Sicherheit in eigenständigen Windows-Clustern](service-fabric-windows-cluster-x509-security.md) und [Windows-Sicherheit in eigenständigen Windows-Clustern](service-fabric-windows-cluster-windows-security.md).

### <a name="node-types"></a>Knotentypen
Im Abschnitt „nodeTypes“ werden die Typen der Knoten beschrieben, die in Ihrem Cluster vorhanden sind. Es muss wie im folgenden Codeausschnitt gezeigt mindestens ein Knotentyp für einen Cluster angegeben sein: 

```json
"nodeTypes": [{
    "name": "NodeType0",
    "clientConnectionEndpointPort": "19000",
    "clusterConnectionEndpointPort": "19001",
    "leaseDriverEndpointPort": "19002"
    "serviceConnectionEndpointPort": "19003",
    "httpGatewayEndpointPort": "19080",
    "reverseProxyEndpointPort": "19081",
    "applicationPorts": {
        "startPort": "20575",
        "endPort": "20605"
    },
    "ephemeralPorts": {
        "startPort": "20606",
        "endPort": "20861"
    },
    "isPrimary": true
}]
```

Der Name ist der Anzeigename für diesen bestimmten Knotentyp. Weisen Sie zum Erstellen eines Knotens dieses Typs seinen Anzeigenamen der Variablen „nodeTypeRef“ für den Knoten zu, wie [zuvor beschrieben](#nodes-on-the-cluster). Definieren Sie für jeden Knotentyp die zu verwendenden Verbindungsendpunkte. Für diese Verbindungsendpunkte können Sie jede beliebige Portnummer auswählen, sofern dadurch keine Konflikte mit anderen Endpunkten in diesem Cluster entstehen. In einem Cluster mit mehreren Knoten gibt es mindestens einen primären Knoten („isPrimary“ ist *TRUE*). Dies richtet sich nach der Zuverlässigkeitsstufe ([reliabilityLevel](#reliability)). Informationen zu den primären Knoten und anderen Knotentypen sowie zu „nodeTypes“ und „reliabilityLevel“ erhalten Sie unter [Überlegungen zur Kapazitätsplanung für Service Fabric-Cluster](service-fabric-cluster-capacity.md). 

#### <a name="endpoints-used-to-configure-the-node-types"></a>Endpunkte zum Konfigurieren der Knotentypen
* „clientConnectionEndpointPort“ ist der Port, der vom Client zum Herstellen der Verbindung mit dem Cluster verwendet wird, wenn die Client-APIs genutzt werden. 
* „clusterConnectionEndpointPort“ ist der Port, über den die Knoten miteinander kommunizieren.
* „leaseDriverEndpointPort“ ist der Port, über den vom Clusterleasetreiber ermittelt wird, ob die Knoten noch aktiv sind. 
* „serviceConnectionEndpointPort“ ist der Port, der von den auf einem Knoten bereitgestellten Anwendungen und Diensten verwendet wird, um mit dem Service Fabric-Client auf dem jeweiligen Knoten zu kommunizieren.
* „httpGatewayEndpointPort“ ist der Port, der vom Service Fabric Explorer zum Herstellen der Verbindung mit dem Cluster verwendet wird.
* „ephemeralPorts“ setzen die [vom Betriebssystem verwendeten dynamischen Ports](https://support.microsoft.com/kb/929851) außer Kraft. Service Fabric nutzt einen Teil dieser Ports als Anwendungsports, und die restlichen Ports sind für das Betriebssystem verfügbar. Außerdem wird dieser Bereich dem vorhandenen Bereich des Betriebssystems zugeordnet, und Sie können die in den JSON-Beispieldateien angegebenen Bereiche für alle Zwecke verwenden. Stellen Sie sicher, dass der Unterschied zwischen den Start- und Endports mindestens 255 beträgt. Wenn dieser Unterschied zu gering ist, kommt es womöglich zu Konflikten, da dieser Bereich für das Betriebssystem freigegeben ist. Um den konfigurierten dynamischen Portbereich anzuzeigen, führen Sie `netsh int ipv4 show dynamicport tcp` aus.
* „applicationPorts“ sind die Ports, die von den Service Fabric-Anwendungen verwendet werden. Der Portbereich der Anwendung sollte ausreichend sein, um die Endpunktanforderung Ihrer Anwendungen abzudecken. Dieser Bereich sollte vom dynamischen Portbereich auf dem Computer (dem „ephemeralPorts“-Bereich in der Konfiguration) getrennt sein. Service Fabric greift auf diese Ports zurück, wenn neue Ports benötigt werden, und öffnet die Firewall für diese Ports. 
* „reverseProxyEndpointPort“ ist ein optionaler Reverseproxy-Endpunkt. Weitere Informationen finden Sie unter [Service Fabric-Reverseproxy](service-fabric-reverseproxy.md). 

### <a name="log-settings"></a>Protokolleinstellungen
Im Abschnitt „fabricSettings“ können Sie die Stammverzeichnisse für die Service Fabric-Daten und -Protokolle festlegen. Sie können diese Verzeichnisse nur während der erstmaligen Clustererstellung anpassen. Informationen hierzu finden Sie auch im Codeausschnitt dieses Abschnitts, der als Beispiel dient:

```json
"fabricSettings": [{
    "name": "Setup",
    "parameters": [{
        "name": "FabricDataRoot",
        "value": "C:\\ProgramData\\SF"
    }, {
        "name": "FabricLogRoot",
        "value": "C:\\ProgramData\\SF\\Log"
}]
```

Es wird empfohlen, dass Sie ein Nicht-Betriebssystem-Laufwerk als FabricDataRoot und FabricLogRoot verwenden. Er bietet höhere Zuverlässigkeit, wenn es darum geht, Situationen zu vermeiden, in denen das Betriebssystem nicht mehr reagiert. Wenn Sie nur das Stammverzeichnis für die Daten anpassen, wird das Stammverzeichnis für die Protokolle genau eine Ebene unterhalb des Stammverzeichnisses für die Daten angesiedelt.

### <a name="stateful-reliable-services-settings"></a>Einstellungen für zustandsbehaftete Reliable Services
Im Abschnitt „KtlLogger“ können Sie die globalen Konfigurationseinstellungen für Reliable Services festlegen. Weitere Informationen zu diesen Einstellungen finden Sie unter [Konfigurieren zustandsbehafteter Reliable Services](service-fabric-reliable-services-configuration.md). Im folgenden Beispiel wird gezeigt, wie Sie das freigegebene Transaktionsprotokoll ändern, das als Unterstützung für zuverlässige Sammlungen für zustandsbehaftete Dienste erstellt wird:

```json
"fabricSettings": [{
    "name": "KtlLogger",
    "parameters": [{
        "name": "SharedLogSizeInMB",
        "value": "4096"
    }]
}]
```

### <a name="add-on-features"></a>Add-On-Funktionen
Um Add-on-Funktionen zu konfigurieren, konfigurieren Sie die API-Version als „04-2017“ oder höher, und konfigurieren Sie „addonFeatures“, so wie hier gezeigt:

```json
"apiVersion": "04-2017",
"properties": {
    "addOnFeatures": [
        "DnsService",
        "RepairManager"
    ]
}
```

### <a name="container-support"></a>Containerunterstützung
Um bei eigenständigen Clustern die Containerunterstützung sowohl für Windows Server-Container als auch für Hyper-V-Container zu aktivieren, muss das Add-on-Feature von „DnsService“ aktiviert sein.

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie die *ClusterConfig.json*-Datei gemäß dem Setup Ihres eigenständigen Clusters vollständig konfiguriert haben, können Sie den Cluster bereitstellen. Befolgen Sie die Schritte unter [Erstellen eines eigenständigen Azure Service Fabric-Clusters](service-fabric-cluster-creation-for-windows-server.md). 

Wenn Sie einen eigenständigen Cluster bereitgestellt haben, können Sie auch [die Konfiguration eines eigenständigen Clusters aktualisieren](service-fabric-cluster-config-upgrade-windows-server.md). 

Unter [Visualisieren Ihres Clusters mit Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) ist die entsprechende Vorgehensweise beschrieben.

