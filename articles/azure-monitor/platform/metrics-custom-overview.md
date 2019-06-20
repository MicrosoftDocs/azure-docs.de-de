---
title: Benutzerdefinierte Metriken in Azure Monitor
description: Erfahren Sie mehr zu benutzerdefinierten Metriken in Azure Monitor und wie diese modelliert werden.
author: ancav
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 8602027431fdf2c1378834419977606bab5c6921
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60254058"
---
# <a name="custom-metrics-in-azure-monitor"></a>Benutzerdefinierte Metriken in Azure Monitor

Wenn Sie Ressourcen und Anwendungen in Azure bereitstellen, sollten Sie mit dem Erfassen von Telemetriedaten beginnen, um Einblicke in deren Leistung und Integrität zu erhalten. Azure stellt Ihnen einige sofort einsetzbare Metriken zur Verfügung. Diese werden als Standard- oder Plattformmetriken bezeichnet. Allerdings sind sie beschränkt. Möglicherweise möchten Sie einige benutzerdefinierte Leistungsindikatoren oder unternehmensspezifische Metriken sammeln, um eingehendere Erkenntnisse bereitzustellen.
Diese **benutzerdefinierten** Metriken können über Ihre Anwendungstelemetrie, einen auf Ihren Azure-Ressourcen ausgeführten Agent, oder sogar über ein außerhalb des Unternehmens befindliches Überwachungssystem gesammelt und direkt an Azure Monitor übermittelt werden. Nach der Veröffentlichung in Azure Monitor können Sie nach benutzerdefinierten Metriken für Ihre Azure-Ressourcen und -Anwendungen suchen, diese abfragen und Warnungen ausgeben, und zwar parallel zu den von Azure ausgegebenen Standardmetriken.

## <a name="send-custom-metrics"></a>Senden benutzerdefinierter Metriken
Benutzerdefinierte Metriken können über verschiedene Methoden an Azure Monitor übermittelt werden:
- Instrumentieren Ihrer Anwendung mit dem Azure Application Insights SDK und Senden benutzerdefinierter Telemetriedaten an Azure Monitor 
- Installieren der WAD-Erweiterung (Windows Azure-Diagnose) auf Ihrer [Azure-VM](collect-custom-metrics-guestos-resource-manager-vm.md), [VM-Skalierungsgruppe](collect-custom-metrics-guestos-resource-manager-vmss.md), [klassischen VM](collect-custom-metrics-guestos-vm-classic.md) oder Ihrem [klassischen Clouddienst](collect-custom-metrics-guestos-vm-cloud-service-classic.md) und Senden der Leistungsindikatoren an Azure Monitor 
- Installieren des [InfluxData Telegraf-Agents](collect-custom-metrics-linux-telegraf.md) auf Ihrer Azure-Linux-VM und Senden der Metriken mithilfe des Ausgabe-Plug-Ins von Azure Monitor
- Senden von benutzerdefinierten Metriken [direkt an die Azure Monitor-REST-API](../../azure-monitor/platform/metrics-store-custom-rest-api.md), `https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics`.

Wenn Sie benutzerdefinierte Metriken an Azure Monitor senden, muss jeder gemeldete Datenpunkt (oder Wert) die folgenden Informationen enthalten.

