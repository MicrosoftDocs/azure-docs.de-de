---
title: Übersicht über die Zugriffssteuerung in Azure Data Lake Storage Gen2 | Microsoft-Dokumentation
description: Grundlegende Informationen zur Funktionsweise der Zugriffssteuerung in Azure Data Lake Storage Gen2
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 72a72e385217178cb6afee237cc3a3e5c5d1248b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66751638"
---
# <a name="access-control-in-azure-data-lake-storage-gen2"></a>Zugriffssteuerung in Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 implementiert ein Zugriffssteuerungsmodell, das sowohl die rollenbasierte Zugriffssteuerung (Role Based Access Control, RBAC) in Azure als auch POSIX-ähnliche Zugriffssteuerungslisten (Access Control Lists, ACLs) unterstützt. In diesem Artikel werden die Grundlagen des Zugriffssteuerungsmodells für Data Lake Storage Gen2 zusammengefasst.

<a id="azure-role-based-access-control-rbac" />

## <a name="role-based-access-control"></a>Rollenbasierte Zugriffssteuerung

Für die RBAC werden Rollenzuweisungen verwendet, um *Sicherheitsprinzipalen* effektiv Berechtigungen zuzuweisen. Ein *Sicherheitsprinzipal* ist ein Objekt, das einen Benutzer, eine Gruppe, einen Dienstprinzipal oder eine verwaltete Identität darstellt, der bzw. die in Azure Active Directory (AD) definiert ist und Zugriff auf Azure-Ressourcen anfordert.

In der Regel sind diese Azure-Ressourcen auf Ressourcen oberster Ebene beschränkt, z. B. Azure Storage-Konten. Im Falle von Azure Storage und damit auch von Azure Data Lake Storage Gen2 wurde dieser Mechanismus auf die Containerressource (Dateisystem) ausgeweitet.

Informationen zum Zuweisen von Rollen zu Sicherheitsprinzipalen im Bereich Ihres Speicherkontos finden Sie unter [Grant access to Azure blob and queue data with RBAC in the Azure portal](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) (Erteilen des Zugriffs auf Azure-Blob- und Warteschlangendaten mithilfe von RBAC im Azure-Portal).

### <a name="the-impact-of-role-assignments-on-file-and-directory-level-access-control-lists"></a>Die Auswirkungen von Rollenzuweisungen auf Zugriffssteuerungslisten auf Datei- und Verzeichnisebene

RBAC-Rollenzuweisungen sind zwar ein effektiver Mechanismus zur Steuerung von Zugriffsberechtigungen, aber im Vergleich zu Zugriffssteuerungslisten nicht sehr detailliert. Der kleinste Detailgrad für die RBAC ist die Dateisystemebene, die mit einer höheren Priorität als Zugriffssteuerungslisten ausgewertet wird. Wenn Sie einem Sicherheitsprinzipal im Bereich eines Dateisystems eine Rolle zuweisen, hat dieser Sicherheitsprinzipal daher die Autorisierungsstufe dieser Rolle für ALLE Verzeichnisse und Dateien in diesem Dateisystem, unabhängig von den ACL-Zuweisungen.

