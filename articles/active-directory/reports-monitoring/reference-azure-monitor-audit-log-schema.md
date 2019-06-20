---
title: Interpretieren des Azure Active Directory-Überwachungsprotokollschemas in Azure Monitor | Microsoft-Dokumentation
description: Informationen zum Azure AD-Überwachungsprotokollschema für die Verwendung in Azure Monitor
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
ms.openlocfilehash: d9f58b213e50a021651f35112a48d8f74ae59571
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60437072"
---
# <a name="interpret-the-azure-ad-audit-logs-schema-in-azure-monitor-preview"></a>Interpretieren des Azure AD-Überwachungsprotokollschemas in Azure Monitor (Vorschauversion)

Dieser Artikel beschreibt das Azure Active Directory-Überwachungsprotokollschema (Azure AD) in Azure Monitor. Jeder einzelne Protokolleintrag wird als Text gespeichert und als JSON-Blob formatiert, wie in den beiden folgenden Beispielen dargestellt: 

```json
{ 
    "records": [ 
    { 
        "time": "2018-03-17T00:14:31.2585575Z", 
        "operationName": "Change password (self-service)",
        "operationVersion": "1.0",
        "category": "Audit", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "Success", 
        "resultSignature": "-1", 
        "resultDescription": "None", 
        "durationMs": "-1", 
        "correlationId": "60d5e89a-b890-413f-9e25-a047734afe9f", 
        "identity": "sreens@wingtiptoysonline.com", 
        "Level": "Informational", 
        "location": "WUS", 
        "properties": { 
            "identityType": "UPN", 
            "operationType": "Update", 
            "additionalDetails": "None", 
            "additionalTargets": "", 
            "targetUpdatedProperties": "", 
            "targetResourceType": "UPN__TenantContextID__PUID__ObjectID__ObjectClass", 
            "targetResourceName": "sreens@wingtiptoysonline.com__bf85dc9d-cb43-44a4-80c4-469e8c58249e__1003BFFD9FEB17DB__7a408bdd-7d97-4574-8511-dd747b56465d__User", 
            "auditEventCategory": "UserManagement" 
        } 
    } 
    ] 
} 
```

```json
{ 
    "records": [ 
    { 
        "time": "2018-03-18T19:47:43.0368859Z", 
        "operationName": "Update service principal.", 
        "operationVersion": "1.0", 
        "category": "Audit", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "Success", 
        "resultSignature": "-1", 
        "durationMs": "-1", 
        "callerIpAddress": "<null>", 
        "correlationId": "14916c7a-5a7d-44e8-9b06-74b49efb08ee", 
        "identity": "NA", 
        "Level": "Informational", 
        "properties": { 
            "identityType": "NA", 
            "operationType": "Update", 
            "additionalDetails": {}, 
            "additionalTargets": "", 
            "targetUpdatedProperties": [ 
            { 
                "Name": "Included Updated Properties", 
                "OldValue": null, 
                "NewValue": "" 
            }, 
            { 
                "Name": "TargetId.ServicePrincipalNames", 
                "OldValue": null, 
                "NewValue": "http://adapplicationregistry.onmicrosoft.com/salesforce.com/primary;cd3ed3de-93ee-400b-8b19-b61ef44a0f29" 
            } 
            ], 
        "targetResourceType": "Other__ObjectID__ObjectClass__Name__AppId__SPN", 
        "targetResourceName": "ServicePrincipal_ea70a262-4da3-440a-b396-9734ddfd9df2__ea70a262-4da3-440a-b396-9734ddfd9df2__ServicePrincipal__Salesforce__cd3ed3de-93ee-400b-8b19-b61ef44a0f29__http://adapplicationregistry.onmicrosoft.com/salesforce.com/primary;cd3ed3de-93ee-400b-8b19-b61ef44a0f29", 
        "auditEventCategory": "ApplicationManagement" 
      } 
    } 
    ] 
} 
```

