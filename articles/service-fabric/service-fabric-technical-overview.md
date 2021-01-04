---
title: Informationen zur Azure Service Fabric-Terminologie
description: Erfahren Sie mehr über wichtige Begriffe und Konzepte bei Service Fabric, die in der restlichen Dokumentation verwendet werden.
author: masnider
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: masnider
ms.openlocfilehash: 2ac4b81a284ed8c38bc9cefccd08db5afa51d600
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575940"
---
# <a name="service-fabric-terminology-overview"></a>Übersicht über Service Fabric-Terminologie

Azure Service Fabric ist eine Plattform für verteilte Systeme, die das Packen, Bereitstellen und Verwalten skalierbarer und zuverlässiger Microservices vereinfacht.  Sie können [Service Fabric-Cluster überall hosten](service-fabric-deploy-anywhere.md): In Azure, in einem lokalen Datencenter oder bei einem Cloudanbieter.  Service Fabric ist der Orchestrator für [Azure Service Fabric Mesh](../service-fabric-mesh/index.yml). Sie können ein beliebiges Framework verwenden, um Ihre Dienste zu programmieren, und aus verschiedenen Umgebungen auszuwählen, in denen die Anwendung ausgeführt werden soll. In diesem Artikel wird die von Service Fabric verwendete Terminologie erläutert, damit Sie die in der Dokumentation verwendeten Begriffe verstehen.

## <a name="infrastructure-concepts"></a>Infrastrukturkonzepte

**Cluster**: Per Netzwerk verbundene virtuelle oder physische Computer, auf denen Ihre Microservices bereitgestellt und verwaltet werden.  Cluster können auf Tausende von Computern skaliert werden.

**Knoten:** Ein physischer oder virtueller Computer, der Teil eines Clusters ist, wird als *Knoten* bezeichnet. Jedem Knoten wird ein Knotenname (Zeichenfolge) zugewiesen. Knoten weisen Merkmale wie etwa Platzierungseigenschaften auf. Jeder Computer oder virtuelle Computer verfügt über einen Windows-Dienst für den automatischen Start (`FabricHost.exe`), der beim Start ausgeführt wird und seinerseits zwei ausführbare Dateien startet: `Fabric.exe` und `FabricGateway.exe`. Diese zwei ausführbaren Dateien bilden zusammen den Knoten. In Testszenarien können Sie mehrere Knoten auf einem einzelnen Computer oder virtuellen Computer hosten, indem Sie mehrere Instanzen von `Fabric.exe` und `FabricGateway.exe` ausführen.

## <a name="application-and-service-concepts"></a>Anwendungs- und Dienstkonzepte

**Service Fabric Mesh-Anwendung**: Service Fabric Mesh-Anwendungen werden mithilfe des Ressourcenmodells (YAML- und JSON-Ressourcendateien) beschrieben und können in jeder Umgebung bereitgestellt werden, in der Service Fabric ausgeführt wird.

**Native Service Fabric-Anwendung**: Native Service Fabric-Anwendungen werden durch das native Anwendungsmodell (XML-basierte Anwendungs- und Dienstmanifeste) beschrieben.  Native Service Fabric-Anwendungen können nicht in Service Fabric Mesh ausgeführt werden.

### <a name="service-fabric-mesh-application-concepts"></a>Service Fabric Mesh-Anwendungskonzepte

**Anwendung**: Eine Anwendung ist die Einheit aus Bereitstellung, Versionsverwaltung und Lebensdauer einer Mesh-Anwendung. Der Lebenszyklus jeder Anwendungsinstanz kann unabhängig voneinander verwaltet werden.  Anwendungen bestehen aus mindestens einem Dienstcodepaket und Einstellungen. Eine Anwendung wird mit dem Azure-Ressourcenmodellschema (RM-Schema) definiert.  Dienste werden in einer RM-Vorlage als Eigenschaften der Anwendungsressource beschrieben.  Auf die von der Anwendung verwendeten Netzwerke und Volumes wird von der Anwendung verwiesen.  Beim Erstellen einer Anwendung werden die Anwendung, die Dienste, das Netzwerk und die Volumes mithilfe des Service Fabric-Ressourcenmodells modelliert.