Wenn einem Sicherheitsprinzipal RBAC-Datenberechtigungen über eine [integrierte Rolle](https://docs.microsoft.com/azure/storage/common/storage-auth-aad?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#built-in-rbac-roles-for-blobs-and-queues) oder eine benutzerdefinierte Rolle erteilt werden, erfolgt die Auswertung dieser Berechtigungen bei der Autorisierung einer Anforderung als erstes. Wenn der angeforderte Vorgang durch die RBAC-Zuweisungen des Sicherheitsprinzipals autorisiert wird, erfolgt die Berechtigungsauflösung sofort, ohne dass zusätzliche ACL-Prüfungen durchgeführt werden. Wenn der Sicherheitsprinzipal keine RBAC-Zuweisung hat oder der Vorgang der Anforderung nicht mit der zugewiesenen Berechtigung übereinstimmt, erfolgen ACL-Prüfungen, um festzustellen, ob der Sicherheitsprinzipal berechtigt ist, den gewünschten Vorgang durchzuführen.

> [!NOTE]
> Wenn dem Sicherheitsprinzipal die integrierte Rolle des Besitzers der Speicherblobdaten zugewiesen wurde, gilt der Sicherheitsprinzipal als *super-user*, und er erhält Vollzugriff für alle Änderungsvorgänge, einschließlich Festlegung des Besitzers eines Verzeichnisses oder einer Datei sowie von ACLs für Verzeichnisse und Dateien, deren Besitzer er nicht ist. Dieser Administratorzugriff ist die einzige autorisierte Möglichkeit, den Besitzer einer Ressource zu ändern.

## <a name="shared-key-and-shared-access-signature-sas-authentication"></a>Gemeinsam verwendeter Schlüssel und SAS-Authentifizierung (Shared Access Signature)

Azure Data Lake Storage Gen2 unterstützt für die Authentifizierung die Methoden „Gemeinsam verwendeter Schlüssel“ und „SAS“. Ein Merkmal dieser Authentifizierungsmethoden ist, dass dem Aufrufer keine Identität zugeordnet wird und daher keine auf Sicherheitsprinzipalberechtigungen basierende Autorisierung erfolgen kann.

Im Falle des gemeinsam verwendeten Schlüssels erhält der Aufrufer faktisch Administratorzugriff, was Vollzugriff zum Anwenden sämtlicher Vorgänge auf alle Ressourcen bedeutet, einschließlich Festlegung des Besitzers und Änderung von ACLs.

SAS-Token enthalten zulässige Berechtigungen als Teil des Tokens. Die im SAS-Token enthaltenen Berechtigungen fließen effektiv in alle Berechtigungsentscheidungen ein, ohne dass zusätzliche ACL-Prüfungen erfolgen.

## <a name="access-control-lists-on-files-and-directories"></a>Zugriffssteuerungslisten für Dateien und Verzeichnisse

Sie können einem Sicherheitsprinzipal eine Zugriffsebene für Dateien und Verzeichnisse zuordnen. Diese Zuordnungen werden in einer *Zugriffssteuerungsliste (ACL)* erfasst. Jede Datei und jedes Verzeichnis in Ihrem Speicherkonto verfügt über eine Zugriffssteuerungsliste.

Wenn Sie einem Sicherheitsprinzipal eine Rolle auf Speicherkontoebene zugewiesen haben, können Sie diesem Sicherheitsprinzipal mit Zugriffssteuerungslisten Zugriff mit erhöhten Rechten auf bestimmte Dateien und Verzeichnisse gewähren.

Zugriffssteuerungslisten können nicht verwendet werden, um eine Zugriffsebene bereitzustellen, die niedriger als eine durch eine Rollenzuweisung gewährte Ebene ist. Wenn Sie beispielsweise einem Sicherheitsprinzipal die Rolle [Mitwirkender an Speicherblob](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) zuweisen, können Sie nicht mithilfe von Zugriffssteuerungslisten verhindern, dass dieser Sicherheitsprinzipal in ein Verzeichnis schreibt.

### <a name="set-file-and-directory-level-permissions-by-using-access-control-lists"></a>Festlegen von Berechtigungen auf Datei- und Verzeichnisebene mithilfe von Zugriffssteuerungslisten

Informationen zum Festlegen von Berechtigungen auf Datei- und Verzeichnisebene finden Sie in den folgenden Artikeln:

|Gewünschtes Tool:    |In diesem Artikel finden Sie weitere Informationen:    |
|--------|-----------|
|Azure Storage-Explorer    |[Festlegen von Berechtigungen auf Datei- und Verzeichnisebene mithilfe des Azure Storage-Explorers mit Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)|
|REST-API    |[Pfad – Aktualisierung](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/update)|

> [!IMPORTANT]
> Wenn der Sicherheitsprinzipal ein *Dienstprinzipal* ist, muss die Objekt-ID des Dienstprinzipals verwendet werden, nicht die Objekt-ID der zugehörigen App-Registrierung. Um die Objekt-ID des Dienstprinzipals abzurufen, öffnen Sie die Azure-Befehlszeilenschnittstelle, und verwenden Sie diesen Befehl: `az ad sp show --id <Your App ID> --query objectId`. Achten Sie darauf, den Platzhalter `<Your App ID>` durch die App-ID der App-Registrierung zu ersetzen.

### <a name="types-of-access-control-lists"></a>Arten von Zugriffssteuerungslisten

Es gibt zwei Arten von Zugriffssteuerungslisten: *Zugriffs-ACLs* und *Standard-ACLs*.

Zugriffs-ACLs steuern den Zugriff auf ein Objekt. Dateien und Verzeichnisse verfügen jeweils über Zugriffs-ACLs.

Standard-ACLs sind Vorlagen von ACLs, die einem Verzeichnis zugeordnet sind, das die Zugriffs-ACLs für alle unter diesem Verzeichnis erstellten untergeordneten Elemente festlegt. Dateien verfügen über keine Standard-ACLs.

Zugriffs- und Standard-ACLs haben die gleiche Struktur.

> [!NOTE]
> Änderungen an der Standard-ACL für ein übergeordnetes Element haben keine Auswirkungen auf die Zugriffs- oder Standard-ACL bereits vorhandener untergeordneter Elemente.

### <a name="levels-of-permission"></a>Berechtigungsebenen

Die Berechtigungen für ein Dateisystemobjekt sind **Lesen**, **Schreiben** und **Ausführen**. Sie können wie in der folgenden Tabelle beschrieben auf Dateien und Verzeichnisse angewendet werden:

|            |    Datei     |   Verzeichnis |
|------------|-------------|----------|
| **Lesen (Read, R)** | Berechtigt zum Lesen von Dateiinhalten | Erfordert **Lesen** und **Ausführen**, um den Inhalt des Verzeichnisses aufzulisten. |
| **Schreiben (Write, W)** | Berechtigt zum Schreiben in eine Datei sowie zum Anfügen an eine Datei | Erfordert **Schreiben** und **Ausführen**, um untergeordnete Elemente in einem Verzeichnis zu erstellen. |
| **Ausführen (Execute, X)** | Hat im Kontext von Data Lake Storage Gen2 keine Bedeutung | Erfordert das Durchlaufen der untergeordneten Elemente eines Verzeichnisses |

> [!NOTE]
> Wenn Sie Berechtigungen ausschließlich mithilfe von ACLs (ohne RBAC) erteilen, müssen Sie zum Erteilen von Lese- oder Schreibzugriff auf eine Datei für einen Dienstprinzipal dem Dienstprinzipal die Berechtigung **Ausführen** für das Dateisystem und zu jedem Ordner in der Ordnerhierarchie erteilen, der zu der betreffenden Datei führt.

#### <a name="short-forms-for-permissions"></a>Kurzformen für Berechtigungen

**RWX** steht für **Lesen (Read), Schreiben (Write) und Ausführen (Execute)** . Es gibt auch ein noch kürzeres numerisches Format. Hierbei steht **4 für Lesen**, **2 für Schreiben** und **1 für Ausführen**, und Berechtigungen werden als Summe dieser Werte angegeben. Hier einige Beispiele.

| Numerische Form | Kurzform |      Bedeutung     |
|--------------|------------|------------------------|
| 7            | `RWX`        | Lesen, Schreiben und Ausführen |
| 5            | `R-X`        | Lesen und Ausführen         |
| 4            | `R--`        | Lesen                   |
| 0            | `---`        | Keine Berechtigungen         |

#### <a name="permissions-inheritance"></a>Vererbung von Berechtigungen

Im von Data Lake Storage Gen2 verwendeten POSIX-basierten Modell werden Berechtigungen für ein Element direkt im Element selbst gespeichert. Berechtigungen für ein Element können also nicht von den übergeordneten Elementen geerbt werden, wenn die Berechtigungen festgelegt werden, nachdem das untergeordnete Element bereits erstellt wurde. Berechtigungen werden nur dann geerbt, wenn für die übergeordneten Elemente Standardberechtigungen festgelegt wurden, bevor die untergeordneten Elemente erstellt werden.

### <a name="common-scenarios-related-to-permissions"></a>Allgemeine Szenarien im Zusammenhang mit Berechtigungen

In der folgenden Tabelle sind einige allgemeine Szenarien aufgeführt, die veranschaulichen, welche Berechtigungen zum Anwenden bestimmter Vorgänge auf ein Speicherkonto erforderlich sind.

|    Vorgang             |    /    | Oregon/ | Portland/ | Data.txt     |
|--------------------------|---------|----------|-----------|--------------|
| Lesen von „Data.txt“            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| Anfügen an „Data.txt“       |   `--X`   |   `--X`    |  `--X`      | `RW-`          |
| Löschen von „Data.txt“          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Erstellen von „Data.txt“          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Auflisten von „/“                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| List /Oregon/           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| List /Oregon/Portland/  |   `--X`   |   `--X`    |  `R-X`      | `---`          |

> [!NOTE]
> Wenn die beiden obigen Bedingungen erfüllt sind, werden zum Löschen der Datei keine Schreibberechtigungen für die Datei benötigt.

### <a name="users-and-identities"></a>Benutzer und Identitäten

Alle Dateien und Verzeichnisse verfügen über eigene Berechtigungen für folgende Identitäten:

- Der zuständige Benutzer
- Die zuständige Gruppe
- Benannte Benutzer
- Benannte Gruppen
- Benannte Dienstprinzipale
- Benannte verwaltete Identitäten
- Alle anderen Benutzer

Die Identitäten von Benutzern und Gruppen sind Azure AD-Identitäten (Azure Active Directory). Sofern nicht anderes angegeben, kann ein *Benutzer* im Data Lake Storage Gen2-Kontext also ein Benutzer, ein Dienstprinzipal, eine verwaltete Identität oder eine Sicherheitsgruppe in Azure AD sein.

#### <a name="the-owning-user"></a>Der zuständige Benutzer

Der Benutzer, der das Element erstellt hat, ist automatisch der zuständige Benutzer für das Element. Der zuständige Benutzer hat folgende Möglichkeiten:

* Er kann die Berechtigungen einer Datei ändern, für die er als Besitzer fungiert.
* Er kann die zuständige Gruppe einer Datei ändern, für die er als Besitzer fungiert, solange der zuständige Benutzer auch der Zielgruppe angehört.

> [!NOTE]
> Der zuständige Benutzer kann den zuständigen Benutzer einer Datei oder eines Verzeichnisses *nicht* ändern. Nur Administratoren können den zuständigen Benutzer einer Datei oder eines Verzeichnisses ändern.

#### <a name="the-owning-group"></a>Die zuständige Gruppe

In den POSIX-Zugriffssteuerungslisten ist jeder Benutzer einer *primären Gruppe* zugeordnet. So kann beispielsweise der Benutzer „Alice“ der Gruppe „finance“ angehören. Alice kann außerdem mehreren Gruppen angehören, aber eine Gruppe wird immer als ihre primäre Gruppe festgelegt. In POSIX gilt: Wenn Alice eine Datei erstellt, wird die zuständige Gruppe der Datei auf ihre primäre Gruppe festgelegt (in diesem Fall „finance“). Andernfalls verhält sich die zuständige Gruppe ähnlich wie zugewiesene Berechtigungen für andere Benutzer oder Gruppen.

##### <a name="assigning-the-owning-group-for-a-new-file-or-directory"></a>Zuweisen der zuständigen Gruppe für eine neue Datei oder ein neues Verzeichnis

* **Fall 1**: Das Stammverzeichnis „/“. Dieses Verzeichnis wird erstellt, wenn ein Data Lake Storage Gen2-Dateisystem erstellt wird. In diesem Fall wird die zuständige Gruppe auf den Benutzer festgelegt, der das Dateisystem erstellt hat, sofern dies mithilfe von OAuth erfolgt ist. Wenn das Dateisystem mithilfe eines gemeinsam verwendeten Schlüssels, einer Konto-SAS oder Dienst-SAS erstellt wird, werden der Besitzer und die zuständige Gruppe auf **$superuser** festgelegt.
* **Fall 2** (jeder andere Fall): Beim Erstellen eines neuen Elements wird die zuständige Gruppe aus dem übergeordneten Verzeichnis kopiert.

##### <a name="changing-the-owning-group"></a>Ändern der zuständigen Gruppe

Die zuständige Gruppe kann von folgenden Benutzern geändert werden:
* Beliebiger Administrator
* Zuständiger Benutzer, sofern er auch der Zielgruppe angehört

> [!NOTE]
> Die zuständige Gruppe kann die ACLs einer Datei oder eines Verzeichnisses nicht ändern.  Im Fall des Stammordners (**Fall 1** weiter oben) wird die zuständige Gruppe zwar auf den Benutzer festgelegt, der das Konto erstellt hat, für die Bereitstellung von Berechtigungen über die zuständige Gruppe ist jedoch kein einzelnes Benutzerkonto zulässig. Sie können diese Berechtigung ggf. einer gültigen Benutzergruppe zuweisen.

### <a name="access-check-algorithm"></a>Algorithmus für die Zugriffsüberprüfung

Im folgenden Pseudocode wird der Zugriffsüberprüfungsalgorithmus für Speicherkonten veranschaulicht.

```
def access_check( user, desired_perms, path ) : 
  # access_check returns true if user has the desired permissions on the path, false otherwise
  # user is the identity that wants to perform an operation on path
  # desired_perms is a simple integer with values from 0 to 7 ( R=4, W=2, X=1). User desires these permissions
  # path is the file or directory
  # Note: the "sticky bit" isn't illustrated in this algorithm
  
# Handle super users.
  if (is_superuser(user)) :
    return True

# Handle the owning user. Note that mask isn't used.
entry = get_acl_entry( path, OWNER )
if (user == entry.identity)
    return ( (desired_perms & entry.permissions) == desired_perms )

# Handle the named users. Note that mask IS used.
entries = get_acl_entries( path, NAMED_USER )
for entry in entries:
    if (user == entry.identity ) :
        mask = get_mask( path )
        return ( (desired_perms & entry.permissions & mask) == desired_perms)

# Handle named groups and owning group
member_count = 0
perms = 0
entries = get_acl_entries( path, NAMED_GROUP | OWNING_GROUP )
for entry in entries:
if (user_is_member_of_group(user, entry.identity)) :
    member_count += 1
    perms | =  entry.permissions
if (member_count>0) :
return ((desired_perms & perms & mask ) == desired_perms)

# Handle other
perms = get_perms_for_other(path)
mask = get_mask( path )
return ( (desired_perms & perms & mask ) == desired_perms)
```

#### <a name="the-mask"></a>Die Maske

Wie im Algorithmus für die Zugriffsüberprüfung gezeigt, beschränkt die Maske den Zugriff auf benannte Benutzer, die zuständige Gruppe und benannte Gruppen.  

> [!NOTE]
> Bei einem neuen Data Lake Storage Gen2-Konto wird die Maske für die Zugriffs-ACL des Stammverzeichnisses („/“) für Verzeichnisse standardmäßig auf 750 und für Dateien auf 640 festgelegt. Dateien erhalten nicht das X-Bit, da es für Dateien in einem reinen Speichersystem irrelevant ist.
>
> Die Maske kann aufrufbezogen festgelegt werden. Dies ermöglicht verschiedenen verarbeitenden Systemen, wie beispielsweise Clustern, für ihre Dateivorgänge unterschiedliche effektive Masken zu verwenden. Wenn eine Maske für eine bestimmte Anforderung angegeben wird, überschreibt sie die Standardmaske vollständig.

#### <a name="the-sticky-bit"></a>Das Sticky Bit

Das Sticky Bit ist ein erweitertes Merkmal eines POSIX-Dateisystems. Im Kontext von Data Lake Storage Gen2 wird das Sticky Bit höchstwahrscheinlich nicht benötigt. Kurz gefasst kann ein untergeordnetes Element nur vom jeweiligen zuständigen Benutzer gelöscht oder umbenannt werden, wenn das Sticky Bit für ein Verzeichnis aktiviert ist.

Das Sticky Bit wird im Azure-Portal nicht angezeigt.

### <a name="default-permissions-on-new-files-and-directories"></a>Standardberechtigungen für neue Dateien und Verzeichnisse

Wenn in einem bereits vorhandenen Verzeichnis eine neue Datei oder ein Verzeichnis erstellt wird, bestimmt die Standard-ACL des übergeordneten Verzeichnisses Folgendes:

- Eine Standard- und eine Zugriffs-ACL des untergeordneten Verzeichnisses
- Eine Zugriffs-ACL der untergeordneten Datei (Dateien haben keine Standard-ACL)

#### <a name="umask"></a>umask

Beim Erstellen einer Datei oder eines Verzeichnisses wird „umask“ verwendet, um zu ändern, wie Standard-ACLs für das untergeordnete Element festgelegt werden. „umask“ ist ein 9-Bit-Wert für übergeordnete Verzeichnisse, der einen RWX-Wert für den **zuständigen Benutzer**, die **zuständige Gruppe** und **andere** enthält.

Für Azure Data Lake Storage Gen2 ist „umask“ ein konstanter Wert, der auf „007“ festgelegt ist. Dieser Wert wird wie folgt übersetzt:

| umask-Komponente     | Numerische Form | Kurzform | Bedeutung |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | Für zuständige Benutzer wird die Standard-ACL des übergeordneten Elements für die Zugriffs-ACL des untergeordneten Elements kopiert. | 
| umask.owning_group  |    0         |   `---`      | Für die zuständige Gruppe wird die Standard-ACL des übergeordneten Elements für die Zugriffs-ACL des untergeordneten Elements kopiert. | 
| umask.other         |    7         |   `RWX`      | Für „other“ (andere) werden alle Berechtigungen für die Zugriffs-ACL des untergeordneten Elements entfernt. |

Der „umask“-Wert, der von Azure Data Lake Storage Gen2 verwendet wird, bedeutet, dass der Wert für **other** nie standardmäßig an untergeordnete Elemente übertragen wird, unabhängig davon, was die Standard-ACL angibt. 

Im folgenden Pseudocode wird gezeigt, wie der umask-Wert angewendet wird, wenn die ACLs für ein untergeordnetes Element erstellt werden.

```
def set_default_acls_for_new_child(parent, child):
    child.acls = []
    for entry in parent.acls :
        new_entry = None
        if (entry.type == OWNING_USER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_user))
        elif (entry.type == OWNING_GROUP) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_group))
        elif (entry.type == OTHER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.other))
        else :
            new_entry = entry.clone(perms = entry.perms )
        child_acls.add( new_entry )
```

## <a name="common-questions-about-acls-in-data-lake-storage-gen2"></a>Allgemeine Fragen zu ACLs in Data Lake Storage Gen2

### <a name="do-i-have-to-enable-support-for-acls"></a>Muss ich die Unterstützung für ACLs aktivieren?

Nein. Die Zugriffssteuerung über ACLs ist für ein Speicherkonto so lange aktiviert, wie die Funktion „Hierarchischer Namespace“ (HNS) aktiviert ist.

Wenn HNS deaktiviert ist, gelten weiterhin die RBAC-Autorisierungsregeln von Azure.

### <a name="what-is-the-best-way-to-apply-acls"></a>Wie werden ACLs am besten angewendet?

Verwenden Sie in ACLs Azure AD-Sicherheitsgruppen stets als den zugewiesenen Prinzipal. Widerstehen Sie der Möglichkeit, einzelne Benutzer oder Dienstprinzipale direkt zuzuweisen. Die Verwendung dieser Struktur ermöglicht Ihnen, Benutzer oder Dienstprinzipale hinzuzufügen und zu entfernen, ohne dass Sie ACLs erneut auf eine gesamte Verzeichnisstruktur anwenden müssen. Stattdessen müssen Sie sie einfach der entsprechenden Azure AD-Sicherheitsgruppe hinzufügen oder aus ihr entfernen. Beachten Sie, dass ACLs nicht vererbt werden und die erneute Anwendung von ACLs daher eine Aktualisierung der ACL für jede Datei und jedes Unterverzeichnis erfordert. 

### <a name="which-permissions-are-required-to-recursively-delete-a-directory-and-its-contents"></a>Welche Berechtigungen werden zum rekursiven Löschen eines Verzeichnisses und seines Inhalts benötigt?

- Der Aufrufer verfügt über die Berechtigung „super-user“

oder

- Das übergeordnete Verzeichnis muss über Schreib- und Ausführungsberechtigungen verfügen.
- Das zu löschende Verzeichnis und alle darin enthaltenen Verzeichnisse müssen über Lese-, Schreib- und Ausführungsberechtigungen verfügen.

> [!NOTE]
> Sie benötigen zum Löschen von Dateien in Verzeichnissen keine Schreibberechtigungen. Außerdem kann das Stammverzeichnis„/“ kann nicht gelöscht werden.

### <a name="who-is-the-owner-of-a-file-or-directory"></a>Wer ist der Besitzer einer Datei oder eines Verzeichnisses?

Der Ersteller einer Datei oder eines Verzeichnisses wird als Besitzer festgelegt. Im Falle des Stammverzeichnisses ist dies die Identität des Benutzers, der das Dateisystem erstellt hat.

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-directory-at-creation"></a>Welche Gruppe wird bei der Erstellung als zuständige Gruppe einer Datei oder eines Verzeichnisses festgelegt?

Die zuständige Gruppe wird aus der zuständigen Gruppe des übergeordneten Verzeichnisses kopiert, in dem die neue Datei oder das neue Verzeichnis erstellt wird.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Ich bin der zuständige Benutzer einer Datei, verfüge aber nicht über die erforderlichen RWX-Berechtigungen. Wie gehe ich vor?

Der zuständige Benutzer kann die Berechtigungen der Datei ändern und sich so selbst die erforderlichen RWX-Berechtigungen gewähren.

### <a name="why-do-i-sometimes-see-guids-in-acls"></a>Warum werden im Portal mitunter GUIDs in ACLs angezeigt?

Eine GUID wird angezeigt, wenn der Eintrag einen Benutzer darstellt, der in Azure AD nicht mehr vorhanden ist. Dies ist meist der Fall, wenn der Benutzer aus dem Unternehmen ausgeschieden ist oder sein Konto in Azure AD gelöscht wurde. Darüber hinaus haben Dienstprinzipale und Sicherheitsgruppen keinen Benutzerprinzipalnamen (UPN), um sie zu identifizieren, und werden daher durch ihr OID-Attribut (eine GUID) abgebildet.

### <a name="how-do-i-set-acls-correctly-for-a-service-principal"></a>Wie lege ich ACLs für einen Dienstprinzipal richtig fest?

Beim Definieren von ACLs für Dienstprinzipale ist es wichtig, die Objekt-ID (OID) des *Dienstprinzipals* für die von Ihnen erstellte App-Registrierung zu verwenden. Sie müssen beachten, dass registrierte Apps über einen separaten Dienstprinzipal im jeweiligen Azure AD-Mandanten verfügen. Registrierte Apps haben eine OID, die im Azure-Portal angezeigt wird, doch hat der *Dienstprinzipal* eine andere (davon abweichende) OID.

Mit dem Befehl `az ad sp show` können Sie die OID für den Dienstprinzipal abrufen, der einer App-Registrierung entspricht. Geben Sie die Anwendungs-ID als Parameter an. Es folgt ein Beispiel für das Abrufen der OID für den Dienstprinzipal, der einer App-Registrierung mit der App-ID „18218b12-1895-43e9-ad80-6e8fc1ea88ce“ entspricht. Führen Sie in der Azure CLI den folgenden Befehl aus:

`az ad sp show --id 18218b12-1895-43e9-ad80-6e8fc1ea88ce --query objectId
<<OID will be displayed>>`

Wenn Sie über die richtige OID für den Dienstprinzipal verfügen, wechseln Sie zur Seite **Zugang verwalten** im Storage-Explorer, um die OID hinzuzufügen und entsprechende Berechtigungen für die OID zuzuweisen. Klicken Sie auf **Speichern**.

### <a name="does-data-lake-storage-gen2-support-inheritance-of-acls"></a>Unterstützt Data Lake Storage Gen2 die Vererbung von ACLs?

RBAC-Zuweisungen von Azure werden vererbt. Zuweisungen werden aus Abonnement-, Ressourcengruppen und Speicherkontenressourcen an die Dateisystemressource übertragen.

ACLs werden nicht vererbt. Standard-ACLs können jedoch zum Festlegen von ACLs für untergeordnete Unterverzeichnisse und Dateien verwendet werden, die im übergeordneten Verzeichnis erstellt wurden. 

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>Wo finde ich weitere Informationen zum POSIX-Zugriffssteuerungsmodell?

* [POSIX-Zugriffssteuerungslisten (ACLs) unter Linux](https://www.linux.com/news/posix-acls-linux)
* [HDFS Permission Guide](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html) (Handbuch zu HDFS-Berechtigungen)
* [POSIX FAQ (Häufig gestellte Fragen zu POSIX)](https://www.opengroup.org/austin/papers/posix_faq.html)
* [POSIX 1003.1 2008](https://standards.ieee.org/findstds/standard/1003.1-2008.html)
* [POSIX 1003.1 2013](https://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)
* [POSIX 1003.1 2016](https://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)
* [POSIX-ACL unter Ubuntu](https://help.ubuntu.com/community/FilePermissionsACLs)
* [ACL using Access Control Lists on Linux](https://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/) (ACL mit Zugriffssteuerungslisten unter Linux)

## <a name="see-also"></a>Weitere Informationen

* [Übersicht über Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md)
