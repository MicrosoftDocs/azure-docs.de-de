---
title: In Azure integrierte Rollen – Azure RBAC
description: In diesem Artikel werden die in Azure integrierten Rollen für die auf Azure-Rollen basierte Zugriffssteuerung (Azure RBAC) beschrieben. Es werden „Actions“, „NotActions“, „DataActions“ und „NotDataActions“ aufgelistet.
services: active-directory
ms.service: role-based-access-control
ms.topic: reference
ms.workload: identity
author: rolyon
ms.author: rolyon
ms.date: 11/18/2020
ms.custom: generated
ms.openlocfilehash: e18dc7e16eb0b114bd279d53b7dcc895bd62f338
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002730"
---
# <a name="azure-built-in-roles"></a>Integrierte Azure-Rollen

Die auf [Azure-Rollen basierte Zugriffssteuerung (Azure RBAC)](overview.md) verfügt über mehrere integrierte Azure-Rollen, die Sie Benutzern, Gruppen, Dienstprinzipalen und verwalteten Identitäten zuweisen können. Durch Rollenzuweisungen wird die Art und Weise gesteuert, wie Sie auf Azure-Ressourcen zugreifen. Wenn die integrierten Rollen den Ansprüchen Ihrer Organisation nicht entsprechen, können Sie Ihre eigenen [benutzerdefinierten Azure-Rollen](custom-roles.md) erstellen.