**Dienst**: Ein Dienst stellt in einer Anwendung einen Microservice dar und führt eine vollständige und eigenständige Funktion aus. Jeder Dienst besteht aus mindestens einem Codepaket, das alles beschreibt, was zum Ausführen des dem Codepaket zugeordneten Containerimages erforderlich ist.  Die Anzahl von Diensten in einer Anwendung kann zentral hoch- oder herunterskaliert werden.

**Netzwerk:** Eine Netzwerkressource erstellt ein privates Netzwerk für Ihre Anwendungen und ist unabhängig von den Anwendungen oder Diensten, die darauf verweisen können. Mehrere Dienste aus verschiedenen Anwendungen können Teil desselben Netzwerks sein. Netzwerke sind bereitstellbare Ressourcen, auf die von Anwendungen verwiesen wird.

**Codepaket**: Codepakete beschreiben alles, was benötigt wird, um das mit dem Codepaket verbundene Containerimage auszuführen, beispielsweise:

* Name, Version und Registrierung des Containers
* Für jeden Container erforderliche CPU- und Speicherressourcen
* Netzwerkendpunkte
* Im Container einzubindende Volumes, die auf eine separate Volumeressource verweisen

Alle Codepakete, die als Teil einer Anwendungsressource definiert sind, werden zusammen als Gruppe bereitgestellt und aktiviert.

**Volume**: Volumes sind Verzeichnisse, die in Ihre Containerinstanzen eingebunden werden, und mit denen Sie Zustände erhalten können. Der Azure Files-Volumetreiber stellt eine Azure Files-Freigabe in einem Container bereit und bietet zuverlässige Datenspeicherung über jede API, die Dateispeicherung unterstützt. Volumes sind bereitstellbare Ressourcen, auf die von Anwendungen verwiesen wird.

### <a name="service-fabric-native-application-concepts"></a>Konzepte nativer Service Fabric-Anwendungen

**Anwendung**: Eine Anwendung ist eine Sammlung von einzelnen Diensten, die eine bzw. mehrere bestimmte Funktionen ausführen. Der Lebenszyklus jeder Anwendungsinstanz kann unabhängig voneinander verwaltet werden.

**Dienst**: Ein Dienst führt eine vollständige und eigenständige Funktion aus und kann unabhängig von anderen Diensten gestartet und ausgeführt werden. Ein Dienst besteht aus Code, Konfiguration und Daten. Für jeden Dienst besteht der Code aus den ausführbaren Binärdateien, die Konfiguration umfasst Diensteinstellungen, die zur Laufzeit geladen werden können, und die Daten bestehen aus beliebigen statischen Daten, die vom Dienst verarbeitet werden.

**Anwendungstyp**: Name und Version, die einer Sammlung von Diensttypen zugewiesen sind. Beides ist in einer `ApplicationManifest.xml`-Datei definiert und in ein Anwendungspaketverzeichnis eingebettet. Das Verzeichnis wird dann in den Imagespeicher des Service Fabric-Clusters kopiert. Anschließend können Sie aus diesem Anwendungstyp im Cluster eine benannte Anwendung erstellen.

Weitere Informationen finden Sie im Artikel [Anwendungsmodell](service-fabric-application-model.md).

**Anwendungspaket**: Ein Datenträgerverzeichnis mit der Datei `ApplicationManifest.xml` des Anwendungstyps. Diese Datei verweist auf die Dienstpakete für jeden Diensttyp, der den Anwendungstyp bildet. Die Dateien im Anwendungspaketverzeichnis werden in den Imagespeicher des Service Fabric-Clusters kopiert. Ein Anwendungspaket für einen E-Mail-Anwendungstyp kann beispielsweise Verweise auf ein Warteschlangendienstpaket, ein Front-End-Dienstpaket und ein Datenbankdienstpaket enthalten.

**Benannte Anwendung**: Nachdem Sie ein Anwendungspaket in den Imagespeicher kopiert haben, erstellen Sie eine Instanz der Anwendung im Cluster. Sie erstellen eine Instanz, wenn Sie den Anwendungstyp des Anwendungspakets mithilfe des Namens oder der Version angeben. Jeder Anwendungstypinstanz wird ein URI-Name (Uniform Resource Identifier) zugewiesen, der wie folgt aussieht: `"fabric:/MyNamedApp"`. Innerhalb eines Clusters können Sie mehrere benannte Anwendungen aus einem einzelnen Anwendungstyp erstellen. Sie können auch benannte Applikationen aus verschiedenen Anwendungstypen erstellen. Jede benannte Anwendung wird unabhängig verwaltet und versioniert.

