---
title: Premium-Tarif für Azure Functions
description: Details und Konfigurationsoptionen (VNet, kein Kaltstart, unbegrenzte Ausführungsdauer) für den Premium-Plan (Premium-Tarif) für Azure Functions.
author: jeffhollan
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: jehollan
ms.custom:
- references_regions
- fasttrack-edit
- devx-track-azurecli
ms.openlocfilehash: 7efcff5709995898a6ec950dfea6450f7e0dd48d
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92736800"
---
# <a name="azure-functions-premium-plan"></a>Premium-Tarif für Azure Functions

Der Premium-Plan für Azure Functions (manchmal auch als elastischer Premium-Plan bezeichnet) ist eine Hostingoption für Funktions-Apps. Der Premium-Plan bietet Features wie VNet-Konnektivität, keinen Kaltstart und Premium-Hardware.  Mehrere Funktions-Apps können im selben Premium-Plan bereitgestellt werden, und der Tarif ermöglicht es Ihnen, die Compute-Instanzgröße, Basisplangröße und maximale Plangröße zu konfigurieren.  Einen Vergleich des Premium-Plans und anderer Pläne und Hostingtypen finden Sie unter [Skalierung und Hosting von Azure Functions](functions-scale.md).

## <a name="create-a-premium-plan"></a>Erstellen eines Premium-Plans

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

