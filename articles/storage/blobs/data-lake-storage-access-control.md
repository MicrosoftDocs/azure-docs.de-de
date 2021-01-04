---
title: Zugriffssteuerungslisten in Azure Data Lake Storage Gen2 | Microsoft-Dokumentation
description: Es wird beschrieben, wie POSIX-ähnliche Zugriffssteuerungslisten (Access Control Lists, ACLs) in Azure Data Lake Storage Gen2 funktionieren.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 2418a8813e7b9de603b7e7cdc11fc756d73ac2a4
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350754"
---
# <a name="access-control-lists-acls-in-azure-data-lake-storage-gen2"></a>Zugriffssteuerungslisten (ACLs) in Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 implementiert ein Zugriffssteuerungsmodell, das sowohl die rollenbasierte Zugriffssteuerung (Role Based Access Control, RBAC) in Azure als auch POSIX-ähnliche Zugriffssteuerungslisten (Access Control Lists, ACLs) unterstützt. In diesem Artikel werden Zugriffssteuerungslisten in Data Lake Storage Gen2 beschrieben. Informationen dazu, wie Sie Azure RBAC zusammen mit ACLs einbinden und wie diese vom System zum Treffen der Autorisierungsentscheidungen ausgewertet werden, finden Sie unter [Zugriffssteuerungsmodell in Azure Data Lake Storage Gen2](data-lake-storage-access-control-model.md).

<a id="access-control-lists-on-files-and-directories"></a>

## <a name="about-acls"></a>Informationen zu ACLs