**Diensttyp**: Angaben zu Name und Version, die den Code-, Daten- und Konfigurationspaketen eines Diensts zugewiesen sind. Der Diensttyp wird in der Datei `ServiceManifest.xml` definiert und in ein Dienstpaketverzeichnis eingebettet. Auf das Dienstpaketverzeichnis wird dann durch eine `ApplicationManifest.xml`-Datei eines Anwendungspakets verwiesen. Innerhalb des Clusters kann nach der Erstellung einer benannten Anwendung ein benannter Dienst aus einem der Diensttypen des Anwendungstyps erstellt werden. Die Datei `ServiceManifest.xml` des Diensttyps beschreibt den Dienst.

Weitere Informationen finden Sie im Artikel [Anwendungsmodell](service-fabric-application-model.md).

Es gibt zwei Arten von Diensten:

* **Zustandslos**: Verwenden Sie einen zustandslosen Dienst, wenn der persistente Zustand des Diensts in einem externen Speicherdienst wie Azure Storage, Azure SQL-Datenbank oder Azure Cosmos DB gespeichert ist. Wenn der Dienst über keinen persistenten Speicher verfügt, verwenden Sie einen zustandslosen Dienst. Ein Beispiel hierfür ist ein Rechnerdienst, in dem Werte an den Dienst übergeben werden, eine Berechnung unter Verwendung dieser Werte ausgeführt und dann ein Ergebnis zurückgegeben wird.
* **Zustandsbehaftet**: Verwenden Sie einen zustandsbehafteten Dienst, wenn Service Fabric zur Verwaltung des Dienstzustands Programmiermodelle mit zuverlässigen Sammlungen oder Reliable Actors verwenden soll. Wenn Sie einen benannten Dienst erstellen, geben Sie an, über wie viele Partitionen Sie den Zustand für die Skalierbarkeit verteilen möchten. Geben Sie für die Zuverlässigkeit auch an, wie viele Male der Zustand über Knoten repliziert werden soll. Jeder benannte Dienst verfügt über ein einzelnes primäres Replikat und mehrere sekundäre Replikate. Sie ändern den Zustand Ihres benannten Diensts, wenn Sie in das primäre Replikat schreiben. Service Fabric repliziert dann diesen Zustand an alle sekundären Replikate, um den Zustand synchron zu halten. Service Fabric erkennt automatisch, wenn ein Fehler bei einem primären Replikat auftritt, und stuft ein vorhandenes sekundäres Replikat zum primären Replikat hoch. Service Fabric erstellt dann ein neues sekundäres Replikat.  

**Replikate oder Instanzen** beziehen sich auf Code (und den Zustand bei zustandsbehafteten Diensten) eines Diensts, der bereitgestellt und ausgeführt wird. Siehe [Replikate und Instanzen](service-fabric-concepts-replica-lifecycle.md).

**Neukonfiguration** bezieht sich auf alle Änderungsvorgänge in der Replikatgruppe eines Diensts. Siehe [Neukonfiguration](service-fabric-concepts-reconfiguration.md).

**Dienstpaket**: Ein Datenträgerverzeichnis mit der Datei `ServiceManifest.xml` des Diensttyps. Diese Datei verweist auf den Code, die statischen Daten und die Konfigurationspakete für den Diensttyp. Auf die Dateien im Dienstpaketverzeichnis wird in der Datei `ApplicationManifest.xml` des Anwendungstyps verwiesen. Ein Dienstpaket kann beispielsweise auf den Code, statische Daten und Konfigurationspakete verweisen, die zusammen einen Datenbankdienst bilden.

**Benannter Dienst:** Nach der Erstellung einer benannten Anwendung können Sie eine Instanz eines Diensttyps im Cluster erstellen. Sie geben den Diensttyp mithilfe von Name/Version an. Jeder Diensttypinstanz wird ein URI-Name zugewiesen, der dem URI der benannten Anwendung zugeordnet ist. Wenn Sie beispielsweise den benannten Dienst MyDatabase in der benannten Anwendung MyNamedApp erstellen, sieht der URI folgendermaßen aus: `"fabric:/MyNamedApp/MyDatabase"`. Innerhalb einer benannten Anwendung können Sie mehrere benannte Dienste erstellen. Jeder benannte Dienst kann ein eigenes Partitionsschema und eigene Werte für die Instanzen- oder Replikatanzahl besitzen.

