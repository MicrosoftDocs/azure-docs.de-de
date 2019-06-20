---
title: Kopieren oder Verschieben von Daten in Azure Storage mit AzCopy v10 | Microsoft-Dokumentation
description: AzCopy ist ein Befehlszeilenhilfsprogramm, das Sie verwenden können, um Daten in ein oder aus einem Speicherkonto zu kopieren. Dieser Artikel hilft Ihnen, AzCopy herunterzuladen, eine Verbindung mit Ihrem Speicherkonto herzustellen und dann Dateien zu übertragen.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 7d14de200f5301781130e01e75f1b7813389fa76
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/05/2019
ms.locfileid: "66688193"
---
# <a name="get-started-with-azcopy"></a>Erste Schritte mit AzCopy

AzCopy ist ein Befehlszeilenhilfsprogramm, das Sie verwenden können, um Blobs oder Dateien in oder aus einem Speicherkonto zu kopieren. Dieser Artikel hilft Ihnen, AzCopy herunterzuladen, eine Verbindung mit Ihrem Speicherkonto herzustellen und dann Dateien zu übertragen.

> [!NOTE]
> AzCopy **V10** ist die aktuell unterstützte Version von AzCopy.
>
> Wenn Sie AzCopy **v8.1** verwenden müssen, finden Sie Informationen hierzu im Abschnitt [Verwenden der vorherige Version von AzCopy](#previous-version) in diesem Artikel.

<a id="download-and-install-azcopy" />

## <a name="download-azcopy"></a>Herunterladen von AzCopy

Laden Sie zuerst die ausführbare AzCopy V10-Datei in ein beliebiges Verzeichnis auf Ihrem Computer herunter. Der Einfachheit halber erwägen Sie das AzCopy-Verzeichnis zum Systempfad zur einfacheren Verwendung.

- [Windows](https://aka.ms/downloadazcopy-v10-windows) (ZIP)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (TAR)
- [macOS](https://aka.ms/downloadazcopy-v10-mac) (ZIP)

> [!NOTE]
> Wenn Sie Daten in und aus Ihrem [Azure Table Storage](https://docs.microsoft.com/azure/storage/tables/table-storage-overview) kopieren möchten, installieren Sie [AzCopy, Version 7.3](https://aka.ms/downloadazcopynet).

## <a name="run-azcopy"></a>Ausführen von AzCopy

Navigieren Sie in einer Eingabeaufforderung zu dem Verzeichnis, in das Sie die Datei heruntergeladen haben.

Um eine Liste mit Befehlen anzuzeigen, geben `azcopy -h` und drücken Sie dann die EINGABETASTE.

Weitere Informationen zu einem bestimmten Befehl fügen Sie einfach den Namen des Befehls (z. B.: `azcopy list -h`).

![Inline-Hilfe](media/storage-use-azcopy-v10/azcopy-inline-help.png)

Bevor Sie etwas Sinnvolles mit AzCopy machen können, müssen Sie entscheiden, wie Sie dem Speicherdienst die Autorisierungsanmeldeinformationen bereitstellen werden.

## <a name="choose-how-youll-provide-authorization-credentials"></a>Auswählen, wie Sie die Autorisierungsanmeldeinformationen bereitstellen

Sie können die Autorisierungsanmeldeinformationen bereitstellen, indem Sie Azure Active Directory (AD) oder ein SAS-Token (Shared Access Signature) verwenden.

Verwenden Sie diese Tabelle als Richtlinie:

| Speichertyp | Derzeit unterstützte Methode der Autorisierung |
|--|--|
|**Blob Storage** | Azure AD und SAS |
|**Blob-Speicher (hierarchischer Namespace)** | Nur Azure AD |
|**File Storage** | Nur SAS |

### <a name="option-1-use-azure-ad"></a>Option 1: Verwenden von Azure AD

Die Ebene der Autorisierung, die Sie benötigen, hängt davon ab, ob Sie planen, Dateien hoch- oder nur herunterzuladen.

#### <a name="authorization-to-upload-files"></a>Autorisierung zum Hochladen von Dateien

Stellen Sie sicher, dass Ihrer Identität eine dieser Rollen zugewiesen wurde:

- [Mitwirkender an Speicherblobdaten](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [Besitzer von Speicherblobdaten](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

Diese Rollen können Ihrer Identität in jedem dieser Bereiche zugewiesen werden:

- Container (Dateisystem)
- Speicherkonto
- Ressourcengruppe
- Abonnement

Informationen zum Überprüfen und Zuweisen von Rollen finden Sie unter [Gewähren von Zugriff auf Azure Blob- und -Warteschlangendaten mit RBAC im Azure-Portal](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Sie benötigen eine dieser Rollen, die für Ihre Identität zugewiesen wird, wenn die Zugriffssteuerungsliste (ACL) der Zielcontainer oder des Verzeichnisses Ihrer Identität hinzugefügt wird. In der ACL benötigt Ihre Identität, Schreibberechtigung für das Zielverzeichnis und execute-Berechtigung für den Container und jeder übergeordnete Verzeichnis.

Weitere Informationen finden Sie unter [Zugriffssteuerung in Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

#### <a name="authorization-to-download-files"></a>Autorisierung zum Herunterladen von Dateien

Stellen Sie sicher, dass Ihrer Identität eine dieser Rollen zugewiesen wurde:

- [Leser von Speicherblobdaten](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)
- [Mitwirkender an Speicherblobdaten](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [Besitzer von Speicherblobdaten](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

Diese Rollen können Ihrer Identität in jedem dieser Bereiche zugewiesen werden:

- Container (Dateisystem)
- Speicherkonto
- Ressourcengruppe
- Abonnement

Informationen zum Überprüfen und Zuweisen von Rollen finden Sie unter [Gewähren von Zugriff auf Azure Blob- und -Warteschlangendaten mit RBAC im Azure-Portal](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Sie benötigen eine dieser Rollen, die für Ihre Identität zugewiesen wird, wenn die Zugriffssteuerungsliste (ACL) der Zielcontainer oder des Verzeichnisses Ihrer Identität hinzugefügt wird. In der ACL benötigt Ihre Identität, read-Berechtigung für das Zielverzeichnis und execute-Berechtigung für den Container und jedes übergeordneten Verzeichnis.

Weitere Informationen finden Sie unter [Zugriffssteuerung in Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

#### <a name="authenticate-your-identity"></a>Authentifizieren Ihrer Identität

Nachdem Sie überprüft haben, ob Ihrer Identität die notwendige Berechtigungsstufe gewährt wurde, öffnen Sie eine Eingabeaufforderung, geben Sie den folgenden Befehl ein, und drücken Sie dann die EINGABETASTE.

```azcopy
azcopy login
```

Dieser Befehl gibt einen Authentifizierungscode und die URL einer Website zurück. Öffnen Sie die Website, geben Sie den Code ein, und wählen Sie dann die Schaltfläche **Weiter** aus.

![Erstellen eines Containers](media/storage-use-azcopy-v10/azcopy-login.png)

Daraufhin wird ein Anmeldefenster geöffnet. Melden Sie sich in diesem Fenster mit Ihren Azure-Kontoanmeldeinformationen bei Ihrem Azure-Konto an. Wenn Sie sich erfolgreich angemeldet haben, können Sie das Browserfenster schließen und mit der Verwendung von AzCopy beginnen.

### <a name="option-2-use-a-sas-token"></a>Option 2: Verwenden eines SAS-Token

Sie können jeder Quell- oder Ziel-URL, die Sie in Ihren AzCopy-Befehlen verwenden, ein SAS-Token anfügen.

Dieser Beispielbefehl kopiert Daten rekursiv aus einem lokalen Verzeichnis in einen Blob-Container. Am Ende der Container-URL wird ein fiktives SAS-Token angefügt.

```azcopy
azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

Weitere Informationen zu SAS-Tokens und wie Sie eins erhalten, finden Sie unter [Verwenden von SAS (Shared Access Signatures)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).

## <a name="transfer-files"></a>Übertragen von Dateien

Nachdem Sie Ihre Identität authentifiziert oder ein SAS-Token abgerufen haben, können Sie mit der Übertragung von Dateien beginnen.

Beispielbefehle finden Sie in diesen Artikeln.

- [Übertragen von Daten mit AzCopy und Blob Storage](storage-use-azcopy-blobs.md)

- [Übertragen von Daten mit AzCopy und Dateispeicher](storage-use-azcopy-files.md)

- [Übertragen von Daten mit AzCopy und Amazon S3-Buckets](storage-use-azcopy-s3.md)

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>Konfigurieren, Optimieren und Problembehandlung in AzCopy

Siehe [Konfigurieren, Optimieren und Problembehandlung in AzCopy](storage-use-azcopy-configure.md).

## <a name="use-azcopy-in-storage-explorer"></a>Verwenden von AzCopy im Storage-Explorer

Wenn Sie die Leistungsvorteile von AzCopy nutzen möchten, aber lieber Storage-Explorer statt der Befehlszeile verwenden, um mit Ihren Dateien zu interagieren, aktivieren Sie AzCopy in Storage-Explorer.

Wählen Sie in Storage-Explorer **Preview**->**Use AzCopy for Improved Blob Upload and Download** (AzCopy für verbessertes Hoch- und Herunterladen von Blobs verwenden) aus.

![Aktivieren von AzCopy als Übertragungsmodul in Azure Storage-Explorer](media/storage-use-azcopy-v10/enable-azcopy-storage-explorer.jpg)

> [!NOTE]
> Sie müssen diese Einstellung nicht aktivieren, wenn Sie einen hierarchischen Namespace in Ihrem Speicherkonto aktiviert haben. Der Grund hierfür ist, dass Storage-Explorer AzCopy automatisch in Speicherkonten verwendet, die einen hierarchischen Namespace haben.  

<a id="previous-version" />

## <a name="use-the-previous-version-of-azcopy"></a>Verwenden der vorherigen Version von AzCopy

Wenn Sie die vorherige Version von AzCopy (AzCopy v8.1) verwenden müssen, werden Sie unter den folgenden Links fündig:

- [AzCopy unter Windows (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy)
- [AzCopy unter Linux (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie Fragen haben, Probleme melden oder allgemeines Feedback bereitstellen möchten, können Sie dies [auf der GitHub-Seite](https://github.com/Azure/azure-storage-azcopy) tun.
