---
title: Bereitstellen des Durchsatzes für Azure Cosmos-Container und -Datenbanken
description: Hier erfahren Sie, wie Sie bereitgestellten Durchsatz für Ihre Azure Cosmos-Container und -Datenbanken festlegen.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rimman
ms.openlocfilehash: 598a1562127a67c78f67cdd02b00d83d4a606739
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65953547"
---
# <a name="provision-throughput-on-containers-and-databases"></a>Bereitstellen des Durchsatzes für Container und Datenbanken

Eine Azure Cosmos-Datenbank ist eine Verwaltungseinheit für eine Gruppe von Containern. Eine Datenbank besteht aus einem Satz von schemaunabhängigen Containern. Ein Azure Cosmos-Container ist die Skalierungseinheit für Durchsatz und Speicher. Ein Container wird horizontal über eine Gruppe von Computern innerhalb einer Azure-Region partitioniert und auf alle Azure-Regionen, die Ihrem Azure Cosmos-Konto zugeordnet sind, verteilt.

Mit Azure Cosmos DB können Sie Durchsatz in zwei Größen bereitstellen:
 
- Azure Cosmos-Container
- Azure Cosmos-Datenbanken

## <a name="set-throughput-on-a-container"></a>Festlegen des Durchsatzes für einen Container  

Der für einen Azure Cosmos-Container bereitgestellte Durchsatz ist ausschließlich für diesen Container reserviert. Der Container erhält den bereitgestellten Durchsatz durchgängig. Der bereitgestellte Durchsatz für einen Container wird finanziell durch SLAs gesichert. Informationen zum Konfigurieren des Durchsatzes für einen Container finden Sie unter [Bereitstellen von Durchsatz für einen Azure Cosmos-Container](how-to-provision-container-throughput.md).

Das Festlegen von bereitgestelltem Durchsatz für einen Container ist die am häufigsten genutzte Option. Sie können den Durchsatz für einen Container elastisch skalieren, indem Sie mithilfe von [Anforderungseinheiten (Request Units, RUs)](request-units.md) eine beliebige Menge an Durchsatz bereitstellen. 

Der für einen Azure Cosmos-Container bereitgestellte Durchsatz wird gleichmäßig auf alle logischen Partitionen des Containers aufgeteilt. Sie können den Durchsatz für logische Partitionen nicht selektiv angeben. Da mindestens eine logische Partition eines Containers auf einer physischen Partition gehostet wird, gehören die physischen Partitionen ausschließlich zu dem Container und unterstützen den Durchsatz für den Container. 

Wenn die Workload in einer logischen Partition mehr als den Durchsatz verbraucht, der dieser logischen Partition zugewiesen wurde, werden Ihre Vorgänge begrenzt. Bei einer Ratenbegrenzung können Sie entweder den bereitgestellten Durchsatz für den gesamten Container erhöhen oder die Vorgänge wiederholen. Weitere Informationen zur Partitionierung finden Sie unter [Logische Partitionen](partition-data.md).

Es wird empfohlen, den Durchsatz auf Containerebene zu konfigurieren, wenn Sie eine garantierte Leistung für den Container benötigen.

Die folgende Abbildung zeigt, wie eine physische Partition eine bzw. mehrere logische Partitionen eines Containers hostet:

![Physische Partition](./media/set-throughput/resource-partition.png)

## <a name="set-throughput-on-a-database"></a>Festlegen des Durchsatzes für eine Datenbank

Wenn Sie Durchsatz für eine Azure Cosmos-Datenbank bereitstellen, wird der Durchsatz von allen Containern in der Datenbank gemeinsam genutzt. Ausnahme: Sie haben einen bereitgestellten Durchsatz für bestimmte Container in der Datenbank angegeben. Die gemeinsame Nutzung des auf Datenbankebene bereitgestellten Durchsatzes durch die Container entspricht dem Hosten einer Datenbank in einem Computercluster. Da alle Container in einer Datenbank die auf einem Computer verfügbaren Ressourcen gemeinsam nutzen, erhalten Sie natürlich keine vorhersagbare Leistung für einen bestimmten Container. Informationen zum Konfigurieren des bereitgestellten Durchsatzes für eine Datenbank finden Sie unter [Bereitstellen von Durchsatz für eine Azure Cosmos-Datenbank](how-to-provision-database-throughput.md).