**Codepaket**: Ein Datenträgerverzeichnis mit den ausführbaren Dateien des Diensttyps (in der Regel EXE-/DLL-Dateien). Auf die Dateien im Codepaketverzeichnis wird in der Datei `ServiceManifest.xml` des Diensttyps verwiesen. Wenn Sie einen benannten Dienst erstellen, wird das Codepaket auf den bzw. die zur Ausführung des benannten Diensts ausgewählten Knoten kopiert. Dann beginnt die Ausführung des Codes. Es gibt zwei Arten von ausführbaren Dateien für Codepakete:

* **Ausführbare Gastanwendungsdateien**: Ausführbare Dateien, die ohne Änderungen im Hostbetriebssystem (Windows oder Linux) ausgeführt werden. Diese ausführbaren Dateien sind nicht mit Service Fabric-Laufzeitdateien verknüpft bzw. verweisen nicht auf solche Dateien und verwenden daher keine Service Fabric-Programmiermodelle. Diese ausführbaren Dateien können manche Service Fabric-Features, z.B. den Naming Service für die Endpunktermittlung, nicht verwenden. Ausführbare Gastdateien können keine spezifischen Auslastungsmetriken für jede Dienstinstanz melden.
* **Ausführbare Dateien des Diensthosts**: Ausführbare Dateien, die Service Fabric-Programmiermodelle nutzen, indem eine Verknüpfung mit Service Fabric-Laufzeitdateien hergestellt wird und so Service Fabric-Features aktiviert werden. Eine Instanz eines benannten Diensts kann beispielsweise Endpunkte im Service FabricNaming-Dienst registrieren und Lastmetriken melden.

**Datenpaket**: Ein Datenträgerverzeichnis mit den statischen, schreibgeschützten Datendateien eines Diensttyps (in der Regel Foto-, Audio- und Videodateien). Auf die Dateien im Datenpaketverzeichnis wird in der Datei `ServiceManifest.xml` des Diensttyps verwiesen. Wenn Sie einen benannten Dienst erstellen, wird das Datenpaket auf den bzw. die zur Ausführung des benannten Diensts ausgewählten Knoten kopiert. Die Ausführung des Codes beginnt, und er kann jetzt auf die Datendateien zugreifen.

**Konfigurationspaket**: Ein Datenträgerverzeichnis mit den statischen, schreibgeschützten Konfigurationsdateien eines Diensttyps (in der Regel Textdateien). Auf die Dateien im Konfigurationspaketverzeichnis wird in der Datei `ServiceManifest.xml` des Diensttyps verwiesen. Wenn Sie einen benannten Dienst erstellen, werden die Dateien im Konfigurationspaket auf den bzw. die zur Ausführung des benannten Diensts ausgewählten Knoten kopiert. Dann beginnt die Ausführung des Codes, und er kann jetzt auf die Konfigurationsdateien zugreifen.

**Container**: Standardmäßig werden Dienste von Service Fabric als Prozesse bereitgestellt und aktiviert. Service Fabric kann Dienste auch in Containerimages bereitstellen. Container sind eine Virtualisierungstechnologie, bei der das zugrunde liegende Betriebssystem von den Anwendungen abstrahiert wird. Eine Anwendung und deren Runtime, Abhängigkeiten und Systembibliotheken werden in einem Container ausgeführt. Der Container verfügt über vollständigen, privaten Zugriff auf die zum Container gehörende isolierte Ansicht der Betriebssystemkonstrukte. Service Fabric unterstützt Windows Server-Container und Docker-Container unter Linux. Weitere Informationen finden Sie unter [Service Fabric und Container](service-fabric-containers-overview.md).

