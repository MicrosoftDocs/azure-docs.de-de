---
title: Senden benutzerdefinierter Metriken für eine Azure-Ressource an den Azure Monitor-Metrikspeicher mithilfe einer REST-API
description: Senden benutzerdefinierter Metriken für eine Azure-Ressource an den Azure Monitor-Metrikspeicher mithilfe einer REST-API
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: aa842979bf86410e9dab97d6209f336eb6b02bd3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60253867"
---
# <a name="send-custom-metrics-for-an-azure-resource-to-the-azure-monitor-metric-store-by-using-a-rest-api"></a>Senden benutzerdefinierter Metriken für eine Azure-Ressource an den Azure Monitor-Metrikspeicher mithilfe einer REST-API

In diesem Artikel erfahren Sie, wie Sie benutzerdefinierte Metriken für Azure-Ressourcen über eine REST-API an den Metrikenspeicher von Azure Monitor senden können. Sobald sich die Metriken in Azure Monitor befinden, können Sie die gleichen Aktionen für sie ausführen wie für die Standardmetriken. Beispiele wären etwa das Erstellen von Diagrammen und Warnungen sowie das Weiterleiten an andere externe Tools.  

>[!NOTE]  
>Die REST-API gestattet nur das Senden von benutzerdefinierten Metriken für Azure-Ressourcen. Sie können mit [Application Insights](../../azure-monitor/app/api-custom-events-metrics.md) Metriken für Ressourcen in verschiedenen Umgebungen oder lokal senden.    


## <a name="create-and-authorize-a-service-principal-to-emit-metrics"></a>Erstellen und Autorisieren eines Dienstprinzipals zur Ausgabe von Metriken 

Erstellen Sie anhand der Anleitung unter [Erstellen eines Dienstprinzipals](../../active-directory/develop/howto-create-service-principal-portal.md) einen Dienstprinzipal in Ihrem Azure Active Directory-Mandanten. 

Beachten Sie dabei Folgendes: 

- Sie können für die Anmelde-URL eine beliebige URL eingeben.  
- Erstellen Sie einen neuen geheimen Clientschlüssel für diese App.  
- Speichern Sie den Schlüssel und die Client-ID für die Verwendung in späteren Schritten.  

Erteilen Sie der App, die im Rahmen des ersten Schritts erstellt wurde, Berechtigungen vom Typ „Überwachungsmetriken veröffentlichen“ für die Ressource, für die Sie Metriken ausgeben möchten. Wenn Sie planen, die App zu verwenden, um benutzerdefinierte Metriken für viele Ressourcen auszugeben, können Sie diese Berechtigungen auf der Ebene der Ressourcengruppe oder des Abonnements vergeben. 

## <a name="get-an-authorization-token"></a>Abrufen eines Autorisierungstokens
Öffnen Sie eine Eingabeaufforderung, und führen Sie den folgenden Befehl aus:

```shell
curl -X POST https://login.microsoftonline.com/<yourtenantid>/oauth2/token -F "grant_type=client_credentials" -F "client_id=<insert clientId from earlier step>" -F "client_secret=<insert client secret from earlier step>" -F "resource=https://monitoring.azure.com/"
```
Speichern Sie das Zugriffstoken aus der Antwort.

![Zugriffstoken](./media/metrics-store-custom-rest-api/accesstoken.png)

## <a name="emit-the-metric-via-the-rest-api"></a>Ausgeben der Metrik über die REST-API 

1. Fügen Sie den folgenden JSON-Code in eine Datei ein, und speichern Sie sie als  **custommetric.json** auf Ihrem lokalen Computer. Aktualisieren Sie den Zeitparameter in der JSON-Datei: 
    
    ```json
    { 
        "time": "2018-09-13T16:34:20", 
        "data": { 
            "baseData": { 
                "metric": "QueueDepth", 
                "namespace": "QueueProcessing", 
                "dimNames": [ 
                  "QueueName", 
                  "MessageType" 
                ], 
                "series": [ 
                  { 
                    "dimValues": [ 
                      "ImagesToProcess", 
                      "JPEG" 
                    ], 
                    "min": 3, 
                    "max": 20, 
                    "sum": 28, 
                    "count": 3 
                  } 
                ] 
            } 
        } 
    } 
    ``` 

1. Geben Sie im Eingabeaufforderungsfenster die Metrikdaten an: 
   - **azureRegion**: Muss der Bereitstellungsregion der Ressource entsprechen, für die Sie Metriken ausgeben möchten. 
   - **resourceID**:  Die Ressourcen-ID der Azure-Ressource, mit der Sie die Metrik nachverfolgen.  
   - **AccessToken**: Fügen Sie das Token ein, das Sie zuvor abgerufen haben.

     ```Shell 
     curl -X POST https://<azureRegion>.monitoring.azure.com/<resourceId>/metrics -H "Content-Type: application/json" -H "Authorization: Bearer <AccessToken>" -d @custommetric.json 
     ```
1. Ändern Sie den Zeitstempel und die Werte in der JSON-Datei. 
1. Wiederholen Sie die beiden vorherigen Schritte einige Male, um Daten für mehrere Minuten zu erhalten.

## <a name="troubleshooting"></a>Problembehandlung 
Sollte im Rahmen des Prozesses eine Fehlermeldung angezeigt werden, berücksichtigen Sie die folgenden Informationen zur Problembehandlung:

1. Sie können Metriken nicht für ein Abonnement oder eine Ressourcengruppe als Ihre Azure-Ressource ausgeben. 
1. Dem Speicher können keine Metriken hinzugefügt werden, die älter als 20 Minuten sind. Der Metrikspeicher ist für die Benachrichtigung und Diagrammerstellung in Echtzeit optimiert. 
2. Die Anzahl von Dimensionsnamen muss mit den Werten übereinstimmen (und umgekehrt). Überprüfen Sie die Werte. 
2. Möglicherweise geben Sie Metriken für Regionen aus, die keine benutzerdefinierten Metriken unterstützen. Weitere Informationen finden Sie unter [Unterstützte Regionen](../../azure-monitor/platform/metrics-custom-overview.md#supported-regions). 



## <a name="view-your-metrics"></a>Anzeigen Ihrer Metriken 

1. Melden Sie sich beim Azure-Portal an. 

1. Wählen Sie im Menü auf der linken Seite **Monitor** aus. 

1. Wählen Sie auf der Seite **Monitor** die Option **Metriken** aus. 

   ![Auswählen von „Metriken“](./media/metrics-store-custom-rest-api/metrics.png) 

1. Ändern Sie den Aggregationszeitraum in **Letzte 30 Minuten**.  

1. Wählen Sie im Dropdownmenü **Ressource** die Ressource aus, für die Sie die Metrik ausgegeben haben.  

1. Wählen Sie im Dropdownmenü **Namespaces** die Option **QueueProcessing** aus. 

1. Wählen Sie im Dropdownmenü **Metriken** die Option **QueueDepth** aus.  

 
## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr über [benutzerdefinierte Metriken](../../azure-monitor/platform/metrics-custom-overview.md).

