---
title: Das Azure Active Directory-Anmeldeprotokollschema in Azure Monitor | Microsoft-Dokumentation
description: Informationen zum Azure AD-Anmeldeprotokollschema für die Verwendung in Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8ac6c56dca100ea9836158f46881c4eb12213e1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60285196"
---
# <a name="interpret-the-azure-ad-sign-in-logs-schema-in-azure-monitor"></a>Interpretieren des Azure AD-Anmeldeprotokollschemas in Azure Monitor

Dieser Artikel beschreibt das Azure Active Directory-Anmeldeprotokollschema (Azure AD) in Azure Monitor. Die meisten Informationen zu Anmeldungen werden unter dem *Properties*-Attribut des `records`-Objekts bereitgestellt.


```json
{ 
    "time": "2019-03-12T16:02:15.5522137Z", 
    "resourceId": "/tenants/<TENANT ID>/providers/Microsoft.aadiam",
    "operationName": "Sign-in activity", 
    "operationVersion": "1.0", 
    "category": "SignInLogs", 
    "tenantId": "<TENANT ID>", 
    "resultType": "50140", 
    "resultSignature": "None", 
    "resultDescription": "This error occurred due to 'Keep me signed in' interrupt when the user was signing-in.", 
    "durationMs": 0, 
    "callerIpAddress": "<CALLER IP ADDRESS>", 
    "correlationId": "a75a10bd-c126-486b-9742-c03110d36262", 
    "identity": "Timothy Perkins", 
    "Level": 4, 
    "location": "US", 
    "properties": 
        {
            "id":"0231f922-93fa-4005-bb11-b344eca03c01",
            "createdDateTime":"2019-03-12T16:02:15.5522137+00:00",
            "userDisplayName":"Timothy Perkins",
            "userPrincipalName":"<USER PRINCIPAL NAME>",
            "userId":"<USER ID>",
            "appId":"<APPLICATION ID>",
            "appDisplayName":"Azure Portal",
            "ipAddress":"<IP ADDRESS>",
            "status":
            {
                "errorCode":50140,
                "failureReason":"This error occurred due to 'Keep me signed in' interrupt when the user was signing-in."
            },
            "clientAppUsed":"Browser",
            "deviceDetail":
            {
                "operatingSystem":"Windows 10",
                "browser":"Chrome 72.0.3626"
            },
            "location":
                {
                    "city":"Bellevue",
                    "state":"Washington",
                    "countryOrRegion":"US",
                    "geoCoordinates":
                    {
                        "latitude":45,
                        "longitude":122
                    }
                },
            "correlationId":"a75a10bd-c126-486b-9742-c03110d36262",
            "conditionalAccessStatus":"notApplied",
            "appliedConditionalAccessPolicies":
            [
                {
                    "id":"ae11ffaa-9879-44e0-972c-7538fd5c4d1a",
                    "displayName":"Hr app access policy",
                    "enforcedGrantControls":
                    [
                        "Mfa"
                    ],
                    "enforcedSessionControls":
                    [
                    ],
                    "result":"notApplied"
                },
                {
                    "id":"b915a70b-2eee-47b6-85b6-ff4f4a66256d",
                    "displayName":"MFA for all but global support access",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
                {
                    "id":"830f27fa-67a8-461f-8791-635b7225caf1",
                    "displayName":"Header Based Application Control",
                    "enforcedGrantControls":["Mfa"],
                    "enforcedSessionControls":[],
                    "result":"notApplied"
                },
                {
                    "id":"8ed8d7f7-0a2e-437b-b512-9e47bed562e6",
                    "displayName":"MFA for everyones",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
                {
                    "id":"52924e0f-798b-4afd-8c42-49055c7d6395",
                    "displayName":"Device compliant",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
             ],
            "isInteractive":true,
            "tokenIssuerType":"AzureAD",
            "authenticationProcessingDetails":[],
            "networkLocationDetails":[],
            "processingTimeInMilliseconds":0,
            "riskDetail":"hidden",
            "riskLevelAggregated":"hidden",
            "riskLevelDuringSignIn":"hidden",
            "riskState":"none",
            "riskEventTypes":[],
            "resourceDisplayName":"windows azure service management api",
            "resourceId":"797f4846-ba00-4fd7-ba43-dac1f8f63013",
            "authenticationMethodsUsed":[]
        }
}
```


## <a name="field-descriptions"></a>Feldbeschreibungen

| Feldname | BESCHREIBUNG |
|------------|-------------|
| Time | Das Datum und die Uhrzeit in UTC. |
| resourceId | Dieser Wert wird nicht zugeordnet, sodass das Feld ignoriert werden kann.  |
| OperationName | Für Anmeldungen ist dieser Wert immer *Anmeldeaktivität*. |
| OperationVersion | Die vom Client angeforderte REST-API-Version. |
| Category (Kategorie) | Für Anmeldungen ist dieser Wert immer *SignIn*. | 
| TenantId | Die mit den Protokollen verknüpfte Mandanten-GUID. |
| ResultType | Das Ergebnis des Anmeldevorgangs ist *Erfolgreich* oder *Fehler*. | 
| ResultSignature | Gibt den Fehlercode (falls vorhanden) des Anmeldevorgangs an. |
| ResultDescription | Gibt die Fehlerbeschreibung des Anmeldevorgangs an. |
| DurationMs |  Dieser Wert wird nicht zugeordnet, sodass das Feld ignoriert werden kann.|
| CallerIpAddress | Die IP-Adresse des Clients, der die Anforderung gestellt hat. | 
| CorrelationId | Die optionale GUID, die vom Client übergeben wird. Dieser Wert kann dabei helfen, clientseitige Vorgänge mit serverseitigen Vorgängen zu korrelieren und Protokolle zu verfolgen, die sich über mehrere Dienste erstrecken. |
| Identity | Die Identität des Tokens, das beim Erstellen der Anforderung angegeben wurde. Dies kann ein Benutzerkonto, ein Systemkonto oder einen Dienstprinzipal sein. |
| Level | Gibt den Typ der Nachricht an. Bei Überwachungen ist es immer *Information*. |
| Location | Gibt den Standort der Anmeldeaktivität an. |
| Eigenschaften | Listet alle Eigenschaften auf, die mit Anmeldungen verknüpft sind. Weitere Informationen finden Sie in der [Microsoft Graph-API-Referenz](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin). Dieses Schema verwendet zur besseren Lesbarkeit die gleichen Attributnamen wie die Anmelderessource.

## <a name="next-steps"></a>Nächste Schritte

* [Interpret audit logs schema in Azure Monitor](reference-azure-monitor-audit-log-schema.md) (Interpretieren des Überwachungsprotokollschemas in Azure Monitor)
* [Informieren Sie sich ausführlicher über Azure-Diagnoseprotokolle](../../azure-monitor/platform/diagnostic-logs-overview.md)