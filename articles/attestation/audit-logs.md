---
title: Azure Attestation-Überwachungsprotokolle
description: Hier werden die vollständigen Überwachungsprotokolle beschrieben, die für Azure Attestation gesammelt werden.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 11/23/2020
ms.author: mbaldwin
ms.openlocfilehash: 1fa4a458a4e3e1df1d84c343a32e3a41a4a25e75
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95758678"
---
# <a name="audit-logs-for-azure-attestation"></a>Überwachungsprotokolle für Azure Attestation

Überwachungsprotokolle sind sichere, unveränderliche, mit einem Zeitstempel versehene Aufzeichnungen von einzelnen Ereignissen, die im Laufe der Zeit aufgetreten sind. In diesen Protokollen werden wichtige Ereignisse erfasst, die sich auf die Funktionalität Ihrer Nachweisinstanz auswirken können.

Von Azure Attestation werden Nachweisinstanzen und die ihnen zugeordneten Richtlinien verwaltet. Aktionen im Zusammenhang mit der Instanzverwaltung und Richtlinienänderungen werden überwacht und protokolliert.

Dieser Artikel enthält Informationen zu den protokollierten Ereignissen, zu den erfassten Informationen und zum Speicherort dieser Protokolle.

## <a name="about-audit-logs"></a>Informationen zu Überwachungsprotokollen

Von Azure Attestation wird Code verwendet, um Überwachungsprotokolle für Ereignisse zu generieren, die sich auf den Nachweisablauf auswirken. Dabei geht es üblicherweise im Kern darum, wie oder wann Richtlinienänderungen für Ihre Nachweisinstanz vorgenommen werden, sowie um einige Administratoraktionen.

### <a name="auditable-events"></a>Überwachbare Ereignisse
Im Anschluss finden Sie einige der gesammelten Überwachungsprotokolle:

|     Ereignis/API                              |     Ereignisbeschreibung                                                                         |
|--------------------------------------------|-----------------------------------------------------------------------------------------------|
|     Create Instance (Instanz erstellen)                        |     Erstellt eine neue Instanz eines Nachweisdiensts. |
|     Destroy Instance (Instanz zerstören)                       |     Zerstört eine Instanz eines Nachweisdiensts. |
|     Add Policy Certificate (Richtlinienzertifikat hinzufügen)                 |     Fügt den aktuell vorhandenen Richtlinienverwaltungszertifikaten ein Zertifikat hinzu. |
|     Remove Policy Certificate (Richtlinienzertifikat entfernen)              |     Entfernt ein Zertifikat aus den aktuell vorhandenen Richtlinienverwaltungszertifikaten. |
|     Set Current Policy (Aktuelle Richtlinie festlegen)                     |     Legt die Nachweisrichtlinie für einen bestimmten TEE-Typ fest. |
|     Reset Attestation Policy (Nachweisrichtlinie zurücksetzen)               |     Setzt die Nachweisrichtlinie für einen bestimmten TEE-Typ zurück. |
|     Prepare to Update Policy (Richtlinienaktualisierung vorbereiten)               |     Bereitet die Aktualisierung der Nachweisrichtlinie für einen bestimmten TEE-Typ vor. |
|     Rehydrate Tenants After Disaster (Mandanten nach einem Notfall aktivieren)       |     Versiegelt alle Nachweismandanten in dieser Instanz des Nachweisdiensts neu. Dieser Vorgang kann nur von Administratoren des Nachweisdiensts ausgeführt werden. |

### <a name="collected--information"></a>Erfasste Informationen
Für jedes dieser Ereignisse werden von Azure Attestation folgende Informationen erfasst:

- Vorgangsname
- Erfolg des Vorgangs
- Aufrufer des Vorgangs. Mögliche Optionen:
    - Azure AD-UPN
    - ObjectID
    - Zertifikat
    - Azure AD-Mandanten-ID
- Ziel des Vorgangs. Mögliche Optionen:
    - Environment
    - Dienstregion
    - Dienstrolle
    - Dienstrolleninstanz
    - Ressourcen-ID
    - Ressourcenregion

### <a name="sample-audit-log"></a>Exemplarisches Überwachungsprotokoll

Überwachungsprotokolle werden im JSON-Format bereitgestellt. Hier sehen Sie ein Beispiel für ein Überwachungsprotokoll.

```json
{"operationName":"SetCurrentPolicy","resultType":"Success","resultDescription":null,"auditEventCategory":["ApplicationManagement"],"nCloud":null,"requestId":null,"callerIpAddress":null,"callerDisplayName":null,"callerIdentities":[{"callerIdentityType":"ObjectID","callerIdentity":"<some object ID>"},{"callerIdentityType":"TenantId","callerIdentity":"<some tenant ID>"}],"targetResources":[{"targetResourceType":"Environment","targetResourceName":"PublicCloud"},{"targetResourceType":"ServiceRegion","targetResourceName":"EastUS2"},{"targetResourceType":"ServiceRole","targetResourceName":"AttestationRpType"},{"targetResourceType":"ServiceRoleInstance","targetResourceName":"<some service role instance>"},{"targetResourceType":"ResourceId","targetResourceName":"/subscriptions/<some subscription ID>/resourceGroups/<some resource group name>/providers/Microsoft.Attestation/attestationProviders/<some instance name>"},{"targetResourceType":"ResourceRegion","targetResourceName":"EastUS2"}],"ifxAuditFormat":"Json","env_ver":"2.1","env_name":"#Ifx.AuditSchema","env_time":"2020-11-23T18:23:29.9427158Z","env_epoch":"MKZ6G","env_seqNum":1277,"env_popSample":0.0,"env_iKey":null,"env_flags":257,"env_cv":"##00000000-0000-0000-0000-000000000000_00000000-0000-0000-0000-000000000000_00000000-0000-0000-0000-000000000000","env_os":null,"env_osVer":null,"env_appId":null,"env_appVer":null,"env_cloud_ver":"1.0","env_cloud_name":null,"env_cloud_role":null,"env_cloud_roleVer":null,"env_cloud_roleInstance":null,"env_cloud_environment":null,"env_cloud_location":null,"env_cloud_deploymentUnit":null}
```

## <a name="access-audit-logs"></a>Zugriffsüberwachungsprotokolle

Diese Protokolle werden in Azure gespeichert, und es kann nicht direkt auf sie zugegriffen werden. Wenn Sie Zugriff benötigen, müssen Sie ein Supportticket erstellen. Weitere Informationen finden Sie unter [Kontaktaufnahme mit dem Microsoft-Support](https://azure.microsoft.com/support/options/). 

Sobald das Supportticket erstellt wurde, werden die Protokolle von Microsoft heruntergeladen, und Sie erhalten Zugriff darauf.
