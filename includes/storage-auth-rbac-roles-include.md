---
title: include file
description: include file
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/06/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a4a2034d44d98de9696b435c68c97a8a54a1c351
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96028363"
---
Azure stellt die folgenden integrierten Azure-Rollen zum Autorisieren des Zugriffs auf Blob- und Warteschlangendaten mit Azure AD und OAuth bereit:

- [Besitzer von Speicherblobdaten](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): Dient zum Festlegen des Besitzes sowie zum Verwalten der POSIX-Zugriffssteuerung für Azure Data Lake Storage Gen2. Weitere Informationen finden Sie unter [Zugriffssteuerung in Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Mitwirkender an Speicherblobdaten](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Dient zum Gewähren von Lese-/Schreib-/Löschberechtigungen für Blobspeicherressourcen.
- [Leser von Speicherblobdaten](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): Dient zum Gewähren von Leseberechtigungen für Blobspeicherressourcen.
- [Storage Blob-Delegator](../articles/role-based-access-control/built-in-roles.md#storage-blob-delegator): Abrufen eines Benutzerdelegierungsschlüssels zum Erstellen einer SAS (Shared Access Signature), die mit Azure AD-Anmeldeinformationen für einen Container oder ein Blob signiert ist.
- [Mitwirkender an Storage-Warteschlangendaten](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Dient zum Gewähren von Lese-/Schreib-/Löschberechtigungen für Azure-Warteschlangen.
- [Storage-Warteschlangendatenleser](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): Dient zum Gewähren von Leseberechtigungen für Azure-Warteschlangen.
- [Verarbeiter von Speicherwarteschlangen-Datennachrichten](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): Dient zum Gewähren von Berechtigungen zum Einsehen, Abrufen und Löschen für Nachrichten in Azure Storage-Warteschlangen.
- [Absender der Speicherwarteschlangen-Datennachricht](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): Dient zum Gewähren von Berechtigungen zum Hinzufügen für Nachrichten in Azure Storage-Warteschlangen.

Nur Rollen, die explizit für den Datenzugriff definiert sind, ermöglichen einem Sicherheitsprinzipal den Zugriff auf Blob- oder Warteschlangendaten. Integrierte Rollen wie **Besitzer**, **Mitwirkender** und **Speicherkontomitwirkender** gestatten einem Sicherheitsprinzipal die Verwaltung eines Speicherkontos, gewähren aber keinen Zugriff auf die Blob- oder Warteschlangendaten in diesem Konto über Azure AD. Wenn eine Rolle jedoch **Microsoft.Storage/storageAccounts/listKeys/action** enthält, kann ein Benutzer, dem diese Rolle zugewiesen ist, über die Autorisierung mit gemeinsam verwendetem Schlüssel mit den Kontozugriffsschlüsseln auf Daten im Speicherkonto zugreifen. Weitere Informationen finden Sie unter [Zugreifen auf Blob- oder Warteschlangendaten über das Azure-Portal](../articles/storage/blobs/authorize-data-operations-portal.md).

Ausführliche Informationen zu integrierten Azure-Rollen für Azure Storage für die Datendienste und den Verwaltungsdienst finden Sie im Artikel [In Azure integrierte Rollen für Azure RBAC](../articles/role-based-access-control/built-in-roles.md#storage) im Abschnitt **Storage**. Informationen zu den verschiedenen Typen von Rollen, die Berechtigungen in Azure bereitstellen, finden Sie unter [Administratorrollen für klassische Abonnements, Azure-Rollen und Azure AD-Rollen](../articles/role-based-access-control/rbac-and-directory-admin-roles.md).

> [!IMPORTANT]
> Die Verteilung von Azure-Rollenzuweisungen kann bis zu fünf Minuten dauern.