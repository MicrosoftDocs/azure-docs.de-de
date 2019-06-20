---
title: Azure Service Fabric-CLI – sfctl chaos schedule | Microsoft-Dokumentation
description: Beschreibt die sfctl chaos-Befehle der Service Fabric-CLI.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: dc3dd06b5feac1f66598cd65fa79f447a1bbd9be
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60837503"
---
# <a name="sfctl-chaos-schedule"></a>sfctl chaos schedule
Ruft den Zeitplan des Chaostests ab und legt diesen fest.

## <a name="commands"></a>Befehle

|Get-Help|BESCHREIBUNG|
| --- | --- |
| get | Ruft den Chaostestzeitplan ab, der definiert, wann und wie der Chaostest ausgeführt werden soll. |
| set | Legt den von Chaos verwendeten Zeitplan fest. |

## <a name="sfctl-chaos-schedule-get"></a>sfctl chaos schedule get
Ruft den Chaostestzeitplan ab, der definiert, wann und wie der Chaostest ausgeführt werden soll.

Ruft die Version des verwendeten Chaostestzeitplans und den Chaostestzeitplan ab, der definiert, wann und wie der Chaostest ausgeführt werden soll.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-chaos-schedule-set"></a>sfctl chaos schedule set
Legt den von Chaos verwendeten Zeitplan fest.

Der Chaostest wird automatisch basierend auf dem Chaostestzeitplan ausgeführt. Die Version im angegebenen Eingabezeitplan muss mit der Version des Chaostestzeitplans auf dem Server übereinstimmen. Wenn die angegebene Version nicht der Version auf dem Server entspricht, wird der Chaostestzeitplan nicht aktualisiert. Wenn die angegebene Version der Version auf dem Server entspricht, wird der Chaostestzeitplan aktualisiert und die Version des Chaostestzeitplans auf dem Server erhöht sich um einen Schritt und springt nach 2.147.483.647 Schritten auf 0 zurück. Wenn der Chaostest während der Durchführung dieses Aufrufs ausgeführt wird, tritt beim Aufruf ein Fehler auf.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --chaos-parameters-dictionary | JSON-codierte Liste, die eine Zuordnung von Zeichenfolgennamen für die ChaosParameters-Klasse darstellt, die bei vom Jobs-Argument verwendet werden soll. |
| --expiry-date-utc | Datum und Uhrzeit, wann die Verwendung des Chaostestzeitplans für die Planung des Chaostests beendet werden soll.  Standardwert\: „9999-12-31T23\:59\:59.999Z“. |
| --jobs | JSON-codierte Liste von ChaosScheduleJobs, die angibt, wann der Chaostest ausgeführt werden soll und welche Parameter beim Chaostest ausgeführt werden sollen. |
| --start-date-utc | Datum und Uhrzeit, wann der Zeitplan für die Planung des Chaostests gestartet werden soll.  Standardwert\: „1601-01-01T00\:00\:00.000Z“. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |
| --version | Die Versionsnummer des Zeitplans. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

### <a name="examples"></a>Beispiele

Der folgende Befehl legt einen Zeitplan fest (vorausgesetzt, dass der aktuelle Zeitplan Version 0 aufweist), der am 01.01.2016 beginnt und am 01.01.2038 abläuft und den Chaostest rund um die Uhr ausführt. Der Chaostest wird auf dem Cluster für diesen Zeitpunkt geplant.

    sfctl chaos schedule set --version 0 --start-date-utc "2016-01-01T00:00:00.000Z" --expiry-date-utc "2038-01-01T00:00:00.000Z"
    --chaos-parameters-dictionary
    [
    {
        "Key":"adhoc",
        "Value":{
            "MaxConcurrentFaults":3,
            "EnableMoveReplicaFaults":true,
            "ChaosTargetFilter":{
                "NodeTypeInclusionList":[
                "N0010Ref",
                "N0020Ref",
                "N0030Ref",
                "N0040Ref",
                "N0050Ref"
                ]
            },
            "MaxClusterStabilizationTimeoutInSeconds":60,
            "WaitTimeBetweenIterationsInSeconds":15,
            "WaitTimeBetweenFaultsInSeconds":30,
            "TimeToRunInSeconds":"600",
            "Context":{
                "Map":{
                "test":"value"
                }
            },
            "ClusterHealthPolicy":{
                "MaxPercentUnhealthyNodes":0,
                "ConsiderWarningAsError":true,
                "MaxPercentUnhealthyApplications":0
            }
        }
    }
    ]
    --jobs
    [
    {
        "ChaosParameters":"adhoc",
        "Days":{
            "Sunday":true,
            "Monday":true,
            "Tuesday":true,
            "Wednesday":true,
            "Thursday":true,
            "Friday":true,
            "Saturday":true
        },
        "Times":[
            {
                "StartTime":{
                "Hour":0,
                "Minute":0
                },
                "EndTime":{
                "Hour":23,
                "Minute":59
                }
            }
        ]
    }
    ]


## <a name="next-steps"></a>Nächste Schritte
- [Einrichten](service-fabric-cli.md) der Service Fabric-Befehlszeilenschnittstelle
- Informationen zum Verwenden der Service Fabric-Befehlszeilenschnittstelle mit den [Beispielskripts](/azure/service-fabric/scripts/sfctl-upgrade-application)