Mit dem Festlegen des Durchsatzes für eine Azure Cosmos-Datenbank wird sichergestellt, dass Sie zu jeder Zeit den bereitgestellten Durchsatz für diese Datenbank erhalten. Da alle Container in der Datenbank den bereitgestellten Durchsatz gemeinsam nutzen, garantiert Azure Cosmos DB keinen vorhersagbaren Durchsatz für einen bestimmten Container in der Datenbank. Der Anteil des Durchsatzes, den ein bestimmter Container erhält, hängt von den folgenden Faktoren ab:

* Anzahl von Containern
* Auswahl der Partitionsschlüssel für verschiedene Container
* Verteilung der Workload auf die verschiedenen logischen Partitionen der Container 

Es wird empfohlen, den Durchsatz für eine Datenbank zu konfigurieren, wenn der Durchsatz von mehreren Containern gemeinsam genutzt werden soll, Sie aber keinen spezifischen Durchsatz für bestimmte Container bereitstellen möchten. 

In den folgenden Beispielen wird erläutert, wo das Bereitstellen von Durchsatz auf Datenbankebene empfehlenswert ist:

* Die gemeinsame Nutzung des für eine Datenbank bereitgestellten Durchsatzes durch eine Gruppe von Containern ist bei mehrinstanzenfähigen Anwendungen nützlich. Jeder Benutzer kann durch einen eigenen Azure Cosmos-Container dargestellt werden.

* Die gemeinsame Nutzung des bereitgestellten Durchsatzes einer Datenbank durch eine Gruppe von Containern ist nützlich, wenn Sie eine NoSQL-Datenbank (z. B. MongoDB oder Cassandra), die in einem Cluster von virtuellen Computern oder auf lokalen physischen Servern gehostet wird, zu Azure Cosmos DB migrieren. Stellen Sie sich den bereitgestellten Durchsatz, der für Ihre Azure Cosmos-Datenbank konfiguriert wurde, als logische Entsprechung (jedoch kostengünstiger und flexibler) zur Computekapazität des MongoDB- oder Cassandra-Clusters vor.  

Alle in einer Datenbank erstellten Container mit bereitgestelltem Durchsatz müssen mit einem [Partitionsschlüssel](partition-data.md) erstellt werden. Der einem Container innerhalb einer Datenbank bereitgestellte Durchsatz wird zu jedem Zeitpunkt auf alle logischen Partitionen des Containers aufgeteilt. Wenn Ihre Container den für eine Datenbank konfigurierten bereitgestellten Durchsatz gemeinsam nutzen, können Sie den Durchsatz nicht selektiv einem bestimmten Container oder einer logischen Partition zuordnen. 

Wenn die Workload in einer logischen Partition mehr als den Durchsatz verbraucht, der der jeweiligen logischen Partition zugewiesen wurde, werden Ihre Vorgänge begrenzt. Bei einer Ratenbegrenzung können Sie entweder den Durchsatz für die gesamte Datenbank erhöhen oder die Vorgänge wiederholen. Weitere Informationen zur Partitionierung finden Sie unter [Logische Partitionen](partition-data.md).

Sie können mehrere logische Partitionen, die zu verschiedenen, den für eine Datenbank bereitgestellten Durchsatz gemeinsam nutzenden Containern gehören, in einer einzelnen physischen Partition hosten. Während eine einzelne logische Partition eines Containers immer zum Gültigkeitsbereich einer physischen Partition gehört, können *L* logische Partitionen für *C* Container, die den bereitgestellten Durchsatz einer Datenbank gemeinsam nutzen, auf *R* physischen Partitionen gehostet werden. 

Die folgende Abbildung zeigt, wie eine physische Partition eine bzw. mehrere logische Partitionen hosten kann, die zu unterschiedlichen Containern innerhalb einer Datenbank gehören:

![Physische Partition](./media/set-throughput/resource-partition2.png)

## <a name="set-throughput-on-a-database-and-a-container"></a>Festlegen des Durchsatzes für eine Datenbank und einen Container

Sie können die beiden Modelle kombinieren. Es ist erlaubt, Durchsatz sowohl auf Datenbank- als auch auf Containerebene bereitzustellen. Das folgende Beispiel zeigt die Vorgehensweise bei der Bereitstellung von Durchsatz für eine Azure Cosmos-Datenbank und einen Container:

* Sie können eine Azure Cosmos-Datenbank mit dem Namen *Z* erstellen und für diese als Durchsatz *K* RUs bereitstellen. 
* Als Nächstes erstellen Sie die fünf Container *A*, *B*, *C*, *D* und *E* innerhalb der Datenbank.
* Sie können explizit *P* RUs des bereitgestellten Durchsatzes für den Container mit dem Namen *B* konfigurieren.
* Der Durchsatz von *K* RUs ist für die vier Container *A*, *C*, *D*, und *E* freigegeben. Die genaue für *A*, *C*, *D* oder *E* verfügbare Durchsatzmenge variiert. Es gibt keine SLAs für die einzelnen Containerdurchsätze.
* Für den Container *B* wird jederzeit ein Durchsatz von *P* RUs sichergestellt. Er wird durch SLAs abgedeckt.

## <a name="update-throughput-on-a-database-or-a-container"></a>Aktualisieren des Durchsatzes für eine Datenbank oder einen Container

Nachdem Sie einen Azure Cosmos-Container oder eine Datenbank erstellt haben, können Sie den bereitgestellten Durchsatz aktualisieren. Es gibt keine Beschränkung für den bereitgestellten Maximaldurchsatz, den Sie in der Datenbank oder dem Container konfigurieren können. Der bereitgestellte Mindestdurchsatz hängt von den folgenden Faktoren ab: 

* Maximale Datengröße, die jemals im Container gespeichert wird
* Maximaler Durchsatz, der jemals im Container bereitgestellt wird
* Maximale Anzahl von Azure Cosmos-Containern, die jemals in einer Datenbank mit gemeinsam genutztem Durchsatz erstellt werden 

Sie können den Mindestdurchsatz eines Containers oder einer Datenbank programmgesteuert mithilfe der SDKs abrufen oder den Wert im Azure-Portal anzeigen. Bei Verwendung des .NET SDK ermöglicht Ihnen die [DocumentClient.ReplaceOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync?view=azure-dotnet)-Methode das Skalieren des Werts für den bereitgestellten Durchsatz. Bei Verwendung des Java SDK ermöglicht Ihnen die [RequestOptions.setOfferThroughput](sql-api-java-samples.md#offer-examples)-Methode das Skalieren des Werts für den bereitgestellten Durchsatz. 

Bei Verwendung des .NET SDK ermöglicht Ihnen die [DocumentClient.ReadOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.readofferasync?view=azure-dotnet)-Methode das Abrufen des Mindestdurchsatzes eines Containers oder einer Datenbank. 

Sie können den bereitgestellten Durchsatz eines Containers oder einer Datenbank jederzeit skalieren. 

## <a name="comparison-of-models"></a>Vergleich der Modelle

|**Parameter**  |**Bereitgestellter Durchsatz für eine Datenbank**  |**Bereitgestellter Durchsatz für einen Container**|
|---------|---------|---------|
|Minimale RUs |400 (Nach den ersten vier Containern benötigt jeder weitere Container mindestens 100 RUs pro Sekunde.) |400|
|Minimale RUs pro Container|100|400|
|Erforderliche minimale RUs für die Nutzung von 1 GB Speicher|40|40|
|Maximale RUs|Unbegrenzt, in der Datenbank|Unbegrenzt, im Container|
|RUs zugewiesen oder verfügbar für einen bestimmten Container|Keine Garantien. Die einem bestimmten Container zugewiesenen RUs hängen von den Eigenschaften ab. Bei diesen Eigenschaften kann es sich um die Partitionsschlüssel der Container, die den Durchsatz gemeinsam nutzen, um die Verteilung der Workload oder um die Anzahl von Containern handeln. |Alle RUs, die für den Container konfiguriert wurden, sind ausschließlich für diesen Container reserviert.|
|Maximale Speicherkapazität für einen Container|Unbegrenzt.|Unbegrenzt.|
|Maximaler Durchsatz pro logischer Partition eines Containers|10.000 RUs|10.000 RUs|
|Maximale Speicherkapazität (Daten und Index) pro logischer Partition eines Containers|10 GB|10 GB|

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über [logische Partitionen](partition-data.md).
* Erfahren Sie mehr über das [Bereitstellen von Durchsatz für einen Azure Cosmos-Container](how-to-provision-container-throughput.md).
* Erfahren Sie mehr über das [Bereitstellen des Durchsatzes für eine Datenbank in Azure Cosmos](how-to-provision-database-throughput.md).