**Partitionsschema**: Bei der Erstellung eines benannten Diensts geben Sie ein Partitionsschema an. Dienste mit beträchtlichen Mengen an Zustandsdaten teilen die Daten auf verschiedene Partitionen auf, wodurch der Zustand auf die Knoten des Clusters verteilt wird. Durch Aufteilen der Daten auf Partitionen kann der Zustand des benannten Diensts skaliert werden. Innerhalb einer Partition besitzen zustandslose benannte Dienste Instanzen, wohingegen zustandsbehaftete benannte Dienste Replikate besitzen. In der Regel verfügen zustandslose benannte Dienste nur über eine einzelne Partition, da sie keinen internen Zustand aufweisen. Die Partitionsinstanzen sorgen für Verfügbarkeit. Wenn eine Instanz ausfällt, werden andere Instanzen weiterhin normal ausgeführt, und Service Fabric erstellt dann eine neue Instanz. Zustandsbehaftete benannte Dienste behalten ihren Zustand in Replikaten bei, und jede Partition verfügt über eine eigene Replikatgruppe, sodass der Zustand synchron bleibt. Fällt ein Replikat aus, erstellt Service Fabric aus den vorhandenen Replikaten ein neues Replikat.

Weitere Informationen finden Sie unter [Partitionieren von Service Fabric Reliable Services](service-fabric-concepts-partitioning.md) .

## <a name="system-services"></a>Systemdienste

In jedem Cluster werden Systemdienste erstellt, die die Plattformfunktionen von Service Fabric bereitstellen.

**Naming Service**: Jeder Service Fabric-Cluster verfügt über einen Naming Service, der Dienstnamen in einen Speicherort im Cluster auflöst. Ähnlich wie bei einem Internet-DNS (Domain Name System) verwalten Sie die Dienstnamen und Eigenschaften für den Cluster. Mithilfe des Naming Service kommunizieren Clients sicher mit allen Knoten im Cluster, um einen Dienstnamen und seinen Speicherort aufzulösen. Anwendungen werden im Cluster verschoben. Ursache können beispielsweise Fehler, ein Ressourcenausgleich oder eine Größenänderung des Clusters sein. Sie können Dienste und Clients entwickeln, die die aktuelle Netzwerkadresse auflösen. Clients rufen die tatsächliche Computer-IP-Adresse und den Port ab, wo er derzeit ausgeführt wird.

Weitere Informationen zu Client- und Dienstkommunikations-APIs, die mit Naming Service zusammenarbeiten, finden Sie unter [Kommunikation mit Diensten](service-fabric-connect-and-communicate-with-services.md).

**Image Store-Dienst**: Jeder Service Fabric-Cluster verfügt über einen Image Store-Dienst, in dem bereitgestellte Anwendungspakete mit Versionsangabe aufbewahrt werden. Kopieren Sie ein Anwendungspaket in den Imagespeicher, und registrieren Sie anschließend den in diesem Anwendungspaket enthaltenen Anwendungstyp. Erstellen Sie nach der Bereitstellung des Anwendungstyps daraus benannte Anwendungen. Sie können die Registrierung eines Anwendungstyps im Imagespeicherdienst aufheben, wenn alle seine benannten Anwendungen gelöscht wurden.

Weitere Informationen zum Imagespeicherdienst finden Sie unter [Grundlegendes zur ImageStoreConnectionString-Einstellung](service-fabric-image-store-connection-string.md).

Weitere Informationen zum Bereitstellen von Anwendungen an den Imagespeicherdienst finden Sie unter [Bereitstellen von Anwendungen](service-fabric-deploy-remove-applications.md).

**Failover-Manager-Dienst**: Jeder Service Fabric-Cluster verfügt über einen Failover-Manager-Dienst, der die folgenden Aktionen ausführt:

 - Er führt Funktionen im Zusammenhang mit Hochverfügbarkeit und Konsistenz der Dienste aus.
 - Er orchestriert Anwendungs- und Clusterupgrades.
 - Er interagiert mit anderen Systemkomponenten.

**Repair Manager-Dienst**: Dies ist ein optionaler Systemdienst, mit dem Reparaturaktionen in einem Cluster auf sichere, automatisierbare und transparente Weise durchgeführt werden können. Der Reparatur-Manager wird für folgende Zwecke verwendet:

   - Azure-Wartungsreparaturen in Azure Service Fabric-Clustern mit der [Dauerhaftigkeitsstufe „Silver“ und „Gold“](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)
   - Reparaturaktionen für die [Anwendung für die Patchorchestrierung](service-fabric-patch-orchestration-application.md)

