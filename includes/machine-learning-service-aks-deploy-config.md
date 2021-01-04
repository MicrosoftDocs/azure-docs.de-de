---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 95422ace63c9f79583de5a2c86d777ad4d0a8834
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026665"
---
Die Einträge im Dokument `deploymentconfig.json` werden den Parametern für [AksWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py) zugeordnet. In der folgenden Tabelle wird die Zuordnung zwischen den Entitäten im JSON-Dokument und den Parametern für die Methode beschrieben:

| JSON-Entität | Methodenparameter | BESCHREIBUNG |
| ----- | ----- | ----- |
| `computeType` | Nicht verfügbar | Das Computeziel. Für AKS muss der Wert `aks` sein. |
| `autoScaler` | Nicht verfügbar | Enthält Konfigurationselemente für die automatische Skalierung. Weitere Informationen finden Sie in der Tabelle zur automatischen Skalierung. |
| &emsp;&emsp;`autoscaleEnabled` | `autoscale_enabled` | Gibt an, ob die automatische Skalierung für den Webdienst aktiviert werden soll. Wenn `numReplicas` = `0`, dann `True`; andernfalls `False`. |
| &emsp;&emsp;`minReplicas` | `autoscale_min_replicas` | Die Mindestanzahl von Containern, die bei der automatischen Skalierung dieses Webdiensts verwendet werden sollen. Standardwert: `1`. |
| &emsp;&emsp;`maxReplicas` | `autoscale_max_replicas` | Die maximale Anzahl von Containern, die bei der automatischen Skalierung dieses Webdiensts verwendet werden sollen. Standardwert: `10`. |
| &emsp;&emsp;`refreshPeriodInSeconds` | `autoscale_refresh_seconds` | Gibt an, wie oft die automatische Skalierung versucht, diesen Webdienst zu skalieren. Standardwert: `1`. |
| &emsp;&emsp;`targetUtilization` | `autoscale_target_utilization` | Die Zielauslastung (in Prozent), die von der automatischen Skalierung für diesen Webdienst beibehalten werden soll. Standardwert: `70`. |
| `dataCollection` | Nicht verfügbar | Enthält Konfigurationselemente für die Datensammlung. |
| &emsp;&emsp;`storageEnabled` | `collect_model_data` | Gibt an, ob die Modelldatensammlung für den Webdienst aktiviert werden soll. Standardwert: `False`. |
| `authEnabled` | `auth_enabled` | Gibt an, ob die Schlüsselauthentifizierung für den Webdienst aktiviert werden soll. Weder `tokenAuthEnabled` noch `authEnabled` dürfen den Wert `True` aufweisen. Standardwert: `True`. |
| `tokenAuthEnabled` | `token_auth_enabled` | Gibt an, ob die Tokenauthentifizierung für den Webdienst aktiviert werden soll. Weder `tokenAuthEnabled` noch `authEnabled` dürfen den Wert `True` aufweisen. Standardwert: `False`. |
| `containerResourceRequirements` | Nicht verfügbar | Der Container für die CPU- und Arbeitsspeicherentitäten. |
| &emsp;&emsp;`cpu` | `cpu_cores` | Die Anzahl der CPU-Kerne, die für diesen Webdienst zuzuordnen sind. Standardwert: `0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | Der Arbeitsspeicherumfang (in GB), der für diesen Webdienst zugeordnet werden soll. Standardwert: `0.5`. |
| `appInsightsEnabled` | `enable_app_insights` | Gibt an, ob für den Webdienst die Application Insights-Protokollierung aktiviert werden soll. Standardwert: `False`. |
| `scoringTimeoutMs` | `scoring_timeout_ms` | Ein Zeitlimit zum Erzwingen der Bewertung von Aufrufen des Webdiensts. Standardwert: `60000`. |
| `maxConcurrentRequestsPerContainer` | `replica_max_concurrent_requests` | Die maximale Anzahl gleichzeitiger Anforderungen pro Knoten für diesen Webdienst. Standardwert: `1`. |
| `maxQueueWaitMs` | `max_request_wait_time` | Die maximale Zeit, die eine Anforderung in der Warteschlange (in Millisekunden) verbleibt, bevor der Fehler 503 zurückgegeben wird. Standardwert: `500`. |
| `numReplicas` | `num_replicas` | Die Anzahl der Container, die für diesen Webdienst zuzuordnen sind. Für dieses Feld gibt es keinen Standardwert. Wenn dieser Parameter nicht festgelegt ist, wird die automatische Skalierung standardmäßig aktiviert. |
| `keys` | Nicht verfügbar | Enthält Konfigurationselemente für Schlüssel. |
| &emsp;&emsp;`primaryKey` | `primary_key` | Ein primärer Authentifizierungsschlüssel, der für diesen Webdienst verwendet werden soll. |
| &emsp;&emsp;`secondaryKey` | `secondary_key` | Ein sekundärer Authentifizierungsschlüssel, der für diesen Webdienst verwendet werden soll. |
| `gpuCores` | `gpu_cores` | Die Anzahl der GPU-Kerne (containerbezogenes Replikat), die diesem Webdienst zuzuordnen sind. Der Standardwert ist 1. Unterstützt nur ganze Zahlen. |
| `livenessProbeRequirements` | Nicht verfügbar | Enthält Konfigurationselemente für die Anforderungen an Livetests. |
| &emsp;&emsp;`periodSeconds` | `period_seconds` | Gibt an, wie häufig (in Sekunden) ein Livetest durchgeführt werden soll. Der Standardwert ist 10 Sekunden. Der Mindestwert ist 1. |
| &emsp;&emsp;`initialDelaySeconds` | `initial_delay_seconds` | Die Anzahl der Sekunden, die nach dem Start des Containers vergehen, bevor Livetests initiiert werden. Der Standardwert ist 310. |
| &emsp;&emsp;`timeoutSeconds` | `timeout_seconds` | Die Anzahl der Sekunden, nach denen das Zeitlimit für den Livetest überschritten ist. Der Standardwert ist 2 Sekunden. Der Mindestwert ist 1. |
| &emsp;&emsp;`successThreshold` | `success_threshold` | Die Anzahl der Erfolge, die mindestens aufeinander folgen müssen, damit ein Livetest nach einem Fehler wieder als erfolgreich betrachtet wird. Der Standardwert lautet 1. Der Mindestwert ist 1. |
| &emsp;&emsp;`failureThreshold` | `failure_threshold` | Wenn ein Pod startet und der Livetest fehlschlägt, versucht Kubernetes, die Zeiten des Parameters FailureThreshold zu verwenden, bevor aufgegeben wird. Der Standardwert ist 3. Der Mindestwert ist 1. |
| `namespace` | `namespace` | Der Kubernetes-Namespace, in dem der Webdienst bereitgestellt wird. Bis zu 63 alphanumerische Zeichen in Kleinbuchstaben (a–z, 0–9) und Bindestriche (-). Bindestriche dürfen nicht als erstes und letztes Zeichen verwendet werden. |

Der folgende JSON-Code ist ein Beispiel für eine Bereitstellungskonfiguration für die Verwendung mit der CLI:

```json
{
    "computeType": "aks",
    "autoScaler":
    {
        "autoscaleEnabled": true,
        "minReplicas": 1,
        "maxReplicas": 3,
        "refreshPeriodInSeconds": 1,
        "targetUtilization": 70
    },
    "dataCollection":
    {
        "storageEnabled": true
    },
    "authEnabled": true,
    "containerResourceRequirements":
    {
        "cpu": 0.5,
        "memoryInGB": 1.0
    }
}
```