### <a name="authentication"></a>Authentication
Zum Übermitteln von benutzerdefinierten Metriken an Azure Monitor muss die Entität über ein gültiges Azure Active Directory-Token (Azure AD) im **Bearer**-Header der Anforderung verfügen. Es gibt einige unterstützte Möglichkeiten, einen gültigen Bearertoken zu erhalten:
1. [Verwaltete Identitäten für Azure-Ressourcen](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) Gibt einer Azure-Ressource (z.B. einer VM) eine Identität. Verwaltete Dienstidentität (MSI) gewährt Ressourcen die Berechtigungen zum Durchführen bestimmter Vorgänge. Zum Beispiel erlaubt sie einer Ressource das Ausgeben von Metriken über sich. Einer Ressource oder ihrer MSI kann die Berechtigung **Überwachungsmetriken veröffentlichen** für eine andere Ressource gewährt werden. Mit dieser Berechtigung kann die MSI auch Metriken für andere Ressourcen ausgeben.
2. [Azure AD-Dienstprinzipal](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) In diesem Szenario können einer AAD-Anwendung oder einem Dienst die Berechtigungen zum Ausgeben von Metriken zu einer Azure-Ressource gewährt werden.
Azure Monitor überprüft das Anwendungstoken mithilfe von öffentlichen Azure AD-Schlüsseln, um die Anforderung zu authentifizieren. Die vorhandene Rolle **Überwachungsmetriken veröffentlichen** verfügt bereits über diese Berechtigung. Sie ist im Azure-Portal verfügbar. Dem Dienstprinzipal kann die Rolle **Überwachungsmetriken veröffentlichen** im erforderlichen Gültigkeitsbereich erteilt werden, je nachdem, für welche Ressourcen er benutzerdefinierte Metriken ausgibt. Dabei kann es sich um ein Abonnement, eine Ressourcengruppe oder eine bestimmte Ressource handeln.

> [!NOTE]  
> Wenn Sie ein Azure AD-Token zum Ausgeben von benutzerdefinierten Metriken anfordern, stellen Sie sicher, dass die Zielgruppe oder Ressource, für die das Token angefordert wird, https://monitoring.azure.com/ ist. Fügen Sie auch unbedingt den nachgestellten Schrägstrich (/) hinzu.

### <a name="subject"></a>Subject
Diese Eigenschaft erfasst, für welche Azure-Ressourcen-ID die benutzerdefinierte Metrik gemeldet wird. Diese Informationen werden in der URL des jeweiligen API-Aufrufs codiert. Jede API kann nur metrische Werte für eine einzelne Azure-Ressource übermitteln.

> [!NOTE]  
> Sie können keine benutzerdefinierten Metriken für die Ressourcen-ID einer Ressourcengruppe oder eines Abonnements ausgeben.
>
>

### <a name="region"></a>Region
Diese Eigenschaft erfasst, in welcher Azure-Region die Ressource, für die Sie Metriken senden, bereitgestellt wird. Metriken müssen an denselben regionalen Endpunkt von Azure Monitor ausgegeben werden wie die Region, in der die Ressource bereitgestellt wird. Benutzerdefinierte Metriken für eine VM, die in der Region „USA, Westen“ bereitgestellt wurde, müssen z.B. an den regionalen Endpunkt „WestUS“ von Azure Monitor gesendet werden. Die Regionsinformationen sind auch in der URL des API-Aufrufs codiert.

> [!NOTE]  
> Während der öffentlichen Vorschau sind benutzerdefinierte Metriken nur in einigen Azure-Regionen verfügbar. Eine Liste der unterstützten Regionen ist in einem späteren Abschnitt dieses Artikels dokumentiert.
>
>

### <a name="timestamp"></a>Timestamp
Jeder Datenpunkt, der an Azure Monitor gesendet wird, muss mit einem Zeitstempel gekennzeichnet sein. Dieser Zeitstempel erfasst den Zeitpunkt (Datum/Uhrzeit), zu dem der Metrikwert gemessen oder erfasst wurde. Azure Monitor akzeptiert metrische Daten mit Zeitstempeln, die bis zu 20 Minuten in der Vergangenheit und bis zu 5 Minuten in der Zukunft liegen. Der Zeitstempel muss im ISO 8601-Format vorliegen.