```json
{
    "records": [
    {
        "time": "2018-12-10T00:03:46.6161822Z",
        "resourceId": "/tenants/7918d4b5-0442-4a97-be2d-36f9f9962ece/providers/Microsoft.aadiam",
        "operationName": "Update policy",
        "operationVersion": "1.0",
        "category": "AuditLogs",
        "tenantId": "7918d4b5-0442-4a97-be2d-36f9f9962ece",
        "resultSignature": "None",
        "durationMs": 0,
        "callerIpAddress": "<null>",
        "correlationId": "192298c1-0994-4dd6-b05a-a6c5984c31cb",
        "identity": "MS-PIM",
        "level": "Informational",
        "properties": {
            "id": "Directory_VNXV4_28148892",
            "category": "Policy",
            "correlationId": "192298c1-0994-4dd6-b05a-a6c5984c31cb",
            "result": 0,
            "resultReason": "",
            "activityDisplayName": "Update policy",
            "activityDateTime": "2018-12-10T00:03:46.6161822+00:00",
            "loggedByService": "Core Directory",
            "operationType": "Update",
            "initiatedBy": {},
            "targetResources": [
            {
                "id": "5e7a8ae7-165d-44a4-a4f4-6141f8c8ef40",
                "displayName": "Default Policy",
                "type": "Policy",
                "modifiedProperties": []
            }
            ],
            "additionalDetails": []
        }
    }
    ]
}

```

## <a name="field-and-property-descriptions"></a>Beschreibungen der Felder und Eigenschaften

| Feldname | BESCHREIBUNG |
|------------|-------------|
| time       | Das Datum und die Uhrzeit (UTC). |
| operationName | Der Name des Vorgangs. |
| operationVersion | Die vom Client angeforderte REST-API-Version. |
| category | Derzeit ist *Audit* der einzige unterstützte Wert. |
| tenantId | Die mit den Protokollen verknüpfte Mandanten-GUID. |
| resultType | Das Ergebnis des Vorgangs. Das Ergebnis kann *Erfolg* oder *Fehler* sein. |
| resultSignature |  Dieses Feld wird nicht zugeordnet, sodass es ignoriert werden kann. | 
| resultDescription | Eine zusätzliche Ergebnisbeschreibung (falls verfügbar). | 
| durationMs |  Dieses Feld wird nicht zugeordnet, sodass es ignoriert werden kann. |
| callerIpAddress | Die IP-Adresse des Clients, der die Anforderung gestellt hat. | 
| correlationId | Eine optionale GUID, die vom Client übergeben wird. Sie kann dabei helfen, clientseitige Vorgänge mit serverseitigen Vorgängen zu korrelieren und Protokolle zu verfolgen, die sich über mehrere Dienste erstrecken. |
| identity | Die Identität des Tokens, das beim Erstellen der Anforderung angegeben wurde. Die Identität kann ein Benutzerkonto, ein Systemkonto oder ein Dienstprinzipal sein. |
| level | Der Nachrichtentyp. Bei Überwachungsprotokollen ist die Ebene immer *Information*. |
| location | Der Standort des Rechenzentrums. |
| properties | Listet die unterstützten Eigenschaften auf, die mit einem Überwachungsprotokoll verknüpft sind. Weitere Informationen finden Sie in der nächsten Tabelle. | 

<br>

| Eigenschaftenname | BESCHREIBUNG |
|---------------|-------------|
| AuditEventCategory | Der Typ des Überwachungsereignisses. Dies kann *Benutzerverwaltung*, *Anwendungsverwaltung* oder ein anderer Typ sein.|
| Identity Type | Der Typ kann *Anwendung* oder *Benutzer* sein. |
| Vorgangstyp | Der Typ kann *Hinzufügen*, *Aktualisieren*, *Löschen* sein. *Sonstiges* ist auch möglich. |
| Target Resource Type | Gibt den Zielressourcentyp an, auf dem der Vorgang ausgeführt wurde. Der Typ kann *Anwendung*, *Benutzer*, *Rolle* oder *Richtlinie* sein. | 
| Target Resource Name | Der Name der Zielressource. Dies kann ein Anwendungsname, ein Rollenname, ein Benutzerprinzipalname oder ein Dienstprinzipalname sein. |
| additionalTargets | Listet alle zusätzlichen Eigenschaften für bestimmte Vorgänge auf. Beispielsweise werden bei einem Aktualisierungsvorgang die alten und die neuen Werte unter *targetUpdatedProperties* aufgeführt. | 

## <a name="next-steps"></a>Nächste Schritte

* [Interpret sign-in logs schema in Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md) (Interpretieren des Anmeldeprotokollschemas in Azure Monitor)
* [Azure-Diagnoseprotokolle](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Häufig gestellte Fragen und bekannte Probleme](concept-activity-logs-azure-monitor.md#frequently-asked-questions)