Sie können auch einen Premium-Plan erstellen. Verwenden Sie dazu [az functionapp plan create](/cli/azure/functionapp/plan#az-functionapp-plan-create) in der Azure-Befehlszeilenschnittstelle. Im folgenden Beispiel wird ein Plan vom Typ _Elastic Premium 1_ erstellt:

```azurecli-interactive
az functionapp plan create --resource-group <RESOURCE_GROUP> --name <PLAN_NAME> \
--location <REGION> --sku EP1
```

Ersetzen Sie in diesem Beispiel `<RESOURCE_GROUP>` durch Ihre Ressourcengruppe und `<PLAN_NAME>` durch einen Namen für Ihren Plan, der in der Ressourcengruppe eindeutig ist. Geben Sie eine [unterstützte `<REGION>`](https://azure.microsoft.com/global-infrastructure/services/?products=functions) an. Wenn Sie einen Premium-Plan mit Linux-Unterstützung erstellen möchten, schließen Sie die Option `--is-linux` ein.

Nach Erstellung des Plans können Sie mithilfe von [az functionapp create](/cli/azure/functionapp#az-functionapp-create) Ihre Funktions-App erstellen. Im Portal werden Plan und App gleichzeitig erstellt. Ein Beispiel für ein vollständiges Azure CLI-Skript finden Sie unter [Erstellen einer Funktions-App in einem Premium-Tarif](scripts/functions-cli-create-premium-plan.md).

## <a name="features"></a>Features

Die folgenden Features sind für Funktions-Apps verfügbar, die für einen Premium-Plan bereitgestellt werden.

### <a name="always-ready-instances"></a>Jederzeit bereite Instanzen

Wenn für einen bestimmten Tag keine Ereignisse und Ausführungen im Verbrauchsplan aufgeführt werden, wird Ihre App möglicherweise auf null Instanzen abskaliert. Wenn neue Ereignisse eingehen, muss eine neue Instanz zum Ausführen Ihrer App eingerichtet werden.  Das Einrichten einer neuen Instanz kann je nach App einige Zeit dauern.  Diese zusätzliche Wartezeit beim ersten Aufruf wird häufig als App-Kaltstart bezeichnet.

Im Premium-Plan können Sie Ihre App jederzeit bereit für eine angegebene Anzahl von Instanzen vorhalten.  Die maximale Anzahl von jederzeit bereiten Instanzen beträgt 20.  Wenn Ereignisse mit dem Auslösen der App beginnen, werden Sie zuerst an die jederzeit bereiten Instanzen weitergeleitet.  Wenn die Funktion aktiv wird, werden zusätzliche Instanzen als Puffer „vorgewärmt“.  Dieser Puffer verhindert einen Kaltstart für neue Instanzen, die während der Skalierung erforderlich sind.  Diese gepufferten Instanzen werden als [vorgewärmte Instanzen](#pre-warmed-instances) aufgerufen.  Durch die Kombination aus jederzeit bereiten Instanzen und einem vorgewärmten Puffer kann Ihre App einen Kaltstart effektiv vermeiden.

> [!NOTE]
> Jeder Premium-Tarif verfügt immer über mindestens eine aktive (in Rechnung gestellte) Instanz.

Sie können die Anzahl der jederzeit bereiten Instanzen im Azure-Portal konfigurieren, indem Sie Ihre **Funktions-App** auswählen, zur Registerkarte **Plattformfeatures** wechseln und die Optionen zum **Aufskalieren** auswählen. Im Bearbeitungsfenster der Funktions-App sind jederzeit bereite Instanzen für diese App spezifisch.

![Einstellungen für elastisches Skalieren](./media/functions-premium-plan/scale-out.png)

Sie können jederzeit bereite Instanzen für eine App auch mit der Azure CLI konfigurieren.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.minimumElasticInstanceCount=<desired_always_ready_count> --resource-type Microsoft.Web/sites
```

#### <a name="pre-warmed-instances"></a>Vorab aufgewärmte Instanzen

Bei vorab aufgewärmten Instanzen handelt es sich um die Anzahl von Instanzen, die während Skalierungs- und Aktivierungsereignissen als Puffer vorgewärmt werden.  Vorab aufgewärmte Instanzen setzen die Pufferung fort, bis der Grenzwert für maximale horizontale Skalierung erreicht wird.  Die standardmäßige Anzahl vorab aufgewärmten Instanzen ist 1 und sollte für die meisten Szenarien auch 1 bleiben.  Wenn eine App eine lange Aufwärmphase aufweist (z. B. ein benutzerdefiniertes Containerimage), möchten Sie diesen Puffer möglicherweise vergrößern.  Eine vorab aufgewärmte Instanz wird erst dann aktiv, wenn alle aktiven Instanzen ausreichend ausgelastet wurden.

Sehen Sie sich dieses Beispiel an, wie jederzeit bereite Instanzen und vorab aufgewärmte Instanzen zusammenarbeiten.  Für eine Premium-Funktions-App sind fünf jederzeit bereite Instanzen konfiguriert, und die Standardeinstellung bietet eine vorab aufgewärmte Instanz.  Wenn sich die App im Leerlauf befindet und keine Ereignisse als Auslöser vorhanden sind, wird die App für fünf Instanzen bereitgestellt und ausgeführt.  Zu dieser Zeit wird Ihnen keine vorab aufgewärmte Instanz in Rechnung gestellt, da die jederzeit bereiten Instanzen nicht verwendet werden und nicht einmal eine vorab aufgewärmte Instanz zugeordnet ist.

Sobald der erste Trigger eingeht, werden die fünf jederzeit bereiten Instanzen aktiv, und es wird eine vorab aufgewärmte Instanz zugeordnet.  Die App wird nun mit sechs bereitgestellten Instanzen ausgeführt: mit den fünf jetzt aktiven, jederzeit bereiten Instanzen und dem sechsten vorab aufgewärmten und inaktiven Puffer.  Wenn die Ausführungsrate weiterhin zunimmt, sind die fünf aktiven Instanzen schließlich ausgelastet.  Wenn die Plattform eine Skalierung über fünf Instanzen hinaus beschließt, erfolgt diese in die vorab aufgewärmte Instanz.  Wenn dies geschieht, gibt es nun sechs aktive Instanzen, und eine siebte Instanz wird sofort bereitgestellt und füllt den vorab aufgewärmten Puffer auf.  Diese Sequenz der Skalierung und Vorabaufwärmung wird fortgesetzt, bis die maximale Anzahl von Instanzen für die App erreicht ist.  Über den maximalen Wert hinaus werden keine Instanzen vorab aufgewärmt oder aktiviert.

Sie können die Anzahl der vorab aufgewärmten Instanzen für eine App ändern, indem Sie die Azure CLI verwenden.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

#### <a name="maximum-instances-for-an-app"></a>Maximale Anzahl von Instanzen für eine App

Zusätzlich zur [Maximalen Anzahl von Instanzen des Tarifs](#plan-and-sku-settings) können Sie einen maximale Anzahl pro App konfigurieren.  Die maximale Anzahl pro App kann mit der [App-Skalierungsgrenze](./functions-scale.md#limit-scale-out) konfiguriert werden.

### <a name="private-network-connectivity"></a>Private Netzwerkkonnektivität

Für Azure Functions, bereitgestellt in einem Premium-Plan, wird die [neue VNET-Integration für Web-Apps](../app-service/web-sites-integrate-with-vnet.md) genutzt.  Ist diese Integration konfiguriert, kann Ihre App mit Ressourcen in Ihrem VNET oder geschützt über Dienstendpunkte kommunizieren.  IP-Einschränkungen sind ebenfalls für die App verfügbar, um eingehenden Datenverkehr zu beschränken.

Wenn Sie Ihrer Funktions-App in einem Premium-Plan ein Subnetz zuweisen, benötigen Sie ein Subnetz mit genügend IP-Adressen für jede mögliche-Instanz. Wir benötigen einen IP-Block mit mindestens 100 verfügbaren Adressen.

Weitere Informationen finden Sie unter [Integrieren einer Funktions-App in ein Azure Virtual Network](functions-create-vnet.md).

### <a name="rapid-elastic-scale"></a>Schnelle elastische Skalierung

Weitere Compute-Instanzen werden automatisch für Ihre App hinzugefügt. Dazu wird die gleiche Logik für schnelle Skalierung verwendet wie für den Verbrauchsplan. Apps im gleichen App Service-Plan werden unabhängig voneinander auf der Grundlage der Anforderungen der jeweiligen App skaliert. Von Funktions-Apps im gleichen App Service-Plan werden VM-Ressourcen jedoch nach Möglichkeit gemeinsam genutzt, um Kosten zu sparen. Die Anzahl von Apps, die einem virtuellen Computer zugeordnet sind, hängt vom Speicherbedarf der einzelnen Apps sowie von der Größe des virtuellen Computers ab.

Weitere Informationen zur Funktionsweise von Skalierung finden Sie unter [Skalierung und Hosting von Azure Functions](./functions-scale.md#how-the-consumption-and-premium-plans-work).

### <a name="longer-run-duration"></a>Längere Ausführungsdauer

Azure Functions in einem Verbrauchsplan sind auf 10 Minuten für eine einzelne Ausführung beschränkt.  Im Premium-Plan wird die Ausführungsdauer standardmäßig auf 30 Minuten festgelegt, um Endlosausführungen zu verhindern. Sie können jedoch [die Konfiguration von „host.json“ ändern](./functions-host-json.md#functiontimeout), um die Dauer für Apps im Premium-Tarif auf unbegrenzt (garantierte 60 Minuten) festzulegen.

## <a name="plan-and-sku-settings"></a>Plan- und SKU-Einstellungen

Wenn Sie den Plan erstellen, konfigurieren Sie zwei Einstellungen für die Plangröße: die Mindestanzahl von Instanzen (oder Plangröße) und den maximalen Burstgrenzwert.

Wenn Ihre App Instanzen über die jederzeit bereiten Instanzen hinaus erfordert, kann die Aufskalierung fortgesetzt werden, bis die Anzahl von Instanzen den maximalen Burstgrenzwert erreicht.  Instanzen, die sich außerhalb Ihrer Plangröße befinden, werden Ihnen nur in Rechnung gestellt, während sie ausgeführt werden und Ihnen zugeordnet sind. Die Abrechnung erfolgt sekundengenau.  Wir bemühen uns, Ihre App bis auf den definierten maximalen Grenzwert zu skalieren.

Sie können die Plangröße und die Maximalwerte im Azure-Portal konfigurieren, indem Sie die **Aufskalieren** -Optionen im Plan oder eine Funktions-App auswählen, die für diesen Plan bereitgestellt ist (unter **Plattformfeatures** ).

Sie können auch den maximalen Burstgrenzwert über die Azure-Befehlszeilenschnittstelle erhöhen:

```azurecli-interactive
az functionapp plan update -g <resource_group> -n <premium_plan_name> --max-burst <desired_max_burst>
```

Die Mindestanzahl für jeden Plan beträgt mindestens eine Instanz.  Die tatsächliche Mindestanzahl von Instanzen wird basierend auf den jederzeit bereiten Apps im Plan automatisch konfiguriert, die von Apps im Plan angefordert werden.  Wenn App A z. B. fünf jederzeit bereite Instanzen anfordert und App B zwei jederzeit bereite Instanzen im gleichen Plan anfordert, wird die Mindestplangröße als fünf berechnet.  App A wird in allen 5 Instanzen ausgeführt, App B nur in 2 Instanzen.

> [!IMPORTANT]
> Ihnen wird jede Instanz, die entsprechend der Mindestanzahl von Instanzen zugeordnet ist, in Rechnung gestellt, unabhängig davon, ob Funktionen ausgeführt werden oder nicht.

In den meisten Fällen sollte dieser automatisch berechnete Mindestwert ausreichen.  Eine Skalierung über den Mindestwert hinaus erfolgt jedoch nach bestem Bemühen.  Es ist möglich (wenn auch unwahrscheinlich), dass sich die horizontale Skalierung zu einem bestimmten Zeitpunkt verzögert, wenn keine weiteren Instanzen verfügbar sind.  Wenn Sie einen Mindestwert festlegen, der höher als der automatisch berechnete Mindestwert ist, reservieren Sie Instanzen vor dem horizontalen Hochskalieren.

Die Erhöhung des berechneten Mindestwerts für einen Plan kann mit der Azure CLI erfolgen.

```azurecli-interactive
az functionapp plan update -g <resource_group> -n <premium_plan_name> --min-instances <desired_min_instances>
```

### <a name="available-instance-skus"></a>Verfügbare Instanz-SKUs

Wenn Sie Ihren Plan erstellen oder skalieren, können Sie zwischen drei Instanzgrößen wählen.  Ihnen werden die Gesamtanzahl bereitgestellter Kerne und der bereitgestellte Arbeitsspeicher in Rechnung gestellt (sekundengenau für die Zeit, die Ihnen die Instanz zugeordnet ist).  Ihre App kann automatisch nach Bedarf auf mehrere Instanzen aufskaliert werden.

|SKU|Kerne|Arbeitsspeicher|Storage|
|--|--|--|--|
|EP1|1|3,5 GB|250 GB|
|EP2|2|7 GB|250 GB|
|EP3|4|14 GB|250 GB|

### <a name="memory-utilization-considerations"></a>Überlegungen zur Arbeitsspeichernutzung
Das Ausführen auf einem Computer mit mehr Arbeitsspeicher bedeutet nicht immer, dass Ihre Funktions-App den gesamten verfügbaren Arbeitsspeicher auch verwendet.

Beispielsweise wird eine JavaScript-Funktions-App durch das standardmäßige Arbeitsspeicherlimit in „Node.js“ eingeschränkt. Um dieses feste Arbeitsspeicherlimit zu erhöhen, fügen Sie die App-Einstellung `languageWorkers:node:arguments` mit dem Wert `--max-old-space-size=<max memory in MB>` hinzu.

## <a name="region-max-scale-out"></a>Maximale horizontale Hochskalierung der Region

Im Folgenden werden die derzeit unterstützten maximalen Werte für horizontales Hochskalieren für einen einzelnen Tarif in jeder Region und Betriebssystemkonfiguration aufgeführt. Wenn Sie eine Erhöhung anfordern möchten, öffnen Sie ein Supportticket.

Sehen Sie sich die gesamte regionale Verfügbarkeit von Functions an: [Azure.com](https://azure.microsoft.com/global-infrastructure/services/?products=functions)

|Region| Windows | Linux |
|--| -- | -- |
|Australien, Mitte| 100 | Nicht verfügbar. |
|Australien, Mitte 2| 100 | Nicht verfügbar. |
|Australien (Osten)| 100 | 20 |
|Australien, Südosten | 100 | 20 |
|Brasilien Süd| 100 | 20 |
|Kanada, Mitte| 100 | 20 |
|USA (Mitte)| 100 | 20 |
|China, Osten 2| 100 | 20 |
|China, Norden 2| 100 | 20 |
|Asien, Osten| 100 | 20 |
|East US | 100 | 20 |
|USA (Ost) 2| 100 | 20 |
|Frankreich, Mitte| 100 | 20 |
|Deutschland, Westen-Mitte| 100 | Nicht verfügbar. |
|Japan, Osten| 100 | 20 |
|Japan, Westen| 100 | 20 |
|Korea, Mitte| 100 | 20 |
|Korea, Süden| Nicht verfügbar | 20 |
|USA Nord Mitte| 100 | 20 |
|Nordeuropa| 100 | 20 |
|Norwegen, Osten| 100 | 20 |
|USA Süd Mitte| 100 | 20 |
|Indien (Süden) | 100 | Nicht verfügbar. |
|Asien, Südosten| 100 | 20 |
|Schweiz, Norden| 100 | Nicht verfügbar. |
|Schweiz, Westen| 100 | Nicht verfügbar. |
|UK, Süden| 100 | 20 |
|UK, Westen| 100 | 20 |
|US Gov Arizona| 100 | 20 |
|US Government, Virginia| 100 | 20 |
|US Nat East| 100 | Nicht verfügbar. |
|US Nat West| 100 | Nicht verfügbar. |
|Europa, Westen| 100 | 20 |
|Indien, Westen| 100 | 20 |
|USA, Westen-Mitte| 100 | 20 |
|USA (Westen)| 100 | 20 |
|USA, Westen 2| 100 | 20 |

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Grundlegendes zu Skalierung und Hosting von Azure Functions](functions-scale.md)