## <a name="deployment-and-application-models"></a>Bereitstellungs- und Anwendungsmodelle

Für die Bereitstellung Ihrer Dienste müssen Sie beschreiben, wie sie ausgeführt werden sollen. Service Fabric unterstützt drei Bereitstellungsmodelle:

### <a name="resource-model-preview"></a>Ressourcenmodell (Vorschau)

Service Fabric-Ressourcen sind Ressourcen, die in Service Fabric einzeln bereitgestellt werden können, z.B. Anwendungen, Dienste, Netzwerke und Volumes. Ressourcen werden mithilfe einer JSON-Datei definiert, die für einen Clusterendpunkt bereitgestellt werden kann.  Für Service Fabric Mesh wird das Azure-Ressourcenmodellschema verwendet. Ein YAML-Dateischema kann ebenfalls verwendet werden, um Definitionsdateien einfacher zu erstellen. Ressourcen können überall bereitgestellt werden, wo Service Fabric ausgeführt wird. Das Ressourcenmodell ist die einfachste Möglichkeit zum Beschreiben Ihrer Service Fabric-Anwendungen. Sein Hauptaugenmerk liegt auf der einfachen Bereitstellung und Verwaltung von Diensten in Containern. Weitere Informationen finden Sie unter [Einführung in das Service Fabric-Ressourcenmodell](../service-fabric-mesh/service-fabric-mesh-service-fabric-resources.md).

### <a name="native-model"></a>Natives Modell

Das native Anwendungsmodell bietet Ihren Anwendungen eine Low-level-Vollzugriff auf Service Fabric. Anwendungen und Dienste werden als registrierte Typen in XML-Manifestdateien definiert.

Das native Datenmodell unterstützt das Reliable Services- und Reliable Actors-Framework, das Zugriff auf die Service Fabric-Laufzeit-APIs und Clusterverwaltungs-APIs in C# und Java bietet. Das native Modell unterstützt auch beliebige Container und ausführbare Dateien. Es wird nicht in der [Service Fabric Mesh-Umgebung](../service-fabric-mesh/service-fabric-mesh-overview.md) unterstützt.

**Reliable Services**: Eine API zum Erstellen zustandsloser und zustandsbehafteter Dienste. Zustandsbehaftete Dienste speichern ihren Zustand in zuverlässigen Sammlungen (z.B. in einem Wörterbuch oder einer Warteschlange). Sie können auch verschiedene Kommunikationsstapel verknüpfen, z.B. Web-API und Windows Communication Foundation (WCF).

**Reliable Actors**: Eine API zum Erstellen zustandsloser und zustandsbehafteter Objekte über das Programmiermodell mit virtuellen Actors. Dieses Modell ist bei zahlreichen unabhängigen Berechnungs- oder Zustandseinheiten nützlich. Dieses Modell verwendet ein rundenbasiertes Threadingmodell. Daher sollten Sie Code vermeiden, der andere Actors oder Dienste aufruft, weil ein einzelner Actor andere eingehende Anforderungen erst verarbeiten kann, wenn alle ausgehenden Anforderungen abgeschlossen sind.

Sie können auch Ihre vorhandenen Anwendungen unter Service Fabric ausführen:

**Container**:  Service Fabric unterstützt die Bereitstellung von Docker-Containern in Linux- und Windows Server-Containern unter Windows Server 2016 zusammen mit der Unterstützung für den Hyper-V-Isolationsmodus. Im Service Fabric- [Anwendungsmodell](service-fabric-application-model.md)stellt ein Container einen Anwendungshost dar, in dem mehrere Dienstreplikate angeordnet werden. Service Fabric kann alle Container ausführen, und das Szenario ähnelt dem Gastanwendungsszenario, bei dem eine vorhandene Anwendung in einem Container verpackt wird. Darüber hinaus können Sie [Service Fabric-Dienste innerhalb von Containern](service-fabric-services-inside-containers.md) ausführen.