Dieser Artikel enthält eine Liste der integrierten Azure-Rollen, die ständig weiterentwickelt werden. Verwenden Sie zum Abrufen der aktuellen Rollen [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) oder [az role definition list](/cli/azure/role/definition#az-role-definition-list). Eine Liste mit Administratorrollen für Azure Active Directory (Azure AD) finden Sie unter [Berechtigungen der Administratorrolle in Azure Active Directory](../active-directory/roles/permissions-reference.md).

Die folgende Tabelle enthält eine kurze Beschreibung und die eindeutige ID aller integrierten Rollen. Klicken Sie auf den Rollennamen, um die Liste der `Actions`, `NotActions`, `DataActions` und `NotDataActions` für jede Rolle anzuzeigen. Informationen zur Bedeutung dieser Aktionen und deren Anwendung auf die Verwaltung und Datenebenen finden Sie unter [Grundlegendes zu Azure-Rollendefinitionen](role-definitions.md).

## <a name="all"></a>All

> [!div class="mx-tableFixed"]
> | Integrierte Rolle | BESCHREIBUNG | id |
> | --- | --- | --- |
> | **Allgemein** |  |  |
> | [Mitwirkender](#contributor) | Hiermit wird Vollzugriff zum Verwalten aller Ressourcen gewährt, allerdings nicht zum Zuweisen von Rollen in Azure RBAC. | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | [Besitzer](#owner) | Hiermit wird Vollzugriff zum Verwalten aller Ressourcen gewährt, einschließlich der Möglichkeit, Rollen in Azure RBAC zuzuweisen. | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | [Leser](#reader) | Hiermit können Sie alle Ressourcen anzeigen, aber keine Änderungen vornehmen. | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | [Benutzerzugriffsadministrator](#user-access-administrator) | Ermöglicht Ihnen die Verwaltung von Benutzerzugriffen auf Azure-Ressourcen. | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Compute** |  |  |
> | [Mitwirkender von klassischen virtuellen Computern](#classic-virtual-machine-contributor) | Ermöglicht Ihnen das Verwalten klassischer virtueller Computer, aber weder den Zugriff darauf noch auf die mit ihnen verbundenen virtuellen Netzwerke oder Speicherkonten. | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | [VM-Administratoranmeldung](#virtual-machine-administrator-login) | Anzeigen von virtuellen Computern im Portal und Anmelden als Administrator | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | [Mitwirkender von virtuellen Computern](#virtual-machine-contributor) | Ermöglicht Ihnen das Verwalten virtueller Computer, aber weder den Zugriff darauf, noch auf deren verbundenen virtuellen Netzwerke oder Speicherkonten. | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | [VM-Benutzeranmeldung](#virtual-machine-user-login) | Anzeigen von virtuellen Computern im Portal und Anmelden als regulärer Benutzer. | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Netzwerk** |  |  |
> | [Mitwirkender für den CDN-Endpunkt](#cdn-endpoint-contributor) | Diese Rolle kann CDN-Endpunkte verwalten, aber anderen Benutzern keinen Zugriff erteilen. | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | [CDN-Endpunktleser](#cdn-endpoint-reader) | Diese Rolle kann CDN-Endpunkte anzeigen, aber keine Änderungen vornehmen. | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | [Mitwirkender für das CDN-Profil](#cdn-profile-contributor) | Diese Rolle kann CDN-Profile und deren Endpunkte verwalten, aber anderen Benutzern keinen Zugriff erteilen. | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | [CDN-Profilleser](#cdn-profile-reader) | Diese Rolle kann CDN-Profile und deren Endpunkte anzeigen, aber keine Änderungen vornehmen. | 8f96442b-4075-438f-813d-ad51ab4019af |
> | [Mitwirkender von klassischem Netzwerk](#classic-network-contributor) | Ermöglicht Ihnen das Verwalten von klassischen Netzwerken, nicht aber den Zugriff darauf. | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | [DNS Zone Contributor](#dns-zone-contributor) | Ermöglicht Ihnen die Verwaltung von DNS-Zonen und Ressourceneintragssätzen in Azure DNS, aber nicht zu steuern, wer darauf Zugriff hat. | befefa01-2a29-4197-83a8-272ff33ce314 |
> | [Mitwirkender von virtuellem Netzwerk](#network-contributor) | Ermöglicht Ihnen das Verwalten von Netzwerken, nicht aber den Zugriff darauf. | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | [Mitwirkender für private DNS-Zone](#private-dns-zone-contributor) | Ermöglicht Ihnen das Verwalten privater DNS-Zonenressourcen, aber nicht der virtuellen Netzwerke, mit denen sie verknüpft sind. | b12aa53e-6015-4669-85d0-8515ebb3ae7f |
> | [Traffic Manager-Mitwirkender](#traffic-manager-contributor) | Ermöglicht Ihnen die Verwaltung von Traffic Manager-Profilen, aber nicht die Steuerung des Zugriffs darauf. | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Storage** |  |  |
> | [Avere-Mitwirkender](#avere-contributor) | Kann einen Avere vFXT-Cluster erstellen und verwalten. | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | [Avere-Bediener](#avere-operator) | Wird vom Avere vFXT-Cluster zum Verwalten des Clusters verwendet | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | [Mitwirkender für Sicherungen](#backup-contributor) | Ermöglicht Ihnen die Verwaltung des Sicherungsdiensts. Sie können jedoch keine Tresore erstellen oder anderen Benutzern Zugriff gewähren. | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | [Sicherungsoperator](#backup-operator) | Ermöglicht Ihnen das Verwalten von Sicherungsdiensten, jedoch nicht das Entfernen der Sicherung, die Tresorerstellung und das Erteilen von Zugriff an andere Benutzer. | 00c29273-979b-4161-815c-10b084fb9324 |
> | [Sicherungsleser](#backup-reader) | Kann Sicherungsdienste anzeigen, aber keine Änderungen vornehmen. | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | [Mitwirkender von klassischem Speicherkonto](#classic-storage-account-contributor) | Ermöglicht Ihnen das Verwalten klassischer Speicherkonten, nicht aber den Zugriff darauf. | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | [Klassische Dienstrolle „Speicherkonto-Schlüsseloperator“](#classic-storage-account-key-operator-service-role) | Klassische Speicherkonto-Schlüsseloperatoren dürfen Schlüssel für klassische Speicherkonten auflisten und neu generieren. | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | [Data Box-Mitwirkender](#data-box-contributor) | Ermöglicht Ihnen das Verwalten aller Komponenten unter dem Data Box-Dienst, mit Ausnahme der Gewährung des Zugriffs für andere Benutzer. | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | [Data Box-Leser](#data-box-reader) | Ermöglicht Ihnen das Verwalten des Data Box-Diensts, mit Ausnahme der Erstellung von Aufträgen oder der Bearbeitung von Auftragsdetails und der Gewährung des Zugriffs für andere Benutzer. | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | [Data Lake Analytics-Entwickler](#data-lake-analytics-developer) | Ermöglicht Ihnen das Übermitteln, Überwachen und Verwalten Ihrer eigenen Aufträge, aber nicht das Erstellen oder Löschen von Data Lake Analytics-Konten. | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | [Lese- und Datenzugriff](#reader-and-data-access) | Ermöglicht Ihnen das Anzeigen sämtlicher Aspekte, jedoch nicht das Löschen oder Erstellen eines Speicherkontos oder einer darin enthaltenen Ressource. Sie können auch Lese-/Schreibzugriff auf alle Daten in einem Speicherkonto durch Zugriff auf Speicherkontoschlüssel gewähren. | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | [Mitwirkender von Speicherkonto](#storage-account-contributor) | Erlaubt die Verwaltung von Speicherkonten. Ermöglicht den Zugriff auf den Kontoschlüssel, der für den Datenzugriff über die Autorisierung mit einem gemeinsam verwendetem Schlüssel genutzt werden kann. | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | [Dienstrolle „Speicherkonto-Schlüsseloperator“](#storage-account-key-operator-service-role) | Ermöglicht das Auflisten und erneute Generieren von Zugriffsschlüsseln für Speicherkonten. | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | [Mitwirkender an Speicherblobdaten](#storage-blob-data-contributor) | Lesen, Schreiben und Löschen von Azure Storage-Containern und -Blobs. Um zu erfahren, welche Aktionen für einen bestimmten Datenvorgang erforderlich sind, siehe [Berechtigungen für den Aufruf von Datenvorgängen für Blobs und Warteschlangen](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | [Besitzer von Speicherblobdaten](#storage-blob-data-owner) | Bietet Vollzugriff auf Azure Storage-Blobcontainer und -daten, einschließlich POSIX-Zugriffssteuerung. Um zu erfahren, welche Aktionen für einen bestimmten Datenvorgang erforderlich sind, siehe [Berechtigungen für den Aufruf von Datenvorgängen für Blobs und Warteschlangen](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | [Leser von Speicherblobdaten](#storage-blob-data-reader) | Lesen und Auflisten von Azure Storage-Containern und -Blobs. Um zu erfahren, welche Aktionen für einen bestimmten Datenvorgang erforderlich sind, siehe [Berechtigungen für den Aufruf von Datenvorgängen für Blobs und Warteschlangen](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | [Storage Blob-Delegator](#storage-blob-delegator) | Abrufen eines Benutzerdelegierungsschlüssels, mit dem dann eine SAS (Shared Access Signature) für einen Container oder Blob erstellt werden kann, die mit Azure AD-Anmeldeinformationen signiert ist. Weitere Informationen finden Sie unter [Erstellen einer SAS für die Benutzerdelegierung](/rest/api/storageservices/create-user-delegation-sas). | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | [Speicherdateidaten-SMB-Freigabemitwirkender](#storage-file-data-smb-share-contributor) | Ermöglicht den Lese-, Schreib- und Löschzugriff auf Dateien/Verzeichnisse in Azure-Dateifreigaben. Für diese Rolle gibt es keine integrierte Entsprechung auf Windows-Dateiservern. | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
> | [Speicherdateidaten-SMB-Freigabemitwirkender mit erhöhten Rechten](#storage-file-data-smb-share-elevated-contributor) | Ermöglicht das Lesen, Schreiben, Löschen und Bearbeiten von Zugriffssteuerungslisten für Dateien/Verzeichnisse in Azure-Dateifreigaben. Diese Rolle entspricht einer Dateifreigabe-ACL für das Bearbeiten auf Windows-Dateiservern. | a7264617-510b-434b-a828-9731dc254ea7 |
> | [Speicherdateidaten-SMB-Freigabeleser](#storage-file-data-smb-share-reader) | Ermöglicht den Lesezugriff auf Dateien/Verzeichnisse in Azure-Dateifreigaben. Diese Rolle entspricht einer Dateifreigabe-ACL für das Lesen auf Windows-Dateiservern. | aba4ae5f-2193-4029-9191-0cb91df5e314 |
> | [Mitwirkender an Storage-Warteschlangendaten](#storage-queue-data-contributor) | Lesen, Schreiben und Löschen von Azure Storage-Warteschlangen und -Warteschlangennachrichten. Um zu erfahren, welche Aktionen für einen bestimmten Datenvorgang erforderlich sind, siehe [Berechtigungen für den Aufruf von Datenvorgängen für Blobs und Warteschlangen](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | [Verarbeiter von Speicherwarteschlangen-Datennachrichten](#storage-queue-data-message-processor) | Einsehen, Abrufen und Löschen einer Nachricht aus einer Azure Storage-Warteschlange. Um zu erfahren, welche Aktionen für einen bestimmten Datenvorgang erforderlich sind, siehe [Berechtigungen für den Aufruf von Datenvorgängen für Blobs und Warteschlangen](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | [Absender der Speicherwarteschlangen-Datennachricht](#storage-queue-data-message-sender) | Hinzufügen von Nachrichten zu einer Azure Storage-Warteschlange. Um zu erfahren, welche Aktionen für einen bestimmten Datenvorgang erforderlich sind, siehe [Berechtigungen für den Aufruf von Datenvorgängen für Blobs und Warteschlangen](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | [Storage-Warteschlangendatenleser](#storage-queue-data-reader) | Lesen und Auflisten von Azure Storage-Warteschlangen und -Warteschlangennachrichten. Um zu erfahren, welche Aktionen für einen bestimmten Datenvorgang erforderlich sind, siehe [Berechtigungen für den Aufruf von Datenvorgängen für Blobs und Warteschlangen](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Web** |  |  |
> | [Azure Maps-Datenleser](#azure-maps-data-reader) | Gewährt Lesezugriff auf kartenbezogene Daten von einem Azure Maps-Konto. | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | [Mitwirkender von Suchdienst](#search-service-contributor) | Ermöglicht Ihnen das Verwalten von Search-Diensten, nicht aber den Zugriff darauf. | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | [SignalR-AccessKey-Leser](#signalr-accesskey-reader) | Ermöglicht das Lesen von SignalR Service-Zugriffsschlüsseln. | 04165923-9d83-45d5-8227-78b77b0a687e |
> | [SignalR-App-Server (Vorschau)](#signalr-app-server-preview) | Ermöglicht Ihrem App-Server den Zugriff auf SignalR Service mit AAD-Authentifizierungsoptionen. | 420fcaa2-552c-430f-98ca-3264be4806c7 |
> | [SignalR-Mitwirkender](#signalr-contributor) | Ermöglicht das Erstellen, Lesen, Aktualisieren und Löschen von SignalR Service-Ressourcen. | 8cf5e20a-e4b2-4e9d-b3a1-5ceb692c2761 |
> | [SignalR Serverless-Mitwirkender (Vorschau)](#signalr-serverless-contributor-preview) | Ermöglicht Ihrer App den Zugriff auf den Dienst im serverlosen Modus mit AAD-Authentifizierungsoptionen. | fd53cd77-2268-407a-8f46-7e7863d0f521 |
> | [SignalR Service-Besitzer (Vorschau)](#signalr-service-owner-preview) | Bietet Vollzugriff auf Azure SignalR Service-REST-APIs. | 7e4f1700-ea5a-4f59-8f37-079cfe29dce3 |
> | [SignalR Service-Leser (Vorschau)](#signalr-service-reader-preview) | Bietet schreibgeschützten Zugriff auf Azure SignalR Service-REST-APIs. | ddde6b66-c0df-4114-a159-3618637b3035 |
> | [Mitwirkender von Webplan](#web-plan-contributor) | Ermöglicht Ihnen das Verwalten der Webpläne für Websites, nicht aber den Zugriff darauf. | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | [Mitwirkender von Website](#website-contributor) | Ermöglicht Ihnen das Verwalten von Websites (nicht der Webpläne), nicht aber den Zugriff darauf. | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Container** |  |  |
> | [AcrDelete](#acrdelete) | ACR-Löschvorgang | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | [AcrImageSigner](#acrimagesigner) | ACR-Imagesignaturgeber | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | [AcrPull](#acrpull) | ACR-Pullvorgang | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | [AcrPush](#acrpush) | ACR-Pushvorgang | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | [AcrQuarantineReader](#acrquarantinereader) | ACR-Quarantänedatenleser | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | [AcrQuarantineWriter](#acrquarantinewriter) | ACR-Quarantänedatenschreiber | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | [Administratorrolle für Azure Kubernetes Service-Cluster](#azure-kubernetes-service-cluster-admin-role) | Listet die Aktion für Anmeldeinformationen des Clusteradministrators auf. | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | [Benutzerrolle für Azure Kubernetes Service-Cluster](#azure-kubernetes-service-cluster-user-role) | Listet die Aktion für Anmeldeinformationen des Clusterbenutzer auf. | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | [Rolle „Mitwirkender“ für Azure Kubernetes Service](#azure-kubernetes-service-contributor-role) | Gewährt Lese- und Schreibzugriff auf Azure Kubernetes Service-Cluster. | ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8 |
> | [RBAC-Administrator von Azure Kubernetes Service](#azure-kubernetes-service-rbac-admin) | Ermöglicht Ihnen das Verwalten aller Ressourcen unter einem Cluster/Namespace, außer das Aktualisieren oder Löschen von Ressourcenkontingenten und Namespaces. | 3498e952-d568-435e-9b2c-8d77e338d7f7 |
> | [RBAC-Clusteradministrator von Azure Kubernetes Service](#azure-kubernetes-service-rbac-cluster-admin) | Ermöglicht Ihnen das Verwalten aller Ressourcen im Cluster. | b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b |
> | [RBAC-Leser von Azure Kubernetes Service](#azure-kubernetes-service-rbac-reader) | Ermöglicht schreibgeschützten Zugriff, um die meisten Objekte in einem Namespace anzuzeigen. Es ist nicht möglich, Rollen oder Rollenbindungen anzuzeigen. Diese Rolle lässt das Anzeigen von Geheimnissen nicht zu, da das Lesen des Inhalts von Geheimnissen den Zugriff auf ServiceAccount-Anmeldeinformationen im Namespace ermöglicht, was den API-Zugriff als beliebiges Dienstkonto im Namespace ermöglichen würde (eine Form von Berechtigungsausweitung). Wenn Sie diese Rolle im Clusterumfang anwenden, wird der Zugriff auf alle Namespaces ermöglicht. | 7f6c6a51-bcf8-42ba-9220-52d62157d7db |
> | [RBAC-Writer von Azure Kubernetes Service](#azure-kubernetes-service-rbac-writer) | Ermöglicht den Lese-/Schreibzugriff auf die meisten Objekte in einem Namespace. Diese Rolle gestattet nicht das Anzeigen oder Ändern von Rollen oder Rollenbindungen. Diese Rolle ermöglicht jedoch den Zugriff auf Geheimnisse und das Ausführen von Pods als beliebiges Dienstkonto im Namespace, sodass sie verwendet werden kann, um die API-Zugriffsebenen eines beliebigen ServiceAccount im Namespace zu erhalten. Wenn Sie diese Rolle im Clusterumfang anwenden, wird der Zugriff auf alle Namespaces ermöglicht. | a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb |
> | **Datenbanken** |  |  |
> | [Cosmos DB-Rolle „Kontoleser“](#cosmos-db-account-reader-role) | Kann Azure Cosmos DB-Kontodaten lesen. Informationen zum Verwalten von Azure Cosmos DB-Konten finden Sie unter [Mitwirkender von DocumentDB-Konto](#documentdb-account-contributor). | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | [Cosmos DB-Operator](#cosmos-db-operator) | Ermöglicht das Verwalten von Azure Cosmos DB-Konten, aber nicht das Zugreifen auf deren Daten. Verhindert den Zugriff auf Kontoschlüssel und Verbindungszeichenfolgen. | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | [CosmosBackupOperator](#cosmosbackupoperator) | Kann eine Wiederherstellungsanforderung für eine Cosmos DB-Datenbank oder einen Container für ein Konto übermitteln. | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | [Mitwirkender von DocumentDB-Konto](#documentdb-account-contributor) | Kann Azure Cosmos DB-Konten verwalten. Azure Cosmos DB wurde früher als DocumentDB bezeichnet. | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | [Mitwirkender von Redis-Cache](#redis-cache-contributor) | Ermöglicht Ihnen das Verwalten von Redis Caches, nicht aber den Zugriff darauf. | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | [Mitwirkender von SQL DB](#sql-db-contributor) | Ermöglicht Ihnen das Verwalten von SQL-Datenbanken, nicht aber den Zugriff darauf. Darüber hinaus können Sie deren sicherheitsbezogenen Richtlinien oder übergeordneten SQL-Server nicht verwalten. | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | [Verwaltete SQL-Instanz: Mitwirkender](#sql-managed-instance-contributor) | Diese Rolle ermöglicht Ihnen das Verwalten verwalteter SQL-Instanzen und der erforderlichen Netzwerkkonfiguration, jedoch nicht das Erteilen des Zugriffs an andere. | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | [SQL-Sicherheits-Manager](#sql-security-manager) | Ermöglicht Ihnen das Verwalten von sicherheitsbezogenen Richtlinien von SQL-Server und Datenbanken, jedoch nicht den Zugriff darauf. | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | [Mitwirkender von SQL Server](#sql-server-contributor) | Diese Rolle ermöglicht es Ihnen, SQL-Server und -Datenbanken zu verwalten, gewährt Ihnen jedoch keinen Zugriff darauf und auch nicht auf deren sicherheitsbezogenen Richtlinien. | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Analyse** |  |  |
> | [Azure Event Hubs-Datenbesitzer](#azure-event-hubs-data-owner) | Ermöglicht den uneingeschränkten Zugriff auf die Azure Event Hubs-Ressourcen. | f526a384-b230-433a-b45c-95f59c4a2dec |
> | [Azure Event Hubs-Datenempfänger](#azure-event-hubs-data-receiver) | Ermöglicht Empfängern den Zugriff auf die Azure Event Hubs-Ressourcen. | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | [Azure Event Hubs-Datensender](#azure-event-hubs-data-sender) | Ermöglicht Absendern den Zugriff auf die Azure Event Hubs-Ressourcen. | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | [Mitwirkender von Data Factory](#data-factory-contributor) | Erstellen und verwalten Sie Data Factorys sowie die darin enthaltenen untergeordneten Ressourcen. | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | [Datenpurger](#data-purger) | Kann Analysedaten endgültig löschen. | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | [HDInsight-Clusteroperator](#hdinsight-cluster-operator) | Ermöglicht Ihnen das Lesen und Ändern von HDInsight-Clusterkonfigurationen. | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | [Mitwirkender für die HDInsight-Domänendienste](#hdinsight-domain-services-contributor) | Ermöglicht Ihnen, Vorgänge im Zusammenhang mit Domänendiensten, die für das HDInsight Enterprise-Sicherheitspaket erforderlich sind, zu lesen, zu erstellen, zu ändern und zu löschen. | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | [Log Analytics-Mitwirkender](#log-analytics-contributor) | Ein Log Analytics-Mitwirkender kann alle Überwachungsdaten lesen und Überwachungseinstellungen bearbeiten. Das Bearbeiten von Überwachungseinstellungen schließt folgende Aufgaben ein: Hinzufügen der VM-Erweiterung zu VMs, Lesen von Speicherkontoschlüsseln zum Konfigurieren von Protokollsammlungen aus Azure Storage, Erstellen und Konfigurieren von Automation-Konten, Hinzufügen von Lösungen, Konfigurieren der Azure-Diagnose für alle Azure-Ressourcen. | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | [Log Analytics-Leser](#log-analytics-reader) | Ein Log Analytics-Leser kann alle Überwachungsdaten anzeigen und durchsuchen sowie Überwachungseinstellungen anzeigen. Hierzu zählt auch die Anzeige der Konfiguration von Azure-Diagnosen für alle Azure-Ressourcen. | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | [Mitwirkender der Schemaregistrierung (Vorschau)](#schema-registry-contributor-preview) | Lesen, Schreiben und Löschen von Schemaregistrierungsgruppen und Schemas. | 5dffeca3-4936-4216-b2bc-10343a5abb25 |
> | [Schemaregistrierungsleser (Vorschau)](#schema-registry-reader-preview) | Lesen und Auflisten von Schemaregistrierungsgruppen und Schemas. | 2c56ea50-c6b3-40a6-83c0-9d98858bc7d2 |
> | **Blockchain** |  |  |
> | [Zugriff auf Blockchainmitgliedsknoten (Vorschauversion)](#blockchain-member-node-access-preview) | Ermöglicht den Zugriff auf Blockchainmitgliedsknoten. | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | **KI und Machine Learning** |  |  |
> | [Mitwirkender für Cognitive Services](#cognitive-services-contributor) | Ermöglicht Ihnen das Erstellen, Lesen, Aktualisieren, Löschen und Verwalten von Cognitive Services-Schlüsseln. | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | [Cognitive Services-Datenleser (Vorschau)](#cognitive-services-data-reader-preview) | Ermöglicht das Lesen von Cognitive Services-Daten. | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | [Cognitive Services-Benutzer](#cognitive-services-user) | Ermöglicht Ihnen das Lesen und Auflisten von Cognitive Services-Schlüsseln. | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **Mixed Reality** |  |  |
> | [Remote Rendering-Administrator](#remote-rendering-administrator) | Bietet dem Benutzer Konvertierungs-, Sitzungsverwaltungs-, Rendering- und Diagnosefunktionen für Azure Remote Rendering. | 3df8b902-2a6f-47c7-8cc5-360e9b272a7e |
> | [Remote Rendering-Client](#remote-rendering-client) | Bietet dem Benutzer Sitzungsverwaltungs-, Rendering- und Diagnosefunktionen für Azure Remote Rendering. | d39065c4-c120-43c9-ab0a-63eed9795f0a |
> | [Spatial Anchors-Kontomitwirkender](#spatial-anchors-account-contributor) | Ermöglicht Ihnen das Verwalten von Raumankern in Ihrem Konto, nicht jedoch das Löschen von Ankern. | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | [Spatial Anchors-Kontobesitzer](#spatial-anchors-account-owner) | Ermöglicht Ihnen das Verwalten von Raumankern in Ihrem Konto, einschließlich der Löschung von Ankern. | 70bbe301-9835-447d-afdd-19eb3167307c |
> | [Spatial Anchors-Kontoleser](#spatial-anchors-account-reader) | Ermöglicht Ihnen das Ermitteln und Lesen von Eigenschaften für Raumanker in Ihrem Dokument. | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | **Integration** |  |  |
> | [Mitwirkender des API-Verwaltungsdienstes](#api-management-service-contributor) | Kann Dienst und APIs verwalten. | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | [Operatorrolle des API Management-Diensts](#api-management-service-operator-role) | Kann den Dienst, aber nicht die APIs verwalten. | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | [Leserrolle des API Management-Diensts](#api-management-service-reader-role) | Schreibgeschützter Zugriff auf Dienst und APIs | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | [App Configuration-Datenbesitzer](#app-configuration-data-owner) | Ermöglicht den Vollzugriff auf App Configuration-Daten. | 5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b |
> | [App Configuration-Datenleser](#app-configuration-data-reader) | Ermöglicht den Lesezugriff auf App Configuration-Daten. | 516239f1-63e1-4d78-a4de-a74fb236a071 |
> | [Azure Service Bus-Datenbesitzer](#azure-service-bus-data-owner) | Ermöglicht den uneingeschränkten Zugriff auf die Azure Service Bus-Ressourcen. | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | [Azure Service Bus-Datenempfänger](#azure-service-bus-data-receiver) | Ermöglicht Empfängern den Zugriff auf die Azure Service Bus-Ressourcen. | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
> | [Azure Service Bus-Datensender](#azure-service-bus-data-sender) | Ermöglicht Absendern den Zugriff auf die Azure Service Bus-Ressourcen. | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | [Besitzer der Azure Stack-Registrierung](#azure-stack-registration-owner) | Ermöglicht Ihnen die Verwaltung von Azure Stack-Registrierungen. | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | [EventGrid EventSubscription-Mitwirkender](#eventgrid-eventsubscription-contributor) | Ermöglicht die Verwaltung von EventGrid-Ereignisabonnementvorgängen. | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | [EventGrid EventSubscription-Leser](#eventgrid-eventsubscription-reader) | Ermöglicht das Lesen von EventGrid-Ereignisabonnements. | 2414bbcf-6497-4faf-8c65-045460748405 |
> | [Mitwirkender an FHIR-Daten](#fhir-data-contributor) | Die Rolle ermöglicht dem Benutzer oder Prinzipal vollen Zugriff auf FHIR-Daten. | 5a1fc7df-4bf1-4951-a576-89034ee01acd |
> | [FHIR-Datenexportierer](#fhir-data-exporter) | Die Rolle ermöglicht dem Benutzer oder Prinzipal das Lesen und Exportieren von FHIR-Daten. | 3db33094-8700-4567-8da5-1501d4e7e843 |
> | [FHIR-Datenleser](#fhir-data-reader) | Die Rolle ermöglicht dem Benutzer oder Prinzipal das Lesen von FHIR-Daten. | 4c8d0bbc-75d3-4935-991f-5f3c56d81508 |
> | [FHIR-Datenschreiber](#fhir-data-writer) | Die Rolle ermöglicht dem Benutzer oder Prinzipal das Lesen und Schreiben von FHIR-Daten. | 3f88fce4-5892-4214-ae73-ba5294559913 |
> | [Mitwirkender für Integrationsdienstumgebungen](#integration-service-environment-contributor) | Hiermit wird das Verwalten von Integrationsdienstumgebungen ermöglicht, nicht aber der Zugriff auf diese. | a41e2c5b-bd99-4a07-88f4-9bf657a760b8 |
> | [Entwickler für Integrationsdienstumgebungen](#integration-service-environment-developer) | Hiermit wird Entwicklern das Erstellen und Aktualisieren von Workflows, Integrationskonten und API-Verbindungen in Integrationsdienstumgebungen ermöglicht. | c7aa55d3-1abb-444a-a5ca-5e51e485d6ec |
> | [Mitwirkender von Intelligent Systems-Konto](#intelligent-systems-account-contributor) | Ermöglicht Ihnen das Verwalten von Intelligent Systems-Konten, nicht aber den Zugriff darauf. | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | [Logik-App-Mitwirkender](#logic-app-contributor) | Ermöglicht Ihnen die Verwaltung von Logik-Apps. Sie können aber nicht den App-Zugriff ändern. | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | [Logik-App-Operator](#logic-app-operator) | Ermöglicht Ihnen das Lesen, Aktivieren und Deaktivieren von Logik-Apps. Sie können diese aber nicht bearbeiten oder aktualisieren. | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **Identität** |  |  |
> | [Mitwirkender für verwaltete Identität](#managed-identity-contributor) | Dem Benutzer zugewiesene Identität erstellen, lesen, aktualisieren und löschen. | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | [Operator für verwaltete Identität](#managed-identity-operator) | Dem Benutzer zugewiesene Identität lesen und zuweisen. | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Security** |  |  |
> | [Azure Sentinel-Mitwirkender](#azure-sentinel-contributor) | Azure Sentinel-Mitwirkender | ab8e14d6-4a74-4a29-9ba8-549422addade |
> | [Azure Sentinel-Leser](#azure-sentinel-reader) | Azure Sentinel-Leser | 8d289c81-5878-46d4-8554-54e1e3d8b5cb |
> | [Azure Sentinel-Antwortender](#azure-sentinel-responder) | Azure Sentinel-Antwortender | 3e150937-b8fe-4cfb-8069-0eaf05ecd056 |
> | [Key Vault-Administrator (Vorschau)](#key-vault-administrator-preview) | Ausführen beliebiger Vorgänge auf Datenebene für einen Schlüsseltresor und alle darin enthaltenen Objekte (einschließlich Zertifikate, Schlüssel und Geheimnisse). Kann keine Key Vault-Ressourcen oder Rollenzuweisungen verwalten. Funktioniert nur für Schlüsseltresore, die das Berechtigungsmodell „Rollenbasierte Azure-Zugriffssteuerung“ verwenden. | 00482a5a-887f-4fb3-b363-3b7fe8e74483 |
> | [Key Vault Certificates Officer (Vorschau)](#key-vault-certificates-officer-preview) | Ausführen beliebiger Aktionen für die Zertifikate eines Schlüsseltresors mit Ausnahme der Verwaltung von Berechtigungen. Funktioniert nur für Schlüsseltresore, die das Berechtigungsmodell „Rollenbasierte Azure-Zugriffssteuerung“ verwenden. | a4417e6f-fecd-4de8-b567-7b0420556985 |
> | [Key Vault-Mitwirkender](#key-vault-contributor) | Verwalten von Schlüsseltresoren, gestattet Ihnen jedoch nicht, Rollen in Azure RBAC zuzuweisen, und ermöglicht keinen Zugriff auf Geheimnisse, Schlüssel oder Zertifikate. | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | [Key Vault Crypto Officer (Vorschau)](#key-vault-crypto-officer-preview) | Ausführen beliebiger Aktionen für die Schlüssel eines Schlüsseltresors mit Ausnahme der Verwaltung von Berechtigungen. Funktioniert nur für Schlüsseltresore, die das Berechtigungsmodell „Rollenbasierte Azure-Zugriffssteuerung“ verwenden. | 14b46e9e-c2b7-41b4-b07b-48a6ebf60603 |
> | [Key Vault Crypto Service Encryption (Vorschau)](#key-vault-crypto-service-encryption-preview) | Lesen von Metadaten von Schlüsseln und Ausführen von Vorgängen zum Packen/Entpacken. Funktioniert nur für Schlüsseltresore, die das Berechtigungsmodell „Rollenbasierte Azure-Zugriffssteuerung“ verwenden. | e147488a-f6f5-4113-8e2d-b22465e65bf6 |
> | [Key Vault Crypto-Benutzer (Vorschau)](#key-vault-crypto-user-preview) | Ausführen kryptografischer Vorgänge mithilfe von Schlüsseln. Funktioniert nur für Schlüsseltresore, die das Berechtigungsmodell „Rollenbasierte Azure-Zugriffssteuerung“ verwenden. | 12338af0-0e69-4776-bea7-57ae8d297424 |
> | [Key Vault Reader (Vorschau)](#key-vault-reader-preview) | Lesen von Metadaten von Schlüsseltresoren und deren Zertifikaten, Schlüsseln und Geheimnissen. Sensible Werte, z. B. der Inhalt von Geheimnissen oder Schlüsselmaterial, können nicht gelesen werden. Funktioniert nur für Schlüsseltresore, die das Berechtigungsmodell „Rollenbasierte Azure-Zugriffssteuerung“ verwenden. | 21090545-7ca7-4776-b22c-e363652d74d2 |
> | [Key Vault Secrets Officer (Vorschau)](#key-vault-secrets-officer-preview) | Ausführen beliebiger Aktionen für die Geheimnisse eines Schlüsseltresors mit Ausnahme der Verwaltung von Berechtigungen. Funktioniert nur für Schlüsseltresore, die das Berechtigungsmodell „Rollenbasierte Azure-Zugriffssteuerung“ verwenden. | b86a8fe4-44ce-4948-aee5-eccb2c155cd7 |
> | [Key Vault Secrets-Benutzer (Vorschau)](#key-vault-secrets-user-preview) | Lesen der Inhalte von Geheimnissen. Funktioniert nur für Schlüsseltresore, die das Berechtigungsmodell „Rollenbasierte Azure-Zugriffssteuerung“ verwenden. | 4633458b-17de-408a-b874-0445c86b69e6 |
> | [Sicherheitsadministrator](#security-admin) | Anzeigen und Aktualisieren von Berechtigungen für Security Center. Gleiche Rechte wie der Sicherheitsleseberechtigte und kann darüber hinaus die Sicherheitsrichtlinie aktualisieren sowie Warnungen und Empfehlungen verwerfen. | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | [Mitwirkender für Sicherheitsbewertungen](#security-assessment-contributor) | Ermöglicht das Pushen von Bewertungen an Security Center | 612c2aa1-cb24-443b-ac28-3ab7272de6f5 |
> | [Sicherheits-Manager (Legacy)](#security-manager-legacy) | Dies ist eine Legacyrolle. Verwenden Sie stattdessen „Sicherheitsadministrator“. | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | [Sicherheitsleseberechtigter](#security-reader) | Anzeigen von Berechtigungen für Security Center. Kann Empfehlungen, Warnungen, Sicherheitsrichtlinien und Sicherheitszustände anzeigen, jedoch keine Änderungen vornehmen. | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **DevOps** |  |  |
> | [DevTest Labs-Benutzer](#devtest-labs-user) | Ermöglicht Ihnen das Verbinden, Starten, Neustarten und Herunterfahren Ihrer virtuellen Computer in Ihren Azure DevTest Labs. | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | [Lab-Ersteller](#lab-creator) | Ermöglicht Ihnen das Erstellen neuer Labs unter ihren Azure Lab-Konten. | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Überwachen** |  |  |
> | [Mitwirkender der Application Insights-Komponente](#application-insights-component-contributor) | Kann Application Insights-Komponenten verwalten | ae349356-3a1b-4a5e-921d-050484c6347e |
> | [Application Insights-Momentaufnahmedebugger](#application-insights-snapshot-debugger) | Gibt dem Benutzer die Berechtigung zum Anzeigen und Herunterladen von Debugmomentaufnahmen, die mit dem Application Insights-Momentaufnahmedebugger erfasst wurden. Beachten Sie, dass diese Berechtigungen in der Rolle [Besitzer](#owner) oder [Mitwirkender](#contributor) nicht enthalten sind. Die Application Insights-Rolle „Momentaufnahmedebugger“ muss Benutzern direkt zugewiesen werden. Die Rolle wird nicht erkannt, wenn sie einer benutzerdefinierten Rolle hinzugefügt wird. | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | [Überwachungsmitwirkender](#monitoring-contributor) | Kann sämtliche Überwachungsdaten lesen und Überwachungseinstellungen bearbeiten. Siehe auch [Erste Schritte mit Rollen, Berechtigungen und Sicherheit in Azure Monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | [Herausgeber von Überwachungsmetriken](#monitoring-metrics-publisher) | Ermöglicht die Veröffentlichung von Metriken für Azure-Ressourcen. | 3913510d-42f4-4e42-8a64-420c390055eb |
> | [Überwachungsleser](#monitoring-reader) | Kann alle Überwachungsdaten (Metriken, Protokolle usw.) lesen. Siehe auch [Erste Schritte mit Rollen, Berechtigungen und Sicherheit in Azure Monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | [Arbeitsmappenmitwirkender](#workbook-contributor) | Kann freigegebene Arbeitsmappen speichern. | e8ddcd69-c73f-4f9f-9844-4100522f16ad |
> | [Arbeitsmappenleser](#workbook-reader) | Kann Arbeitsmappen lesen. | b279062a-9be3-42a0-92ae-8b3cf002ec4d |
> | **Verwaltung und Governance** |  |  |
> | [Automation-Auftragsoperator](#automation-job-operator) | Hiermit werden Aufträge mithilfe von Automation-Runbooks erstellt und verwaltet. | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | [Operator für Automation](#automation-operator) | Automatisierungsoperatoren können Aufträge starten, beenden, anhalten und fortsetzen. | d3881f73-407a-4167-8283-e981cbba0404 |
> | [Automation-Runbookoperator](#automation-runbook-operator) | Runbookeigenschaften lesen: Ermöglicht das Erstellen von Runbookaufträgen. | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | [Onboarding von Azure Connected Machine](#azure-connected-machine-onboarding) | Kann Azure Connected Machine integrieren. | b64e21ea-ac4e-4cdf-9dc9-5b892992bee7 |
> | [Ressourcenadministrator für Azure Connected Machine](#azure-connected-machine-resource-administrator) | Kann Azure Connected Machines lesen, schreiben, löschen und erneut integrieren. | cd570a14-e51a-42ad-bac8-bafd67325302 |
> | [Abrechnungsleser](#billing-reader) | Hiermit wird Lesezugriff auf Abrechnungsdaten ermöglicht. | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | [Blueprint-Mitwirkender](#blueprint-contributor) | Kann Blaupausendefinitionen verwalten, aber nicht zuweisen. | 41077137-e803-4205-871c-5a86e6a753b4 |
> | [Blueprint-Operator](#blueprint-operator) | Kann vorhandene veröffentlichte Blaupausen zuweisen, aber keine neuen Blaupausen erstellen. Beachten Sie, dass dies nur funktioniert, wenn die Zuweisung mit einer vom Benutzer zugewiesenen verwalteten Identität erfolgt. | 437d2ced-4a38-4302-8479-ed2bcb43d090 |
> | [Mitwirkender für Cost Management](#cost-management-contributor) | Ermöglicht Ihnen das Anzeigen der Kosten und das Verwalten der Kostenkonfiguration (z. B. Budgets, Exporte). | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | [Cost Management-Leser](#cost-management-reader) | Ermöglicht Ihnen das Anzeigen der Kostendaten und -konfiguration (z. B. Budgets, Exporte). | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | [Hierarchieeinstellungsadministrator](#hierarchy-settings-administrator) | Ermöglicht Benutzern das Bearbeiten und Löschen von Hierarchieeinstellungen. | 350f8d15-c687-4448-8ae1-157740a3936d |
> | [Kubernetes-Cluster – Azure Arc-Onboarding](#kubernetes-cluster---azure-arc-onboarding) | Rollendefinition zum Autorisieren eines Benutzers/Diensts zum Erstellen einer connectedClusters-Ressource | 34e09817-6cbe-4d01-b1a2-e0eac5743d41 |
> | [Rolle „Mitwirkender für verwaltete Anwendungen“](#managed-application-contributor-role) | Ermöglicht das Erstellen von Ressourcen für verwaltete Anwendungen. | 641177b8-a67a-45b9-a033-47bc880bb21e |
> | [Rolle „Bediener für verwaltete Anwendung“](#managed-application-operator-role) | Ermöglicht Ihnen das Lesen und Durchführen von Aktionen für Ressourcen der verwalteten Anwendung. | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | [Leser für verwaltete Anwendungen](#managed-applications-reader) | Ermöglicht Ihnen, Ressourcen in einer verwalteten App zu lesen und JIT-Zugriff anzufordern. | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | [Rolle „Registrierungszuweisung für verwaltete Dienste löschen“](#managed-services-registration-assignment-delete-role) | Mit der Rolle „Registrierungszuweisung für verwaltete Dienste löschen“ können Benutzer des verwaltenden Mandanten die ihrem Mandanten zugewiesene Registrierungszuweisung löschen. | 91c1777a-f3dc-4fae-b103-61d183457e46 |
> | [Verwaltungsgruppenmitwirkender](#management-group-contributor) | Rolle „Verwaltungsgruppenmitwirkender“ | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | [Verwaltungsgruppenleser](#management-group-reader) | Rolle „Verwaltungsgruppenleser“ | ac63b705-F282-497d-ac71-919bf39d939d |
> | [Mitwirkender von New Relic APM-Konto](#new-relic-apm-account-contributor) | Ermöglicht Ihnen das Verwalten von New Relic Application Performance Management-Konten und -Anwendungen, nicht aber den Zugriff auf diese. | 5d28c62d-5b37-4476-8438-e587778df237 |
> | [Policy Insights-Datenschreiber (Vorschau)](#policy-insights-data-writer-preview) | Ermöglicht Lesezugriff auf Ressourcenrichtlinien und Schreibzugriff auf Richtlinienereignisse für Ressourcenkomponenten. | 66bb4e9e-b016-4a94-8249-4c0511c2be84 |
> | [Mitwirkender bei Ressourcenrichtlinien](#resource-policy-contributor) | Benutzer mit Rechten zum Erstellen/Ändern der Ressourcenrichtlinie, zum Erstellen eines Supporttickets und zum Lesen von Ressourcen/der Hierarchie. | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | [Site Recovery-Mitwirkender](#site-recovery-contributor) | Ermöglicht Ihnen die Verwaltung des Site Recovery-Diensts mit Ausnahme der Tresorerstellung und der Rollenzuweisung. | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | [Site Recovery-Operator](#site-recovery-operator) | Ermöglicht Ihnen ein Failover und ein Failback, aber nicht das Durchführen weiterer Site Recovery-Verwaltungsvorgänge. | 494ae006-db33-4328-bf46-533a6560a3ca |
> | [Site Recovery-Leser](#site-recovery-reader) | Ermöglicht Ihnen die Anzeige des Site Recovery-Status, aber nicht die Durchführung weiterer Verwaltungsvorgänge. | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | [Mitwirkender für Supportanfragen](#support-request-contributor) | Ermöglicht Ihnen die Erstellung und Verwaltung von Supportanfragen. | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | [Tagmitwirkender](#tag-contributor) | Hiermit können Tags für Entitäten verwaltet werden, ohne Zugriff auf die Entitäten selbst zu gewähren. | 4a9ae827-6dc8-4573-8ac7-8239d42aa03f |
> | **Andere** |  |  |
> | [Azure Digital Twins Data Owner (Azure Digital Twins-Datenbesitzer)](#azure-digital-twins-data-owner) | Vollzugriffsrolle für Digital Twins-Datenebene | bcd981a7-7f74-457b-83e1-cceb9e632ffe |
> | [Azure Digital Twins Data Reader (Azure Digital Twins-Datenleser)](#azure-digital-twins-data-reader) | Schreibgeschützte Rolle für Digital Twins-Datenebeneneigenschaften | d57506d4-4c8d-48b1-8587-93c323f6a5a3 |
> | [Mitwirkender von BizTalk](#biztalk-contributor) | Ermöglicht Ihnen das Verwalten von BizTalk-Diensten, nicht aber den Zugriff darauf. | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | [Desktopvirtualisierungsbenutzer](#desktop-virtualization-user) | Ermöglicht dem Benutzer die Verwendung der Anwendungen in einer Anwendungsgruppe. | 1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63 |
> | [Mitwirkender von Zeitplanungsauftragssammlung](#scheduler-job-collections-contributor) | Ermöglicht Ihnen das Verwalten von Scheduler-Auftragssammlungen, nicht aber den Zugriff darauf. | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |


## <a name="general"></a>Allgemein


### <a name="contributor"></a>Mitwirkender

Hiermit wird Vollzugriff zum Verwalten aller Ressourcen gewährt, allerdings nicht zum Zuweisen von Rollen in Azure RBAC. [Weitere Informationen](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | * | Erstellen und Verwalten von Ressourcen aller Typen |
> | **NotActions** |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/Delete | Löschen von Rollen, Richtlinienzuweisungen, Richtliniendefinitionen und Richtliniensatzdefinitionen |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/Write | Erstellen von Rollen, Rollenzuweisungen, Richtlinienzuweisungen, Richtliniendefinitionen und Richtliniensatzdefinitionen |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/elevateAccess/Action | Gewährt dem Aufrufer Zugriff vom Typ „Benutzerzugriffsadministrator“ auf der Mandantenebene. |
> | [Microsoft.Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/write | Erstellt oder aktualisiert alle Blaupausenzuweisungen |
> | [Microsoft.Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/delete | Löscht alle Blaupausenzuweisungen |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/galleries/share/action | Gibt einen Katalog in verschiedenen Bereichen frei. |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants full access to manage all resources, but does not allow you to assign roles in Azure RBAC.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
  "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [
        "Microsoft.Authorization/*/Delete",
        "Microsoft.Authorization/*/Write",
        "Microsoft.Authorization/elevateAccess/Action",
        "Microsoft.Blueprint/blueprintAssignments/write",
        "Microsoft.Blueprint/blueprintAssignments/delete",
        "Microsoft.Compute/galleries/share/action"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="owner"></a>Besitzer

Hiermit wird Vollzugriff zum Verwalten aller Ressourcen gewährt, einschließlich der Möglichkeit, Rollen in Azure RBAC zuzuweisen. [Weitere Informationen](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | * | Erstellen und Verwalten von Ressourcen aller Typen |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants full access to manage all resources, including the ability to assign roles in Azure RBAC.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
  "name": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reader"></a>Leser

Hiermit können Sie alle Ressourcen anzeigen, aber keine Änderungen vornehmen. [Weitere Informationen](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | */Lesen | Lesen von Ressourcen aller Typen mit Ausnahme geheimer Schlüssel |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View all resources, but does not allow you to make any changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
  "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
  "permissions": [
    {
      "actions": [
        "*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="user-access-administrator"></a>Benutzerzugriffsadministrator

Ermöglicht Ihnen die Verwaltung von Benutzerzugriffen auf Azure-Ressourcen. [Weitere Informationen](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | */Lesen | Lesen von Ressourcen aller Typen mit Ausnahme geheimer Schlüssel |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/* | Verwalten der Autorisierung |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage user access to Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
  "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Authorization/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "User Access Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="compute"></a>Compute


### <a name="classic-virtual-machine-contributor"></a>Mitwirkender von klassischen virtuellen Computern

Ermöglicht Ihnen das Verwalten klassischer virtueller Computer, aber weder den Zugriff darauf noch auf die mit ihnen verbundenen virtuellen Netzwerke oder Speicherkonten.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/domainNames/* | Erstellen und Verwalten von klassischen Compute-Domänennamen |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/* | Erstellen und Verwalten von virtuellen Computern |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/networkSecurityGroups/join/action |  |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/reservedIps/link/action | Dient zum Verknüpfen einer reservierten IP-Adresse. |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/reservedIps/read | Ruft die reservierten IP-Adressen ab. |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/virtualNetworks/join/action | Führt zum Beitritt zum virtuellen Netzwerk. |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/virtualNetworks/read | Dient zum Abrufen des virtuellen Netzwerks. |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/disks/read | Gibt den Speicherkontodatenträger zurück. |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/images/read | Gibt das Speicherkontoimage zurück. (Veraltet. Verwenden Sie „Microsoft.ClassicStorage/storageAccounts/vmImages“) |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/action | Listet die Zugriffsschlüssel für die Speicherkonten auf. |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/read | Dient zum Zurückgeben des Speicherkontos mit dem angegebenen Konto. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic virtual machines, but not access to them, and not the virtual network or storage account they're connected to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d73bb868-a0df-4d4d-bd69-98a00b01fccb",
  "name": "d73bb868-a0df-4d4d-bd69-98a00b01fccb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicCompute/domainNames/*",
        "Microsoft.ClassicCompute/virtualMachines/*",
        "Microsoft.ClassicNetwork/networkSecurityGroups/join/action",
        "Microsoft.ClassicNetwork/reservedIps/link/action",
        "Microsoft.ClassicNetwork/reservedIps/read",
        "Microsoft.ClassicNetwork/virtualNetworks/join/action",
        "Microsoft.ClassicNetwork/virtualNetworks/read",
        "Microsoft.ClassicStorage/storageAccounts/disks/read",
        "Microsoft.ClassicStorage/storageAccounts/images/read",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.ClassicStorage/storageAccounts/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Virtual Machine Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-administrator-login"></a>VM-Administratoranmeldung

Anzeigen von virtuellen Computern im Portal und Anmelden als Administrator [Weitere Informationen](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | Ruft eine Definition für eine öffentliche IP-Adresse ab. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Dient zum Abrufen der Definition des virtuellen Netzwerks. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/read | Ruft eine Lastenausgleichsdefinition ab. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | Ruft eine Netzwerkschnittstellendefinition ab.  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/read |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/login/action | Hiermit melden Sie sich bei einem virtuellen Computer als normaler Benutzer an. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/loginAsAdmin/action | Hiermit melden Sie sich bei einem virtuellen Computer mit Windows-Administrator- oder Linux-Root-Benutzerrechten an. |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View Virtual Machines in the portal and login as administrator",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1c0163c0-47e6-4577-8991-ea5c82e286e4",
  "name": "1c0163c0-47e6-4577-8991-ea5c82e286e4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Compute/virtualMachines/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Compute/virtualMachines/login/action",
        "Microsoft.Compute/virtualMachines/loginAsAdmin/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine Administrator Login",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-contributor"></a>Mitwirkender von virtuellen Computern

Ermöglicht Ihnen das Verwalten virtueller Computer, aber weder den Zugriff darauf, noch auf deren verbundenen virtuellen Netzwerke oder Speicherkonten. [Weitere Informationen](../virtual-machines/linux/tutorial-govern-resources.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/availabilitySets/* | Erstellen und Verwalten von Compute-Verfügbarkeitsgruppen |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/locations/* | Erstellen und Verwalten von Compute-Speicherorten |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/* | Ausführen beliebiger Aktionen für virtuelle Computer, einschließlich Erstellen, Aktualisieren, Löschen, Starten, Neustarten und Ausschalten virtueller Computer. Ausführen vordefinierter Skripts auf virtuellen Computern. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachineScaleSets/* | Erstellen und Verwalten von Skalierungsgruppen für virtuelle Computer |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/write | Erstellt einen neuen Datenträger oder aktualisiert einen bereits vorhandenen. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/read | Dient zum Abrufen der Eigenschaften eines Datenträgers. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/delete | Löscht den Datenträger. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/schedules/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/applicationGateways/backendAddressPools/join/action | Verknüpft einen Back-End-Adresspool für ein Application Gateway. Nicht warnbar. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/backendAddressPools/join/action | Verknüpft einen Back-End-Adresspool für den Lastenausgleich. Nicht warnbar. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatPools/join/action | Verknüpft einen NAT-Pool für eingehenden Datenverkehr für den Lastenausgleich. Nicht warnbar. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatRules/join/action | Verknüpft eine NAT-Regel für eingehenden Datenverkehr für den Lastenausgleich. Nicht warnbar. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/probes/join/action | Ermöglicht die Verwendung von Prüfpunkten eines Lastenausgleichs. Beispielsweise kann mit dieser Berechtigung die healthProbe-Eigenschaft einer VM-Skalierungsgruppe auf den Prüfpunkt verweisen. Nicht warnbar. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/read | Ruft eine Lastenausgleichsdefinition ab. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/locations/* | Erstellen und Verwalten von Netzwerkspeicherorten |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/* | Erstellen und Verwalten von Netzwerkschnittstellen |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/join/action | Verknüpft eine Netzwerksicherheitsgruppe. Nicht warnbar. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/read | Ruft eine Netzwerksicherheitsgruppen-Definition ab. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/join/action | Verknüpft eine öffentliche IP-Adresse. Nicht warnbar. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | Ruft eine Definition für eine öffentliche IP-Adresse ab. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Dient zum Abrufen der Definition des virtuellen Netzwerks. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | Verknüpft ein virtuelles Netzwerk. Nicht warnbar. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/write | Erstellt einen beabsichtigten Sicherungsschutz. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/*/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/read | Gibt Objektdetails des geschützten Elements zurück. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/write | Dient zum Erstellen eines geschützten Elements für die Sicherung. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/read | Gibt alle Schutzrichtlinien zurück. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/write | Erstellt Schutzrichtlinien. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | Der Vorgang „Tresor abrufen“ ruft ein Objekt ab, das die Azure-Ressource vom Typ „Tresor“ darstellt. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Gibt Nutzungsdetails für einen Recovery Services-Tresor zurück. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/write | Der Vorgang „Tresor erstellen“ erstellt eine Azure-Ressource vom Typ „Tresor“. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.SqlVirtualMachine](resource-provider-operations.md#microsoftsqlvirtualmachine)/* |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | Gibt die Zugriffsschlüssel für das angegebene Speicherkonto zurück. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Gibt die Liste mit Speicherkonten zurück oder ruft die Eigenschaften für das angegebene Speicherkonto ab. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they're connected to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/availabilitySets/*",
        "Microsoft.Compute/locations/*",
        "Microsoft.Compute/virtualMachines/*",
        "Microsoft.Compute/virtualMachineScaleSets/*",
        "Microsoft.Compute/disks/write",
        "Microsoft.Compute/disks/read",
        "Microsoft.Compute/disks/delete",
        "Microsoft.DevTestLab/schedules/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
        "Microsoft.Network/loadBalancers/probes/join/action",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/locations/*",
        "Microsoft.Network/networkInterfaces/*",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Network/networkSecurityGroups/read",
        "Microsoft.Network/publicIPAddresses/join/action",
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.RecoveryServices/locations/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/write",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/write",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.SqlVirtualMachine/*",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-user-login"></a>VM-Benutzeranmeldung

Anzeigen von virtuellen Computern im Portal und Anmelden als regulärer Benutzer. [Weitere Informationen](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | Ruft eine Definition für eine öffentliche IP-Adresse ab. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Dient zum Abrufen der Definition des virtuellen Netzwerks. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/read | Ruft eine Lastenausgleichsdefinition ab. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | Ruft eine Netzwerkschnittstellendefinition ab.  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/read |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/login/action | Hiermit melden Sie sich bei einem virtuellen Computer als normaler Benutzer an. |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View Virtual Machines in the portal and login as a regular user.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fb879df8-f326-4884-b1cf-06f3ad86be52",
  "name": "fb879df8-f326-4884-b1cf-06f3ad86be52",
  "permissions": [
    {
      "actions": [
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Compute/virtualMachines/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Compute/virtualMachines/login/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine User Login",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="networking"></a>Netzwerk


### <a name="cdn-endpoint-contributor"></a>Mitwirkender für den CDN-Endpunkt

Diese Rolle kann CDN-Endpunkte verwalten, aber anderen Benutzern keinen Zugriff erteilen.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/profiles/endpoints/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN endpoints, but can't grant access to other users.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/426e0c7f-0c7e-4658-b36f-ff54d6c29b45",
  "name": "426e0c7f-0c7e-4658-b36f-ff54d6c29b45",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/endpoints/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Endpoint Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-endpoint-reader"></a>CDN-Endpunktleser

Diese Rolle kann CDN-Endpunkte anzeigen, aber keine Änderungen vornehmen.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/profiles/endpoints/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN endpoints, but can't make changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/871e35f6-b5c1-49cc-a043-bde969a0f2cd",
  "name": "871e35f6-b5c1-49cc-a043-bde969a0f2cd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/endpoints/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Endpoint Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-profile-contributor"></a>Mitwirkender für das CDN-Profil

Diese Rolle kann CDN-Profile und deren Endpunkte verwalten, aber anderen Benutzern keinen Zugriff erteilen. [Weitere Informationen](../cdn/cdn-app-dev-net.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/profiles/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN profiles and their endpoints, but can't grant access to other users.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ec156ff8-a8d1-4d15-830c-5b80698ca432",
  "name": "ec156ff8-a8d1-4d15-830c-5b80698ca432",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Profile Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-profile-reader"></a>CDN-Profilleser

Diese Rolle kann CDN-Profile und deren Endpunkte anzeigen, aber keine Änderungen vornehmen.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/profiles/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN profiles and their endpoints, but can't make changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8f96442b-4075-438f-813d-ad51ab4019af",
  "name": "8f96442b-4075-438f-813d-ad51ab4019af",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Profile Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-network-contributor"></a>Mitwirkender von klassischem Netzwerk

Ermöglicht Ihnen das Verwalten von klassischen Netzwerken, nicht aber den Zugriff darauf. [Weitere Informationen](../virtual-network/virtual-network-manage-peering.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/* | Erstellen und Verwalten von klassischen Netzwerken |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic networks, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b34d265f-36f7-4a0d-a4d4-e158ca92e90f",
  "name": "b34d265f-36f7-4a0d-a4d4-e158ca92e90f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicNetwork/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Network Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="dns-zone-contributor"></a>DNS Zone Contributor

Ermöglicht Ihnen die Verwaltung von DNS-Zonen und Ressourceneintragssätzen in Azure DNS, aber nicht zu steuern, wer darauf Zugriff hat. [Weitere Informationen](../dns/dns-protect-zones-recordsets.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/dnsZones/* | Erstellen und Verwalten von DNS-Zonen und -Einträgen |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage DNS zones and record sets in Azure DNS, but does not let you control who has access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/befefa01-2a29-4197-83a8-272ff33ce314",
  "name": "befefa01-2a29-4197-83a8-272ff33ce314",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/dnsZones/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DNS Zone Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="network-contributor"></a>Mitwirkender von virtuellem Netzwerk

Ermöglicht Ihnen das Verwalten von Netzwerken, nicht aber den Zugriff darauf.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/* | Erstellen und Verwalten von Netzwerken |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage networks, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4d97b98b-1d4f-4787-a291-c67834d212e7",
  "name": "4d97b98b-1d4f-4787-a291-c67834d212e7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Network Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="private-dns-zone-contributor"></a>Mitwirkender für private DNS-Zone

Ermöglicht Ihnen das Verwalten privater DNS-Zonenressourcen, aber nicht der virtuellen Netzwerke, mit denen sie verknüpft sind. [Weitere Informationen](../dns/dns-protect-private-zones-recordsets.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/privateDnsZones/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/privateDnsOperationResults/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/privateDnsOperationStatuses/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Dient zum Abrufen der Definition des virtuellen Netzwerks. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/join/action | Verknüpft ein virtuelles Netzwerk. Nicht warnbar. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage private DNS zone resources, but not the virtual networks they are linked to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b12aa53e-6015-4669-85d0-8515ebb3ae7f",
  "name": "b12aa53e-6015-4669-85d0-8515ebb3ae7f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/privateDnsZones/*",
        "Microsoft.Network/privateDnsOperationResults/*",
        "Microsoft.Network/privateDnsOperationStatuses/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/join/action",
        "Microsoft.Authorization/*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Private DNS Zone Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="traffic-manager-contributor"></a>Traffic Manager-Mitwirkender

Ermöglicht Ihnen die Verwaltung von Traffic Manager-Profilen, aber nicht die Steuerung des Zugriffs darauf.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/trafficManagerProfiles/* |  |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Traffic Manager profiles, but does not let you control who has access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a4b10055-b0c7-44c2-b00f-c7b5b3550cf7",
  "name": "a4b10055-b0c7-44c2-b00f-c7b5b3550cf7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/trafficManagerProfiles/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Traffic Manager Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="storage"></a>Storage


### <a name="avere-contributor"></a>Avere-Mitwirkender

Kann einen Avere vFXT-Cluster erstellen und verwalten. [Weitere Informationen](../avere-vfxt/avere-vfxt-deploy-plan.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/*/read |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/availabilitySets/* |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/proximityPlacementGroups/* |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/* |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/*/read |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Dient zum Abrufen der Definition des virtuellen Netzwerks. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/read | Ruft eine Subnetzdefinition für virtuelle Netzwerke ab. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | Verknüpft ein virtuelles Netzwerk. Nicht warnbar. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | Verknüpft Ressourcen wie etwa ein Speicherkonto oder eine SQL-Datenbank mit einem Subnetz. Nicht warnbar. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/join/action | Verknüpft eine Netzwerksicherheitsgruppe. Nicht warnbar. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/*/read |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/* | Erstellen und Verwalten von Speicherkonten |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/resources/read | Ruft die Ressourcen für die Ressourcengruppe ab. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/delete | Gibt das Ergebnis beim Löschen eines Blobs zurück. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | Gibt ein Blob oder eine Liste von Blobs zurück. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/write | Gibt das Ergebnis beim Schreiben eines Blobs zurück. |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can create and manage an Avere vFXT cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4f8fab4f-1852-4a58-a46a-8eaf358af14a",
  "name": "4f8fab4f-1852-4a58-a46a-8eaf358af14a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/*/read",
        "Microsoft.Compute/availabilitySets/*",
        "Microsoft.Compute/proximityPlacementGroups/*",
        "Microsoft.Compute/virtualMachines/*",
        "Microsoft.Compute/disks/*",
        "Microsoft.Network/*/read",
        "Microsoft.Network/networkInterfaces/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/*/read",
        "Microsoft.Storage/storageAccounts/*",
        "Microsoft.Support/*",
        "Microsoft.Resources/subscriptions/resourceGroups/resources/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Avere Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="avere-operator"></a>Avere-Bediener

Wird vom Avere vFXT-Cluster zum Verwalten des Clusters verwendet [Weitere Informationen](../avere-vfxt/avere-vfxt-manage-cluster.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/read | Dient zum Abrufen der Eigenschaften eines virtuellen Computers. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | Ruft eine Netzwerkschnittstellendefinition ab.  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/write | Erstellt eine Netzwerkschnittstelle oder aktualisiert eine vorhandene Netzwerkschnittstelle.  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Dient zum Abrufen der Definition des virtuellen Netzwerks. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/read | Ruft eine Subnetzdefinition für virtuelle Netzwerke ab. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | Verknüpft ein virtuelles Netzwerk. Nicht warnbar. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/join/action | Verknüpft eine Netzwerksicherheitsgruppe. Nicht warnbar. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/delete | Gibt das Ergebnis beim Löschen eines Containers zurück. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/read | Hiermit wird eine Liste von Containern zurückgegeben. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/write | Gibt das Ergebnis des PUT-Vorgangs für den Blobcontainer zurück. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/delete | Gibt das Ergebnis beim Löschen eines Blobs zurück. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | Gibt ein Blob oder eine Liste von Blobs zurück. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/write | Gibt das Ergebnis beim Schreiben eines Blobs zurück. |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Used by the Avere vFXT cluster to manage the cluster",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c025889f-8102-4ebf-b32c-fc0c6f0c6bd9",
  "name": "c025889f-8102-4ebf-b32c-fc0c6f0c6bd9",
  "permissions": [
    {
      "actions": [
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Avere Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-contributor"></a>Mitwirkender für Sicherungen

Ermöglicht Ihnen die Verwaltung des Sicherungsdiensts. Sie können jedoch keine Tresore erstellen oder anderen Benutzern Zugriff gewähren. [Weitere Informationen](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Dient zum Abrufen der Definition des virtuellen Netzwerks. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/* | Verwalten der Ergebnisse eines Vorgangs in der Sicherungsverwaltung |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/* | Erstellen und Verwalten von Sicherungscontainern in Sicherungsfabrics des Recovery Services-Tresors |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/refreshContainers/action | Aktualisiert die Containerliste. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/* | Erstellen und Verwalten von Sicherungsaufträgen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/action | Dient zum Exportieren von Aufträgen. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/* | Erstellen und Verwalten der Ergebnisse von Sicherungsverwaltungsvorgängen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/* | Erstellen und Verwalten von Sicherungsrichtlinien |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectableItems/* | Erstellen und Verwalten von Elementen, die gesichert werden können |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/* | Erstellen und Verwalten von gesicherten Elementen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/* | Erstellen und Verwalten von Containern mit Sicherungselementen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupSecurityPIN/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/read | Gibt Zusammenfassungen für geschützte Elemente und geschützte Server für einen Recovery Services-Tresor zurück. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/certificates/* | Erstellen und Verwalten von Zertifikaten in Zusammenhang mit Sicherungen in einem Recovery Services-Tresor |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/* | Erstellen und Verwalten erweiterter Informationen in Zusammenhang mit einem Tresor |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | Ruft die Warnungen für den Recovery Services-Tresor ab. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | Der Vorgang „Tresor abrufen“ ruft ein Objekt ab, das die Azure-Ressource vom Typ „Tresor“ darstellt. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/* | Erstellen und Verwalten von registrierten Identitäten |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/* | Erstellen und Verwalten der Nutzung des Recovery Services-Tresors |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Gibt die Liste mit Speicherkonten zurück oder ruft die Eigenschaften für das angegebene Speicherkonto ab. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupconfig/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupValidateOperation/action | Überprüft Vorgang für geschütztes Element. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/write | Der Vorgang „Tresor erstellen“ erstellt eine Azure-Ressource vom Typ „Tresor“. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/read | Gibt den Status eines Sicherungsvorgangs für Recovery Services-Tresore zurück. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/read | Gibt alle Sicherungsverwaltungsserver zurück, die beim Tresor registriert sind. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectableContainers/read | Ruft alle schützbaren Container ab. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/action | Überprüft den Sicherungsstatus für Recovery Services-Tresore. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupPreValidateProtection/action |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/action | Überprüft Features. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/write | Löst die Warnung auf. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/operations/read | Der Vorgang gibt die Liste der Vorgänge für einen Ressourcenanbieter zurück. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/operationStatus/read | Ruft den Vorgangsstatus eines angegebenen Vorgangs ab. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/read | Listet den gesamten beabsichtigten Sicherungsschutz auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage backup service,but can't create vaults and give access to others",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5e467623-bb1f-42f4-a55d-6e525e11384b",
  "name": "5e467623-bb1f-42f4-a55d-6e525e11384b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action",
        "Microsoft.RecoveryServices/Vaults/backupJobs/*",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectableItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/*",
        "Microsoft.RecoveryServices/Vaults/backupSecurityPIN/*",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/certificates/*",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/*",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/*",
        "Microsoft.RecoveryServices/Vaults/usages/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupValidateOperation/action",
        "Microsoft.RecoveryServices/Vaults/write",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/locations/backupPreValidateProtection/action",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-operator"></a>Sicherungsoperator

Ermöglicht Ihnen das Verwalten von Sicherungsdiensten, jedoch nicht das Entfernen der Sicherung, die Tresorerstellung und das Erteilen von Zugriff an andere Benutzer. [Weitere Informationen](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Dient zum Abrufen der Definition des virtuellen Netzwerks. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/read | Gibt den Status des Vorgangs zurück. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/operationResults/read | Ruft das Ergebnis eines Vorgangs ab, der für den Schutzcontainer ausgeführt wurde. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Führt eine Sicherung für geschützte Elemente aus. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Ruft das Ergebnis eines Vorgangs ab, der für geschützte Elemente ausgeführt wurde. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Gibt den Status eines Vorgangs zurück, der für geschützte Elemente ausgeführt wurde. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/read | Gibt Objektdetails des geschützten Elements zurück. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Dient zum Bereitstellen der sofortigen Elementwiederherstellung für geschützte Elemente. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Dient zum Abrufen von Wiederherstellungspunkten für geschützte Elemente. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Dient zum Wiederherstellen von Wiederherstellungspunkten für geschützte Elemente. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Dient zum Widerrufen der sofortigen Elementwiederherstellung für geschützte Elemente. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/write | Dient zum Erstellen eines geschützten Elements für die Sicherung. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/read | Gibt alle registrierten Container zurück. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/refreshContainers/action | Aktualisiert die Containerliste. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/* | Erstellen und Verwalten von Sicherungsaufträgen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/action | Dient zum Exportieren von Aufträgen. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/* | Erstellen und Verwalten der Ergebnisse von Sicherungsverwaltungsvorgängen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operationResults/read | Dient zum Abrufen der Ergebnisse von Richtlinienvorgängen. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/read | Gibt alle Schutzrichtlinien zurück. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectableItems/* | Erstellen und Verwalten von Elementen, die gesichert werden können |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/read | Gibt die Liste mit allen geschützten Elementen zurück. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/read | Gibt alle zum Abonnement gehörenden Container zurück. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/read | Gibt Zusammenfassungen für geschützte Elemente und geschützte Server für einen Recovery Services-Tresor zurück. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/certificates/write | Der Vorgang „Ressourcenzertifikat aktualisieren“ aktualisiert das Zertifikat für die Ressourcen-/Tresoranmeldeinformationen. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | Der Vorgang „Ausführliche Informationen abrufen“ ruft die ausführlichen Informationen zu einem Objekt ab, das die Azure-Ressource vom Typ „Tresor“ darstellt. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/write | Der Vorgang „Ausführliche Informationen abrufen“ ruft die ausführlichen Informationen zu einem Objekt ab, das die Azure-Ressource vom Typ „Tresor“ darstellt. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | Ruft die Warnungen für den Recovery Services-Tresor ab. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | Der Vorgang „Tresor abrufen“ ruft ein Objekt ab, das die Azure-Ressource vom Typ „Tresor“ darstellt. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | Mit dem Vorgang „Vorgangsergebnisse abrufen“ können der Vorgangsstatus und das Ergebnis für den asynchron übermittelten Vorgang abgerufen werden. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | Der Vorgang „Container abrufen“ kann zum Abrufen der für eine Ressource registrierten Container verwendet werden. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/write | Der Vorgang „Dienstcontainer registrieren“ kann zum Registrieren eines Containers beim Wiederherstellungsdienst verwendet werden. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Gibt Nutzungsdetails für einen Recovery Services-Tresor zurück. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Gibt die Liste mit Speicherkonten zurück oder ruft die Eigenschaften für das angegebene Speicherkonto ab. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupValidateOperation/action | Überprüft Vorgang für geschütztes Element. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/read | Gibt den Status eines Sicherungsvorgangs für Recovery Services-Tresore zurück. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operations/read | Dient zum Abrufen des Status von Richtlinienvorgängen. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/write | Erstellt einen registrierten Container. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/inquire/action | Führt die Abfrage für Workloads innerhalb eines Containers durch. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/read | Gibt alle Sicherungsverwaltungsserver zurück, die beim Tresor registriert sind. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/write | Erstellt einen beabsichtigten Sicherungsschutz. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/read | Ruft einen beabsichtigten Sicherungsschutz ab. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectableContainers/read | Ruft alle schützbaren Container ab. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/items/read | Ruft alle Elemente in einem Container ab. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/action | Überprüft den Sicherungsstatus für Recovery Services-Tresore. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupPreValidateProtection/action |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/action | Überprüft Features. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/write | Löst die Warnung auf. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/operations/read | Der Vorgang gibt die Liste der Vorgänge für einen Ressourcenanbieter zurück. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/operationStatus/read | Ruft den Vorgangsstatus eines angegebenen Vorgangs ab. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/read | Listet den gesamten beabsichtigten Sicherungsschutz auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage backup services, except removal of backup, vault creation and giving access to others",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00c29273-979b-4161-815c-10b084fb9324",
  "name": "00c29273-979b-4161-815c-10b084fb9324",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action",
        "Microsoft.RecoveryServices/Vaults/backupJobs/*",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectableItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/certificates/write",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/write",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/write",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupValidateOperation/action",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/locations/backupPreValidateProtection/action",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-reader"></a>Sicherungsleser

Kann Sicherungsdienste anzeigen, aber keine Änderungen vornehmen. [Weitere Informationen](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | „GetAllocatedStamp“ ist ein interner Vorgang des Diensts. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/read | Gibt den Status des Vorgangs zurück. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/operationResults/read | Ruft das Ergebnis eines Vorgangs ab, der für den Schutzcontainer ausgeführt wurde. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Ruft das Ergebnis eines Vorgangs ab, der für geschützte Elemente ausgeführt wurde. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Gibt den Status eines Vorgangs zurück, der für geschützte Elemente ausgeführt wurde. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/read | Gibt Objektdetails des geschützten Elements zurück. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Dient zum Abrufen von Wiederherstellungspunkten für geschützte Elemente. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/read | Gibt alle registrierten Container zurück. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/operationResults/read | Gibt das Ergebnis von Auftragsvorgängen zurück. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/read | Gibt alle Auftragsobjekte zurück. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/action | Dient zum Exportieren von Aufträgen. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/read | Gibt das Ergebnis eines Sicherungsvorgangs für Recovery Services-Tresore zurück. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operationResults/read | Dient zum Abrufen der Ergebnisse von Richtlinienvorgängen. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/read | Gibt alle Schutzrichtlinien zurück. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/read | Gibt die Liste mit allen geschützten Elementen zurück. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/read | Gibt alle zum Abonnement gehörenden Container zurück. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/read | Gibt Zusammenfassungen für geschützte Elemente und geschützte Server für einen Recovery Services-Tresor zurück. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | Der Vorgang „Ausführliche Informationen abrufen“ ruft die ausführlichen Informationen zu einem Objekt ab, das die Azure-Ressource vom Typ „Tresor“ darstellt. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | Ruft die Warnungen für den Recovery Services-Tresor ab. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | Der Vorgang „Tresor abrufen“ ruft ein Objekt ab, das die Azure-Ressource vom Typ „Tresor“ darstellt. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | Mit dem Vorgang „Vorgangsergebnisse abrufen“ können der Vorgangsstatus und das Ergebnis für den asynchron übermittelten Vorgang abgerufen werden. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | Der Vorgang „Container abrufen“ kann zum Abrufen der für eine Ressource registrierten Container verwendet werden. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/read | Gibt die Speicherkonfiguration für Recovery Services-Tresore zurück. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupconfig/read | Gibt die Konfiguration für Recovery Services-Tresore zurück. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/read | Gibt den Status eines Sicherungsvorgangs für Recovery Services-Tresore zurück. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operations/read | Dient zum Abrufen des Status von Richtlinienvorgängen. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/read | Gibt alle Sicherungsverwaltungsserver zurück, die beim Tresor registriert sind. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/read | Ruft einen beabsichtigten Sicherungsschutz ab. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/items/read | Ruft alle Elemente in einem Container ab. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/action | Überprüft den Sicherungsstatus für Recovery Services-Tresore. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/write | Löst die Warnung auf. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/operations/read | Der Vorgang gibt die Liste der Vorgänge für einen Ressourcenanbieter zurück. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/operationStatus/read | Ruft den Vorgangsstatus eines angegebenen Vorgangs ab. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/read | Listet den gesamten beabsichtigten Sicherungsschutz auf. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Gibt Nutzungsdetails für einen Recovery Services-Tresor zurück. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/action | Überprüft Features. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view backup services, but can't make changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
  "name": "a795c7a0-d4a2-40c1-ae25-d81f01202912",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupJobs/read",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/read",
        "Microsoft.RecoveryServices/Vaults/backupconfig/read",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-storage-account-contributor"></a>Mitwirkender von klassischem Speicherkonto

Ermöglicht Ihnen das Verwalten klassischer Speicherkonten, nicht aber den Zugriff darauf.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/* | Erstellen und Verwalten von Speicherkonten |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic storage accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/86e8f5dc-a6e9-4c67-9d15-de283e8eac25",
  "name": "86e8f5dc-a6e9-4c67-9d15-de283e8eac25",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicStorage/storageAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Storage Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-storage-account-key-operator-service-role"></a>Klassische Dienstrolle „Speicherkonto-Schlüsseloperator“

Klassische Speicherkonto-Schlüsseloperatoren dürfen Schlüssel für klassische Speicherkonten auflisten und neu generieren. [Weitere Informationen](../key-vault/secrets/overview-storage-keys.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listkeys/action | Listet die Zugriffsschlüssel für die Speicherkonten auf. |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/regeneratekey/action | Generiert die vorhandenen Zugriffsschlüssel für das Speicherkonto neu. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Classic Storage Account Key Operators are allowed to list and regenerate keys on Classic Storage Accounts",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/985d6b00-f706-48f5-a6fe-d0ca12fb668d",
  "name": "985d6b00-f706-48f5-a6fe-d0ca12fb668d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ClassicStorage/storageAccounts/listkeys/action",
        "Microsoft.ClassicStorage/storageAccounts/regeneratekey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Storage Account Key Operator Service Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-box-contributor"></a>Data Box-Mitwirkender

Ermöglicht Ihnen das Verwalten aller Komponenten unter dem Data Box-Dienst, mit Ausnahme der Gewährung des Zugriffs für andere Benutzer. [Weitere Informationen](../databox/data-box-logs.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything under Data Box Service except giving access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/add466c9-e687-43fc-8d98-dfcf8d720be5",
  "name": "add466c9-e687-43fc-8d98-dfcf8d720be5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Databox/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Box Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-box-reader"></a>Data Box-Leser

Ermöglicht Ihnen das Verwalten des Data Box-Diensts, mit Ausnahme der Erstellung von Aufträgen oder der Bearbeitung von Auftragsdetails und der Gewährung des Zugriffs für andere Benutzer. [Weitere Informationen](../databox/data-box-logs.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/*/read |  |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/jobs/listsecrets/action |  |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/jobs/listcredentials/action | Hiermit werden die unverschlüsselten Anmeldeinformationen für den Auftrag aufgelistet. |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/locations/availableSkus/action | Mit dieser Methode wird die Liste der verfügbaren SKUs zurückgegeben. |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/locations/validateInputs/action | Diese Methode führt alle Arten von Prüfungen aus. |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/locations/regionConfiguration/action | Diese Methode gibt die Konfigurationen für die Region zurück. |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/locations/validateAddress/action | Hiermit wird die Lieferadresse überprüft, und es werden – sofern vorhanden – alternative Adressen bereitgestellt. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Data Box Service except creating order or editing order details and giving access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027",
  "name": "028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Databox/*/read",
        "Microsoft.Databox/jobs/listsecrets/action",
        "Microsoft.Databox/jobs/listcredentials/action",
        "Microsoft.Databox/locations/availableSkus/action",
        "Microsoft.Databox/locations/validateInputs/action",
        "Microsoft.Databox/locations/regionConfiguration/action",
        "Microsoft.Databox/locations/validateAddress/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Box Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-lake-analytics-developer"></a>Data Lake Analytics-Entwickler

Ermöglicht Ihnen das Übermitteln, Überwachen und Verwalten Ihrer eigenen Aufträge, aber nicht das Erstellen oder Löschen von Data Lake Analytics-Konten. [Weitere Informationen](../data-lake-analytics/data-lake-analytics-manage-use-portal.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.BigAnalytics/accounts/* |  |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | Microsoft.BigAnalytics/accounts/Delete |  |
> | Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
> | Microsoft.BigAnalytics/accounts/Write |  |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/Delete | Löscht ein DataLakeAnalytics-Konto. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/TakeOwnership/action | Erteilt Berechtigungen zum Abbrechen von Aufträgen, die von anderen Benutzern übermittelt wurden. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/Write | Erstellt oder aktualisiert ein DataLakeAnalytics-Konto. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/dataLakeStoreAccounts/Write | Erstellt oder aktualisiert ein verknüpftes DataLakeStore-Konto eines DataLakeAnalytics-Kontos. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/dataLakeStoreAccounts/Delete | Hebt die Verknüpfung eines DataLakeStore-Kontos mit einem DataLakeAnalytics-Konto auf. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/storageAccounts/Write | Erstellt oder aktualisiert ein verknüpftes Speicherkonto eines DataLakeAnalytics-Kontos. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/storageAccounts/Delete | Hebt die Verknüpfung eines Speicherkontos mit einem DataLakeAnalytics-Konto auf. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/firewallRules/Write | Dient zum Erstellen oder Aktualisieren einer Firewallregel. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/firewallRules/Delete | Dient zum Löschen einer Firewallregel. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/computePolicies/Write | Erstellt oder aktualisiert eine Computerichtlinie. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/computePolicies/Delete | Löscht eine Computerichtlinie. |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you submit, monitor, and manage your own jobs but not create or delete Data Lake Analytics accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/47b7735b-770e-4598-a7da-8b91488b4c88",
  "name": "47b7735b-770e-4598-a7da-8b91488b4c88",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.BigAnalytics/accounts/*",
        "Microsoft.DataLakeAnalytics/accounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.BigAnalytics/accounts/Delete",
        "Microsoft.BigAnalytics/accounts/TakeOwnership/action",
        "Microsoft.BigAnalytics/accounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action",
        "Microsoft.DataLakeAnalytics/accounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/firewallRules/Write",
        "Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete",
        "Microsoft.DataLakeAnalytics/accounts/computePolicies/Write",
        "Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Lake Analytics Developer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reader-and-data-access"></a>Lese- und Datenzugriff

Ermöglicht Ihnen das Anzeigen sämtlicher Aspekte, jedoch nicht das Löschen oder Erstellen eines Speicherkontos oder einer darin enthaltenen Ressource. Sie können auch Lese-/Schreibzugriff auf alle Daten in einem Speicherkonto durch Zugriff auf Speicherkontoschlüssel gewähren.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | Gibt die Zugriffsschlüssel für das angegebene Speicherkonto zurück. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/ListAccountSas/action | Gibt das Konto-SAS-Token für das angegebene Speicherkonto zurück. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Gibt die Liste mit Speicherkonten zurück oder ruft die Eigenschaften für das angegebene Speicherkonto ab. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view everything but will not let you delete or create a storage account or contained resource. It will also allow read/write access to all data contained in a storage account via access to storage account keys.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c12c1c16-33a1-487b-954d-41c89c60f349",
  "name": "c12c1c16-33a1-487b-954d-41c89c60f349",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageAccounts/ListAccountSas/action",
        "Microsoft.Storage/storageAccounts/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reader and Data Access",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-account-contributor"></a>Mitwirkender von Speicherkonto

Erlaubt die Verwaltung von Speicherkonten. Ermöglicht den Zugriff auf den Kontoschlüssel, der für den Datenzugriff über die Autorisierung mit einem gemeinsam verwendetem Schlüssel genutzt werden kann. [Weitere Informationen](../storage/common/storage-auth-aad.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Erstellt, aktualisiert oder liest die Diagnoseeinstellung für den Analysis-Server. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | Verknüpft Ressourcen wie etwa ein Speicherkonto oder eine SQL-Datenbank mit einem Subnetz. Nicht warnbar. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/* | Erstellen und Verwalten von Speicherkonten |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage storage accounts, including accessing storage account keys which provide full access to storage account data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/17d1049b-9a84-46fb-8f53-869881c3d3ab",
  "name": "17d1049b-9a84-46fb-8f53-869881c3d3ab",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-account-key-operator-service-role"></a>Dienstrolle „Speicherkonto-Schlüsseloperator“

Ermöglicht das Auflisten und erneute Generieren von Zugriffsschlüsseln für Speicherkonten. [Weitere Informationen](../storage/common/storage-account-keys-manage.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listkeys/action | Gibt die Zugriffsschlüssel für das angegebene Speicherkonto zurück. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/regeneratekey/action | Generiert die Zugriffsschlüssel für das angegebene Speicherkonto neu. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Storage Account Key Operators are allowed to list and regenerate keys on Storage Accounts",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/81a9662b-bebf-436f-a333-f67b29880f12",
  "name": "81a9662b-bebf-436f-a333-f67b29880f12",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/regeneratekey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Account Key Operator Service Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-contributor"></a>Mitwirkender an Storage-Blobdaten

Lesen, Schreiben und Löschen von Azure Storage-Containern und -Blobs. Um zu erfahren, welche Aktionen für einen bestimmten Datenvorgang erforderlich sind, siehe [Berechtigungen für den Aufruf von Datenvorgängen für Blobs und Warteschlangen](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Weitere Informationen](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/delete | Löschen eines Containers. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/read | Zurückgeben eines Containers oder einer Liste von Containern. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/write | Ändern der Metadaten oder Eigenschaften eines Containers. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | Gibt einen Benutzerdelegierungsschlüssel für den Blob-Dienst zurück. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/delete | Löschen eines Blobs |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | Zurückgeben eines Blob oder einer Liste von Blobs. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/move/action | Verschiebt das Blob aus einem Pfad in einen anderen. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/write | Schreiben in ein Blob. |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write and delete access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
  "name": "ba92f5b4-2d11-453d-a403-e96b0029c9fe",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-owner"></a>Besitzer von Speicherblobdaten

Bietet Vollzugriff auf Azure Storage-Blobcontainer und -daten, einschließlich POSIX-Zugriffssteuerung. Um zu erfahren, welche Aktionen für einen bestimmten Datenvorgang erforderlich sind, siehe [Berechtigungen für den Aufruf von Datenvorgängen für Blobs und Warteschlangen](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Weitere Informationen](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/* | Vollzugriffsberechtigungen für Container. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | Gibt einen Benutzerdelegierungsschlüssel für den Blob-Dienst zurück. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/* | Vollzugriffsberechtigungen für Blobs. |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b7e6dc6d-f1e8-4753-8033-0f276bb0955b",
  "name": "b7e6dc6d-f1e8-4753-8033-0f276bb0955b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/*",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-reader"></a>Leser von Speicherblobdaten

Lesen und Auflisten von Azure Storage-Containern und -Blobs. Um zu erfahren, welche Aktionen für einen bestimmten Datenvorgang erforderlich sind, siehe [Berechtigungen für den Aufruf von Datenvorgängen für Blobs und Warteschlangen](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Weitere Informationen](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/read | Zurückgeben eines Containers oder einer Liste von Containern. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | Gibt einen Benutzerdelegierungsschlüssel für den Blob-Dienst zurück. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | Zurückgeben eines Blob oder einer Liste von Blobs. |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-delegator"></a>Storage Blob-Delegator

Abrufen eines Benutzerdelegierungsschlüssels, mit dem dann eine SAS (Shared Access Signature) für einen Container oder Blob erstellt werden kann, die mit Azure AD-Anmeldeinformationen signiert ist. Weitere Informationen finden Sie unter [Erstellen einer SAS für die Benutzerdelegierung](/rest/api/storageservices/create-user-delegation-sas). [Weitere Informationen](/rest/api/storageservices/get-user-delegation-key)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | Gibt einen Benutzerdelegierungsschlüssel für den Blob-Dienst zurück. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for generation of a user delegation key which can be used to sign SAS tokens",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/db58b8e5-c6ad-4a2a-8342-4190687cbf4a",
  "name": "db58b8e5-c6ad-4a2a-8342-4190687cbf4a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Delegator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-contributor"></a>Speicherdateidaten-SMB-Freigabemitwirkender

Ermöglicht den Lese-, Schreib- und Löschzugriff auf Dateien/Verzeichnisse in Azure-Dateifreigaben. Für diese Rolle gibt es keine integrierte Entsprechung auf Windows-Dateiservern. [Weitere Informationen](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | *keine* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/read | Gibt eine Datei oder einen Ordner oder eine Liste mit Dateien/Ordnern zurück. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/write | Gibt das Ergebnis des Schreibens einer Datei oder des Erstellens eines Ordners zurück. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/delete | Gibt das Ergebnis des Löschens einer Datei/eines Ordners zurück. |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, and delete access in Azure Storage file shares over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb",
  "name": "0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-elevated-contributor"></a>Speicherdateidaten-SMB-Freigabemitwirkender mit erhöhten Rechten

Ermöglicht das Lesen, Schreiben, Löschen und Bearbeiten von Zugriffssteuerungslisten für Dateien/Verzeichnisse in Azure-Dateifreigaben. Diese Rolle entspricht einer Dateifreigabe-ACL für das Bearbeiten auf Windows-Dateiservern. [Weitere Informationen](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | *keine* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/read | Gibt eine Datei oder einen Ordner oder eine Liste mit Dateien/Ordnern zurück. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/write | Gibt das Ergebnis des Schreibens einer Datei oder des Erstellens eines Ordners zurück. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/delete | Gibt das Ergebnis des Löschens einer Datei/eines Ordners zurück. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/modifypermissions/action | Gibt das Ergebnis der Berechtigungsänderung für eine Datei/einen Ordner zurück. |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, delete and modify NTFS permission access in Azure Storage file shares over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a7264617-510b-434b-a828-9731dc254ea7",
  "name": "a7264617-510b-434b-a828-9731dc254ea7",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Elevated Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-reader"></a>Speicherdateidaten-SMB-Freigabeleser

Ermöglicht den Lesezugriff auf Dateien/Verzeichnisse in Azure-Dateifreigaben. Diese Rolle entspricht einer Dateifreigabe-ACL für das Lesen auf Windows-Dateiservern. [Weitere Informationen](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | *keine* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/read | Gibt eine Datei oder einen Ordner oder eine Liste mit Dateien/Ordnern zurück. |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure File Share over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/aba4ae5f-2193-4029-9191-0cb91df5e314",
  "name": "aba4ae5f-2193-4029-9191-0cb91df5e314",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-contributor"></a>Mitwirkender an Storage-Warteschlangendaten

Lesen, Schreiben und Löschen von Azure Storage-Warteschlangen und -Warteschlangennachrichten. Um zu erfahren, welche Aktionen für einen bestimmten Datenvorgang erforderlich sind, siehe [Berechtigungen für den Aufruf von Datenvorgängen für Blobs und Warteschlangen](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Weitere Informationen](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/delete | Löschen einer Warteschlange. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/read | Zurückgeben einer Warteschlange oder Liste mit Warteschlangen. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/write | Ändern der Metadaten oder Eigenschaften einer Warteschlange. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/delete | Löschen einer oder mehrerer Nachrichten aus einer Warteschlange. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/read | Einsehen oder Abrufen einer oder mehrerer Nachrichten aus einer Warteschlange. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/write | Hinzufügen von Nachrichten zu einer Warteschlange. |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, and delete access to Azure Storage queues and queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/974c5e8b-45b9-4653-ba55-5f855dd0fb88",
  "name": "974c5e8b-45b9-4653-ba55-5f855dd0fb88",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/delete",
        "Microsoft.Storage/storageAccounts/queueServices/queues/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-processor"></a>Verarbeiter von Speicherwarteschlangen-Datennachrichten

Einsehen, Abrufen und Löschen einer Nachricht aus einer Azure Storage-Warteschlange. Um zu erfahren, welche Aktionen für einen bestimmten Datenvorgang erforderlich sind, siehe [Berechtigungen für den Aufruf von Datenvorgängen für Blobs und Warteschlangen](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Weitere Informationen](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | *keine* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/read | Einsehen einer Nachricht. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/process/action | Abrufen und Löschen einer Nachricht. |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for peek, receive, and delete access to Azure Storage queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8a0f0c08-91a1-4084-bc3d-661d67233fed",
  "name": "8a0f0c08-91a1-4084-bc3d-661d67233fed",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Message Processor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-sender"></a>Absender der Speicherwarteschlangen-Datennachricht

Hinzufügen von Nachrichten zu einer Azure Storage-Warteschlange. Um zu erfahren, welche Aktionen für einen bestimmten Datenvorgang erforderlich sind, siehe [Berechtigungen für den Aufruf von Datenvorgängen für Blobs und Warteschlangen](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Weitere Informationen](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | *keine* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/add/action | Hinzufügen von Nachrichten zu einer Warteschlange. |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for sending of Azure Storage queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c6a89b2d-59bc-44d0-9896-0f6e12d7b80a",
  "name": "c6a89b2d-59bc-44d0-9896-0f6e12d7b80a",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Message Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-reader"></a>Storage-Warteschlangendatenleser

Lesen und Auflisten von Azure Storage-Warteschlangen und -Warteschlangennachrichten. Um zu erfahren, welche Aktionen für einen bestimmten Datenvorgang erforderlich sind, siehe [Berechtigungen für den Aufruf von Datenvorgängen für Blobs und Warteschlangen](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Weitere Informationen](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/read | Gibt eine Warteschlange oder eine Liste von Warteschlangen zurück. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/read | Einsehen oder Abrufen einer oder mehrerer Nachrichten aus einer Warteschlange. |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage queues and queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/19e7f393-937e-4f77-808e-94535e297925",
  "name": "19e7f393-937e-4f77-808e-94535e297925",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="web"></a>Web


### <a name="azure-maps-data-reader"></a>Azure Maps-Datenleser

Gewährt Lesezugriff auf kartenbezogene Daten von einem Azure Maps-Konto.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | *keine* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/read |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read map related data from an Azure maps account.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/423170ca-a8f6-4b0f-8487-9e4eb8f49bfa",
  "name": "423170ca-a8f6-4b0f-8487-9e4eb8f49bfa",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Maps/accounts/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Maps Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="search-service-contributor"></a>Mitwirkender von Suchdienst

Ermöglicht Ihnen das Verwalten von Search-Diensten, nicht aber den Zugriff darauf. [Weitere Informationen](../search/search-security-rbac.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Search](resource-provider-operations.md#microsoftsearch)/searchServices/* | Erstellen und Verwalten von Suchdiensten |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Search services, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7ca78c08-252a-4471-8644-bb5ff32d4ba0",
  "name": "7ca78c08-252a-4471-8644-bb5ff32d4ba0",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Search/searchServices/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Search Service Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-accesskey-reader"></a>SignalR-AccessKey-Leser

Ermöglicht das Lesen von SignalR Service-Zugriffsschlüsseln.

> [!div class="mx-tableFixed"]
> | Aktionen | Beschreibung |
> | --- | --- |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/*/read |  |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/listkeys/action | Zeigt den Wert von SignalR-Zugriffsschlüsseln im Verwaltungsportal oder über die API an. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read SignalR Service Access Keys",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/04165923-9d83-45d5-8227-78b77b0a687e",
  "name": "04165923-9d83-45d5-8227-78b77b0a687e",
  "permissions": [
    {
      "actions": [
        "Microsoft.SignalRService/*/read",
        "Microsoft.SignalRService/SignalR/listkeys/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR AccessKey Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-app-server-preview"></a>SignalR-App-Server (Vorschau)

Ermöglicht Ihrem App-Server den Zugriff auf SignalR Service mit AAD-Authentifizierungsoptionen.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | *keine* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/accessKey/action | Generiert einen temporären AccessKey zum Signieren von Clienttoken. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/serverConnection/write | Startet eine Serververbindung. |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets your app server access SignalR Service with AAD auth options.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/420fcaa2-552c-430f-98ca-3264be4806c7",
  "name": "420fcaa2-552c-430f-98ca-3264be4806c7",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/auth/accessKey/action",
        "Microsoft.SignalRService/SignalR/serverConnection/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR App Server (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-contributor"></a>SignalR-Mitwirkender

Ermöglicht das Erstellen, Lesen, Aktualisieren und Löschen von SignalR Service-Ressourcen.

> [!div class="mx-tableFixed"]
> | Aktionen | Beschreibung |
> | --- | --- |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/* |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create, Read, Update, and Delete SignalR service resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8cf5e20a-e4b2-4e9d-b3a1-5ceb692c2761",
  "name": "8cf5e20a-e4b2-4e9d-b3a1-5ceb692c2761",
  "permissions": [
    {
      "actions": [
        "Microsoft.SignalRService/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-serverless-contributor-preview"></a>SignalR Serverless-Mitwirkender (Vorschau)

Ermöglicht Ihrer App den Zugriff auf den Dienst im serverlosen Modus mit AAD-Authentifizierungsoptionen.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | *keine* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/clientToken/action | Generiert ein ClientToken zum Starten einer Clientverbindung. |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets your app access service in serverless mode with AAD auth options.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fd53cd77-2268-407a-8f46-7e7863d0f521",
  "name": "fd53cd77-2268-407a-8f46-7e7863d0f521",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/auth/clientToken/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Serverless Contributor (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-service-owner-preview"></a>SignalR Service-Besitzer (Vorschau)

Bietet Vollzugriff auf Azure SignalR Service-REST-APIs.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | *keine* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/hub/send/action | Überträgt Nachrichten an alle Clientverbindungen im Hub. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/group/send/action | Überträgt eine Nachricht an eine Gruppe. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/group/read | Überprüft die Existenz einer Gruppe oder die Existenz eines Benutzers in einer Gruppe. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/group/write | Ermöglicht das Beitreten zu einer Gruppe bzw. das Verlassen einer Gruppe. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/send/action | Sendet Nachrichten direkt an eine Clientverbindung. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/read | Überprüft, ob eine Clientverbindung vorhanden ist. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/write | Schließt die Clientverbindung. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/user/send/action | Sendet Nachrichten an einen Benutzer (umfasst möglicherweise mehrere Clientverbindungen). |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/user/read | Überprüft, ob Benutzer vorhanden sind. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/user/write |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access to Azure SignalR Service REST APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7e4f1700-ea5a-4f59-8f37-079cfe29dce3",
  "name": "7e4f1700-ea5a-4f59-8f37-079cfe29dce3",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/hub/send/action",
        "Microsoft.SignalRService/SignalR/group/send/action",
        "Microsoft.SignalRService/SignalR/group/read",
        "Microsoft.SignalRService/SignalR/group/write",
        "Microsoft.SignalRService/SignalR/clientConnection/send/action",
        "Microsoft.SignalRService/SignalR/clientConnection/read",
        "Microsoft.SignalRService/SignalR/clientConnection/write",
        "Microsoft.SignalRService/SignalR/user/send/action",
        "Microsoft.SignalRService/SignalR/user/read",
        "Microsoft.SignalRService/SignalR/user/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Service Owner (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-service-reader-preview"></a>SignalR Service-Leser (Vorschau)

Bietet schreibgeschützten Zugriff auf Azure SignalR Service-REST-APIs.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | *keine* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/group/read | Überprüft die Existenz einer Gruppe oder die Existenz eines Benutzers in einer Gruppe. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/read | Überprüft, ob eine Clientverbindung vorhanden ist. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/user/read | Überprüft, ob Benutzer vorhanden sind. |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only access to Azure SignalR Service REST APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ddde6b66-c0df-4114-a159-3618637b3035",
  "name": "ddde6b66-c0df-4114-a159-3618637b3035",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/group/read",
        "Microsoft.SignalRService/SignalR/clientConnection/read",
        "Microsoft.SignalRService/SignalR/user/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Service Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="web-plan-contributor"></a>Mitwirkender von Webplan

Ermöglicht Ihnen das Verwalten der Webpläne für Websites, nicht aber den Zugriff darauf.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/* | Erstellen und Verwalten von Serverfarmen |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/hostingEnvironments/Join/Action | Tritt einer App Service-Umgebung bei. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage the web plans for websites, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b",
  "name": "2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/serverFarms/*",
        "Microsoft.Web/hostingEnvironments/Join/Action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Web Plan Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="website-contributor"></a>Mitwirkender von Website

Ermöglicht Ihnen das Verwalten von Websites (nicht der Webpläne), nicht aber den Zugriff darauf.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/* | Erstellen und Verwalten von Insights-Komponenten |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/certificates/* | Erstellen und Verwalten von Websitezertifikaten |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/listSitesAssignedToHostName/read | Dient zum Abrufen der Namen von Websites, die dem Hostnamen zugewiesen sind. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/join/action | Hiermit wird der Beitritt zu einem App Service-Plan ausgeführt. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/read | Dient zum Abrufen der Eigenschaften für einen App Service-Plan. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/sites/* | Erstellen und Verwalten von Websites (die Erstellung von Websites erfordert auch Schreibberechtigungen für den zugehörigen App Service-Plan) |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage websites (not web plans), but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/de139f84-1756-47ae-9be6-808fbbe84772",
  "name": "de139f84-1756-47ae-9be6-808fbbe84772",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/components/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/certificates/*",
        "Microsoft.Web/listSitesAssignedToHostName/read",
        "Microsoft.Web/serverFarms/join/action",
        "Microsoft.Web/serverFarms/read",
        "Microsoft.Web/sites/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Website Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="containers"></a>Container


### <a name="acrdelete"></a>AcrDelete

ACR-Löschvorgang [Weitere Informationen](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/artifacts/delete | Löschen von Artefakten aus einer Containerregistrierung. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr delete",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c2f4ef07-c644-48eb-af81-4b1b4947fb11",
  "name": "c2f4ef07-c644-48eb-af81-4b1b4947fb11",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/artifacts/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrDelete",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrimagesigner"></a>AcrImageSigner

ACR-Imagesignaturgeber [Weitere Informationen](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/sign/write | Pushen/Pullen von Inhaltsvertrauen-Metadaten für eine Containerregistrierung |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr image signer",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6cef56e8-d556-48e5-a04f-b8e64114680f",
  "name": "6cef56e8-d556-48e5-a04f-b8e64114680f",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/sign/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrImageSigner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrpull"></a>AcrPull

ACR-Pullvorgang [Weitere Informationen](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/pull/read | Pullen oder Abrufen von Images aus einer Containerregistrierung |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr pull",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "name": "7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/pull/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrPull",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrpush"></a>AcrPush

ACR-Pushvorgang [Weitere Informationen](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/pull/read | Pullen oder Abrufen von Images aus einer Containerregistrierung |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/push/write | Pushen oder Schreiben von Images in eine Containerregistrierung |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr push",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8311e382-0749-4cb8-b61a-304f252e45ec",
  "name": "8311e382-0749-4cb8-b61a-304f252e45ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/pull/read",
        "Microsoft.ContainerRegistry/registries/push/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrPush",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrquarantinereader"></a>AcrQuarantineReader

ACR-Quarantänedatenleser

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/quarantine/read | Pullen oder Abrufen von Images in Quarantäne aus einer Containerregistrierung |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr quarantine data reader",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cdda3590-29a3-44f6-95f2-9f980659eb04",
  "name": "cdda3590-29a3-44f6-95f2-9f980659eb04",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/quarantine/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrQuarantineReader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrquarantinewriter"></a>AcrQuarantineWriter

ACR-Quarantänedatenschreiber

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/quarantine/read | Pullen oder Abrufen von Images in Quarantäne aus einer Containerregistrierung |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/quarantine/write | Schreiben/Ändern des Quarantänezustands von unter Quarantäne gestellten Images |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr quarantine data writer",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c8d4ff99-41c3-41a8-9f60-21dfdad59608",
  "name": "c8d4ff99-41c3-41a8-9f60-21dfdad59608",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/quarantine/read",
        "Microsoft.ContainerRegistry/registries/quarantine/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrQuarantineWriter",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-cluster-admin-role"></a>Administratorrolle für Azure Kubernetes Service-Cluster

Listet die Aktion für Anmeldeinformationen des Clusteradministrators auf. [Weitere Informationen](../aks/control-kubeconfig-access.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterAdminCredential/action | Listet die clusterAdmin-Anmeldeinformationen eines verwalteten Clusters auf. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/accessProfiles/listCredential/action | Ruft ein Zugriffsprofil für verwaltete Cluster anhand des Rollennamens mithilfe der Liste der Anmeldeinformationen ab. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/read | Ruft einen verwalteten Cluster ab. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster admin credential action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8",
  "name": "0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action",
        "Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action",
        "Microsoft.ContainerService/managedClusters/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Cluster Admin Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-cluster-user-role"></a>Benutzerrolle für Azure Kubernetes Service-Cluster

Listet die Aktion für Anmeldeinformationen des Clusterbenutzer auf. [Weitere Informationen](../aks/control-kubeconfig-access.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/action | Listet die clusterUser-Anmeldeinformationen eines verwalteten Clusters auf. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/read | Ruft einen verwalteten Cluster ab. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster user credential action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4abbcc35-e782-43d8-92c5-2d3f1bd2253f",
  "name": "4abbcc35-e782-43d8-92c5-2d3f1bd2253f",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action",
        "Microsoft.ContainerService/managedClusters/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Cluster User Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-contributor-role"></a>Rolle „Mitwirkender“ für Azure Kubernetes Service

Gewährt Lese- und Schreibzugriff auf Azure Kubernetes Service-Cluster [Weitere Informationen](../aks/concepts-identity.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/read | Ruft einen verwalteten Cluster ab. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/write | Erstellt einen neuen verwalteten Cluster oder aktualisiert einen vorhandenen. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read and write Azure Kubernetes Service clusters",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8",
  "name": "ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/read",
        "Microsoft.ContainerService/managedClusters/write",
        "Microsoft.Resources/deployments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Contributor Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-admin"></a>RBAC-Administrator von Azure Kubernetes Service

Ermöglicht Ihnen das Verwalten aller Ressourcen unter einem Cluster/Namespace, außer das Aktualisieren oder Löschen von Ressourcenkontingenten und Namespaces. [Weitere Informationen](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Erstellt oder aktualisiert eine Bereitstellung. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Dient zum Abrufen der Ergebnisse des Abonnementvorgangs. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Ruft die Abonnementliste ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/action | Listet die clusterUser-Anmeldeinformationen eines verwalteten Clusters auf. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/* |  |
> | **NotDataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/write | Schreibt resourcequotas. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/delete | Löscht resourcequotas. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/write | Schreibt namespaces. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/delete | Löscht namespaces. |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources under cluster/namespace, except update or delete resource quotas and namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3498e952-d568-435e-9b2c-8d77e338d7f7",
  "name": "3498e952-d568-435e-9b2c-8d77e338d7f7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/*"
      ],
      "notDataActions": [
        "Microsoft.ContainerService/managedClusters/resourcequotas/write",
        "Microsoft.ContainerService/managedClusters/resourcequotas/delete",
        "Microsoft.ContainerService/managedClusters/namespaces/write",
        "Microsoft.ContainerService/managedClusters/namespaces/delete"
      ]
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-cluster-admin"></a>RBAC-Clusteradministrator von Azure Kubernetes Service

Ermöglicht Ihnen das Verwalten aller Ressourcen im Cluster. [Weitere Informationen](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Erstellt oder aktualisiert eine Bereitstellung. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Dient zum Abrufen der Ergebnisse des Abonnementvorgangs. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Ruft die Abonnementliste ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/action | Listet die clusterUser-Anmeldeinformationen eines verwalteten Clusters auf. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources in the cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b",
  "name": "b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Cluster Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-reader"></a>RBAC-Leser von Azure Kubernetes Service

Ermöglicht schreibgeschützten Zugriff, um die meisten Objekte in einem Namespace anzuzeigen. Es ist nicht möglich, Rollen oder Rollenbindungen anzuzeigen. Diese Rolle lässt das Anzeigen von Geheimnissen nicht zu, da das Lesen des Inhalts von Geheimnissen den Zugriff auf ServiceAccount-Anmeldeinformationen im Namespace ermöglicht, was den API-Zugriff als beliebiges Dienstkonto im Namespace ermöglichen würde (eine Form von Berechtigungsausweitung). Wenn Sie diese Rolle im Clusterumfang anwenden, wird der Zugriff auf alle Namespaces ermöglicht. [Weitere Informationen](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Erstellt oder aktualisiert eine Bereitstellung. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Dient zum Abrufen der Ergebnisse des Abonnementvorgangs. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Ruft die Abonnementliste ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/controllerrevisions/read | Liest controllerrevisions. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/daemonsets/read | Liest daemonsets. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/deployments/read | Liest deployments. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/replicasets/read | Liest replicasets. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/statefulsets/read | Liest statefulsets. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/autoscaling/horizontalpodautoscalers/read | Liest horizontalpodautoscalers. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/cronjobs/read | Liest cronjobs. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/jobs/read | Liest jobs. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/configmaps/read | Liest configmaps. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/endpoints/read | Liest endpoints. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/events.k8s.io/events/read | Liest events. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/events/read | Liest events. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/daemonsets/read | Liest daemonsets. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/deployments/read | Liest deployments. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/ingresses/read | Liest ingresses. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/networkpolicies/read | Liest networkpolicies. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/replicasets/read | Liest replicasets. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/limitranges/read | Liest limitranges. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/read | Liest namespaces. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/networking.k8s.io/ingresses/read | Liest ingresses. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/networking.k8s.io/networkpolicies/read | Liest networkpolicies. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/persistentvolumeclaims/read | Liest persistentvolumeclaims. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/pods/read | Liest pods. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/policy/poddisruptionbudgets/read | Liest poddisruptionbudgets. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/read | Liest replicationcontrollers. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/read | Liest replicationcontrollers. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/read | Liest resourcequotas. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/serviceaccounts/read | Liest serviceaccounts. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/services/read | Liest services. |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read-only access to see most objects in a namespace. It does not allow viewing roles or role bindings. This role does not allow viewing Secrets, since reading the contents of Secrets enables access to ServiceAccount credentials in the namespace, which would allow API access as any ServiceAccount in the namespace (a form of privilege escalation). Applying this role at cluster scope will give access across all namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7f6c6a51-bcf8-42ba-9220-52d62157d7db",
  "name": "7f6c6a51-bcf8-42ba-9220-52d62157d7db",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/apps/controllerrevisions/read",
        "Microsoft.ContainerService/managedClusters/apps/daemonsets/read",
        "Microsoft.ContainerService/managedClusters/apps/deployments/read",
        "Microsoft.ContainerService/managedClusters/apps/replicasets/read",
        "Microsoft.ContainerService/managedClusters/apps/statefulsets/read",
        "Microsoft.ContainerService/managedClusters/autoscaling/horizontalpodautoscalers/read",
        "Microsoft.ContainerService/managedClusters/batch/cronjobs/read",
        "Microsoft.ContainerService/managedClusters/batch/jobs/read",
        "Microsoft.ContainerService/managedClusters/configmaps/read",
        "Microsoft.ContainerService/managedClusters/endpoints/read",
        "Microsoft.ContainerService/managedClusters/events.k8s.io/events/read",
        "Microsoft.ContainerService/managedClusters/events/read",
        "Microsoft.ContainerService/managedClusters/extensions/daemonsets/read",
        "Microsoft.ContainerService/managedClusters/extensions/deployments/read",
        "Microsoft.ContainerService/managedClusters/extensions/ingresses/read",
        "Microsoft.ContainerService/managedClusters/extensions/networkpolicies/read",
        "Microsoft.ContainerService/managedClusters/extensions/replicasets/read",
        "Microsoft.ContainerService/managedClusters/limitranges/read",
        "Microsoft.ContainerService/managedClusters/namespaces/read",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/ingresses/read",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/networkpolicies/read",
        "Microsoft.ContainerService/managedClusters/persistentvolumeclaims/read",
        "Microsoft.ContainerService/managedClusters/pods/read",
        "Microsoft.ContainerService/managedClusters/policy/poddisruptionbudgets/read",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/read",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/read",
        "Microsoft.ContainerService/managedClusters/resourcequotas/read",
        "Microsoft.ContainerService/managedClusters/serviceaccounts/read",
        "Microsoft.ContainerService/managedClusters/services/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-writer"></a>RBAC-Writer von Azure Kubernetes Service

Ermöglicht den Lese-/Schreibzugriff auf die meisten Objekte in einem Namespace. Diese Rolle gestattet nicht das Anzeigen oder Ändern von Rollen oder Rollenbindungen. Diese Rolle ermöglicht jedoch den Zugriff auf Geheimnisse und das Ausführen von Pods als beliebiges Dienstkonto im Namespace, sodass sie verwendet werden kann, um die API-Zugriffsebenen eines beliebigen ServiceAccount im Namespace zu erhalten. Wenn Sie diese Rolle im Clusterumfang anwenden, wird der Zugriff auf alle Namespaces ermöglicht. [Weitere Informationen](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Erstellt oder aktualisiert eine Bereitstellung. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Dient zum Abrufen der Ergebnisse des Abonnementvorgangs. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Ruft die Abonnementliste ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/controllerrevisions/read | Liest controllerrevisions. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/daemonsets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/deployments/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/replicasets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/statefulsets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/autoscaling/horizontalpodautoscalers/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/cronjobs/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/jobs/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/configmaps/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/endpoints/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/events.k8s.io/events/read | Liest events. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/events/read | Liest events. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/daemonsets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/deployments/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/ingresses/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/networkpolicies/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/replicasets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/limitranges/read | Liest limitranges. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/read | Liest namespaces. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/networking.k8s.io/ingresses/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/networking.k8s.io/networkpolicies/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/persistentvolumeclaims/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/pods/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/policy/poddisruptionbudgets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/read | Liest resourcequotas. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/secrets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/serviceaccounts/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/services/* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read/write access to most objects in a namespace.This role does not allow viewing or modifying roles or role bindings. However, this role allows accessing Secrets and running Pods as any ServiceAccount in the namespace, so it can be used to gain the API access levels of any ServiceAccount in the namespace. Applying this role at cluster scope will give access across all namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb",
  "name": "a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/apps/controllerrevisions/read",
        "Microsoft.ContainerService/managedClusters/apps/daemonsets/*",
        "Microsoft.ContainerService/managedClusters/apps/deployments/*",
        "Microsoft.ContainerService/managedClusters/apps/replicasets/*",
        "Microsoft.ContainerService/managedClusters/apps/statefulsets/*",
        "Microsoft.ContainerService/managedClusters/autoscaling/horizontalpodautoscalers/*",
        "Microsoft.ContainerService/managedClusters/batch/cronjobs/*",
        "Microsoft.ContainerService/managedClusters/batch/jobs/*",
        "Microsoft.ContainerService/managedClusters/configmaps/*",
        "Microsoft.ContainerService/managedClusters/endpoints/*",
        "Microsoft.ContainerService/managedClusters/events.k8s.io/events/read",
        "Microsoft.ContainerService/managedClusters/events/read",
        "Microsoft.ContainerService/managedClusters/extensions/daemonsets/*",
        "Microsoft.ContainerService/managedClusters/extensions/deployments/*",
        "Microsoft.ContainerService/managedClusters/extensions/ingresses/*",
        "Microsoft.ContainerService/managedClusters/extensions/networkpolicies/*",
        "Microsoft.ContainerService/managedClusters/extensions/replicasets/*",
        "Microsoft.ContainerService/managedClusters/limitranges/read",
        "Microsoft.ContainerService/managedClusters/namespaces/read",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/ingresses/*",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/networkpolicies/*",
        "Microsoft.ContainerService/managedClusters/persistentvolumeclaims/*",
        "Microsoft.ContainerService/managedClusters/pods/*",
        "Microsoft.ContainerService/managedClusters/policy/poddisruptionbudgets/*",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/*",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/*",
        "Microsoft.ContainerService/managedClusters/resourcequotas/read",
        "Microsoft.ContainerService/managedClusters/secrets/*",
        "Microsoft.ContainerService/managedClusters/serviceaccounts/*",
        "Microsoft.ContainerService/managedClusters/services/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Writer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="databases"></a>Datenbanken


### <a name="cosmos-db-account-reader-role"></a>Cosmos DB-Rolle „Kontoleser“

Kann Azure Cosmos DB-Kontodaten lesen. Informationen zum Verwalten von Azure Cosmos DB-Konten finden Sie unter [Mitwirkender von DocumentDB-Konto](#documentdb-account-contributor). [Weitere Informationen](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/*/read | Lesen einer beliebigen Sammlung |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/readonlykeys/action | Liest die schreibgeschützten Schlüssel für Datenbankkonten. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/read | Dient zum Lesen von Metrikdefinitionen. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Metrics/read | Dient zum Lesen von Metriken. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read Azure Cosmos DB Accounts data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
  "name": "fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DocumentDB/*/read",
        "Microsoft.DocumentDB/databaseAccounts/readonlykeys/action",
        "Microsoft.Insights/MetricDefinitions/read",
        "Microsoft.Insights/Metrics/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cosmos DB Account Reader Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmos-db-operator"></a>Cosmos DB-Operator

Ermöglicht das Verwalten von Azure Cosmos DB-Konten, aber nicht das Zugreifen auf deren Daten. Verhindert den Zugriff auf Kontoschlüssel und Verbindungszeichenfolgen. [Weitere Informationen](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.DocumentDb](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | Verknüpft Ressourcen wie etwa ein Speicherkonto oder eine SQL-Datenbank mit einem Subnetz. Nicht warnbar. |
> | **NotActions** |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/readonlyKeys/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/regenerateKey/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/listKeys/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/listConnectionStrings/* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Azure Cosmos DB accounts, but not access data in them. Prevents access to account keys and connection strings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/230815da-be43-4aae-9cb4-875f7bd000aa",
  "name": "230815da-be43-4aae-9cb4-875f7bd000aa",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDb/databaseAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action"
      ],
      "notActions": [
        "Microsoft.DocumentDB/databaseAccounts/readonlyKeys/*",
        "Microsoft.DocumentDB/databaseAccounts/regenerateKey/*",
        "Microsoft.DocumentDB/databaseAccounts/listKeys/*",
        "Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cosmos DB Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmosbackupoperator"></a>CosmosBackupOperator

Kann eine Wiederherstellungsanforderung für eine Cosmos DB-Datenbank oder einen Container für ein Konto übermitteln. [Weitere Informationen](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/backup/action | Sendet eine Anforderung zum Konfigurieren der Sicherung. |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/restore/action | Sendet eine Wiederherstellungsanforderung. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can submit restore request for a Cosmos DB database or a container for an account",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/db7b14f2-5adf-42da-9f96-f2ee17bab5cb",
  "name": "db7b14f2-5adf-42da-9f96-f2ee17bab5cb",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDB/databaseAccounts/backup/action",
        "Microsoft.DocumentDB/databaseAccounts/restore/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CosmosBackupOperator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="documentdb-account-contributor"></a>Mitwirkender von DocumentDB-Konto

Kann Azure Cosmos DB-Konten verwalten. Azure Cosmos DB wurde früher als DocumentDB bezeichnet. [Weitere Informationen](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.DocumentDb](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/* | Kann Azure Cosmos DB-Konten erstellen und verwalten |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | Verknüpft Ressourcen wie etwa ein Speicherkonto oder eine SQL-Datenbank mit einem Subnetz. Nicht warnbar. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage DocumentDB accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5bd9cd88-fe45-4216-938b-f97437e15450",
  "name": "5bd9cd88-fe45-4216-938b-f97437e15450",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DocumentDb/databaseAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DocumentDB Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="redis-cache-contributor"></a>Mitwirkender von Redis-Cache

Ermöglicht Ihnen das Verwalten von Redis Caches, nicht aber den Zugriff darauf.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Cache](resource-provider-operations.md#microsoftcache)/register/action | Registriert den Ressourcenanbieter „Microsoft.Cache“ bei einem Abonnement. |
> | [Microsoft.Cache](resource-provider-operations.md#microsoftcache)/redis/* | Erstellen und Verwalten von Redis-Caches |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Redis caches, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e0f68234-74aa-48ed-b826-c38b57376e17",
  "name": "e0f68234-74aa-48ed-b826-c38b57376e17",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cache/register/action",
        "Microsoft.Cache/redis/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Redis Cache Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-db-contributor"></a>Mitwirkender von SQL DB

Ermöglicht Ihnen das Verwalten von SQL-Datenbanken, nicht aber den Zugriff darauf. Darüber hinaus können Sie deren sicherheitsbezogenen Richtlinien oder übergeordneten SQL-Server nicht verwalten. [Weitere Informationen](../data-share/concepts-roles-permissions.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/*/read |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/* | Erstellen und Verwalten von SQL-Datenbanken |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/read | Gibt die Liste der Server zurück oder ruft die Eigenschaften für den angegebenen Server ab. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | Dient zum Lesen von Metriken. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | Dient zum Lesen von Metrikdefinitionen. |
> | **NotActions** |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditingSettings/* | Überwachungseinstellungen bearbeiten |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditRecords/read | Dient zum Abrufen der Datensätze für die Datenbankblobüberwachung. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/dataMaskingPolicies/* | Datenmaskierungsrichtlinien bearbeiten |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/extendedAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityAlertPolicies/* | Richtlinien für Sicherheitswarnungen bearbeiten |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityMetrics/* | Sicherheitsmetriken bearbeiten |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL databases, but not access to them. Also, you can't manage their security-related policies or their parent SQL servers.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9b7fa17d-e63e-47b0-bb0a-15c516ac86ec",
  "name": "9b7fa17d-e63e-47b0-bb0a-15c516ac86ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/servers/databases/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL DB Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-managed-instance-contributor"></a>Verwaltete SQL-Instanz: Mitwirkender

Diese Rolle ermöglicht Ihnen das Verwalten verwalteter SQL-Instanzen und der erforderlichen Netzwerkkonfiguration, jedoch nicht das Erteilen des Zugriffs an andere.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/routeTables/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/*/read |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/instanceFailoverGroups/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/* |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/* |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | Dient zum Lesen von Metriken. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | Dient zum Lesen von Metrikdefinitionen. |
> | **NotActions** |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/azureADOnlyAuthentications/delete | Löscht ein spezifisches verwaltetes Serverobjekt für die ausschließliche Azure Active Directory-Authentifizierung. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/azureADOnlyAuthentications/write | Fügt ein spezifisches verwaltetes Serverobjekt für die ausschließliche Azure Active Directory-Authentifizierung hinzu oder aktualisiert es. |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL Managed Instances and required network configuration, but can't give access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4939a1f6-9ae0-4e48-a1e0-f2cbe897382d",
  "name": "4939a1f6-9ae0-4e48-a1e0-f2cbe897382d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Network/networkSecurityGroups/*",
        "Microsoft.Network/routeTables/*",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/locations/instanceFailoverGroups/*",
        "Microsoft.Sql/managedInstances/*",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/*",
        "Microsoft.Network/virtualNetworks/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/azureADOnlyAuthentications/delete",
        "Microsoft.Sql/managedInstances/azureADOnlyAuthentications/write"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Managed Instance Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-security-manager"></a>SQL-Sicherheits-Manager

Ermöglicht Ihnen das Verwalten von sicherheitsbezogenen Richtlinien von SQL-Server und Datenbanken, jedoch nicht den Zugriff darauf. [Weitere Informationen](../azure-sql/database/azure-defender-for-sql.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | Verknüpft Ressourcen wie etwa ein Speicherkonto oder eine SQL-Datenbank mit einem Subnetz. Nicht warnbar. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/administratorAzureAsyncOperation/read | Ruft das Ergebnis asynchroner Azure-Administratorvorgänge für die verwaltete Instanz ab. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/transparentDataEncryption/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/auditingSettings/* | Erstellen und Verwalten von SQL Server-Überwachungseinstellungen |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/extendedAuditingSettings/read | Dient zum Abrufen von Details zur Richtlinie für die erweiterte Serverblobüberwachung, die für einen bestimmten Server konfiguriert ist. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditingSettings/* | Erstellen und Verwalten von Überwachungseinstellungen von SQL Server-Datenbanken |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditRecords/read | Dient zum Abrufen der Datensätze für die Datenbankblobüberwachung. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/dataMaskingPolicies/* | Erstellen und Verwalten von Datenmaskierungsrichtlinien von SQL Server-Datenbanken |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/extendedAuditingSettings/read | Dient zum Abrufen von Details zur erweiterten Blobüberwachungsrichtlinie, die für eine bestimmte Datenbank konfiguriert ist. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/read | Gibt die Liste der Datenbanken zurück oder ruft die Eigenschaften für die angegebene Datenbank ab. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/read | Ruft ein Datenbankschema ab. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/columns/read | Ruft eine Datenbankspalte ab. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/read | Abrufen einer Datentabelle. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityAlertPolicies/* | Erstellen und Verwalten von Richtlinien für Sicherheitswarnungen von SQL Server-Datenbanken |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityMetrics/* | Erstellen und Verwalten von Sicherheitsmetriken von SQL Server-Datenbanken |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/transparentDataEncryption/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/firewallRules/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/read | Gibt die Liste der Server zurück oder ruft die Eigenschaften für den angegebenen Server ab. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/securityAlertPolicies/* | Erstellen und Verwalten von Richtlinien für Sicherheitswarnungen von SQL Server |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/vulnerabilityAssessments/* |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/azureADOnlyAuthentications/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/read | Gibt die Liste der verwalteten Instanzen zurück oder ruft die Eigenschaften für die angegebene verwaltete Instanz ab. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/azureADOnlyAuthentications/* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage the security-related policies of SQL servers and databases, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/056cd41c-7e88-42e1-933e-88ba6a50c9c3",
  "name": "056cd41c-7e88-42e1-933e-88ba6a50c9c3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/administratorAzureAsyncOperation/read",
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/transparentDataEncryption/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/read",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/read",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/read",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/read",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/transparentDataEncryption/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/firewallRules/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*",
        "Microsoft.Support/*",
        "Microsoft.Sql/servers/azureADOnlyAuthentications/*",
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/azureADOnlyAuthentications/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Security Manager",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-server-contributor"></a>Mitwirkender von SQL Server

Diese Rolle ermöglicht es Ihnen, SQL-Server und -Datenbanken zu verwalten, gewährt Ihnen jedoch keinen Zugriff darauf und auch nicht auf deren sicherheitsbezogenen Richtlinien. [Weitere Informationen](../azure-sql/database/authentication-aad-configure.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/*/read |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/* | Erstellen und Verwalten von SQL-Servern |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | Dient zum Lesen von Metriken. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | Dient zum Lesen von Metrikdefinitionen. |
> | **NotActions** |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/auditingSettings/* | SQL Server-Überwachungseinstellungen bearbeiten |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditingSettings/* | Überwachungseinstellungen von SQL Server-Datenbanken bearbeiten |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditRecords/read | Dient zum Abrufen der Datensätze für die Datenbankblobüberwachung. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/dataMaskingPolicies/* | Datenmaskierungsrichtlinien von SQL Server-Datenbanken bearbeiten |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/extendedAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityAlertPolicies/* | Richtlinien für Sicherheitswarnungen von SQL Server-Datenbanken bearbeiten |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityMetrics/* | Sicherheitsmetriken von SQL Server-Datenbanken bearbeiten |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/extendedAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/securityAlertPolicies/* | Richtlinien für Sicherheitswarnungen von SQL Server bearbeiten |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/azureADOnlyAuthentications/delete | Löscht ein spezifisches Objekt für die ausschließliche Azure Active Directory-Authentifizierung. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/azureADOnlyAuthentications/write | Fügt ein spezifisches Objekt für die ausschließliche Azure Active Directory-Authentifizierung hinzu oder aktualisiert es. |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL servers and databases, but not access to them, and not their security -related policies.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437",
  "name": "6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/servers/*",
        "Microsoft.Support/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/azureADOnlyAuthentications/delete",
        "Microsoft.Sql/servers/azureADOnlyAuthentications/write"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Server Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="analytics"></a>Analytics


### <a name="azure-event-hubs-data-owner"></a>Azure Event Hubs-Datenbesitzer

Ermöglicht den uneingeschränkten Zugriff auf die Azure Event Hubs-Ressourcen. [Weitere Informationen](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f526a384-b230-433a-b45c-95f59c4a2dec",
  "name": "f526a384-b230-433a-b45c-95f59c4a2dec",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-event-hubs-data-receiver"></a>Azure Event Hubs-Datenempfänger

Ermöglicht Empfängern den Zugriff auf die Azure Event Hubs-Ressourcen. [Weitere Informationen](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/*/eventhubs/consumergroups/read |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/*/receive/action |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows receive access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a638d3c7-ab3a-418d-83e6-5f17a39d4fde",
  "name": "a638d3c7-ab3a-418d-83e6-5f17a39d4fde",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*/eventhubs/consumergroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*/receive/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Receiver",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-event-hubs-data-sender"></a>Azure Event Hubs-Datensender

Ermöglicht Absendern den Zugriff auf die Azure Event Hubs-Ressourcen. [Weitere Informationen](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/*/eventhubs/read |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/*/send/action |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows send access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2b629674-e913-4c01-ae53-ef4638d8f975",
  "name": "2b629674-e913-4c01-ae53-ef4638d8f975",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*/eventhubs/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-factory-contributor"></a>Mitwirkender von Data Factory

Erstellen und verwalten Sie Data Factorys sowie die darin enthaltenen untergeordneten Ressourcen. [Weitere Informationen](../data-factory/concepts-roles-permissions.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.DataFactory](resource-provider-operations.md#microsoftdatafactory)/dataFactories/* | Erstellt und verwaltet Data Factorys und darin enthaltene untergeordnete Ressourcen. |
> | [Microsoft.DataFactory](resource-provider-operations.md#microsoftdatafactory)/factories/* | Erstellt und verwaltet Data Factorys und darin enthaltene untergeordnete Ressourcen. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/write | Erstellt oder aktualisiert eventSubscription. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create and manage data factories, as well as child resources within them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/673868aa-7521-48a0-acc6-0f60742d39f5",
  "name": "673868aa-7521-48a0-acc6-0f60742d39f5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DataFactory/dataFactories/*",
        "Microsoft.DataFactory/factories/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.EventGrid/eventSubscriptions/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Factory Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-purger"></a>Datenpurger

Kann Analysedaten endgültig löschen. [Weitere Informationen](../azure-monitor/platform/personal-data-mgmt.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/purge/action | Daten werden aus Application Insights gelöscht |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Anzeigen von Log Analytics-Daten |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/purge/action | Löscht die angegebenen Daten aus dem Arbeitsbereich. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can purge analytics data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/150f5e0c-0603-4f03-8c7f-cf70034c4e90",
  "name": "150f5e0c-0603-4f03-8c7f-cf70034c4e90",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/components/*/read",
        "Microsoft.Insights/components/purge/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/purge/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Purger",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hdinsight-cluster-operator"></a>HDInsight-Clusteroperator

Ermöglicht Ihnen das Lesen und Ändern von HDInsight-Clusterkonfigurationen. [Weitere Informationen](../hdinsight/hdinsight-migrate-granular-access-cluster-configurations.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.HDInsight](resource-provider-operations.md#microsofthdinsight)/*/read |  |
> | [Microsoft.HDInsight](resource-provider-operations.md#microsofthdinsight)/clusters/getGatewaySettings/action | Ruft Gatewayeinstellungen für HDInsight-Cluster ab. |
> | [Microsoft.HDInsight](resource-provider-operations.md#microsofthdinsight)/clusters/updateGatewaySettings/action | Aktualisiert Gatewayeinstellungen für HDInsight-Cluster. |
> | [Microsoft.HDInsight](resource-provider-operations.md#microsofthdinsight)/clusters/configurations/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | Ruft Bereitstellungsvorgänge ab oder listet sie auf. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and modify HDInsight cluster configurations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/61ed4efc-fab3-44fd-b111-e24485cc132a",
  "name": "61ed4efc-fab3-44fd-b111-e24485cc132a",
  "permissions": [
    {
      "actions": [
        "Microsoft.HDInsight/*/read",
        "Microsoft.HDInsight/clusters/getGatewaySettings/action",
        "Microsoft.HDInsight/clusters/updateGatewaySettings/action",
        "Microsoft.HDInsight/clusters/configurations/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "HDInsight Cluster Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hdinsight-domain-services-contributor"></a>Mitwirkender für die HDInsight-Domänendienste

Ermöglicht Ihnen, Vorgänge im Zusammenhang mit Domänendiensten, die für das HDInsight Enterprise-Sicherheitspaket erforderlich sind, zu lesen, zu erstellen, zu ändern und zu löschen. [Weitere Informationen](../hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.AAD](resource-provider-operations.md#microsoftaad)/*/read |  |
> | [Microsoft.AAD](resource-provider-operations.md#microsoftaad)/domainServices/*/read |  |
> | [Microsoft.AAD](resource-provider-operations.md#microsoftaad)/domainServices/oucontainer/* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can Read, Create, Modify and Delete Domain Services related operations needed for HDInsight Enterprise Security Package",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8d8d5a11-05d3-4bda-a417-a08778121c7c",
  "name": "8d8d5a11-05d3-4bda-a417-a08778121c7c",
  "permissions": [
    {
      "actions": [
        "Microsoft.AAD/*/read",
        "Microsoft.AAD/domainServices/*/read",
        "Microsoft.AAD/domainServices/oucontainer/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "HDInsight Domain Services Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="log-analytics-contributor"></a>Log Analytics-Mitwirkender

Ein Log Analytics-Mitwirkender kann alle Überwachungsdaten lesen und Überwachungseinstellungen bearbeiten. Das Bearbeiten von Überwachungseinstellungen schließt folgende Aufgaben ein: Hinzufügen der VM-Erweiterung zu VMs, Lesen von Speicherkontoschlüsseln zum Konfigurieren von Protokollsammlungen aus Azure Storage, Erstellen und Konfigurieren von Automation-Konten, Hinzufügen von Lösungen, Konfigurieren der Azure-Diagnose für alle Azure-Ressourcen. [Weitere Informationen](../azure-monitor/platform/manage-access.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | */Lesen | Lesen von Ressourcen aller Typen mit Ausnahme geheimer Schlüssel |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/* |  |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/extensions/* |  |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/action | Listet die Zugriffsschlüssel für die Speicherkonten auf. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/extensions/* |  |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/extensions/write | Installiert oder aktualisiert eine Azure Arc-Erweiterung |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Erstellt, aktualisiert oder liest die Diagnoseeinstellung für den Analysis-Server. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/* |  |
> | [Microsoft.OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourcegroups/deployments/* |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | Gibt die Zugriffsschlüssel für das angegebene Speicherkonto zurück. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Log Analytics Contributor can read all monitoring data and edit monitoring settings. Editing monitoring settings includes adding the VM extension to VMs; reading storage account keys to be able to configure collection of logs from Azure Storage; creating and configuring Automation accounts; adding solutions; and configuring Azure diagnostics on all Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/92aaf0da-9dab-42b6-94a3-d43ce8d16293",
  "name": "92aaf0da-9dab-42b6-94a3-d43ce8d16293",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Automation/automationAccounts/*",
        "Microsoft.ClassicCompute/virtualMachines/extensions/*",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.Compute/virtualMachines/extensions/*",
        "Microsoft.HybridCompute/machines/extensions/write",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.OperationalInsights/*",
        "Microsoft.OperationsManagement/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourcegroups/deployments/*",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Log Analytics Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="log-analytics-reader"></a>Log Analytics-Leser

Ein Log Analytics-Leser kann alle Überwachungsdaten anzeigen und durchsuchen sowie Überwachungseinstellungen anzeigen. Hierzu zählt auch die Anzeige der Konfiguration von Azure-Diagnosen für alle Azure-Ressourcen. [Weitere Informationen](../azure-monitor/platform/manage-access.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | */Lesen | Lesen von Ressourcen aller Typen mit Ausnahme geheimer Schlüssel |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | Führt eine Suche mit der neuen Engine aus. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/search/action | Führt eine Suchabfrage aus. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/sharedKeys/read | Ruft die gemeinsam verwendeten Schlüssel für den Arbeitsbereich ab. Diese Schlüssel werden verwendet, um Microsoft Operational Insights-Agents mit dem Arbeitsbereich zu verbinden. |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Log Analytics Reader can view and search all monitoring data as well as and view monitoring settings, including viewing the configuration of Azure diagnostics on all Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/73c42c96-874c-492b-b04d-ab87d138a893",
  "name": "73c42c96-874c-492b-b04d-ab87d138a893",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.OperationalInsights/workspaces/sharedKeys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Log Analytics Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="schema-registry-contributor-preview"></a>Mitwirkender der Schemaregistrierung (Vorschau)

Lesen, Schreiben und Löschen von Schemaregistrierungsgruppen und Schemas.

> [!div class="mx-tableFixed"]
> | Aktionen | Beschreibung |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemagroups/* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemas/* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read, write, and delete Schema Registry groups and schemas.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5dffeca3-4936-4216-b2bc-10343a5abb25",
  "name": "5dffeca3-4936-4216-b2bc-10343a5abb25",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/namespaces/schemagroups/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/namespaces/schemas/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Schema Registry Contributor (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="schema-registry-reader-preview"></a>Schemaregistrierungsleser (Vorschau)

Lesen und Auflisten von Schemaregistrierungsgruppen und Schemas.

> [!div class="mx-tableFixed"]
> | Aktionen | Beschreibung |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemagroups/read | Dient zum Abrufen einer Liste mit SchemaGroup-Ressourcenbeschreibungen. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemas/read | Dient zum Abrufen von Schemas. |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read and list Schema Registry groups and schemas.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2c56ea50-c6b3-40a6-83c0-9d98858bc7d2",
  "name": "2c56ea50-c6b3-40a6-83c0-9d98858bc7d2",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/namespaces/schemagroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/namespaces/schemas/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Schema Registry Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="blockchain"></a>Blockchain


### <a name="blockchain-member-node-access-preview"></a>Zugriff auf Blockchainmitgliedsknoten (Vorschauversion)

Ermöglicht den Zugriff auf Blockchainmitgliedsknoten. [Weitere Informationen](../blockchain/service/configure-aad.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Blockchain](resource-provider-operations.md#microsoftblockchain)/blockchainMembers/transactionNodes/read | Ruft die vorhandenen Transaktionsknoten eines Blockchainmitglieds ab oder listet sie auf |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.Blockchain](resource-provider-operations.md#microsoftblockchain)/blockchainMembers/transactionNodes/connect/action | Stellt eine Verbindung mit dem Transaktionsknoten eines Blockchainmitglieds her |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for access to Blockchain Member nodes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/31a002a1-acaf-453e-8a5b-297c9ca1ea24",
  "name": "31a002a1-acaf-453e-8a5b-297c9ca1ea24",
  "permissions": [
    {
      "actions": [
        "Microsoft.Blockchain/blockchainMembers/transactionNodes/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Blockchain Member Node Access (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="ai--machine-learning"></a>KI und Machine Learning


### <a name="cognitive-services-contributor"></a>Mitwirkender für Cognitive Services

Ermöglicht Ihnen das Erstellen, Lesen, Aktualisieren, Löschen und Verwalten von Cognitive Services-Schlüsseln. [Weitere Informationen](../cognitive-services/cognitive-services-virtual-networks.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/* |  |
> | [Microsoft.Features](resource-provider-operations.md#microsoftfeatures)/features/read | Ruft die Features eines Abonnements ab. |
> | [Microsoft.Features](resource-provider-operations.md#microsoftfeatures)/providers/features/read | Ruft das Feature eines Abonnements in einem angegebenen Ressourcenanbieter ab. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Erstellt, aktualisiert oder liest die Diagnoseeinstellung für den Analysis-Server. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/logDefinitions/read | Dient zum Lesen von Protokolldefinitionen. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricdefinitions/read | Dient zum Lesen von Metrikdefinitionen. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | Dient zum Lesen von Metriken. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | Ruft Bereitstellungsvorgänge ab oder listet sie auf. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Dient zum Abrufen der Ergebnisse des Abonnementvorgangs. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Ruft die Abonnementliste ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourcegroups/deployments/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create, read, update, delete and manage keys of Cognitive Services.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68",
  "name": "25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.CognitiveServices/*",
        "Microsoft.Features/features/read",
        "Microsoft.Features/providers/features/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Insights/logDefinitions/read",
        "Microsoft.Insights/metricdefinitions/read",
        "Microsoft.Insights/metrics/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourcegroups/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-data-reader-preview"></a>Cognitive Services-Datenleser (Vorschau)

Ermöglicht das Lesen von Cognitive Services-Daten.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | *keine* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read Cognitive Services data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b59867f0-fa02-499b-be73-45a86b5b3e1c",
  "name": "b59867f0-fa02-499b-be73-45a86b5b3e1c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Data Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-user"></a>Cognitive Services-Benutzer

Ermöglicht Ihnen das Lesen und Auflisten von Cognitive Services-Schlüsseln. [Weitere Informationen](../cognitive-services/authentication.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/listkeys/action | Dient zum Auflisten von Schlüsseln. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | Liest eine klassische Metrikwarnung. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/read | Liest eine Diagnoseeinstellung für eine Ressource. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/logDefinitions/read | Dient zum Lesen von Protokolldefinitionen. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricdefinitions/read | Dient zum Lesen von Metrikdefinitionen. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | Dient zum Lesen von Metriken. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | Ruft Bereitstellungsvorgänge ab oder listet sie auf. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Dient zum Abrufen der Ergebnisse des Abonnementvorgangs. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Ruft die Abonnementliste ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and list keys of Cognitive Services.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a97b65f3-24c7-4388-baec-2e87135dc908",
  "name": "a97b65f3-24c7-4388-baec-2e87135dc908",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read",
        "Microsoft.CognitiveServices/accounts/listkeys/action",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Insights/diagnosticSettings/read",
        "Microsoft.Insights/logDefinitions/read",
        "Microsoft.Insights/metricdefinitions/read",
        "Microsoft.Insights/metrics/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="mixed-reality"></a>Mixed Reality


### <a name="remote-rendering-administrator"></a>Remote Rendering-Administrator

Bietet dem Benutzer Konvertierungs-, Sitzungsverwaltungs-, Rendering- und Diagnosefunktionen für Azure Remote Rendering. [Weitere Informationen](../remote-rendering/how-tos/authentication.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | *keine* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/convert/action | Startet die Objektkonvertierung. |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/convert/read | Ruft die Objektkonvertierungseigenschaften ab. |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/convert/delete | Beendet die Objektkonvertierung. |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/read | Ruft Sitzungseigenschaften ab. |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/action | Startet Sitzungen. |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/delete | Beendet Sitzungen. |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/render/read | Stellt eine Verbindung mit einer Sitzung her. |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/diagnostic/read | Stellt eine Verbindung mit der Remote Rendering-Prüfung her. |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides user with conversion, manage session, rendering and diagnostics capabilities for Azure Remote Rendering",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3df8b902-2a6f-47c7-8cc5-360e9b272a7e",
  "name": "3df8b902-2a6f-47c7-8cc5-360e9b272a7e",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/render/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/diagnostic/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Remote Rendering Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="remote-rendering-client"></a>Remote Rendering-Client

Bietet dem Benutzer Sitzungsverwaltungs-, Rendering- und Diagnosefunktionen für Azure Remote Rendering. [Weitere Informationen](../remote-rendering/how-tos/authentication.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | *keine* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/read | Ruft Sitzungseigenschaften ab. |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/action | Startet Sitzungen. |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/delete | Beendet Sitzungen. |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/render/read | Stellt eine Verbindung mit einer Sitzung her. |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/diagnostic/read | Stellt eine Verbindung mit der Remote Rendering-Prüfung her. |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides user with manage session, rendering and diagnostics capabilities for Azure Remote Rendering.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d39065c4-c120-43c9-ab0a-63eed9795f0a",
  "name": "d39065c4-c120-43c9-ab0a-63eed9795f0a",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/render/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/diagnostic/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Remote Rendering Client",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-contributor"></a>Spatial Anchors-Kontomitwirkender

Ermöglicht Ihnen das Verwalten von Raumankern in Ihrem Konto, nicht jedoch das Löschen von Ankern. [Weitere Informationen](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | *keine* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/create/action | Hiermit werden Raumanker erstellt. |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/discovery/read | Hiermit werden Raumanker in räumlicher Nähe ermittelt. |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/properties/read | Hiermit werden die Eigenschaften von Raumankern abgerufen. |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/query/read | Hiermit finden Sie Raumanker. |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/read | Hiermit übermitteln Sie Diagnosedaten, um die Qualität des Azure Spatial Anchors-Diensts zu verbessern. |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/write | Hiermit aktualisieren Sie die Eigenschaften von Raumankern. |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage spatial anchors in your account, but not delete them",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827",
  "name": "8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/create/action",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-owner"></a>Spatial Anchors-Kontobesitzer

Ermöglicht Ihnen das Verwalten von Raumankern in Ihrem Konto, einschließlich der Löschung von Ankern. [Weitere Informationen](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | *keine* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/create/action | Hiermit werden Raumanker erstellt. |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/delete | Hiermit werden Raumanker gelöscht. |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/discovery/read | Hiermit werden Raumanker in räumlicher Nähe ermittelt. |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/properties/read | Hiermit werden die Eigenschaften von Raumankern abgerufen. |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/query/read | Hiermit finden Sie Raumanker. |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/read | Hiermit übermitteln Sie Diagnosedaten, um die Qualität des Azure Spatial Anchors-Diensts zu verbessern. |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/write | Hiermit aktualisieren Sie die Eigenschaften von Raumankern. |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage spatial anchors in your account, including deleting them",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/70bbe301-9835-447d-afdd-19eb3167307c",
  "name": "70bbe301-9835-447d-afdd-19eb3167307c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/create/action",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/delete",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-reader"></a>Spatial Anchors-Kontoleser

Ermöglicht Ihnen das Ermitteln und Lesen von Eigenschaften für Raumanker in Ihrem Dokument. [Weitere Informationen](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | *keine* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/discovery/read | Hiermit werden Raumanker in räumlicher Nähe ermittelt. |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/properties/read | Hiermit werden die Eigenschaften von Raumankern abgerufen. |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/query/read | Hiermit finden Sie Raumanker. |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/read | Hiermit übermitteln Sie Diagnosedaten, um die Qualität des Azure Spatial Anchors-Diensts zu verbessern. |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you locate and read properties of spatial anchors in your account",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d51204f-eb77-4b1c-b86a-2ec626c49413",
  "name": "5d51204f-eb77-4b1c-b86a-2ec626c49413",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="integration"></a>Integration


### <a name="api-management-service-contributor"></a>Mitwirkender des API-Verwaltungsdienstes

Kann Dienst und APIs verwalten. [Weitere Informationen](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/* | Erstellen und Verwalten des API Management-Diensts |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage service and the APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c",
  "name": "312a565d-c81f-4fd8-895a-4e21e48d571c",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="api-management-service-operator-role"></a>Operatorrolle des API Management-Diensts

Kann Dienst, aber nicht die APIs verwalten. [Weitere Informationen](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/*/read | Dient zum Lesen von API Management-Dienstinstanzen. |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/backup/action | Dient zum Sichern des API Management-Diensts im angegebenen Container in einem vom Benutzer bereitgestellten Speicherkonto. |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/delete | Dient zum Löschen einer API Management-Dienstinstanz. |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/managedeployments/action | Dient zum Ändern der SKU/Einheiten sowie zum Hinzufügen/Entfernen regionaler Bereitstellungen des API Management-Diensts. |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/read | Dient zum Lesen der Metadaten für eine API Management-Dienstinstanz. |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/restore/action | Dient zum Wiederherstellen des API Management-Diensts aus dem angegebenen Container in einem vom Benutzer bereitgestellten Speicherkonto. |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/updatecertificate/action | Dient zum Hochladen eines TLS/SSL-Zertifikats für einen API Management-Dienst |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/updatehostname/action | Dient zum Einrichten, Aktualisieren oder Entfernen benutzerdefinierter Domänennamen für einen API Management-Dienst. |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/write | Erstellen oder Aktualisieren einer API Management-Dienstinstanz |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/users/keys/read | Abrufen von Benutzern zugeordneten Schlüsseln |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage service but not the APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e022efe7-f5ba-4159-bbe4-b44f577e9b61",
  "name": "e022efe7-f5ba-4159-bbe4-b44f577e9b61",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*/read",
        "Microsoft.ApiManagement/service/backup/action",
        "Microsoft.ApiManagement/service/delete",
        "Microsoft.ApiManagement/service/managedeployments/action",
        "Microsoft.ApiManagement/service/read",
        "Microsoft.ApiManagement/service/restore/action",
        "Microsoft.ApiManagement/service/updatecertificate/action",
        "Microsoft.ApiManagement/service/updatehostname/action",
        "Microsoft.ApiManagement/service/write",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.ApiManagement/service/users/keys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="api-management-service-reader-role"></a>Leserrolle des API Management-Diensts

Schreibgeschützter Zugriff auf Dienst und APIs [Weitere Informationen](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/*/read | Dient zum Lesen von API Management-Dienstinstanzen. |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/read | Dient zum Lesen der Metadaten für eine API Management-Dienstinstanz. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/users/keys/read | Abrufen von Benutzern zugeordneten Schlüsseln |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only access to service and APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/71522526-b88f-4d52-b57f-d31fc3546d0d",
  "name": "71522526-b88f-4d52-b57f-d31fc3546d0d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*/read",
        "Microsoft.ApiManagement/service/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.ApiManagement/service/users/keys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Reader Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="app-configuration-data-owner"></a>App Configuration-Datenbesitzer

Ermöglicht den Vollzugriff auf App Configuration-Daten. [Weitere Informationen](../azure-app-configuration/concept-enable-rbac.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | *keine* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/read |  |
> | [Microsoft.AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/write |  |
> | [Microsoft.AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/delete |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows full access to App Configuration data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b",
  "name": "5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppConfiguration/configurationStores/*/read",
        "Microsoft.AppConfiguration/configurationStores/*/write",
        "Microsoft.AppConfiguration/configurationStores/*/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "App Configuration Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="app-configuration-data-reader"></a>App Configuration-Datenleser

Ermöglicht den Lesezugriff auf App Configuration-Daten. [Weitere Informationen](../azure-app-configuration/concept-enable-rbac.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | *keine* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/read |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to App Configuration data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/516239f1-63e1-4d78-a4de-a74fb236a071",
  "name": "516239f1-63e1-4d78-a4de-a74fb236a071",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppConfiguration/configurationStores/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "App Configuration Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-owner"></a>Azure Service Bus-Datenbesitzer

Ermöglicht den uneingeschränkten Zugriff auf die Azure Service Bus-Ressourcen. [Weitere Informationen](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/090c5cfd-751d-490a-894a-3ce6f1109419",
  "name": "090c5cfd-751d-490a-894a-3ce6f1109419",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-receiver"></a>Azure Service Bus-Datenempfänger

Ermöglicht Empfängern den Zugriff auf die Azure Service Bus-Ressourcen. [Weitere Informationen](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/queues/read |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/read |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/subscriptions/read |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/receive/action |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for receive access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0",
  "name": "4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*/queues/read",
        "Microsoft.ServiceBus/*/topics/read",
        "Microsoft.ServiceBus/*/topics/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*/receive/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Receiver",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-sender"></a>Azure Service Bus-Datensender

Ermöglicht Absendern den Zugriff auf die Azure Service Bus-Ressourcen. [Weitere Informationen](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/queues/read |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/read |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/subscriptions/read |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/send/action |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for send access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/69a216fc-b8fb-44d8-bc22-1f3c2cd27a39",
  "name": "69a216fc-b8fb-44d8-bc22-1f3c2cd27a39",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*/queues/read",
        "Microsoft.ServiceBus/*/topics/read",
        "Microsoft.ServiceBus/*/topics/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-stack-registration-owner"></a>Besitzer der Azure Stack-Registrierung

Ermöglicht Ihnen die Verwaltung von Azure Stack-Registrierungen.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.AzureStack](resource-provider-operations.md#microsoftazurestack)/edgeSubscriptions/read | Ruft die Eigenschaften eines Azure Stack Edge-Abonnements ab. |
> | [Microsoft.AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/products/*/action |  |
> | [Microsoft.AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/products/read | Ruft die Eigenschaften eines Azure Stack-Marketplace-Produkts ab. |
> | [Microsoft.AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/read | Ruft die Eigenschaften einer Azure Stack-Registrierung ab. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Azure Stack registrations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6f12a6df-dd06-4f3e-bcb1-ce8be600526a",
  "name": "6f12a6df-dd06-4f3e-bcb1-ce8be600526a",
  "permissions": [
    {
      "actions": [
        "Microsoft.AzureStack/edgeSubscriptions/read",
        "Microsoft.AzureStack/registrations/products/*/action",
        "Microsoft.AzureStack/registrations/products/read",
        "Microsoft.AzureStack/registrations/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Stack Registration Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-eventsubscription-contributor"></a>EventGrid EventSubscription-Mitwirkender

Ermöglicht die Verwaltung von EventGrid-Ereignisabonnementvorgängen. [Weitere Informationen](../event-grid/security-authorization.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/* |  |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/topicTypes/eventSubscriptions/read | Listet globale Ereignisabonnements nach Thematyp auf. |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/eventSubscriptions/read | Listet regionale Ereignisabonnements auf. |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/topicTypes/eventSubscriptions/read | Listet regionale Ereignisabonnements nach Thematyp auf. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage EventGrid event subscription operations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/428e0ff0-5e57-4d9c-a221-2c70d0e0a443",
  "name": "428e0ff0-5e57-4d9c-a221-2c70d0e0a443",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/eventSubscriptions/*",
        "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid EventSubscription Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-eventsubscription-reader"></a>EventGrid EventSubscription-Leser

Ermöglicht das Lesen von EventGrid-Ereignisabonnements. [Weitere Informationen](../event-grid/security-authorization.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/read | Liest eventSubscription. |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/topicTypes/eventSubscriptions/read | Listet globale Ereignisabonnements nach Thematyp auf. |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/eventSubscriptions/read | Listet regionale Ereignisabonnements auf. |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/topicTypes/eventSubscriptions/read | Listet regionale Ereignisabonnements nach Thematyp auf. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read EventGrid event subscriptions.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2414bbcf-6497-4faf-8c65-045460748405",
  "name": "2414bbcf-6497-4faf-8c65-045460748405",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/eventSubscriptions/read",
        "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid EventSubscription Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-contributor"></a>Mitwirkender an FHIR-Daten

Die Rolle ermöglicht dem Benutzer oder Prinzipal vollen Zugriff auf FHIR-Daten. [Weitere Informationen](../healthcare-apis/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | *keine* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal full access to FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5a1fc7df-4bf1-4951-a576-89034ee01acd",
  "name": "5a1fc7df-4bf1-4951-a576-89034ee01acd",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-exporter"></a>FHIR-Datenexportierer

Die Rolle ermöglicht dem Benutzer oder Prinzipal das Lesen und Exportieren von FHIR-Daten. [Weitere Informationen](../healthcare-apis/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | *keine* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/read | Liest FHIR-Ressourcen (einschließlich Suche und Verlauf mit Versionsangabe).  |
> | Microsoft.HealthcareApis/services/fhir/resources/export/action | Exportvorgang ($export). |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read and export FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3db33094-8700-4567-8da5-1501d4e7e843",
  "name": "3db33094-8700-4567-8da5-1501d4e7e843",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/read",
        "Microsoft.HealthcareApis/services/fhir/resources/export/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Exporter",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-reader"></a>FHIR-Datenleser

Die Rolle ermöglicht dem Benutzer oder Prinzipal das Lesen von FHIR-Daten. [Weitere Informationen](../healthcare-apis/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | *keine* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/read | Liest FHIR-Ressourcen (einschließlich Suche und Verlauf mit Versionsangabe).  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4c8d0bbc-75d3-4935-991f-5f3c56d81508",
  "name": "4c8d0bbc-75d3-4935-991f-5f3c56d81508",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-writer"></a>FHIR-Datenschreiber

Die Rolle ermöglicht dem Benutzer oder Prinzipal das Lesen und Schreiben von FHIR-Daten. [Weitere Informationen](../healthcare-apis/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | *keine* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/* |  |
> | **NotDataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/hardDelete/action | Endgültiger Löschvorgang (einschließlich Versionsverlauf). |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read and write FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3f88fce4-5892-4214-ae73-ba5294559913",
  "name": "3f88fce4-5892-4214-ae73-ba5294559913",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/*"
      ],
      "notDataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/hardDelete/action"
      ]
    }
  ],
  "roleName": "FHIR Data Writer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="integration-service-environment-contributor"></a>Mitwirkender für Integrationsdienstumgebungen

Hiermit wird das Verwalten von Integrationsdienstumgebungen ermöglicht, nicht aber der Zugriff auf diese. [Weitere Informationen](../logic-apps/add-artifacts-integration-service-environment-ise.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage integration service environments, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a41e2c5b-bd99-4a07-88f4-9bf657a760b8",
  "name": "a41e2c5b-bd99-4a07-88f4-9bf657a760b8",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*",
        "Microsoft.Logic/integrationServiceEnvironments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Integration Service Environment Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="integration-service-environment-developer"></a>Entwickler für Integrationsdienstumgebungen

Hiermit wird Entwicklern das Erstellen und Aktualisieren von Workflows, Integrationskonten und API-Verbindungen in Integrationsdienstumgebungen ermöglicht. [Weitere Informationen](../logic-apps/add-artifacts-integration-service-environment-ise.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/read | Hiermit wird die Integrationsdienstumgebung gelesen. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/join/action | Hiermit erfolgt ein Beitritt zur Integrationsdienstumgebung. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows developers to create and update workflows, integration accounts and API connections in integration service environments.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c7aa55d3-1abb-444a-a5ca-5e51e485d6ec",
  "name": "c7aa55d3-1abb-444a-a5ca-5e51e485d6ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*",
        "Microsoft.Logic/integrationServiceEnvironments/read",
        "Microsoft.Logic/integrationServiceEnvironments/join/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Integration Service Environment Developer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="intelligent-systems-account-contributor"></a>Mitwirkender von Intelligent Systems-Konto

Ermöglicht Ihnen das Verwalten von Intelligent Systems-Konten, nicht aber den Zugriff darauf.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | Microsoft.IntelligentSystems/accounts/* | Erstellen und Verwalten von Intelligent Systems-Konten |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Intelligent Systems accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/03a6d094-3444-4b3d-88af-7477090a9e5e",
  "name": "03a6d094-3444-4b3d-88af-7477090a9e5e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.IntelligentSystems/accounts/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Intelligent Systems Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="logic-app-contributor"></a>Mitwirkender für Logik-Apps

Ermöglicht Ihnen die Verwaltung von Logik-Apps. Sie können aber nicht den App-Zugriff ändern. [Weitere Informationen](../logic-apps/logic-apps-securing-a-logic-app.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/action | Listet die Zugriffsschlüssel für die Speicherkonten auf. |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/read | Dient zum Zurückgeben des Speicherkontos mit dem angegebenen Konto. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Erstellt, aktualisiert oder liest die Diagnoseeinstellung für den Analysis-Server. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/logdefinitions/* | Diese Berechtigung ist für Benutzer notwendig, die über das Portal auf Aktivitätsprotokolle zugreifen müssen. Auflisten der Protokollkategorien im Aktivitätsprotokoll. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/* | Lesen von Metrikdefinitionen (Liste der verfügbaren Metriktypen für eine Ressource). |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/* | Verwaltet Logic Apps-Ressourcen. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Dient zum Abrufen der Ergebnisse des Abonnementvorgangs. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listkeys/action | Gibt die Zugriffsschlüssel für das angegebene Speicherkonto zurück. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Gibt die Liste mit Speicherkonten zurück oder ruft die Eigenschaften für das angegebene Speicherkonto ab. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/connectionGateways/* | Erstellt und verwaltet ein Verbindungsgateway. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/connections/* | Erstellt und verwaltet eine Verbindung. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/customApis/* | Erstellt und verwaltet eine benutzerdefinierte API. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/join/action | Hiermit wird der Beitritt zu einem App Service-Plan ausgeführt. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/read | Dient zum Abrufen der Eigenschaften für einen App Service-Plan. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/sites/functions/listSecrets/action | Auflisten von Funktionsgeheimnissen. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage logic app, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/87a39d53-fc1b-424a-814c-f7e04687dc9e",
  "name": "87a39d53-fc1b-424a-814c-f7e04687dc9e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.ClassicStorage/storageAccounts/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Insights/logdefinitions/*",
        "Microsoft.Insights/metricDefinitions/*",
        "Microsoft.Logic/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*",
        "Microsoft.Web/connectionGateways/*",
        "Microsoft.Web/connections/*",
        "Microsoft.Web/customApis/*",
        "Microsoft.Web/serverFarms/join/action",
        "Microsoft.Web/serverFarms/read",
        "Microsoft.Web/sites/functions/listSecrets/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Logic App Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="logic-app-operator"></a>Logik-App-Operator

Ermöglicht Ihnen das Lesen, Aktivieren und Deaktivieren von Logik-Apps. Sie können diese aber nicht bearbeiten oder aktualisieren. [Weitere Informationen](../logic-apps/logic-apps-securing-a-logic-app.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/*/read | Lesen von Insights-Warnungsregeln |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/*/read | Ruft die Diagnoseeinstellungen für Logic Apps ab. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/*/read | Ruft die verfügbaren Metriken für Logic Apps ab. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/*/read | Liest Logic Apps-Ressourcen. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/disable/action | Deaktiviert den Workflow. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/enable/action | Aktiviert den Workflow. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/validate/action | Überprüft den Workflow. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | Ruft Bereitstellungsvorgänge ab oder listet sie auf. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Dient zum Abrufen der Ergebnisse des Abonnementvorgangs. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/connectionGateways/*/read | Liest Verbindungsgateways. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/connections/*/read | Liest Verbindungen. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/customApis/*/read | Liest benutzerdefinierte API. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/read | Dient zum Abrufen der Eigenschaften für einen App Service-Plan. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read, enable and disable logic app.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/515c2055-d9d4-4321-b1b9-bd0c9a0f79fe",
  "name": "515c2055-d9d4-4321-b1b9-bd0c9a0f79fe",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*/read",
        "Microsoft.Insights/metricAlerts/*/read",
        "Microsoft.Insights/diagnosticSettings/*/read",
        "Microsoft.Insights/metricDefinitions/*/read",
        "Microsoft.Logic/*/read",
        "Microsoft.Logic/workflows/disable/action",
        "Microsoft.Logic/workflows/enable/action",
        "Microsoft.Logic/workflows/validate/action",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/connectionGateways/*/read",
        "Microsoft.Web/connections/*/read",
        "Microsoft.Web/customApis/*/read",
        "Microsoft.Web/serverFarms/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Logic App Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="identity"></a>Identity


### <a name="managed-identity-contributor"></a>Mitwirkender für verwaltete Identität

Dem Benutzer zugewiesene Identität erstellen, lesen, aktualisieren und löschen. [Weitere Informationen](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/read | Ruft eine vorhandene zugewiesene Benutzeridentität ab. |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/write | Erstellt eine neue zugewiesene Benutzeridentität oder aktualisiert die Markierungen, die einer vorhandenen zugewiesenen Benutzeridentität zugeordnet sind. |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/delete | Löscht eine vorhandene zugewiesene Benutzeridentität. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create, Read, Update, and Delete User Assigned Identity",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e40ec5ca-96e0-45a2-b4ff-59039f2c2b59",
  "name": "e40ec5ca-96e0-45a2-b4ff-59039f2c2b59",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedIdentity/userAssignedIdentities/read",
        "Microsoft.ManagedIdentity/userAssignedIdentities/write",
        "Microsoft.ManagedIdentity/userAssignedIdentities/delete",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Identity Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-identity-operator"></a>Operator für verwaltete Identität

Dem Benutzer zugewiesene Identität lesen und zuweisen. [Weitere Informationen](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/*/read |  |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/*/assign/action |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read and Assign User Assigned Identity",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f1a07417-d97a-45cb-824c-7a7467783830",
  "name": "f1a07417-d97a-45cb-824c-7a7467783830",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedIdentity/userAssignedIdentities/*/read",
        "Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Identity Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="security"></a>Sicherheit


### <a name="azure-sentinel-contributor"></a>Azure Sentinel-Mitwirkender

Azure Sentinel-Mitwirkender [Weitere Informationen](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/* |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | Führt eine Suche mit der neuen Engine aus. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Anzeigen von Log Analytics-Daten |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/* |  |
> | [Microsoft.OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/solutions/read | Dient zum Abrufen vorhandener OMS-Lösungen. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/read | Dient zum Ausführen von Abfragen für die Daten im Arbeitsbereich. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/*/read |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | Dient zum Abrufen von Datenquellen unter einem Arbeitsbereich. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/myworkbooks/read | Liest eine private Arbeitsmappe |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Contributor",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ab8e14d6-4a74-4a29-9ba8-549422addade",
  "name": "ab8e14d6-4a74-4a29-9ba8-549422addade",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/*",
        "Microsoft.Insights/myworkbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-reader"></a>Azure Sentinel-Leser

Azure Sentinel-Leser [Weitere Informationen](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/*/read |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/dataConnectorsCheckRequirements/action | Überprüft Benutzerautorisierung und -lizenz. |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/query/action | Abfragen von Threat Intelligence-Indikatoren |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/queryIndicators/action | Abfragen von Threat Intelligence-Indikatoren |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | Führt eine Suche mit der neuen Engine aus. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Anzeigen von Log Analytics-Daten |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/LinkedServices/read | Ruft verknüpfte Dienste im angegebenen Arbeitsbereich ab. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/read | Ruft eine gespeicherte Suchabfrage ab. |
> | [Microsoft.OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/solutions/read | Dient zum Abrufen vorhandener OMS-Lösungen. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/read | Dient zum Ausführen von Abfragen für die Daten im Arbeitsbereich. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/*/read |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | Dient zum Abrufen von Datenquellen unter einem Arbeitsbereich. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | Lesen einer Arbeitsmappe |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/myworkbooks/read | Liest eine private Arbeitsmappe |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Reader",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8d289c81-5878-46d4-8554-54e1e3d8b5cb",
  "name": "8d289c81-5878-46d4-8554-54e1e3d8b5cb",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*/read",
        "Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/query/action",
        "Microsoft.SecurityInsights/threatIntelligence/queryIndicators/action",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/LinkedServices/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Insights/myworkbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-responder"></a>Azure Sentinel-Antwortender

Azure Sentinel-Antwortender [Weitere Informationen](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/*/read |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/dataConnectorsCheckRequirements/action | Überprüft Benutzerautorisierung und -lizenz. |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/cases/* |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/incidents/* |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/appendTags/action | Anfügen von Tags an Threat Intelligence-Indikator |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/query/action | Abfragen von Threat Intelligence-Indikatoren |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/bulkTag/action | Kennzeichnet Informationen zu Bedrohungen in einem Massenvorgang. |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/appendTags/action | Anfügen von Tags an Threat Intelligence-Indikator |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/replaceTags/action | Ersetzen von Tags eines Threat Intelligence-Indikators |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/queryIndicators/action | Abfragen von Threat Intelligence-Indikatoren |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | Führt eine Suche mit der neuen Engine aus. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Anzeigen von Log Analytics-Daten |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | Dient zum Abrufen von Datenquellen unter einem Arbeitsbereich. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/read | Ruft eine gespeicherte Suchabfrage ab. |
> | [Microsoft.OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/solutions/read | Dient zum Abrufen vorhandener OMS-Lösungen. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/read | Dient zum Ausführen von Abfragen für die Daten im Arbeitsbereich. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/*/read |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | Dient zum Abrufen von Datenquellen unter einem Arbeitsbereich. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | Lesen einer Arbeitsmappe |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/myworkbooks/read | Liest eine private Arbeitsmappe |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/cases/*/Delete |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/incidents/*/Delete |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Responder",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3e150937-b8fe-4cfb-8069-0eaf05ecd056",
  "name": "3e150937-b8fe-4cfb-8069-0eaf05ecd056",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*/read",
        "Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action",
        "Microsoft.SecurityInsights/cases/*",
        "Microsoft.SecurityInsights/incidents/*",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/appendTags/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/query/action",
        "Microsoft.SecurityInsights/threatIntelligence/bulkTag/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/appendTags/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/replaceTags/action",
        "Microsoft.SecurityInsights/threatIntelligence/queryIndicators/action",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Insights/myworkbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.SecurityInsights/cases/*/Delete",
        "Microsoft.SecurityInsights/incidents/*/Delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Responder",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-administrator-preview"></a>Key Vault-Administrator (Vorschau)

Ausführen beliebiger Vorgänge auf Datenebene für einen Schlüsseltresor und alle darin enthaltenen Objekte (einschließlich Zertifikate, Schlüssel und Geheimnisse). Kann keine Key Vault-Ressourcen oder Rollenzuweisungen verwalten. Funktioniert nur für Schlüsseltresore, die das Berechtigungsmodell „Rollenbasierte Azure-Zugriffssteuerung“ verwenden.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Prüft, ob ein Schlüsseltresorname gültig ist und noch nicht verwendet wird. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | Dient zum Anzeigen der Eigenschaften vorläufig gelöschter Schlüsseltresore. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Listet die verfügbaren Vorgänge für den Microsoft.KeyVault-Ressourcenanbieter auf. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform all data plane operations on a key vault and all objects in it, including certificates, keys, and secrets. Cannot manage key vault resources or manage role assignments. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00482a5a-887f-4fb3-b363-3b7fe8e74483",
  "name": "00482a5a-887f-4fb3-b363-3b7fe8e74483",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Administrator (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-certificates-officer-preview"></a>Key Vault Certificates Officer (Vorschau)

Ausführen beliebiger Aktionen für die Zertifikate eines Schlüsseltresors mit Ausnahme der Verwaltung von Berechtigungen. Funktioniert nur für Schlüsseltresore, die das Berechtigungsmodell „Rollenbasierte Azure-Zugriffssteuerung“ verwenden.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Prüft, ob ein Schlüsseltresorname gültig ist und noch nicht verwendet wird. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | Dient zum Anzeigen der Eigenschaften vorläufig gelöschter Schlüsseltresore. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Listet die verfügbaren Vorgänge für den Microsoft.KeyVault-Ressourcenanbieter auf. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/certificatecas/* |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/certificates/* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform any action on the certificates of a key vault, except manage permissions. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a4417e6f-fecd-4de8-b567-7b0420556985",
  "name": "a4417e6f-fecd-4de8-b567-7b0420556985",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/certificatecas/*",
        "Microsoft.KeyVault/vaults/certificates/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Certificates Officer (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-contributor"></a>Key Vault-Mitwirkender

Verwalten von Schlüsseltresoren, gestattet Ihnen jedoch nicht, Rollen in Azure RBAC zuzuweisen, und ermöglicht keinen Zugriff auf Geheimnisse, Schlüssel oder Zertifikate. [Weitere Informationen](../key-vault/general/secure-your-key-vault.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/deletedVaults/purge/action | Dient zum endgültigen Löschen eines vorläufig gelöschten Schlüsseltresors. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/hsmPools/* |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/managedHsms/* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage key vaults, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f25e0fa2-a7c8-4377-a976-54943a77a395",
  "name": "f25e0fa2-a7c8-4377-a976-54943a77a395",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.KeyVault/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.KeyVault/locations/deletedVaults/purge/action",
        "Microsoft.KeyVault/hsmPools/*",
        "Microsoft.KeyVault/managedHsms/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-officer-preview"></a>Key Vault Crypto Officer (Vorschau)

Ausführen beliebiger Aktionen für die Schlüssel eines Schlüsseltresors mit Ausnahme der Verwaltung von Berechtigungen. Funktioniert nur für Schlüsseltresore, die das Berechtigungsmodell „Rollenbasierte Azure-Zugriffssteuerung“ verwenden.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Prüft, ob ein Schlüsseltresorname gültig ist und noch nicht verwendet wird. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | Dient zum Anzeigen der Eigenschaften vorläufig gelöschter Schlüsseltresore. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Listet die verfügbaren Vorgänge für den Microsoft.KeyVault-Ressourcenanbieter auf. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform any action on the keys of a key vault, except manage permissions. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/14b46e9e-c2b7-41b4-b07b-48a6ebf60603",
  "name": "14b46e9e-c2b7-41b4-b07b-48a6ebf60603",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto Officer (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-service-encryption-preview"></a>Key Vault Crypto Service Encryption (Vorschau)

Lesen von Metadaten von Schlüsseln und Ausführen von Vorgängen zum Packen/Entpacken. Funktioniert nur für Schlüsseltresore, die das Berechtigungsmodell „Rollenbasierte Azure-Zugriffssteuerung“ verwenden.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | *keine* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/read | Listet Schlüssel im angegebenen Tresor auf oder liest Eigenschaften und öffentliche Informationen eines Schlüssels. Bei asymmetrischen Schlüsseln legt dieser Vorgang den öffentlichen Schlüssel offen und beinhaltet die Fähigkeit, Algorithmen für öffentliche Schlüssel durchzuführen, z. B. Verschlüsselung und Überprüfung der Signatur. Private Schlüssel und symmetrische Schlüssel werden nie offengelegt. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/wrap/action | Umschließt einen symmetrischen Schlüssel mit einem Key Vault-Schlüssel. Hinweis: Wenn es sich um einen asymmetrischen Key Vault-Schlüssel handelt, kann dieser Vorgang mit einem Prinzipal mit Lesezugriff durchgeführt werden. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/unwrap/action | Hebt die Umschließung eines symmetrischen Schlüssels mit einem Key Vault-Schlüssel auf. |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read metadata of keys and perform wrap/unwrap operations. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e147488a-f6f5-4113-8e2d-b22465e65bf6",
  "name": "e147488a-f6f5-4113-8e2d-b22465e65bf6",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/read",
        "Microsoft.KeyVault/vaults/keys/wrap/action",
        "Microsoft.KeyVault/vaults/keys/unwrap/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto Service Encryption (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-user-preview"></a>Key Vault Crypto-Benutzer (Vorschau)

Ausführen kryptografischer Vorgänge mithilfe von Schlüsseln. Funktioniert nur für Schlüsseltresore, die das Berechtigungsmodell „Rollenbasierte Azure-Zugriffssteuerung“ verwenden.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | *keine* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/read | Listet Schlüssel im angegebenen Tresor auf oder liest Eigenschaften und öffentliche Informationen eines Schlüssels. Bei asymmetrischen Schlüsseln legt dieser Vorgang den öffentlichen Schlüssel offen und beinhaltet die Fähigkeit, Algorithmen für öffentliche Schlüssel durchzuführen, z. B. Verschlüsselung und Überprüfung der Signatur. Private Schlüssel und symmetrische Schlüssel werden nie offengelegt. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/update/action | Aktualisiert die angegebenen Attribute, die dem jeweiligen Schlüssel zugeordnet sind. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/backup/action | Erstellt die Sicherungsdatei eines Schlüssels. Die Datei kann verwendet werden, um den Schlüssel in einem Key Vault desselben Abonnements wiederherzustellen. Es können Einschränkungen gelten. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/encrypt/action | Verschlüsselt Klartext mit einem Schlüssel. Hinweis: Wenn es sich um einen asymmetrischen Schlüssel handelt, kann dieser Vorgang mit einem Prinzipal mit Lesezugriff durchgeführt werden. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/decrypt/action | Entschlüsselt Chiffretext mit einem Schlüssel. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/wrap/action | Umschließt einen symmetrischen Schlüssel mit einem Key Vault-Schlüssel. Hinweis: Wenn es sich um einen asymmetrischen Key Vault-Schlüssel handelt, kann dieser Vorgang mit einem Prinzipal mit Lesezugriff durchgeführt werden. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/unwrap/action | Hebt die Umschließung eines symmetrischen Schlüssels mit einem Key Vault-Schlüssel auf. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/sign/action | Signiert einen Nachrichtenhash (Hash) mit einem Schlüssel. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/verify/action | Überprüft die Signatur eines Nachrichtenhashs (Hash) mit einem Schlüssel. Hinweis: Wenn es sich um einen asymmetrischen Schlüssel handelt, kann dieser Vorgang mit einem Prinzipal mit Lesezugriff durchgeführt werden. |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform cryptographic operations using keys. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/12338af0-0e69-4776-bea7-57ae8d297424",
  "name": "12338af0-0e69-4776-bea7-57ae8d297424",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/read",
        "Microsoft.KeyVault/vaults/keys/update/action",
        "Microsoft.KeyVault/vaults/keys/backup/action",
        "Microsoft.KeyVault/vaults/keys/encrypt/action",
        "Microsoft.KeyVault/vaults/keys/decrypt/action",
        "Microsoft.KeyVault/vaults/keys/wrap/action",
        "Microsoft.KeyVault/vaults/keys/unwrap/action",
        "Microsoft.KeyVault/vaults/keys/sign/action",
        "Microsoft.KeyVault/vaults/keys/verify/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto User (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-reader-preview"></a>Key Vault Reader (Vorschau)

Lesen von Metadaten von Schlüsseltresoren und deren Zertifikaten, Schlüsseln und Geheimnissen. Sensible Werte, z. B. der Inhalt von Geheimnissen oder Schlüsselmaterial, können nicht gelesen werden. Funktioniert nur für Schlüsseltresore, die das Berechtigungsmodell „Rollenbasierte Azure-Zugriffssteuerung“ verwenden.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Prüft, ob ein Schlüsseltresorname gültig ist und noch nicht verwendet wird. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | Dient zum Anzeigen der Eigenschaften vorläufig gelöschter Schlüsseltresore. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Listet die verfügbaren Vorgänge für den Microsoft.KeyVault-Ressourcenanbieter auf. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/readMetadata/action | Listet die Eigenschaften eines Geheimnisses auf oder zeigt sie an, nicht aber dessen Wert. |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read metadata of key vaults and its certificates, keys, and secrets. Cannot read sensitive values such as secret contents or key material. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/21090545-7ca7-4776-b22c-e363652d74d2",
  "name": "21090545-7ca7-4776-b22c-e363652d74d2",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/vaults/secrets/readMetadata/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Reader (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-secrets-officer-preview"></a>Key Vault Secrets Officer (Vorschau)

Ausführen beliebiger Aktionen für die Geheimnisse eines Schlüsseltresors mit Ausnahme der Verwaltung von Berechtigungen. Funktioniert nur für Schlüsseltresore, die das Berechtigungsmodell „Rollenbasierte Azure-Zugriffssteuerung“ verwenden.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Prüft, ob ein Schlüsseltresorname gültig ist und noch nicht verwendet wird. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | Dient zum Anzeigen der Eigenschaften vorläufig gelöschter Schlüsseltresore. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Listet die verfügbaren Vorgänge für den Microsoft.KeyVault-Ressourcenanbieter auf. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform any action on the secrets of a key vault, except manage permissions. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b86a8fe4-44ce-4948-aee5-eccb2c155cd7",
  "name": "b86a8fe4-44ce-4948-aee5-eccb2c155cd7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/secrets/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Secrets Officer (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-secrets-user-preview"></a>Key Vault Secrets-Benutzer (Vorschau)

Lesen der Inhalte von Geheimnissen. Funktioniert nur für Schlüsseltresore, die das Berechtigungsmodell „Rollenbasierte Azure-Zugriffssteuerung“ verwenden.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | *keine* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/getSecret/action | Ruft den Wert eines Geheimnisses ab. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/readMetadata/action | Listet die Eigenschaften eines Geheimnisses auf oder zeigt sie an, nicht aber dessen Wert. |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read secret contents. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4633458b-17de-408a-b874-0445c86b69e6",
  "name": "4633458b-17de-408a-b874-0445c86b69e6",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/secrets/getSecret/action",
        "Microsoft.KeyVault/vaults/secrets/readMetadata/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Secrets User (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-admin"></a>Sicherheitsadministrator

Anzeigen und Aktualisieren von Berechtigungen für Security Center. Gleiche Rechte wie der Sicherheitsleseberechtigte und kann darüber hinaus die Sicherheitsrichtlinie aktualisieren sowie Warnungen und Empfehlungen verwerfen. [Weitere Informationen](../security-center/security-center-permissions.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyAssignments/* | Erstellen und Verwalten von Richtlinienzuweisungen |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyDefinitions/* | Erstellen und Verwalten von Richtliniendefinitionen |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyExemptions/* |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policySetDefinitions/* | Erstellen und Verwalten von Richtliniensätzen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Listet die Verwaltungsgruppen für den authentifizierten Benutzer auf. |
> | [Microsoft.operationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Anzeigen von Log Analytics-Daten |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/* | Erstellen und Verwalten von Sicherheitskomponenten und -richtlinien |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Security Admin Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fb1c8493-542b-48eb-b624-b4c8fea62acd",
  "name": "fb1c8493-542b-48eb-b624-b4c8fea62acd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Authorization/policyAssignments/*",
        "Microsoft.Authorization/policyDefinitions/*",
        "Microsoft.Authorization/policyExemptions/*",
        "Microsoft.Authorization/policySetDefinitions/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-assessment-contributor"></a>Mitwirkender für Sicherheitsbewertungen

Ermöglicht das Pushen von Bewertungen an Security Center

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/assessments/write | Erstellen oder Aktualisieren von Sicherheitsbewertungen für Ihr Abonnement |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you push assessments to Security Center",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/612c2aa1-cb24-443b-ac28-3ab7272de6f5",
  "name": "612c2aa1-cb24-443b-ac28-3ab7272de6f5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Security/assessments/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Assessment Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-manager-legacy"></a>Sicherheits-Manager (Legacy)

Dies ist eine Legacyrolle. Verwenden Sie stattdessen „Sicherheitsadministrator“.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/*/read | Lesen von Konfigurationsinformationen zu klassischen virtuellen Computern |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/*/write | Schreiben der Konfiguration für klassische virtuelle Computer |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/*/read | Lesen von Konfigurationsinformationen zu klassischem Netzwerk |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/* | Erstellen und Verwalten von Sicherheitskomponenten und -richtlinien |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "This is a legacy role. Please use Security Administrator instead",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e3d13bf0-dd5a-482e-ba6b-9b8433878d10",
  "name": "e3d13bf0-dd5a-482e-ba6b-9b8433878d10",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicCompute/*/read",
        "Microsoft.ClassicCompute/virtualMachines/*/write",
        "Microsoft.ClassicNetwork/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Manager (Legacy)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-reader"></a>Sicherheitsleseberechtigter

Anzeigen von Berechtigungen für Security Center. Kann Empfehlungen, Warnungen, Sicherheitsrichtlinien und Sicherheitszustände anzeigen, jedoch keine Änderungen vornehmen. [Weitere Informationen](../security-center/security-center-permissions.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | Liest eine klassische Metrikwarnung. |
> | [Microsoft.operationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Anzeigen von Log Analytics-Daten |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/*/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/*/read | Lesen von Sicherheitskomponenten und -richtlinien |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/*/read |  |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Listet die Verwaltungsgruppen für den authentifizierten Benutzer auf. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Security Reader Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/39bc4728-0917-49c7-9d2c-d95423bc2eb4",
  "name": "39bc4728-0917-49c7-9d2c-d95423bc2eb4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*/read",
        "Microsoft.Support/*/read",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="devops"></a>DevOps


### <a name="devtest-labs-user"></a>DevTest Labs-Benutzer

Ermöglicht Ihnen das Verbinden, Starten, Neustarten und Herunterfahren Ihrer virtuellen Computer in Ihren Azure DevTest Labs. [Weitere Informationen](../devtest-labs/devtest-lab-add-devtest-user.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/availabilitySets/read | Dient zum Abrufen der Eigenschaften einer Verfügbarkeitsgruppe. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/read | Lesen der Eigenschaften eines virtuellen Computers (VM-Größen, Laufzeitstatus, VM-Erweiterungen usw.) |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/deallocate/action | Schaltet den virtuellen Computer aus und gibt die Computeressourcen frei. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/read | Dient zum Abrufen der Eigenschaften eines virtuellen Computers. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/restart/action | Startet den virtuellen Computer neu. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/start/action | Startet den virtuellen Computer. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/*/read | Lesen der Eigenschaften eines Labs |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/claimAnyVm/action | Dient zum Anfordern eines beliebigen anforderbaren virtuellen Computers im Lab. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/createEnvironment/action | Dient zum Erstellen von virtuellen Computern in einem Lab. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/ensureCurrentUserProfile/action | Stellt sicher, dass der aktuelle Benutzer über ein gültiges Profil im Lab verfügt |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/formulas/delete | Dient zum Löschen von Formeln. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/formulas/read | Dient zum Lesen von Formeln. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/formulas/write | Dient zum Hinzufügen oder Ändern von Formeln. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/policySets/evaluatePolicies/action | Wertet Labrichtlinien aus. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/virtualMachines/claim/action | Dient dazu, einen vorhandenen virtuellen Computer in Besitz zu nehmen. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/virtualmachines/listApplicableSchedules/action | Listet die anwendbaren Zeitpläne zum Starten/Beenden auf, sofern vorhanden. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/virtualMachines/getRdpFileContents/action | Ruft eine Zeichenfolge ab, die den Inhalt der RDP-Datei für den virtuellen Computer darstellt. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/backendAddressPools/join/action | Verknüpft einen Back-End-Adresspool für den Lastenausgleich. Nicht warnbar. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatRules/join/action | Verknüpft eine NAT-Regel für eingehenden Datenverkehr für den Lastenausgleich. Nicht warnbar. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/*/read | Lesen der Eigenschaften einer Netzwerkschnittstelle (z.B. alle Load Balancer, zu denen die Netzwerkschnittstelle gehört) |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/join/action | Verknüpft einen virtuellen Computer mit einer Netzwerkschnittstelle. Nicht warnbar. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | Ruft eine Netzwerkschnittstellendefinition ab.  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/write | Erstellt eine Netzwerkschnittstelle oder aktualisiert eine vorhandene Netzwerkschnittstelle.  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/*/read | Lesen der Eigenschaften einer öffentlichen IP-Adresse |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/join/action | Verknüpft eine öffentliche IP-Adresse. Nicht warnbar. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | Ruft eine Definition für eine öffentliche IP-Adresse ab. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | Verknüpft ein virtuelles Netzwerk. Nicht warnbar. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | Ruft Bereitstellungsvorgänge ab oder listet sie auf. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/read | Ruft Bereitstellungen ab oder listet sie auf. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | Gibt die Zugriffsschlüssel für das angegebene Speicherkonto zurück. |
> | **NotActions** |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/vmSizes/read | Listet die verfügbaren Größen auf, auf die der virtuelle Computer aktualisiert werden kann. |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you connect, start, restart, and shutdown your virtual machines in your Azure DevTest Labs.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/76283e04-6283-4c54-8f91-bcf1374a3c64",
  "name": "76283e04-6283-4c54-8f91-bcf1374a3c64",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/availabilitySets/read",
        "Microsoft.Compute/virtualMachines/*/read",
        "Microsoft.Compute/virtualMachines/deallocate/action",
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Compute/virtualMachines/restart/action",
        "Microsoft.Compute/virtualMachines/start/action",
        "Microsoft.DevTestLab/*/read",
        "Microsoft.DevTestLab/labs/claimAnyVm/action",
        "Microsoft.DevTestLab/labs/createEnvironment/action",
        "Microsoft.DevTestLab/labs/ensureCurrentUserProfile/action",
        "Microsoft.DevTestLab/labs/formulas/delete",
        "Microsoft.DevTestLab/labs/formulas/read",
        "Microsoft.DevTestLab/labs/formulas/write",
        "Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action",
        "Microsoft.DevTestLab/labs/virtualMachines/claim/action",
        "Microsoft.DevTestLab/labs/virtualmachines/listApplicableSchedules/action",
        "Microsoft.DevTestLab/labs/virtualMachines/getRdpFileContents/action",
        "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
        "Microsoft.Network/networkInterfaces/*/read",
        "Microsoft.Network/networkInterfaces/join/action",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/publicIPAddresses/*/read",
        "Microsoft.Network/publicIPAddresses/join/action",
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/listKeys/action"
      ],
      "notActions": [
        "Microsoft.Compute/virtualMachines/vmSizes/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DevTest Labs User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="lab-creator"></a>Lab-Ersteller

Ermöglicht Ihnen das Erstellen neuer Labs unter ihren Azure Lab-Konten. [Weitere Informationen](../lab-services/add-lab-creator.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/*/read |  |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/createLab/action | Hiermit wird ein Lab in einem Lab-Konto erstellt. |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/getPricingAndAvailability/action | Ruft die Preise und die Verfügbarkeit von Kombinationen aus Größen, Regionen und Betriebssystemen für das Labkonto ab. |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/getRestrictionsAndUsage/action | Ruft Kerneinschränkungen und Nutzungsdaten für dieses Abonnement ab |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create new labs under your Azure Lab Accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "name": "b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.LabServices/labAccounts/*/read",
        "Microsoft.LabServices/labAccounts/createLab/action",
        "Microsoft.LabServices/labAccounts/getPricingAndAvailability/action",
        "Microsoft.LabServices/labAccounts/getRestrictionsAndUsage/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Lab Creator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="monitor"></a>Überwachen


### <a name="application-insights-component-contributor"></a>Mitwirkender der Application Insights-Komponente

Kann Application Insights-Komponenten verwalten [Weitere Informationen](../azure-monitor/app/resources-roles-access-control.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten von klassischen Netzwerkregeln |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/* | Erstellen und Verwalten von neuen Netzwerkregeln |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/* | Erstellen und Verwalten von Insights-Komponenten |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/webtests/* | Erstellen und Verwalten von Insights-Webtests |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage Application Insights components",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e",
  "name": "ae349356-3a1b-4a5e-921d-050484c6347e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/webtests/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Application Insights Component Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="application-insights-snapshot-debugger"></a>Application Insights-Momentaufnahmedebugger

Gibt dem Benutzer die Berechtigung zum Anzeigen und Herunterladen von Debugmomentaufnahmen, die mit dem Application Insights-Momentaufnahmedebugger erfasst wurden. Beachten Sie, dass diese Berechtigungen in der Rolle [Besitzer](#owner) oder [Mitwirkender](#contributor) nicht enthalten sind. Die Application Insights-Rolle „Momentaufnahmedebugger“ muss Benutzern direkt zugewiesen werden. Die Rolle wird nicht erkannt, wenn sie einer benutzerdefinierten Rolle hinzugefügt wird. [Weitere Informationen](../azure-monitor/app/snapshot-debugger.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/*/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives user permission to use Application Insights Snapshot Debugger features",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/08954f03-6346-4c2e-81c0-ec3a5cfae23b",
  "name": "08954f03-6346-4c2e-81c0-ec3a5cfae23b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/components/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Application Insights Snapshot Debugger",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-contributor"></a>Überwachungsmitwirkender

Kann sämtliche Überwachungsdaten lesen und Überwachungseinstellungen bearbeiten. Siehe auch [Erste Schritte mit Rollen, Berechtigungen und Sicherheit in Azure Monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). [Weitere Informationen](../azure-monitor/platform/roles-permissions-security.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | */Lesen | Lesen von Ressourcen aller Typen mit Ausnahme geheimer Schlüssel |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/alerts/* |  |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/alertsSummary/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/actiongroups/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/activityLogAlerts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/AlertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/* | Erstellen und Verwalten von Insights-Komponenten |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/dataCollectionRules/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/dataCollectionRuleAssociations/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/DiagnosticSettings/* | Erstellt, aktualisiert oder liest die Diagnoseeinstellung für den Analysis-Server. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/eventtypes/* | Auflisten von Aktivitätsprotokollereignissen (Verwaltungsereignissen) in einem Abonnement. Diese Berechtigung gilt sowohl für den programmgesteuerten als auch für den Portalzugriff auf das Aktivitätsprotokoll. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/LogDefinitions/* | Diese Berechtigung ist für Benutzer notwendig, die über das Portal auf Aktivitätsprotokolle zugreifen müssen. Auflisten der Protokollkategorien im Aktivitätsprotokoll. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricalerts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/* | Lesen von Metrikdefinitionen (Liste der verfügbaren Metriktypen für eine Ressource). |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Metrics/* | Lesen von Metriken für eine Ressource. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Register/Action | Dient zum Registrieren des Microsoft Insights-Anbieters. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/scheduledqueryrules/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/webtests/* | Erstellen und Verwalten von Insights-Webtests |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/privateLinkScopes/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/privateLinkScopeOperationStatuses/* |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/write | Erstellt einen neuen Arbeitsbereich oder stellt eine Verknüpfung mit einem vorhandenen Arbeitsbereich her, indem die Kunden-ID für den vorhandenen Arbeitsbereich bereitgestellt wird. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/intelligencepacks/* | Lesen/Schreiben/Löschen von Log Analytics-Lösungspaketen. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/* | Lesen/Schreiben/Löschen von gespeicherten Log Analytics-Suchvorgängen. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/search/action | Führt eine Suchabfrage aus. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/sharedKeys/action | Ruft die gemeinsam verwendeten Schlüssel für den Arbeitsbereich ab. Diese Schlüssel werden verwendet, um Microsoft Operational Insights-Agents mit dem Arbeitsbereich zu verbinden. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/storageinsightconfigs/* | Lesen/Schreiben/Löschen von Log Analytics-Speicherdetailinformationen. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | [Microsoft.WorkloadMonitor](resource-provider-operations.md#microsoftworkloadmonitor)/monitors/* | Erhalten Sie Informationen zu Integritätsmonitoren für Gast-VMs. |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/smartDetectorAlertRules/* |  |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/actionRules/* |  |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/smartGroups/* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read all monitoring data and update monitoring settings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/749f88d5-cbae-40b8-bcfc-e573ddc772fa",
  "name": "749f88d5-cbae-40b8-bcfc-e573ddc772fa",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.AlertsManagement/alerts/*",
        "Microsoft.AlertsManagement/alertsSummary/*",
        "Microsoft.Insights/actiongroups/*",
        "Microsoft.Insights/activityLogAlerts/*",
        "Microsoft.Insights/AlertRules/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/dataCollectionRules/*",
        "Microsoft.Insights/dataCollectionRuleAssociations/*",
        "Microsoft.Insights/DiagnosticSettings/*",
        "Microsoft.Insights/eventtypes/*",
        "Microsoft.Insights/LogDefinitions/*",
        "Microsoft.Insights/metricalerts/*",
        "Microsoft.Insights/MetricDefinitions/*",
        "Microsoft.Insights/Metrics/*",
        "Microsoft.Insights/Register/Action",
        "Microsoft.Insights/scheduledqueryrules/*",
        "Microsoft.Insights/webtests/*",
        "Microsoft.Insights/workbooks/*",
        "Microsoft.Insights/privateLinkScopes/*",
        "Microsoft.Insights/privateLinkScopeOperationStatuses/*",
        "Microsoft.OperationalInsights/workspaces/write",
        "Microsoft.OperationalInsights/workspaces/intelligencepacks/*",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.OperationalInsights/workspaces/sharedKeys/action",
        "Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*",
        "Microsoft.Support/*",
        "Microsoft.WorkloadMonitor/monitors/*",
        "Microsoft.AlertsManagement/smartDetectorAlertRules/*",
        "Microsoft.AlertsManagement/actionRules/*",
        "Microsoft.AlertsManagement/smartGroups/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-metrics-publisher"></a>Herausgeber von Überwachungsmetriken

Ermöglicht die Veröffentlichung von Metriken für Azure-Ressourcen. [Weitere Informationen](../azure-monitor/insights/container-insights-update-metrics.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Register/Action | Dient zum Registrieren des Microsoft Insights-Anbieters. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Write | Hiermit werden Metriken geschrieben. |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Enables publishing metrics against Azure resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c390055eb",
  "name": "3913510d-42f4-4e42-8a64-420c390055eb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/Register/Action",
        "Microsoft.Support/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Insights/Metrics/Write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Metrics Publisher",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-reader"></a>Überwachungsleser

Kann alle Überwachungsdaten (Metriken, Protokolle usw.) lesen. Siehe auch [Erste Schritte mit Rollen, Berechtigungen und Sicherheit in Azure Monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). [Weitere Informationen](../azure-monitor/platform/roles-permissions-security.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | */Lesen | Lesen von Ressourcen aller Typen mit Ausnahme geheimer Schlüssel |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/search/action | Führt eine Suchabfrage aus. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read all monitoring data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/43d0d8ad-25c7-4714-9337-8ba259a9fe05",
  "name": "43d0d8ad-25c7-4714-9337-8ba259a9fe05",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="workbook-contributor"></a>Arbeitsmappenmitwirkender

Kann freigegebene Arbeitsmappen speichern. [Weitere Informationen](../sentinel/tutorial-monitor-your-data.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/write | Erstellen oder Aktualisieren einer Arbeitsmappe |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/delete | Löschen einer Arbeitsmappe |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | Lesen einer Arbeitsmappe |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can save shared workbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e8ddcd69-c73f-4f9f-9844-4100522f16ad",
  "name": "e8ddcd69-c73f-4f9f-9844-4100522f16ad",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/workbooks/write",
        "Microsoft.Insights/workbooks/delete",
        "Microsoft.Insights/workbooks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Workbook Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="workbook-reader"></a>Arbeitsmappenleser

Kann Arbeitsmappen lesen. [Weitere Informationen](../sentinel/tutorial-monitor-your-data.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [microsoft.insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | Lesen einer Arbeitsmappe |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read workbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b279062a-9be3-42a0-92ae-8b3cf002ec4d",
  "name": "b279062a-9be3-42a0-92ae-8b3cf002ec4d",
  "permissions": [
    {
      "actions": [
        "microsoft.insights/workbooks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Workbook Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="management--governance"></a>Verwaltung + Governance


### <a name="automation-job-operator"></a>Automation-Auftragsoperator

Hiermit werden Aufträge mithilfe von Automation-Runbooks erstellt und verwaltet. [Weitere Informationen](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/hybridRunbookWorkerGroups/read | Liest Hybrid Runbook Worker-Ressourcen. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/read | Ruft einen Azure Automation-Auftrag ab. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/resume/action | Setzt einen Azure Automation-Auftrag fort. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/stop/action | Beendet einen Azure Automation-Auftrag. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/streams/read | Ruft einen Azure Automation-Auftragsdatenstrom ab. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/suspend/action | Hält einen Azure Automation-Auftrag an. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/write | Erstellt einen Azure Automation-Auftrag. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/output/read | Ruft die Ausgabe eines Auftrags ab. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create and Manage Jobs using Automation Runbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4fe576fe-1146-4730-92eb-48519fa6bf9f",
  "name": "4fe576fe-1146-4730-92eb-48519fa6bf9f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read",
        "Microsoft.Automation/automationAccounts/jobs/read",
        "Microsoft.Automation/automationAccounts/jobs/resume/action",
        "Microsoft.Automation/automationAccounts/jobs/stop/action",
        "Microsoft.Automation/automationAccounts/jobs/streams/read",
        "Microsoft.Automation/automationAccounts/jobs/suspend/action",
        "Microsoft.Automation/automationAccounts/jobs/write",
        "Microsoft.Automation/automationAccounts/jobs/output/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Job Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-operator"></a>Operator für Automation

Automatisierungsoperatoren können Aufträge starten, beenden, anhalten und fortsetzen. [Weitere Informationen](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/hybridRunbookWorkerGroups/read | Liest Hybrid Runbook Worker-Ressourcen. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/read | Ruft einen Azure Automation-Auftrag ab. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/resume/action | Setzt einen Azure Automation-Auftrag fort. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/stop/action | Beendet einen Azure Automation-Auftrag. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/streams/read | Ruft einen Azure Automation-Auftragsdatenstrom ab. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/suspend/action | Hält einen Azure Automation-Auftrag an. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/write | Erstellt einen Azure Automation-Auftrag. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobSchedules/read | Ruft einen Azure Automation-Auftragszeitplan ab. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobSchedules/write | Erstellt einen Azure Automation-Auftragszeitplan. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/linkedWorkspace/read | Ruft den Arbeitsbereich ab, der mit dem Automation-Konto verknüpft ist. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/read | Ruft ein Azure Automation-Konto ab. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/runbooks/read | Ruft ein Azure Automation-Runbook ab. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/schedules/read | Ruft ein Azure Automation-Zeitplanasset ab. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/schedules/write | Erstellt oder aktualisiert ein Azure Automation-Zeitplanasset. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/output/read | Ruft die Ausgabe eines Auftrags ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Automation Operators are able to start, stop, suspend, and resume jobs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d3881f73-407a-4167-8283-e981cbba0404",
  "name": "d3881f73-407a-4167-8283-e981cbba0404",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read",
        "Microsoft.Automation/automationAccounts/jobs/read",
        "Microsoft.Automation/automationAccounts/jobs/resume/action",
        "Microsoft.Automation/automationAccounts/jobs/stop/action",
        "Microsoft.Automation/automationAccounts/jobs/streams/read",
        "Microsoft.Automation/automationAccounts/jobs/suspend/action",
        "Microsoft.Automation/automationAccounts/jobs/write",
        "Microsoft.Automation/automationAccounts/jobSchedules/read",
        "Microsoft.Automation/automationAccounts/jobSchedules/write",
        "Microsoft.Automation/automationAccounts/linkedWorkspace/read",
        "Microsoft.Automation/automationAccounts/read",
        "Microsoft.Automation/automationAccounts/runbooks/read",
        "Microsoft.Automation/automationAccounts/schedules/read",
        "Microsoft.Automation/automationAccounts/schedules/write",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Automation/automationAccounts/jobs/output/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-runbook-operator"></a>Automation-Runbookoperator

Runbookeigenschaften lesen: Ermöglicht das Erstellen von Runbookaufträgen. [Weitere Informationen](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/runbooks/read | Ruft ein Azure Automation-Runbook ab. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read Runbook properties - to be able to create Jobs of the runbook.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5fb5aef8-1081-4b8e-bb16-9d5d0385bab5",
  "name": "5fb5aef8-1081-4b8e-bb16-9d5d0385bab5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/runbooks/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Runbook Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-connected-machine-onboarding"></a>Onboarding von Azure Connected Machine

Kann Azure Connected Machine integrieren. [Weitere Informationen](../azure-arc/servers/onboard-service-principal.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/read | Liest einen beliebigen Azure Arc-Computer. |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/write | Schreibt auf einem Azure Arc-Computer |
> | [Microsoft.GuestConfiguration](resource-provider-operations.md#microsoftguestconfiguration)/guestConfigurationAssignments/read | Abrufen der Zuweisung der Gastkonfiguration |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can onboard Azure Connected Machines.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b64e21ea-ac4e-4cdf-9dc9-5b892992bee7",
  "name": "b64e21ea-ac4e-4cdf-9dc9-5b892992bee7",
  "permissions": [
    {
      "actions": [
        "Microsoft.HybridCompute/machines/read",
        "Microsoft.HybridCompute/machines/write",
        "Microsoft.GuestConfiguration/guestConfigurationAssignments/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected Machine Onboarding",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-connected-machine-resource-administrator"></a>Ressourcenadministrator für Azure Connected Machine

Kann Azure Connected Machines lesen, schreiben, löschen und erneut integrieren.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/read | Liest einen beliebigen Azure Arc-Computer. |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/write | Schreibt auf einem Azure Arc-Computer |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/delete | Löscht einen Azure Arc-Computer |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/reconnect/action |  |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/extensions/write | Installiert oder aktualisiert eine Azure Arc-Erweiterung |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/*/read |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read, write, delete and re-onboard Azure Connected Machines.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cd570a14-e51a-42ad-bac8-bafd67325302",
  "name": "cd570a14-e51a-42ad-bac8-bafd67325302",
  "permissions": [
    {
      "actions": [
        "Microsoft.HybridCompute/machines/read",
        "Microsoft.HybridCompute/machines/write",
        "Microsoft.HybridCompute/machines/delete",
        "Microsoft.HybridCompute/machines/reconnect/action",
        "Microsoft.HybridCompute/machines/extensions/write",
        "Microsoft.HybridCompute/*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected Machine Resource Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="billing-reader"></a>Abrechnungsleser

Hiermit wird Lesezugriff auf Abrechnungsdaten ermöglicht. [Weitere Informationen](../cost-management-billing/manage/manage-billing-access.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/*/read | Lesen von Abrechnungsinformationen |
> | [Microsoft.Commerce](resource-provider-operations.md#microsoftcommerce)/*/read |  |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/*/read |  |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Listet die Verwaltungsgruppen für den authentifizierten Benutzer auf. |
> | [Microsoft.CostManagement](resource-provider-operations.md#microsoftcostmanagement)/*/read |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to billing data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64",
  "name": "fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Billing/*/read",
        "Microsoft.Commerce/*/read",
        "Microsoft.Consumption/*/read",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.CostManagement/*/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Billing Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="blueprint-contributor"></a>Blueprint-Mitwirkender

Kann Blaupausendefinitionen verwalten, aber nicht zuweisen. [Weitere Informationen](../governance/blueprints/overview.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprints/* | Erstellen und Verwalten von Blaupausendefinitionen oder -artefakten |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage blueprint definitions, but not assign them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/41077137-e803-4205-871c-5a86e6a753b4",
  "name": "41077137-e803-4205-871c-5a86e6a753b4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Blueprint/blueprints/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Blueprint Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="blueprint-operator"></a>Blueprint-Operator

Kann vorhandene veröffentlichte Blaupausen zuweisen, aber keine neuen Blaupausen erstellen. Beachten Sie, dass dies nur funktioniert, wenn die Zuweisung mit einer vom Benutzer zugewiesenen verwalteten Identität erfolgt. [Weitere Informationen](../governance/blueprints/overview.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/* | Erstellen und Verwalten von Blaupausenzuweisungen |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can assign existing published blueprints, but cannot create new blueprints. NOTE: this only works if the assignment is done with a user-assigned managed identity.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/437d2ced-4a38-4302-8479-ed2bcb43d090",
  "name": "437d2ced-4a38-4302-8479-ed2bcb43d090",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Blueprint/blueprintAssignments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Blueprint Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cost-management-contributor"></a>Mitwirkender für Cost Management

Ermöglicht Ihnen das Anzeigen der Kosten und das Verwalten der Kostenkonfiguration (z. B. Budgets, Exporte). [Weitere Informationen](../cost-management-billing/costs/understand-work-scopes.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/* |  |
> | [Microsoft.CostManagement](resource-provider-operations.md#microsoftcostmanagement)/* |  |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/billingPeriods/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Ruft die Abonnementliste ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | [Microsoft.Advisor](resource-provider-operations.md#microsoftadvisor)/configurations/read | Konfigurationen abrufen |
> | [Microsoft.Advisor](resource-provider-operations.md#microsoftadvisor)/recommendations/read | Liest Empfehlungen. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Listet die Verwaltungsgruppen für den authentifizierten Benutzer auf. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view costs and manage cost configuration (e.g. budgets, exports)",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/434105ed-43f6-45c7-a02f-909b2ba83430",
  "name": "434105ed-43f6-45c7-a02f-909b2ba83430",
  "permissions": [
    {
      "actions": [
        "Microsoft.Consumption/*",
        "Microsoft.CostManagement/*",
        "Microsoft.Billing/billingPeriods/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Advisor/configurations/read",
        "Microsoft.Advisor/recommendations/read",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cost Management Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cost-management-reader"></a>Cost Management-Leser

Ermöglicht Ihnen das Anzeigen der Kostendaten und -konfiguration (z. B. Budgets, Exporte). [Weitere Informationen](../cost-management-billing/costs/understand-work-scopes.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/*/read |  |
> | [Microsoft.CostManagement](resource-provider-operations.md#microsoftcostmanagement)/*/read |  |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/billingPeriods/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Ruft die Abonnementliste ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | [Microsoft.Advisor](resource-provider-operations.md#microsoftadvisor)/configurations/read | Konfigurationen abrufen |
> | [Microsoft.Advisor](resource-provider-operations.md#microsoftadvisor)/recommendations/read | Liest Empfehlungen. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Listet die Verwaltungsgruppen für den authentifizierten Benutzer auf. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view cost data and configuration (e.g. budgets, exports)",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/72fafb9e-0641-4937-9268-a91bfd8191a3",
  "name": "72fafb9e-0641-4937-9268-a91bfd8191a3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Consumption/*/read",
        "Microsoft.CostManagement/*/read",
        "Microsoft.Billing/billingPeriods/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Advisor/configurations/read",
        "Microsoft.Advisor/recommendations/read",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cost Management Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hierarchy-settings-administrator"></a>Hierarchieeinstellungsadministrator

Ermöglicht Benutzern das Bearbeiten und Löschen von Hierarchieeinstellungen.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/settings/write | Erstellt oder aktualisiert Hierarchieeinstellungen einer Verwaltungsgruppe. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/settings/delete | Löscht Hierarchieeinstellungen einer Verwaltungsgruppe. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows users to edit and delete Hierarchy Settings",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/350f8d15-c687-4448-8ae1-157740a3936d",
  "name": "350f8d15-c687-4448-8ae1-157740a3936d",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/settings/write",
        "Microsoft.Management/managementGroups/settings/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Hierarchy Settings Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="kubernetes-cluster---azure-arc-onboarding"></a>Kubernetes-Cluster – Azure Arc-Onboarding

Rollendefinition zum Autorisieren eines Benutzers/Diensts zum Erstellen einer connectedClusters-Ressource [Weitere Informationen](../azure-arc/kubernetes/connect-cluster.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Erstellt oder aktualisiert eine Bereitstellung. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Dient zum Abrufen der Ergebnisse des Abonnementvorgangs. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Ruft die Abonnementliste ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/Write | Schreibt connectedClusters. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/read | Liest connectedClusters. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role definition to authorize any user/service to create connectedClusters resource",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "name": "34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Kubernetes/connectedClusters/Write",
        "Microsoft.Kubernetes/connectedClusters/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Kubernetes Cluster - Azure Arc Onboarding",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-contributor-role"></a>Rolle „Mitwirkender für verwaltete Anwendungen“

Ermöglicht das Erstellen von Ressourcen für verwaltete Anwendungen.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | */Lesen | Lesen von Ressourcen aller Typen mit Ausnahme geheimer Schlüssel |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/applications/* |  |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/register/action | Hiermit registrieren Sie sich bei Lösungen. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for creating managed application resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/641177b8-a67a-45b9-a033-47bc880bb21e",
  "name": "641177b8-a67a-45b9-a033-47bc880bb21e",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Solutions/applications/*",
        "Microsoft.Solutions/register/action",
        "Microsoft.Resources/subscriptions/resourceGroups/*",
        "Microsoft.Resources/deployments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Application Contributor Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-operator-role"></a>Rolle „Bediener für verwaltete Anwendung“

Ermöglicht Ihnen das Lesen und Durchführen von Aktionen für Ressourcen der verwalteten Anwendung.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | */Lesen | Lesen von Ressourcen aller Typen mit Ausnahme geheimer Schlüssel |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/applications/read | Hiermit wird eine Liste mit Anwendungen abgerufen. |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/*/action |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and perform actions on Managed Application resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c7393b34-138c-406f-901b-d8cf2b17e6ae",
  "name": "c7393b34-138c-406f-901b-d8cf2b17e6ae",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Solutions/applications/read",
        "Microsoft.Solutions/*/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Application Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-applications-reader"></a>Leser für verwaltete Anwendungen

Ermöglicht Ihnen, Ressourcen in einer verwalteten App zu lesen und JIT-Zugriff anzufordern.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | */Lesen | Lesen von Ressourcen aller Typen mit Ausnahme geheimer Schlüssel |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/jitRequests/* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read resources in a managed app and request JIT access.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b9331d33-8a36-4f8c-b097-4f54124fdb44",
  "name": "b9331d33-8a36-4f8c-b097-4f54124fdb44",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Solutions/jitRequests/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Applications Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-services-registration-assignment-delete-role"></a>Rolle „Registrierungszuweisung für verwaltete Dienste löschen“

Mit der Rolle „Registrierungszuweisung für verwaltete Dienste löschen“ können Benutzer des verwaltenden Mandanten die ihrem Mandanten zugewiesene Registrierungszuweisung löschen. [Weitere Informationen](../lighthouse/how-to/remove-delegation.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/registrationAssignments/read | Ruft eine Liste der Registrierungszuweisungen für verwaltete Dienste ab. |
> | [Microsoft.ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/registrationAssignments/delete | Entfernt die Registrierungszuweisung für verwaltete Dienste. |
> | [Microsoft.ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/operationStatuses/read | Liest den Vorgangsstatus für die Ressource. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Managed Services Registration Assignment Delete Role allows the managing tenant users to delete the registration assignment assigned to their tenant.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/91c1777a-f3dc-4fae-b103-61d183457e46",
  "name": "91c1777a-f3dc-4fae-b103-61d183457e46",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedServices/registrationAssignments/read",
        "Microsoft.ManagedServices/registrationAssignments/delete",
        "Microsoft.ManagedServices/operationStatuses/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Services Registration assignment Delete Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="management-group-contributor"></a>Verwaltungsgruppenmitwirkender

Rolle „Verwaltungsgruppenmitwirkender“ [Weitere Informationen](../governance/management-groups/overview.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/delete | Löscht eine Verwaltungsgruppe. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Listet die Verwaltungsgruppen für den authentifizierten Benutzer auf. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/delete | Hebt die Zuordnung des Abonnements mit der Verwaltungsgruppe auf. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/write | Ordnet ein vorhandenes Abonnement der Verwaltungsgruppe zu. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/write | Erstellt oder aktualisiert eine Verwaltungsgruppe. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/read | Listet das Abonnement unter der angegebenen Verwaltungsgruppe auf. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Management Group Contributor Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c",
  "name": "5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/delete",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Management/managementGroups/subscriptions/delete",
        "Microsoft.Management/managementGroups/subscriptions/write",
        "Microsoft.Management/managementGroups/write",
        "Microsoft.Management/managementGroups/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Management Group Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="management-group-reader"></a>Verwaltungsgruppenleser

Rolle „Verwaltungsgruppenleser“

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Listet die Verwaltungsgruppen für den authentifizierten Benutzer auf. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/read | Listet das Abonnement unter der angegebenen Verwaltungsgruppe auf. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Management Group Reader Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ac63b705-f282-497d-ac71-919bf39d939d",
  "name": "ac63b705-f282-497d-ac71-919bf39d939d",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Management/managementGroups/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Management Group Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="new-relic-apm-account-contributor"></a>Mitwirkender von New Relic APM-Konto

Ermöglicht Ihnen das Verwalten von New Relic Application Performance Management-Konten und -Anwendungen, nicht aber den Zugriff auf diese.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | NewRelic.APM/accounts/* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage New Relic Application Performance Management accounts and applications, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d28c62d-5b37-4476-8438-e587778df237",
  "name": "5d28c62d-5b37-4476-8438-e587778df237",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "NewRelic.APM/accounts/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "New Relic APM Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="policy-insights-data-writer-preview"></a>Policy Insights-Datenschreiber (Vorschau)

Ermöglicht Lesezugriff auf Ressourcenrichtlinien und Schreibzugriff auf Richtlinienereignisse für Ressourcenkomponenten. [Weitere Informationen](../governance/policy/concepts/policy-for-kubernetes.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyassignments/read | Dient zum Abrufen von Informationen zu einer Richtlinienzuweisung. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policydefinitions/read | Dient zum Abrufen von Informationen zu einer Richtliniendefinition. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyexemptions/read | Hiermit werden Informationen zu einer Richtlinienausnahme abgerufen. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policysetdefinitions/read | Ruft Informationen zu einer Richtliniengruppendefinition ab. |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/checkDataPolicyCompliance/action | Überprüft den Compliancestatus einer angegebenen Komponente anhand von Datenrichtlinien. |
> | [Microsoft.PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/policyEvents/logDataEvents/action | Protokollieren der Ressourcenkomponenten-Richtlinienereignisse. |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to resource policies and write access to resource component policy events.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/66bb4e9e-b016-4a94-8249-4c0511c2be84",
  "name": "66bb4e9e-b016-4a94-8249-4c0511c2be84",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/policyassignments/read",
        "Microsoft.Authorization/policydefinitions/read",
        "Microsoft.Authorization/policyexemptions/read",
        "Microsoft.Authorization/policysetdefinitions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.PolicyInsights/checkDataPolicyCompliance/action",
        "Microsoft.PolicyInsights/policyEvents/logDataEvents/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Policy Insights Data Writer (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="resource-policy-contributor"></a>Ressourcenrichtlinienmitwirkender

Benutzer mit Rechten zum Erstellen/Ändern der Ressourcenrichtlinie, zum Erstellen eines Supporttickets und zum Lesen von Ressourcen/der Hierarchie. [Weitere Informationen](../governance/policy/overview.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | */Lesen | Lesen von Ressourcen aller Typen mit Ausnahme geheimer Schlüssel |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyassignments/* | Erstellen und Verwalten von Richtlinienzuweisungen |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policydefinitions/* | Erstellen und Verwalten von Richtliniendefinitionen |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyexemptions/* |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policysetdefinitions/* | Erstellen und Verwalten von Richtliniensätzen |
> | [Microsoft.PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/* |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Users with rights to create/modify resource policy, create support ticket and read resources/hierarchy.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/36243c78-bf99-498c-9df9-86d9f8d28608",
  "name": "36243c78-bf99-498c-9df9-86d9f8d28608",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Authorization/policyassignments/*",
        "Microsoft.Authorization/policydefinitions/*",
        "Microsoft.Authorization/policyexemptions/*",
        "Microsoft.Authorization/policysetdefinitions/*",
        "Microsoft.PolicyInsights/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Resource Policy Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-contributor"></a>Site Recovery-Mitwirkender

Ermöglicht Ihnen die Verwaltung des Site Recovery-Diensts mit Ausnahme der Tresorerstellung und der Rollenzuweisung. [Weitere Informationen](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Dient zum Abrufen der Definition des virtuellen Netzwerks. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | „GetAllocatedStamp“ ist ein interner Vorgang des Diensts. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocateStamp/action | „AllocateStamp“ ist ein interner Vorgang des Diensts. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/certificates/write | Der Vorgang „Ressourcenzertifikat aktualisieren“ aktualisiert das Zertifikat für die Ressourcen-/Tresoranmeldeinformationen. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/* | Erstellen und Verwalten erweiterter Informationen in Zusammenhang mit einem Tresor |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | Der Vorgang „Tresor abrufen“ ruft ein Objekt ab, das die Azure-Ressource vom Typ „Tresor“ darstellt. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/* | Erstellen und Verwalten von registrierten Identitäten |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationAlertSettings/* | Erstellen oder Aktualisieren von Einstellungen für Replikationswarnungen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationEvents/read | Dient zum Lesen beliebiger Ereignisse |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/* | Erstellen und Verwalten von Replikationsfabrics |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationJobs/* | Erstellen und Verwalten von Replikationsaufträgen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationPolicies/* | Erstellen und Verwalten von Replikationsrichtlinien |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/* | Erstellen und Verwalten von Wiederherstellungsplänen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/* | Erstellen und Verwalten der Speicherkonfiguration des Recovery Services-Tresors |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Gibt Nutzungsdetails für einen Recovery Services-Tresor zurück. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/read | Der Vorgang „Tresortoken“ kann zum Abrufen des Tresortokens für Back-End-Vorgänge auf Tresorebene verwendet werden. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/* | Lesen von Warnungen für den Recovery Services-Tresor |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Gibt die Liste mit Speicherkonten zurück oder ruft die Eigenschaften für das angegebene Speicherkonto ab. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationOperationStatus/read | Liest alle Status des Tresorreplikationsvorgangs |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Site Recovery service except vault creation and role assignment",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6670b86e-a3f7-4917-ac9b-5d6ab1be4567",
  "name": "6670b86e-a3f7-4917-ac9b-5d6ab1be4567",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/locations/allocateStamp/action",
        "Microsoft.RecoveryServices/Vaults/certificates/write",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/*",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/*",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/*",
        "Microsoft.RecoveryServices/vaults/replicationJobs/*",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/*",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/*",
        "Microsoft.RecoveryServices/Vaults/storageConfig/*",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/*",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/vaults/replicationOperationStatus/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-operator"></a>Site Recovery-Operator

Ermöglicht Ihnen ein Failover und ein Failback, aber nicht das Durchführen weiterer Site Recovery-Verwaltungsvorgänge. [Weitere Informationen](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Dient zum Abrufen der Definition des virtuellen Netzwerks. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | „GetAllocatedStamp“ ist ein interner Vorgang des Diensts. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocateStamp/action | „AllocateStamp“ ist ein interner Vorgang des Diensts. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | Der Vorgang „Ausführliche Informationen abrufen“ ruft die ausführlichen Informationen zu einem Objekt ab, das die Azure-Ressource vom Typ „Tresor“ darstellt. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | Der Vorgang „Tresor abrufen“ ruft ein Objekt ab, das die Azure-Ressource vom Typ „Tresor“ darstellt. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | Mit dem Vorgang „Vorgangsergebnisse abrufen“ können der Vorgangsstatus und das Ergebnis für den asynchron übermittelten Vorgang abgerufen werden. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | Der Vorgang „Container abrufen“ kann zum Abrufen der für eine Ressource registrierten Container verwendet werden. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationAlertSettings/read | Dient zum Lesen beliebiger Warnungseinstellungen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationEvents/read | Dient zum Lesen beliebiger Ereignisse |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/checkConsistency/action | Prüft die Konsistenz des Fabrics. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/read | Dient zum Lesen beliebiger Fabrics |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/reassociateGateway/action | Dient zum Neuzuordnen des Gateways. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/renewcertificate/action | Verlängert das Zertifikat für Fabrics. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/read | Dient zum Lesen beliebiger Netzwerke |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Dient zum Lesen beliebiger Netzwerkzuordnungen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/read | Dient zum Lesen beliebiger Schutzcontainer |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Dient zum Lesen beliebiger schützbarer Elemente |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Dient zum Anwenden des Wiederherstellungspunkts. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Failovercommit |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Geplantes Failover |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Dient zum Lesen beliebiger geschützter Elemente |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Dient zum Lesen beliebiger Wiederherstellungspunkte für die Replikation |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Dient zum Reparieren der Replikation. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Dient zum erneuten Schützen geschützter Elemente. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Dient zum Wechseln von Schutzcontainern. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Testfailover |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Testfailoverbereinigung |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Failover |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Dient zum Aktualisieren von Mobility Service. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Dient zum Lesen beliebiger Schutzcontainerzuordnungen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Dient zum Lesen beliebiger Recovery Services-Anbieter |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Dient zum Aktualisieren des Anbieters. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/read | Dient zum Lesen beliebiger Speicherklassifizierungen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Dient zum Lesen beliebiger Speicherklassifizierungszuordnungen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationvCenters/read | Dienst zum Lesen beliebiger vCenter |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationJobs/* | Erstellen und Verwalten von Replikationsaufträgen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationPolicies/read | Dient zum Lesen beliebiger Richtlinien |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/failoverCommit/action | Wiederherstellungsplan für Failovercommit |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/plannedFailover/action | Wiederherstellungsplan für geplantes Failover |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/read | Dient zum Lesen beliebiger Wiederherstellungspläne |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/reProtect/action | Wiederherstellungsplan für erneutes Schützen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/testFailover/action | Wiederherstellungsplan für Testfailover |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Wiederherstellungsplan für Testfailoverbereinigung |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/unplannedFailover/action | Wiederherstellungsplan für Failover |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/* | Lesen von Warnungen für den Recovery Services-Tresor |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Gibt Nutzungsdetails für einen Recovery Services-Tresor zurück. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/read | Der Vorgang „Tresortoken“ kann zum Abrufen des Tresortokens für Back-End-Vorgänge auf Tresorebene verwendet werden. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Gibt die Liste mit Speicherkonten zurück oder ruft die Eigenschaften für das angegebene Speicherkonto ab. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you failover and failback but not perform other Site Recovery management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/494ae006-db33-4328-bf46-533a6560a3ca",
  "name": "494ae006-db33-4328-bf46-533a6560a3ca",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/locations/allocateStamp/action",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/read",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read",
        "Microsoft.RecoveryServices/vaults/replicationJobs/*",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/*",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.RecoveryServices/Vaults/storageConfig/read",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-reader"></a>Site Recovery-Leser

Ermöglicht Ihnen die Anzeige des Site Recovery-Status, aber nicht die Durchführung weiterer Verwaltungsvorgänge. [Weitere Informationen](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | „GetAllocatedStamp“ ist ein interner Vorgang des Diensts. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | Der Vorgang „Ausführliche Informationen abrufen“ ruft die ausführlichen Informationen zu einem Objekt ab, das die Azure-Ressource vom Typ „Tresor“ darstellt. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | Ruft die Warnungen für den Recovery Services-Tresor ab. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | Der Vorgang „Tresor abrufen“ ruft ein Objekt ab, das die Azure-Ressource vom Typ „Tresor“ darstellt. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | Mit dem Vorgang „Vorgangsergebnisse abrufen“ können der Vorgangsstatus und das Ergebnis für den asynchron übermittelten Vorgang abgerufen werden. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | Der Vorgang „Container abrufen“ kann zum Abrufen der für eine Ressource registrierten Container verwendet werden. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationAlertSettings/read | Dient zum Lesen beliebiger Warnungseinstellungen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationEvents/read | Dient zum Lesen beliebiger Ereignisse |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/read | Dient zum Lesen beliebiger Fabrics |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/read | Dient zum Lesen beliebiger Netzwerke |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Dient zum Lesen beliebiger Netzwerkzuordnungen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/read | Dient zum Lesen beliebiger Schutzcontainer |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Dient zum Lesen beliebiger schützbarer Elemente |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Dient zum Lesen beliebiger geschützter Elemente |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Dient zum Lesen beliebiger Wiederherstellungspunkte für die Replikation |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Dient zum Lesen beliebiger Schutzcontainerzuordnungen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Dient zum Lesen beliebiger Recovery Services-Anbieter |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/read | Dient zum Lesen beliebiger Speicherklassifizierungen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Dient zum Lesen beliebiger Speicherklassifizierungszuordnungen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationvCenters/read | Dienst zum Lesen beliebiger vCenter |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationJobs/read | Dient zum Lesen beliebiger Aufträge |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationPolicies/read | Dient zum Lesen beliebiger Richtlinien |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/read | Dient zum Lesen beliebiger Wiederherstellungspläne |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Gibt Nutzungsdetails für einen Recovery Services-Tresor zurück. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/read | Der Vorgang „Tresortoken“ kann zum Abrufen des Tresortokens für Back-End-Vorgänge auf Tresorebene verwendet werden. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view Site Recovery status but not perform other management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/dbaa88c4-0c30-4179-9fb3-46319faa6149",
  "name": "dbaa88c4-0c30-4179-9fb3-46319faa6149",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/read",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read",
        "Microsoft.RecoveryServices/vaults/replicationJobs/read",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read",
        "Microsoft.RecoveryServices/Vaults/storageConfig/read",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="support-request-contributor"></a>Mitwirkender für Supportanfragen

Ermöglicht Ihnen die Erstellung und Verwaltung von Supportanfragen. [Weitere Informationen](../azure-portal/supportability/how-to-create-azure-support-request.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create and manage Support requests",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e",
  "name": "cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Support Request Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="tag-contributor"></a>Tagmitwirkender

Hiermit können Tags für Entitäten verwaltet werden, ohne Zugriff auf die Entitäten selbst zu gewähren. [Weitere Informationen](../azure-resource-manager/management/tag-resources.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/resources/read | Ruft die Ressourcen für die Ressourcengruppe ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resources/read | Ruft die Ressourcen eines Abonnements ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/tags/* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage tags on entities, without providing access to the entities themselves.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4a9ae827-6dc8-4573-8ac7-8239d42aa03f",
  "name": "4a9ae827-6dc8-4573-8ac7-8239d42aa03f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
        "Microsoft.Resources/subscriptions/resources/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*",
        "Microsoft.Resources/tags/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Tag Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="other"></a>Andere


### <a name="azure-digital-twins-data-owner"></a>Azure Digital Twins Data Owner (Azure Digital Twins-Datenbesitzer)

Vollzugriffsrolle für Digital Twins-Datenebene [Weitere Informationen](../digital-twins/concepts-security.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | *keine* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/eventroutes/* | Lesen, Löschen, Erstellen oder Aktualisieren jeder Ereignisroute |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/* | Lesen, Erstellen, Aktualisieren oder Löschen jedes digitalen Zwillings |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/commands/* | Aufrufen jedes Befehls für einen digitalen Zwilling |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/relationships/* | Lesen, Erstellen, Aktualisieren oder Löschen jeder Beziehung zwischen digitalen Zwillingen |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/models/* | Lesen, Erstellen, Aktualisieren oder Löschen jedes Modells |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/query/* | Abfragen jedes Digital Twins-Graphen |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access role for Digital Twins data-plane",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/bcd981a7-7f74-457b-83e1-cceb9e632ffe",
  "name": "bcd981a7-7f74-457b-83e1-cceb9e632ffe",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.DigitalTwins/eventroutes/*",
        "Microsoft.DigitalTwins/digitaltwins/*",
        "Microsoft.DigitalTwins/digitaltwins/commands/*",
        "Microsoft.DigitalTwins/digitaltwins/relationships/*",
        "Microsoft.DigitalTwins/models/*",
        "Microsoft.DigitalTwins/query/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Digital Twins Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-digital-twins-data-reader"></a>Azure Digital Twins Data Reader (Azure Digital Twins-Datenleser)

Schreibgeschützte Rolle für Digital Twins-Datenebeneneigenschaften [Weitere Informationen](../digital-twins/concepts-security.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | *keine* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/read | Lesen jedes digitalen Zwillings |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/relationships/read | Lesen jeder Beziehung zwischen digitalen Zwillingen |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/eventroutes/read | Lesen jeder Ereignisroute |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/models/read | Lesen jedes Modells |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/query/action | Abfragen jedes Digital Twins-Graphen |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only role for Digital Twins data-plane properties",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d57506d4-4c8d-48b1-8587-93c323f6a5a3",
  "name": "d57506d4-4c8d-48b1-8587-93c323f6a5a3",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.DigitalTwins/digitaltwins/read",
        "Microsoft.DigitalTwins/digitaltwins/relationships/read",
        "Microsoft.DigitalTwins/eventroutes/read",
        "Microsoft.DigitalTwins/models/read",
        "Microsoft.DigitalTwins/query/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Digital Twins Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="biztalk-contributor"></a>Mitwirkender von BizTalk

Ermöglicht Ihnen das Verwalten von BizTalk-Diensten, nicht aber den Zugriff darauf.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | Microsoft.BizTalkServices/BizTalk/* | Erstellen und Verwalten von BizTalk-Diensten |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage BizTalk services, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5e3c6656-6cfa-4708-81fe-0de47ac73342",
  "name": "5e3c6656-6cfa-4708-81fe-0de47ac73342",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.BizTalkServices/BizTalk/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "BizTalk Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-user"></a>Desktopvirtualisierungsbenutzer

Ermöglicht dem Benutzer die Verwendung der Anwendungen in einer Anwendungsgruppe. [Weitere Informationen](../virtual-desktop/delegated-access-virtual-desktop.md)

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | *keine* |  |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | Microsoft.DesktopVirtualization/applicationGroups/useApplications/action | Verwendet „ApplicationGroup“ |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows user to use the applications in an application group.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63",
  "name": "1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.DesktopVirtualization/applicationGroups/useApplications/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="scheduler-job-collections-contributor"></a>Mitwirkender von Zeitplanungsauftragssammlung

Ermöglicht Ihnen das Verwalten von Scheduler-Auftragssammlungen, nicht aber den Zugriff darauf.

> [!div class="mx-tableFixed"]
> | Aktionen | BESCHREIBUNG |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lesen von Rollen und Rollenzuweisungen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Erstellen und Verwalten einer klassischen Metrikwarnung |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Ruft den Verfügbarkeitsstatus für alle Ressourcen im angegebenen Bereich ab. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Erstellen und Verwalten einer Bereitstellung |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Ruft Ressourcengruppen ab oder listet sie auf. |
> | [Microsoft.Scheduler](resource-provider-operations.md#microsoftscheduler)/jobcollections/* | Erstellen und Verwalten von Auftragssammlungen |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Erstellen und Aktualisieren eines Supporttickets |
> | **NotActions** |  |
> | *keine* |  |
> | **DataActions** |  |
> | *keine* |  |
> | **NotDataActions** |  |
> | *keine* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Scheduler job collections, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/188a0f2f-5c9e-469b-ae67-2aa5ce574b94",
  "name": "188a0f2f-5c9e-469b-ae67-2aa5ce574b94",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Scheduler/jobcollections/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Scheduler Job Collections Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="next-steps"></a>Nächste Schritte

- [Zuordnung von Ressourcenanbieter zu Dienst](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Benutzerdefinierte Azure-Rollen](custom-roles.md)
- [Berechtigungen in Azure Security Center](../security-center/security-center-permissions.md)