Sie können einem [Sicherheitsprinzipal](../../role-based-access-control/overview.md#security-principal) eine Zugriffsebene für Dateien und Verzeichnisse zuordnen. Diese Zuordnungen werden in einer *Zugriffssteuerungsliste (ACL)* erfasst. Jede Datei und jedes Verzeichnis in Ihrem Speicherkonto verfügt über eine Zugriffssteuerungsliste. Wenn ein Sicherheitsprinzipal einen Vorgang für eine Datei oder ein Verzeichnis durchführen möchte, wird per ACL-Überprüfung ermittelt, ob dieser Sicherheitsprinzipal (Benutzer, Gruppe, Dienstprinzipal oder verwaltete Identität) über die richtige Berechtigungsstufe für die Durchführung des Vorgangs verfügt.

> [!NOTE]
> ACLs gelten nur für Sicherheitsprinzipale auf demselben Mandanten und nicht für Benutzer, die die Authentifizierung per gemeinsam verwendetem Schlüssel oder SAS-Token (Shared Access Signature) nutzen. Dies liegt daran, dass dem Aufrufer keine Identität zugeordnet ist und daher keine berechtigungsbasierte Autorisierung per Sicherheitsprinzipal durchgeführt werden kann.  

## <a name="how-to-set-acls"></a>Festlegen von ACLs

Informationen zum Festlegen von Berechtigungen auf Datei- und Verzeichnisebene finden Sie in den folgenden Artikeln:

| Environment | Artikel |
|--------|-----------|
|Azure Storage-Explorer |[Verwenden von Azure Storage-Explorer zum Verwalten von Verzeichnissen, Dateien und Zugriffssteuerungslisten in Azure Data Lake Storage Gen2](data-lake-storage-explorer.md#managing-access)|
|.NET |[Verwenden von .NET zum Verwalten von Verzeichnissen, Dateien und Zugriffssteuerungslisten in Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-dotnet.md#manage-access-control-lists-acls)|
|Java|[Verwenden von Java zum Verwalten von Verzeichnissen, Dateien und Zugriffssteuerungslisten in Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-java.md#manage-access-control-lists-acls)|
|Python|[Verwenden von Python zum Verwalten von Verzeichnissen, Dateien und Zugriffssteuerungslisten in Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-python.md#manage-access-control-lists-acls)|
|PowerShell|[Verwenden von PowerShell zum Verwalten von Verzeichnissen, Dateien und Zugriffssteuerungslisten in Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-powershell.md#manage-access-control-lists-acls)|
|Azure CLI|[Verwenden der Azure CLI zum Verwalten von Verzeichnissen, Dateien und Zugriffssteuerungslisten in Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-cli.md#manage-access-control-lists-acls)|
|REST-API |[Pfad – Aktualisierung](/rest/api/storageservices/datalakestoragegen2/path/update)|

> [!IMPORTANT]
> Wenn der Sicherheitsprinzipal ein *Dienstprinzipal* ist, muss die Objekt-ID des Dienstprinzipals verwendet werden, nicht die Objekt-ID der zugehörigen App-Registrierung. Um die Objekt-ID des Dienstprinzipals abzurufen, öffnen Sie die Azure-Befehlszeilenschnittstelle, und verwenden Sie diesen Befehl: `az ad sp show --id <Your App ID> --query objectId`. Achten Sie darauf, den Platzhalter `<Your App ID>` durch die App-ID der App-Registrierung zu ersetzen.

## <a name="types-of-acls"></a>Arten von ACLs

Es gibt zwei Arten von Zugriffssteuerungslisten: *Zugriffs-ACLs* und *Standard-ACLs*.

Zugriffs-ACLs steuern den Zugriff auf ein Objekt. Dateien und Verzeichnisse verfügen jeweils über Zugriffs-ACLs.

Standard-ACLs sind Vorlagen von ACLs, die einem Verzeichnis zugeordnet sind, das die Zugriffs-ACLs für alle unter diesem Verzeichnis erstellten untergeordneten Elemente festlegt. Dateien verfügen über keine Standard-ACLs.

Zugriffs- und Standard-ACLs haben die gleiche Struktur.

> [!NOTE]
> Änderungen an der Standard-ACL für ein übergeordnetes Element haben keine Auswirkungen auf die Zugriffs- oder Standard-ACL bereits vorhandener untergeordneter Elemente.

## <a name="levels-of-permission"></a>Berechtigungsebenen

Die Berechtigungen für ein Containerobjekt sind **Lesen**, **Schreiben** und **Ausführen**. Sie können wie in der folgenden Tabelle beschrieben auf Dateien und Verzeichnisse angewendet werden:

|            |    Datei     |   Verzeichnis |
|------------|-------------|----------|
| **Lesen (Read, R)** | Berechtigt zum Lesen von Dateiinhalten | Erfordert **Lesen** und **Ausführen**, um den Inhalt des Verzeichnisses aufzulisten. |
| **Schreiben (Write, W)** | Berechtigt zum Schreiben in eine Datei sowie zum Anfügen an eine Datei | Erfordert **Schreiben** und **Ausführen**, um untergeordnete Elemente in einem Verzeichnis zu erstellen. |
| **Ausführen (Execute, X)** | Hat im Kontext von Data Lake Storage Gen2 keine Bedeutung | Erfordert das Durchlaufen der untergeordneten Elemente eines Verzeichnisses |

> [!NOTE]
> Wenn Sie Berechtigungen ausschließlich mithilfe von Zugriffssteuerungslisten (ohne Azure RBAC) erteilen, müssen Sie zum Erteilen von Lese- oder Schreibzugriff auf eine Datei für einen Sicherheitsprinzipal dem Sicherheitsprinzipal die Berechtigung **Ausführen** für den Container und für jeden Ordner in der Ordnerhierarchie erteilen, der zu der betreffenden Datei führt.

### <a name="short-forms-for-permissions"></a>Kurzformen für Berechtigungen

**RWX** steht für **Lesen (Read), Schreiben (Write) und Ausführen (Execute)** . Es gibt auch ein noch kürzeres numerisches Format. Hierbei steht **4 für Lesen**, **2 für Schreiben** und **1 für Ausführen**, und Berechtigungen werden als Summe dieser Werte angegeben. Hier einige Beispiele.

| Numerische Form | Kurzform |      Bedeutung     |
|--------------|------------|------------------------|
| 7            | `RWX`        | Lesen, Schreiben und Ausführen |
| 5            | `R-X`        | Lesen und Ausführen         |
| 4            | `R--`        | Lesen                   |
| 0            | `---`        | Keine Berechtigungen         |

### <a name="permissions-inheritance"></a>Vererbung von Berechtigungen

Im von Data Lake Storage Gen2 verwendeten POSIX-basierten Modell werden Berechtigungen für ein Element direkt im Element selbst gespeichert. Berechtigungen für ein Element können also nicht von den übergeordneten Elementen geerbt werden, wenn die Berechtigungen festgelegt werden, nachdem das untergeordnete Element bereits erstellt wurde. Berechtigungen werden nur dann geerbt, wenn für die übergeordneten Elemente Standardberechtigungen festgelegt wurden, bevor die untergeordneten Elemente erstellt werden.

## <a name="common-scenarios-related-to-acl-permissions"></a>Häufige Szenarien in Bezug auf ACL-Berechtigungen

Die folgende Tabelle enthält die ACL-Einträge, die benötigt werden, damit ein Sicherheitsprinzipal die in der Spalte **Vorgang** aufgeführten Vorgänge durchführen kann. 

Diese Tabelle enthält eine Spalte, in der die einzelnen Ebenen einer fiktiven Verzeichnishierarchie dargestellt sind. Es gibt eine Spalte für das Stammverzeichnis des Containers (`\`), ein Unterverzeichnis mit dem Namen **Oregon**, ein Unterverzeichnis des Verzeichnisses „Oregon“ namens **Portland** und eine Textdatei im Verzeichnis „Portland“ mit dem Namen **Data.txt**. 

> [!IMPORTANT]
> Für diese Tabelle gilt die Annahme, dass Sie **ausschließlich** ACLs ohne Azure-Rollenzuweisungen verwenden. Eine ähnliche Tabelle, in der Azure RBAC mit ACLs kombiniert ist, finden Sie unter [Berechtigungstabelle: Kombinieren von Azure RBAC und ACL](data-lake-storage-access-control-model.md#permissions-table-combining-azure-rbac-and-acl).

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

## <a name="users-and-identities"></a>Benutzer und Identitäten

Alle Dateien und Verzeichnisse verfügen über eigene Berechtigungen für folgende Identitäten:

- Der zuständige Benutzer
- Die zuständige Gruppe
- Benannte Benutzer
- Benannte Gruppen
- Benannte Dienstprinzipale
- Benannte verwaltete Identitäten
- Alle anderen Benutzer

Die Identitäten von Benutzern und Gruppen sind Azure AD-Identitäten (Azure Active Directory). Sofern nicht anderes angegeben, kann ein *Benutzer* im Data Lake Storage Gen2-Kontext also ein Benutzer, ein Dienstprinzipal, eine verwaltete Identität oder eine Sicherheitsgruppe in Azure AD sein.

### <a name="the-owning-user"></a>Der zuständige Benutzer

Der Benutzer, der das Element erstellt hat, ist automatisch der zuständige Benutzer für das Element. Der zuständige Benutzer hat folgende Möglichkeiten:

* Er kann die Berechtigungen einer Datei ändern, für die er als Besitzer fungiert.
* Er kann die zuständige Gruppe einer Datei ändern, für die er als Besitzer fungiert, solange der zuständige Benutzer auch der Zielgruppe angehört.

> [!NOTE]
> Der zuständige Benutzer kann den zuständigen Benutzer einer Datei oder eines Verzeichnisses *nicht* ändern. Nur Administratoren können den zuständigen Benutzer einer Datei oder eines Verzeichnisses ändern.

### <a name="the-owning-group"></a>Die zuständige Gruppe

In den POSIX-Zugriffssteuerungslisten ist jeder Benutzer einer *primären Gruppe* zugeordnet. So kann beispielsweise der Benutzer „Alice“ der Gruppe „finance“ angehören. Alice kann außerdem mehreren Gruppen angehören, aber eine Gruppe wird immer als ihre primäre Gruppe festgelegt. In POSIX gilt: Wenn Alice eine Datei erstellt, wird die zuständige Gruppe der Datei auf ihre primäre Gruppe festgelegt (in diesem Fall „finance“). Andernfalls verhält sich die zuständige Gruppe ähnlich wie zugewiesene Berechtigungen für andere Benutzer oder Gruppen.

#### <a name="assigning-the-owning-group-for-a-new-file-or-directory"></a>Zuweisen der zuständigen Gruppe für eine neue Datei oder ein neues Verzeichnis

* **Fall 1**: Das Stammverzeichnis „/“. Dieses Verzeichnis wird erstellt, wenn ein Data Lake Storage Gen2-Container erstellt wird. In diesem Fall wird die zuständige Gruppe auf den Benutzer festgelegt, der den Container erstellt hat, sofern dies mithilfe von OAuth erfolgt ist. Wenn der Container mithilfe eines gemeinsam verwendeten Schlüssels, einer Konto-SAS oder Dienst-SAS erstellt wird, werden der Besitzer und die zuständige Gruppe auf **$superuser** festgelegt.
* **Fall 2** (jeder andere Fall): Beim Erstellen eines neuen Elements wird die zuständige Gruppe aus dem übergeordneten Verzeichnis kopiert.

#### <a name="changing-the-owning-group"></a>Ändern der zuständigen Gruppe

Die zuständige Gruppe kann von folgenden Benutzern geändert werden:
* Beliebiger Administrator
* Zuständiger Benutzer, sofern er auch der Zielgruppe angehört

> [!NOTE]
> Die zuständige Gruppe kann die ACLs einer Datei oder eines Verzeichnisses nicht ändern.  Im Fall des Stammordners (**Fall 1** weiter oben) wird die zuständige Gruppe zwar auf den Benutzer festgelegt, der das Konto erstellt hat, für die Bereitstellung von Berechtigungen über die zuständige Gruppe ist jedoch kein einzelnes Benutzerkonto zulässig. Sie können diese Berechtigung ggf. einer gültigen Benutzergruppe zuweisen.

## <a name="access-check-algorithm"></a>Algorithmus für die Zugriffsüberprüfung

Im folgenden Pseudocode wird der Zugriffsüberprüfungsalgorithmus für Speicherkonten veranschaulicht.

```console
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
  mask = get_mask( path )
  for entry in entries:
    if (user_is_member_of_group(user, entry.identity)) :
        if ((desired_perms & entry.permissions & mask) == desired_perms)
            return True 
        
  # Handle other
  perms = get_perms_for_other(path)
  mask = get_mask( path )
  return ( (desired_perms & perms & mask ) == desired_perms)
```

### <a name="the-mask"></a>Die Maske

Wie im Algorithmus für die Zugriffsüberprüfung gezeigt, beschränkt die Maske den Zugriff auf benannte Benutzer, die zuständige Gruppe und benannte Gruppen.  

Für einen neuen Data Lake Storage Gen2-Container wird die Maske für die Zugriffs-ACL des Stammverzeichnisses („/“) für Verzeichnisse standardmäßig auf **750** und für Dateien auf **640** festgelegt. In der folgenden Tabelle ist die Symbolnotation dieser Berechtigungsstufen angegeben.

|Entität|Verzeichnisse|Dateien|
|--|--|--|
|zuständige Benutzer|`rwx`|`r-w`|
|zuständige Gruppe|`r-x`|`r--`|
|Sonstiges|`---`|`---`|

Dateien erhalten nicht das X-Bit, da es für Dateien in einem reinen Speichersystem irrelevant ist. 

Die Maske kann aufrufbezogen festgelegt werden. Dies ermöglicht verschiedenen verarbeitenden Systemen, wie beispielsweise Clustern, für ihre Dateivorgänge unterschiedliche effektive Masken zu verwenden. Wenn eine Maske für eine bestimmte Anforderung angegeben wird, überschreibt sie die Standardmaske vollständig.

### <a name="the-sticky-bit"></a>Das Sticky Bit

Das Sticky Bit ist ein erweitertes Feature eines POSIX-Containers. Im Kontext von Data Lake Storage Gen2 wird das Sticky Bit höchstwahrscheinlich nicht benötigt. Kurz gefasst kann ein untergeordnetes Element nur vom jeweiligen zuständigen Benutzer gelöscht oder umbenannt werden, wenn das Sticky Bit für ein Verzeichnis aktiviert ist.

Das Sticky Bit wird im Azure-Portal nicht angezeigt.

## <a name="default-permissions-on-new-files-and-directories"></a>Standardberechtigungen für neue Dateien und Verzeichnisse

Wenn in einem bereits vorhandenen Verzeichnis eine neue Datei oder ein Verzeichnis erstellt wird, bestimmt die Standard-ACL des übergeordneten Verzeichnisses Folgendes:

- Eine Standard- und eine Zugriffs-ACL des untergeordneten Verzeichnisses
- Eine Zugriffs-ACL der untergeordneten Datei (Dateien haben keine Standard-ACL)

### <a name="umask"></a>umask

Beim Erstellen einer Datei oder eines Verzeichnisses wird „umask“ verwendet, um zu ändern, wie Standard-ACLs für das untergeordnete Element festgelegt werden. „umask“ ist ein 9-Bit-Wert für übergeordnete Verzeichnisse, der einen RWX-Wert für den **zuständigen Benutzer**, die **zuständige Gruppe** und **andere** enthält.

Für Azure Data Lake Storage Gen2 ist „umask“ ein konstanter Wert, der auf „007“ festgelegt ist. Dieser Wert wird wie folgt übersetzt:

| umask-Komponente     | Numerische Form | Kurzform | Bedeutung |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | Für zuständige Benutzer wird die Standard-ACL des übergeordneten Elements für die Zugriffs-ACL des untergeordneten Elements kopiert. | 
| umask.owning_group  |    0         |   `---`      | Für die zuständige Gruppe wird die Standard-ACL des übergeordneten Elements für die Zugriffs-ACL des untergeordneten Elements kopiert. | 
| umask.other         |    7         |   `RWX`      | Für „other“ (andere) werden alle Berechtigungen für die Zugriffs-ACL des untergeordneten Elements entfernt. |

Der Wert von „umask“, der von Azure Data Lake Storage Gen2 verwendet wird, bedeutet, dass der Wert für **other** nie standardmäßig an untergeordnete Elemente übertragen wird, sofern im übergeordneten Verzeichnis keine Standard-ACL definiert wurde. In diesem Fall wird umask ignoriert, und die von der Standard-ACL definierten Berechtigungen werden auf das untergeordnete Element angewandt. 

Im folgenden Pseudocode wird gezeigt, wie der umask-Wert angewendet wird, wenn die ACLs für ein untergeordnetes Element erstellt werden.

```console
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

## <a name="faq"></a>Häufig gestellte Fragen

### <a name="do-i-have-to-enable-support-for-acls"></a>Muss ich die Unterstützung für ACLs aktivieren?

Nein. Die Zugriffssteuerung über ACLs ist für ein Speicherkonto so lange aktiviert, wie die Funktion „Hierarchischer Namespace“ (HNS) aktiviert ist.

Wenn HNS deaktiviert ist, gelten weiterhin die RBAC-Autorisierungsregeln von Azure.

### <a name="what-is-the-best-way-to-apply-acls"></a>Wie werden ACLs am besten angewendet?

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-groups.md)] 

### <a name="how-are-azure-rbac-and-acl-permissions-evaluated"></a>Wie werden Azure RBAC- und ACL-Berechtigungen ausgewertet?

Informationen dazu, wie Azure RBAC und ACLs gemeinsam ausgewertet werden, um Autorisierungsentscheidungen für Speicherkontoressourcen treffen zu können, finden Sie unter [Auswerten von Berechtigungen](data-lake-storage-access-control-model.md#how-permissions-are-evaluated).

### <a name="what-are-the-limits-for-azure-role-assignments-and-acl-entries"></a>Welche Grenzwerte gelten für Azure-Rollenzuweisungen und ACL-Einträge?

Die folgende Tabelle enthält eine Zusammenfassung der Grenzwerte, die Sie beim Verwenden von Azure RBAC zum Verwalten von „groben“ Berechtigungen (für Speicherkonten oder Container) und von ACLs zum Verwalten von „präzisen“ Berechtigungen (für Dateien und Verzeichnisse) berücksichtigen sollten. Verwenden Sie Sicherheitsgruppen für ACL-Zuweisungen. Wenn Sie Gruppen verwenden, ist es weniger wahrscheinlich, dass die maximale Anzahl von Rollenzuweisungen pro Abonnement und die maximale Anzahl von ACL-Einträgen pro Datei oder Verzeichnis überschritten werden. 

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-rbac-acl-limits.md)] 

### <a name="does-data-lake-storage-gen2-support-inheritance-of-azure-rbac"></a>Unterstützt Data Lake Storage Gen2 die Vererbung von Azure RBAC?

Azure-Rollenzuweisungen werden vererbt. Zuweisungen werden aus Abonnement-, Ressourcengruppen und Speicherkontenressourcen an die Containerressource übertragen.

### <a name="does-data-lake-storage-gen2-support-inheritance-of-acls"></a>Unterstützt Data Lake Storage Gen2 die Vererbung von ACLs?

Standard-ACLs können zum Festlegen von ACLs für neue untergeordnete Unterverzeichnisse und Dateien verwendet werden, die im übergeordneten Verzeichnis erstellt wurden. Zum Aktualisieren von ACLs für vorhandene untergeordnete Elemente müssen Sie ACLs für die gewünschte Verzeichnishierarchie rekursiv hinzufügen, aktualisieren oder entfernen. Weitere Informationen finden Sie unter [Festlegen von Zugriffssteuerungslisten (ACLs) für Azure Data Lake Storage Gen2](recursive-access-control-lists.md). 

### <a name="which-permissions-are-required-to-recursively-delete-a-directory-and-its-contents"></a>Welche Berechtigungen werden zum rekursiven Löschen eines Verzeichnisses und seines Inhalts benötigt?

- Der Aufrufer verfügt über die Berechtigung „super-user“.

oder

- Das übergeordnete Verzeichnis muss über Schreib- und Ausführungsberechtigungen verfügen.
- Das zu löschende Verzeichnis und alle darin enthaltenen Verzeichnisse müssen über Lese-, Schreib- und Ausführungsberechtigungen verfügen.

> [!NOTE]
> Sie benötigen zum Löschen von Dateien in Verzeichnissen keine Schreibberechtigungen. Außerdem kann das Stammverzeichnis„/“ kann nicht gelöscht werden.

### <a name="who-is-the-owner-of-a-file-or-directory"></a>Wer ist der Besitzer einer Datei oder eines Verzeichnisses?

Der Ersteller einer Datei oder eines Verzeichnisses wird als Besitzer festgelegt. Im Falle des Stammverzeichnisses ist dies die Identität des Benutzers, der den Container erstellt hat.

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-directory-at-creation"></a>Welche Gruppe wird bei der Erstellung als zuständige Gruppe einer Datei oder eines Verzeichnisses festgelegt?

Die zuständige Gruppe wird aus der zuständigen Gruppe des übergeordneten Verzeichnisses kopiert, in dem die neue Datei oder das neue Verzeichnis erstellt wird.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Ich bin der zuständige Benutzer einer Datei, verfüge aber nicht über die erforderlichen RWX-Berechtigungen. Wie gehe ich vor?

Der zuständige Benutzer kann die Berechtigungen der Datei ändern und sich so selbst die erforderlichen RWX-Berechtigungen gewähren.

### <a name="why-do-i-sometimes-see-guids-in-acls"></a>Warum werden im Portal mitunter GUIDs in ACLs angezeigt?

Eine GUID wird angezeigt, wenn der Eintrag einen Benutzer darstellt, der in Azure AD nicht mehr vorhanden ist. Dies ist meist der Fall, wenn der Benutzer aus dem Unternehmen ausgeschieden ist oder sein Konto in Azure AD gelöscht wurde. Darüber hinaus haben Dienstprinzipale und Sicherheitsgruppen keinen Benutzerprinzipalnamen (UPN), um sie zu identifizieren, und werden daher durch ihr OID-Attribut (eine GUID) abgebildet.

### <a name="how-do-i-set-acls-correctly-for-a-service-principal"></a>Wie lege ich ACLs für einen Dienstprinzipal richtig fest?

Beim Definieren von ACLs für Dienstprinzipale ist es wichtig, die Objekt-ID (OID) des *Dienstprinzipals* für die von Ihnen erstellte App-Registrierung zu verwenden. Sie müssen beachten, dass registrierte Apps über einen separaten Dienstprinzipal im jeweiligen Azure AD-Mandanten verfügen. Registrierte Apps haben eine OID, die im Azure-Portal angezeigt wird, doch hat der *Dienstprinzipal* eine andere (davon abweichende) OID.

Mit dem Befehl `az ad sp show` können Sie die OID für den Dienstprinzipal abrufen, der einer App-Registrierung entspricht. Geben Sie die Anwendungs-ID als Parameter an. Es folgt ein Beispiel für das Abrufen der OID für den Dienstprinzipal, der einer App-Registrierung mit der App-ID „18218b12-1895-43e9-ad80-6e8fc1ea88ce“ entspricht. Führen Sie in der Azure CLI den folgenden Befehl aus:

```azurecli
az ad sp show --id 18218b12-1895-43e9-ad80-6e8fc1ea88ce --query objectId
```

Die OID wird angezeigt.

Wenn Sie über die richtige OID für den Dienstprinzipal verfügen, wechseln Sie zur Seite **Zugang verwalten** im Storage-Explorer, um die OID hinzuzufügen und entsprechende Berechtigungen für die OID zuzuweisen. Klicken Sie auf **Speichern**.

### <a name="can-i-set-the-acl-of-a-container"></a>Kann ich die ACL eines Containers festlegen?

Nein. Ein Container verfügt nicht über eine ACL. Sie können aber die ACL für das Stammverzeichnis des Containers festlegen. Jeder Container verfügt über ein Stammverzeichnis, das den gleichen Namen wie der Container hat. Wenn der Container beispielsweise den Namen `my-container` hat, lautet das Stammverzeichnis `myContainer/`. 

Die Azure Storage-REST-API enthält einen Vorgang mit dem Namen [Set Container ACL](/rest/api/storageservices/set-container-acl), der aber nicht verwendet werden kann, um die ACL oder das Stammverzeichnis eines Containers festzulegen. Stattdessen wird dieser Vorgang verwendet, um anzugeben, ob auf Blobs in einem Container [öffentlich zugegriffen werden kann](anonymous-read-access-configure.md). 

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

- [Zugriffssteuerungsmodell in Azure Data Lake Storage Gen2](data-lake-storage-access-control-model.md)