**Ausführbare Gastanwendungsdateien**: Sie können jede Art von Code, z.B. Node.js, Python, Java oder C++, in Azure Service Fabric als Dienst ausführen. In Service Fabric wird auf diese Diensttypen als ausführbare Gastanwendungsdateien verwiesen, die als zustandslose Dienste behandelt werden. Vorteile der Ausführung einer ausführbaren Gastanwendungsdatei in einem Service Fabric-Cluster sind beispielsweise Hochverfügbarkeit, Systemüberwachung, Anwendungslebenszyklusverwaltung, hohe Dichte und Ermittelbarkeit.

Weitere Informationen finden Sie im Artikel [Auswählen eines Frameworks für den Dienst](service-fabric-choose-framework.md) .

### <a name="docker-compose"></a>Docker Compose 

[Docker Compose](https://docs.docker.com/compose/) ist Teil des Docker-Projekts. Service Fabric bietet eingeschränkte Unterstützung für die [Bereitstellung von Anwendungen mit dem Docker Compose-Modell](service-fabric-docker-compose.md).

## <a name="environments"></a>Umgebungen

Service Fabric ist eine Open-Source-Plattformtechnologie, auf der mehrere Dienste und Produkte basieren. Microsoft bietet die folgenden Optionen:

 - **Azure Service Fabric Mesh**: Ein vollständig verwalteter Dienst für die Ausführung von Service Fabric-Anwendungen in Microsoft Azure.
 - **Azure Service Fabric**: Das in Azure gehostete Angebot für Service Fabric-Cluster. Es bietet eine Integration zwischen Service Fabric und der Azure-Infrastruktur sowie Upgrades und eine Konfigurationsverwaltung für Service Fabric-Cluster.
 - **Eigenständiges Service Fabric**: Eine Zusammenstellung von Installations- und Konfigurationstools, um [Service Fabric-Cluster überall bereitzustellen](./service-fabric-deploy-anywhere.md) (lokal oder über einen Cloudanbieter). Nicht von Azure verwaltet.
 - **Service Fabric-Entwicklungscluster**: Eine lokale Entwicklungsumgebung unter Windows, Linux oder Mac für die Entwicklung von Service Fabric-Anwendungen.

## <a name="environment-framework-and-deployment-model-support-matrix"></a>Matrix für die Unterstützung von Umgebungen, Frameworks und Bereitstellungsmodellen

Je nach Umgebung werden unterschiedliche Frameworks und Bereitstellungsmodelle unterstützt. In der folgenden Tabelle werden die unterstützten Kombinationen aus Framework und Bereitstellungsmodell beschrieben.

| Typ der Anwendung | Beschrieben von | Azure Service Fabric Mesh | Azure Service Fabric-Cluster (beliebiges Betriebssystem)| Lokaler Cluster | Eigenständiger Cluster |
|---|---|---|---|---|---|
| Service Fabric Mesh-Anwendungen | Ressourcenmodell (YAML und JSON) | Unterstützt |Nicht unterstützt | Windows – unterstützt, Linux und Mac – nicht unterstützt | Windows – nicht unterstützt |
|Native Service Fabric-Anwendungen | Natives Anwendungsmodell (XML) | Nicht unterstützt| Unterstützt|Unterstützt|Windows – unterstützt|

In der folgenden Tabelle werden die verschiedenen Anwendungsmodelle und Tools beschrieben, die sie für Service Fabric bieten.

| Typ der Anwendung | Beschrieben von | Visual Studio | Eclipse | SFCTL | Azure CLI | PowerShell|
|---|---|---|---|---|---|---|
| Service Fabric Mesh-Anwendungen | Ressourcenmodell (YAML und JSON) | VS 2017 |Nicht unterstützt |Nicht unterstützt | Unterstützt: Nur Mesh-Umgebung | Nicht unterstützt|
|Native Service Fabric-Anwendungen | Natives Anwendungsmodell (XML) | VS 2017 und VS 2015| Unterstützt|Unterstützt|Unterstützt|Unterstützt|

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Service Fabric:

* [Übersicht über Service Fabric](service-fabric-overview.md)
* [Gründe für einen Microservice-Ansatz zum Erstellen von Anwendungen](service-fabric-overview-microservices.md)
* [Anwendungsszenarien](service-fabric-application-scenarios.md)

Weitere Informationen zu Service Fabric Mesh:

* [Übersicht über Service Fabric Mesh](../service-fabric-mesh/service-fabric-mesh-overview.md)