### <a name="namespace"></a>Namespace
Namespaces sind eine Möglichkeit, ähnliche Metriken zu kategorisieren oder zu gruppieren. Namespaces ermöglichen es Ihnen, Gruppen von Metriken zu isolieren, die unterschiedliche Erkenntnisse oder Leistungsindikatoren erfassen. Sie könnten z.B. über den Namespace **ContosoMemoryMetrics** verfügen, der Metriken zur Arbeitsspeichernutzung verfolgt, um ein Profile Ihrer App zu erstellen. Ein anderer Namespace mit dem Namen **ContosoAppTransaction** könnte alle Metriken über Benutzertransaktionen in Ihrer Anwendung verfolgen.

### <a name="name"></a>NAME
**Name** ist der Name der Metrik, die gemeldet wird. Normalerweise ist der Name ausreichend beschreibend, um zu erkennen, was gemessen wird. Ein Beispiel ist eine Metrik, die die Anzahl der Speicherbytes misst, die auf einem bestimmten virtuellen Computer genutzt werden. Der Metrikname könnte beispielsweise **Verwendete Arbeitsspeicherbytes** lauten.

### <a name="dimension-keys"></a>Dimensionsschlüssel
Eine Dimension ist ein Schlüssel-Wert-Paar, das hilft, zusätzliche Merkmale der zu erfassenden Metrik zu beschreiben. Die zusätzlichen Merkmale ermöglichen die Erfassung weiterer Informationen zur Metrik und damit umfangreichere Erkenntnisse. Die Metrik **Verwendete Arbeitsspeicherbytes** könnte z.B. einen Dimensionsschlüssel namens **Prozess** verwenden, der erfasst, wie viele Bytes des Arbeitsspeichers pro Prozess auf einer VM belegt werden. Mithilfe dieses Schlüssels können Sie die Metrik filtern, um zu sehen, wie viele speicherspezifische Prozesse verwendet werden, oder um die fünf Prozesse mit der höchsten Speicherauslastung zu identifizieren.
Dimensionen sind optional, möglicherweise weisen nicht alle Metriken Dimensionen auf. Eine benutzerdefinierte Metrik kann bis zu 10 Dimensionen umfassen.

### <a name="dimension-values"></a>Dimensionswerte
Wenn Sie einen metrischen Datenpunkt melden, gibt es für jeden Dimensionsschlüssel der zu meldenden Metrik einen zugehörigen Dimensionswert. Angenommen, Sie möchten den von der ContosoApp auf Ihrer VM belegten Arbeitsspeicher melden:

* Der Metrikname wäre **Verwendete Arbeitsspeicherbytes**.
* Der Dimensionsschlüssel wäre **Prozess**.
* Der Dimensionswert wäre **ContosoApp.exe**.

Wenn Sie einen Metrikwert veröffentlichen, können Sie nur einen einzigen Dimensionswert pro Dimensionsschlüssel angeben. Wenn Sie dieselbe Speicherauslastung für mehrere Prozesse auf der VM erfassen, können Sie mehrere Metrikwerte für diesen Zeitstempel melden. Jeder Metrikwert würde einen anderen Dimensionswert für den Dimensionsschlüssel **Prozess** angeben.
Dimensionen sind optional, möglicherweise weisen nicht alle Metriken Dimensionen auf. Wenn ein Metrikbeitrag Dimensionsschlüssel definiert, sind die entsprechenden Dimensionswerte erforderlich.

### <a name="metric-values"></a>Metrikwerte
Azure Monitor speichert alle Metriken in Granularitätsintervallen von einer Minute. Uns ist bewusst, dass eine Metrik im Lauf einer Minute möglicherweise mehrere Male abgefragt werden muss. Ein Beispiel ist die CPU-Auslastung. Eventuell muss sie auch für viele separate Ereignisse gemessen werden. Ein Beispiel sind Wartezeiten bei Anmeldetransaktionen. Sie können die Werte vorab lokal aggregieren und ausgeben, um die Anzahl der Rohwerte einzuschränken, die Sie in Azure Monitor ausgeben und bezahlen müssen:

* **Min**: Der beobachtete Mindestwert aus allen Stichproben und Messungen während der Minute.
* **Max**: Der beobachtete Höchstwert aus allen Stichproben und Messungen während der Minute.
* **Sum**: Die Summe aller beobachteten Werte aus allen Stichproben und Messungen während der Minute.
* **Count**: Die Anzahl der Stichproben und Messungen, die während der Minute durchgeführt wurden.

Wenn es z.B. innerhalb einer bestimmten Minute vier Anmeldetransaktionen für Ihre App gab, ergeben sich daraus möglicherweise die folgenden gemessenen Wartezeiten für jede dieser Transaktionen:

|Transaktion 1|Transaktion 2|Transaktion 3|Transaktion 4|
|---|---|---|---|
|7 ms|4 ms|13 ms|16 ms|
|

Als Ergebnis würde die folgende Metrik an Azure Monitor gemeldet werden:
* Min: 4
* Max: 16
* Sum: 40
* Count: 4

Wenn Ihre Anwendung nicht in der Lage ist, vorab die lokale Aggregierung durchzuführen und jede einzelne Stichprobe oder jedes einzelne Ereignis sofort nach der Erfassung ausgeben muss, können Sie die Rohmesswerte ausgeben. So würden Sie z.B. jedes Mal, wenn in Ihrer App eine Anmeldetransaktion stattgefunden hat, eine Metrik mit nur einer einzigen Messung an Azure Monitor übermitteln. Für eine Anmeldetransaktion, die 12 ms dauerte, würden also die folgenden Metriken ausgegeben werden:
* Min: 12
* Max: 12
* Sum: 12
* Count: 1

Dieser Prozess ermöglicht es Ihnen, mehrere Werte für dieselbe Kombination aus Metrik und Dimension während einer bestimmten Minute auszugeben. Azure Monitor aggregiert dann alle für eine bestimmte Minute übermittelten Rohwerte.

### <a name="sample-custom-metric-publication"></a>Beispiel einer benutzerdefinierten metrischen Veröffentlichung
Im folgenden Beispiel erstellen Sie eine benutzerdefinierte Metrik namens **Memory Bytes in Use** unter dem Metriknamespace **Memory Profile** für einen virtuellen Computer. Die Metrik weist eine einzelne Dimension namens **Process** auf. Für den angegebenen Zeitstempel werden Metrikwerte für zwei unterschiedliche Prozesse ausgegeben:

```json
{
    "time": "2018-08-20T11:25:20-7:00",
    "data": {

      "baseData": {

        "metric": "Memory Bytes in Use",
        "namespace": "Memory Profile",
        "dimNames": [
          "Process"        ],
        "series": [
          {
            "dimValues": [
              "ContosoApp.exe"
            ],
            "min": 10,
            "max": 89,
            "sum": 190,
            "count": 4
          },
          {
            "dimValues": [
              "SalesApp.exe"
            ],
            "min": 10,
            "max": 23,
            "sum": 86,
            "count": 4
          }
        ]
      }
    }
  }
```
> [!NOTE]  
> Application Insights, die Diagnoseerweiterung und der InfluxData Telegraf-Agent sind bereits so konfiguriert, dass sie Metrikwerte für den richtigen regionalen Endpunkt ausgeben und alle oben genannten Eigenschaften in jeder Ausgabe enthalten.
>
>

## <a name="custom-metric-definitions"></a>Benutzerdefinierte Metrikdefinitionen
Es ist nicht erforderlich, eine benutzerdefinierte Metrik in Azure Monitor vorab zu definieren, bevor sie ausgegeben wird. Jeder veröffentlichte Metrikdatenpunkt enthält die Informationen zu Namespace, Name und Dimension. Bei der ersten Ausgabe einer benutzerdefinierten Metrik an Azure Monitor wird also automatisch eine Metrikdefinition erstellt. Diese Metrikdefinition ist dann für jede Ressource sichtbar, für die die Metrik über die Metrikdefinitionen ausgegeben wurde.

