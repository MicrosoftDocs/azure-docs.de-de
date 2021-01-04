---
title: Zugriffssteuerungsmodell für Azure Data Lake Storage Gen2 | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie den Zugriff auf Container-, Verzeichnis- und Dateiebene in Konten mit einem hierarchischen Namespace konfigurieren.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/10/2020
ms.author: normesta
ms.openlocfilehash: 65d1ef76ffae113a4b526eec75301abbfea751e7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017711"
---
# <a name="access-control-model-in-azure-data-lake-storage-gen2"></a>Zugriffssteuerungsmodell in Azure Data Lake Storage Gen2

Data Lake Storage Gen2 unterstützt die folgenden Autorisierungsmechanismen:

- Autorisierung mit gemeinsam verwendetem Schlüssel
- SAS-Authentifizierung (Shared Access Signature)
- Rollenbasierte Zugriffssteuerung (Azure Role-Based Access Control, Azure RBAC)
- Zugriffssteuerungslisten (Access Control List, ACL)

[Autorisierung mit gemeinsam verwendetem Schlüssel und SAS-Autorisierung](#shared-key-and-shared-access-signature-sas-authorization) gewähren Benutzern (oder Anwendungen) Zugriff, ohne dass diese über eine Identität in Azure Active Directory (Azure AD) verfügen müssen. Bei diesen beiden Authentifizierungsarten haben Azure RBAC und ACLs keinerlei Auswirkungen.

Bei Azure RBAC und ACL muss der Benutzer (oder die Anwendung) über eine Identität in Azure AD verfügen.  Azure RBAC erlaubt es Ihnen, „grobkörnig“ definierten Zugriff auf Speicherkontodaten zu gewähren, z. B. Lese- oder Schreibzugriff auf **alle** Daten im Speicherkonto, mit ACLs können Sie hingegen „feinkörnig“ definierten Zugriff gewähren, z. B. Schreibzugriff auf ein bestimmtes Verzeichnis oder eine bestimmte Datei.  

Der Schwerpunkt dieses Artikels liegt auf Azure RBAC und ACLs und es wird erläutert, wie das System diese auswertet, um Entscheidungen über die Autorisierung für Speicherkontoressourcen zu treffen.

<a id="role-based-access-control"></a>

## <a name="role-based-access-control-azure-rbac"></a>Rollenbasierte Zugriffssteuerung (Azure Role-Based Access Control, Azure RBAC)

Für Azure RBAC werden Rollenzuweisungen verwendet, um [Sicherheitsprinzipalen](../../role-based-access-control/overview.md#security-principal) Berechtigungen zuzuweisen. Ein Sicherheitsprinzipal ist ein Objekt, das einen Benutzer, eine Gruppe, einen Dienstprinzipal oder eine verwaltete Identität darstellt, der bzw. die in Azure Active Directory (AD) definiert ist. Mit einem Berechtigungssatz kann einem Sicherheitsprinzipal eine „grobkörnige“ Zugriffsebene zugewiesen werden, z. B. Lese- oder Schreibzugriff auf **alle** Daten in einem Speicherkonto oder **alle** Daten in einem Container. 

Die folgenden Rollen ermöglichen einem Sicherheitsprinzipal den Zugriff auf Daten in einem Speicherkonto. 

|Role|BESCHREIBUNG|
|--|--|
| [Besitzer von Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) | Vollzugriff auf Blobspeichercontainer und -daten. Dieser Zugriff erlaubt es dem Sicherheitsprinzipal, den Besitzer eines Elements festzulegen und die ACLs aller Elemente zu ändern. |
| [Mitwirkender an Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) | Lesen, Schreiben und Löschen von Blobspeichercontainern und -blobs. Dieser Zugriff gestattet es dem Sicherheitsprinzipal nicht, den Besitzer eines Elements festzulegen, er kann jedoch die ACL von Elementen ändern, deren Besitzer der Sicherheitsprinzipal ist. |
| [Leser von Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) | Lesen und Auflisten von Blobspeichercontainern und -blobs. |

Rollen wie [Besitzer](../../role-based-access-control/built-in-roles.md#owner), [Mitwirkender](../../role-based-access-control/built-in-roles.md#contributor), [Leser](../../role-based-access-control/built-in-roles.md#reader) und [Speicherkontomitwirkender](../../role-based-access-control/built-in-roles.md#storage-account-contributor) gestatten einem Sicherheitsprinzipal die Verwaltung eines Speicherkontos, gewähren jedoch keinen Zugriff auf Daten in diesem Konto. Allerdings können diese Rollen (mit Ausnahme von **Leser**) Zugriff auf die Speicherschlüssel erhalten, die in verschiedenen Clienttools für den Datenzugriff verwendet werden können.

## <a name="access-control-lists-acls"></a>Zugriffssteuerungslisten (ACLs)

ACLs ermöglichen Ihnen die Anwendung einer „feinkörnigen“ Zugriffsebene auf Verzeichnisse und Dateien. Eine *ACL* ist ein Berechtigungskonstrukt, das eine Reihe von *ACL-Einträgen* enthält. Jeder ACL-Eintrag ordnet den Sicherheitsprinzipal einer Zugriffsebene zu.  Weitere Informationen finden Sie unter [Zugriffssteuerung in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

## <a name="how-permissions-are-evaluated"></a>Auswerten von Berechtigungen

Während der auf Sicherheitsprinzipalen beruhenden Autorisierung werden Berechtigungen in der folgenden Reihenfolge ausgewertet:

:one:&nbsp;&nbsp; Azure-Rollenzuweisungen werden zuerst ausgewertet und haben Vorrang vor jeglichen ACL-Zuweisungen.

:two:&nbsp;&nbsp; Wenn der Vorgang auf Grundlage der Azure-Rollenzuweisung bereits vollständig autorisiert ist, werden ACLs überhaupt nicht mehr ausgewertet.

:three:&nbsp;&nbsp; Wenn der Vorgang nicht vollständig autorisiert ist, werden ACLs ausgewertet.

> [!div class="mx-imgBorder"]
> ![Berechtigungsflow für Data Lake Storage](./media/control-access-permissions-data-lake-storage/data-lake-storage-permissions-flow.png)

Aufgrund der Art und Weise, wie Zugriffsberechtigungen vom System ausgewertet werden, können Sie **keine** ACL verwenden, um den Zugriff **einzuschränken**, der bereits durch eine Rollenzuweisung gewährt wurde. Der Grund hierfür ist, dass das System die Azure-Rollenzuweisungen zuerst auswertet. Wenn die Zuweisung ausreichende Zugriffsberechtigungen gewährt, werden ACLs ignoriert. 

Das folgende Diagramm zeigt den Berechtigungsflow für drei häufige Vorgänge: Auflisten von Verzeichnisinhalten, Lesen einer Datei und Schreiben einer Datei.

> [!div class="mx-imgBorder"]
> ![Beispiel für den Berechtigungsflow für Data Lake Storage](./media/control-access-permissions-data-lake-storage/data-lake-storage-permissions-example.png)

## <a name="permissions-table-combining-azure-rbac-and-acl"></a>Berechtigungstabelle: Kombinieren von Azure RBAC und ACL

Die folgende Tabelle zeigt, wie Sie Azure-Rollen und ACL-Einträge kombinieren, damit ein Sicherheitsprinzipal die Vorgänge ausführen kann, die in der Spalte **Vorgang** aufgelistet sind. Diese Tabelle enthält eine Spalte, die die einzelnen Ebenen einer fiktiven Verzeichnishierarchie darstellt. Es gibt eine Spalte für das Stammverzeichnis des Containers (`/`), ein Unterverzeichnis mit dem Namen **Oregon**, ein Unterverzeichnis des Verzeichnisses „Oregon“ namens **Portland** und eine Textdatei im Verzeichnis „Portland“ mit dem Namen **Data.txt**. Diese Spalten enthalten Darstellungen des ACL-Eintrags, der zum Erteilen von Berechtigungen erforderlich ist, in [Kurzform](data-lake-storage-access-control.md#short-forms-for-permissions). **–** (_nicht zutreffend_) steht in der Spalte, wenn der ACL-Eintrag zum Ausführen des Vorgangs nicht erforderlich ist.

|    Vorgang             | Zugewiesene Azure-Rolle               |    /        | Oregon/     | Portland/ | Data.txt |             
|--------------------------|----------------------------------|-------------|-------------|-----------|----------|
| Lesen von „Data.txt“            |   Besitzer von Speicherblobdaten        | –      | –      | –       | –    |  
|                          |   Mitwirkender an Storage-Blobdaten  | –      | –      | –       | –    |
|                          |   Leser von Speicherblobdaten       | –      | –      | –       | Nicht zutreffend    |
|                          |   Keine                           | `--X`    | `--X`    | `--X`     | `R--`  |
| Anfügen an „Data.txt“       |   Besitzer von Speicherblobdaten        | –      | –      | –       | –    |
|                          |   Mitwirkender an Storage-Blobdaten  | –      | –      | –       | –    |
|                          |   Leser von Speicherblobdaten       | `--X`    | `--X`    | `--X`     | `-W-`  |
|                          |   Keine                           | `--X`    | `--X`    | `--X`     | `RW-`  |
| Löschen von „Data.txt“          |   Besitzer von Speicherblobdaten        | –      | –      | –       | –    |
|                          |   Mitwirkender an Storage-Blobdaten  | –      | –      | –       | –    |
|                          |   Leser von Speicherblobdaten       | `--X`    | `--X`    | `-WX`     | Nicht zutreffend    |
|                          |   Keine                           | `--X`    | `--X`    | `-WX`     | –    |
| Erstellen von „Data.txt“          |   Besitzer von Speicherblobdaten        | –      | –      | –       | –    |
|                          |   Mitwirkender an Storage-Blobdaten  | –      | –      | –       | –    |
|                          |   Leser von Speicherblobdaten       | `--X`    | `--X`    | `-WX`     | Nicht zutreffend    |
|                          |   Keine                           | `--X`    | `--X`    | `-WX`     | –    |
| Auflisten von „/“                   |   Besitzer von Speicherblobdaten        | –      | –      | –       | –    |
|                          |   Mitwirkender an Storage-Blobdaten  | –      | –      | –       | –    |
|                          |   Leser von Speicherblobdaten       | –      | –      | –       | Nicht zutreffend    |
|                          |   Keine                           | `R-X`    | –      | –       | –    |
| List /Oregon/            |   Besitzer von Speicherblobdaten        | –      | –      | –       | –    |
|                          |   Mitwirkender an Storage-Blobdaten  | –      | –      | –       | –    |
|                          |   Leser von Speicherblobdaten       | –      | –      | –       | Nicht zutreffend    |
|                          |   Keine                           | `--X`    | `R-X`    | –       | –    |
| List /Oregon/Portland/   |   Besitzer von Speicherblobdaten        | –      | –      | –       | –    |
|                          |   Mitwirkender an Storage-Blobdaten  | –      | –      | –       | –    |
|                          |   Leser von Speicherblobdaten       | –      | –      | –       | Nicht zutreffend    |
|                          |   Keine                           | `--X`    | `--X`    | `R-X`     | –    |


> [!NOTE] 
> Um den Inhalt eines Containers in Azure Storage-Explorer anzuzeigen, müssen sich Sicherheitsprinzipale [mit Azure AD bei Storage-Explorer anmelden](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#add-a-resource-via-azure-ad) und mindestens über Lesezugriff (R--) für den Stammordner (`\`) eines Containers verfügen. Diese Berechtigungsebene erlaubt ihnen, den Inhalt des Stammordners aufzulisten. Wenn Sie nicht möchten, dass der Inhalt des Stammordners sichtbar ist, können Sie ihnen die Rolle [Leser](../../role-based-access-control/built-in-roles.md#reader) zuweisen. Mit dieser Rolle können sie die Container im Konto, aber nicht die Inhalte der Container auflisten. Anschließend können Sie mithilfe von ACLs Zugriff auf bestimmte Verzeichnisse und Dateien gewähren.   

## <a name="security-groups"></a>Sicherheitsgruppen

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-groups.md)]

## <a name="limits-on-azure-role-assignments-and-acl-entries"></a>Begrenzungen für Azure-Rollenzuweisungen und ACL-Einträge

Wenn Sie Gruppen verwenden, ist es weniger wahrscheinlich, dass die maximale Anzahl von Rollenzuweisungen pro Abonnement und die maximale Anzahl von ACL-Einträgen pro Datei oder Verzeichnis überschritten werden. Die Einschränkungen sind in der folgenden Tabelle beschrieben.

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-rbac-acl-limits.md)] 

## <a name="shared-key-and-shared-access-signature-sas-authorization"></a>Autorisierung mit gemeinsam verwendetem Schlüssel und SAS (Shared Access Signature)

Azure Data Lake Storage Gen2 unterstützt für die Authentifizierung auch die Methoden [Gemeinsam verwendeter Schlüssel](/rest/api/storageservices/authorize-with-shared-key) und [SAS](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Ein Merkmal dieser Authentifizierungsmethoden ist, dass dem Aufrufer keine Identität zugeordnet wird und daher keine auf Sicherheitsprinzipalberechtigungen basierende Autorisierung erfolgen kann.

Im Falle des gemeinsam verwendeten Schlüssels erhält der Aufrufer faktisch Administratorzugriff, was Vollzugriff zum Anwenden sämtlicher Vorgänge auf alle Ressourcen bedeutet, einschließlich Daten, Festlegung des Besitzers und Änderung von ACLs.

SAS-Token enthalten zulässige Berechtigungen als Teil des Tokens. Die im SAS-Token enthaltenen Berechtigungen fließen effektiv in alle Berechtigungsentscheidungen ein, ohne dass zusätzliche ACL-Prüfungen erfolgen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Zugriffssteuerungslisten finden Sie unter [Zugriffssteuerung in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).