> [!NOTE]  
> Azure Monitor unterstützt noch nicht die Definition von **Einheiten** für eine benutzerdefinierte Metrik.

## <a name="using-custom-metrics"></a>Verwenden benutzerdefinierter Metriken
Nachdem benutzerdefinierte Metriken an Azure Monitor übermittelt wurden, können Sie sie über das Azure-Portal durchsuchen und über die Azure Monitor-REST-APIs abfragen. Sie können auch Warnungen erstellen, damit Sie benachrichtigt werden, wenn bestimmte Bedingungen erfüllt sind.
### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>Durchsuchen Ihrer benutzerdefinierten Metriken über das Azure-Portal
1.  Öffnen Sie das [Azure-Portal](https://portal.azure.com).
2.  Wählen Sie den Bereich **Überwachen** aus.
3.  Klicken Sie auf **Metriken**.
4.  Wählen Sie eine Ressource aus, für die Sie benutzerdefinierte Metriken übermittelt haben.
5.  Wählen Sie den Namespace Ihrer benutzerdefinierten Metrik aus.
6.  Wählen Sie die benutzerdefinierte Metrik aus.

## <a name="supported-regions"></a>Unterstützte Regionen
Während der öffentlichen Vorschau ist die Möglichkeit, benutzerdefinierte Metriken zu veröffentlichen, nur in einigen Azure-Regionen verfügbar. Das bedeutet, dass Metriken nur für Ressourcen in einer der unterstützten Regionen veröffentlicht werden können. Die folgende Tabelle enthält die unterstützten Azure-Regionen für benutzerdefinierte Metriken. Außerdem werden die zugehörigen Endpunkte aufgeführt, an denen Metriken für Ressourcen in diesen Regionen veröffentlicht werden sollten:

|Azure-Region|Präfix des regionalen Endpunkts|
|---|---|
|USA (Ost)| https:\//eastus.monitoring.azure.com/ |
|USA Süd Mitte| https:\//southcentralus.monitoring.azure.com/ |
|USA, Westen-Mitte| https:\//westcentralus.monitoring.azure.com/ |
|USA, Westen 2| https:\//westus2.monitoring.azure.com/ |
|Asien, Südosten| https:\//southeastasia.monitoring.azure.com/ |
|Nordeuropa| https:\//northeurope.monitoring.azure.com/ |
|Europa, Westen| https:\//westeurope.monitoring.azure.com/ |

## <a name="quotas-and-limits"></a>Kontingente und Grenzwerte
Azure Monitor erzwingt die folgenden Nutzungslimits für benutzerdefinierte Metriken:

|Category (Kategorie)|Begrenzung|
|---|---|
|Aktive Zeitreihen/Abonnements/Region|50\.000|
|Dimensionsschlüssel pro Metrik|10|
|Zeichenkettenlänge für metrische Namespaces, metrische Namen, Dimensionsschlüssel und Dimensionswerte|256 Zeichen|

Eine aktive Zeitreihe ist definiert als eine beliebige eindeutige Kombination aus Metrik, Dimensionsschlüssel oder Dimensionswert, deren Metrikwerte in den letzten 12 Stunden veröffentlicht wurden.

## <a name="next-steps"></a>Nächste Schritte
Verwenden benutzerdefinierter Metriken von verschiedenen Diensten: 
 - [Virtuelle Computer](collect-custom-metrics-guestos-resource-manager-vm.md)
 - [VM-Skalierungsgruppe](collect-custom-metrics-guestos-resource-manager-vmss.md)
 - [Azure Virtual Machines (klassisch)](collect-custom-metrics-guestos-vm-classic.md)
 - [Virtueller Linux-Computer mit Telegraf-Agent](collect-custom-metrics-linux-telegraf.md)
 - [REST-API](../../azure-monitor/platform/metrics-store-custom-rest-api.md)
 - [Cloud Services (klassisch)](collect-custom-metrics-guestos-vm-cloud-service-classic.md)
 
