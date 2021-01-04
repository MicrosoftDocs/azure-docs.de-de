---
title: Verwalten einer Azure-Supportanfrage
description: In diesem Artikel werden das Anzeigen von Supportanfragen, das Senden von Nachrichten, das Ändern des Schweregrads von Anfragen, das Freigeben von Diagnoseinformationen mit Azure-Support, das erneute Öffnen einer geschlossenen Supportanfrage und das Hochladen von Dateien erläutert.
tags: billing
ms.assetid: 86697fdf-3499-4cab-ab3f-10d40d3c1f70
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: a9dd703dc0a3f5e8f85b1022fa2a71ff9a8c295d
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745569"
---
# <a name="manage-an-azure-support-request"></a>Verwalten einer Azure-Supportanfrage

Nachdem Sie eine [Azure-Supportanfrage erstellt](how-to-create-azure-support-request.md) haben, können Sie sie im [Azure-Portal](https://portal.azure.com) verwalten. Dies wird in diesem Artikel beschrieben. Anfragen können aber auch programmgesteuert mithilfe der [Azure-Supportticket-REST-API](/rest/api/support) erstellt und verwaltet werden.

## <a name="view-support-requests"></a>Anzeigen von Supportanfragen

Zeigen Sie Details und den Status von Supportanfragen an, indem Sie zu **Hilfe + Support** >  **Alle Supportanfragen** wechseln.

:::image type="content" source="media/how-to-manage-azure-support-request/all-requests-lower.png" alt-text="Alle Supportanfragen":::

Auf dieser Seite können Sie Supportanfragen durchsuchen, filtern und sortieren. Wählen Sie eine Supportanfrage aus, um Details anzuzeigen, einschließlich ihres Schweregrads und der zur Anfrage gehörenden Meldungen.

## <a name="send-a-message"></a>Senden einer Nachricht

1. Wählen Sie auf der Seite **Alle Supportanfragen** die Supportanfrage aus.

1. Wählen Sie auf der Seite **Supportanfrage** die Option **Neue Nachricht** aus.

1. Geben Sie Ihre Nachricht ein, und wählen Sie **Submit** (Senden) aus.

## <a name="change-the-severity-level"></a>Ändern des Schweregrads

> [!NOTE]
> Der maximale Schweregrad richtet sich nach Ihrem [Supportplan](https://azure.microsoft.com/support/plans).
>

1. Wählen Sie auf der Seite **Alle Supportanfragen** die Supportanfrage aus.

1. Wählen Sie auf der Seite **Supportanfrage** die Option **Change** (Ändern) aus.

    :::image type="content" source="media/how-to-manage-azure-support-request/change-severity.png" alt-text="Ändern des Schweregrads der Supportanfrage":::

1. Im Azure-Portal wird einer von zwei Bildschirmen angezeigt, der davon abhängt, ob die Anfrage bereits einem Supporttechniker zugewiesen ist:

    - Wurde Ihre Anfrage noch nicht zugewiesen, wird ein Bildschirm wie dieser angezeigt. Wählen Sie einen neuen Schweregrad aus, und wählen Sie dann **Change** (Ändern) aus.

        :::image type="content" source="media/how-to-manage-azure-support-request/unassigned-can-change-severity.png" alt-text="Auswählen eines neuen Schweregrads":::

    - Wenn Ihre Anfrage zugewiesen wurde, wird ein Bildschirm wie dieser angezeigt. Wählen Sie **OK** aus, und erstellen Sie dann eine [neue Nachricht](#send-a-message), um eine Änderung des Schweregrads anzufordern.

        :::image type="content" source="media/how-to-manage-azure-support-request/assigned-cant-change-severity.png" alt-text="Neuer Schweregrad kann nicht ausgewählt werden":::

## <a name="share-diagnostic-information-with-azure-support"></a>Teilen von Diagnoseinformationen mit dem Azure-Support

Beim Erstellen einer Supportanfrage ist standardmäßig die Option **Diagnoseinformationen teilen** ausgewählt. Dadurch kann Azure [Diagnoseinformationen](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) zu Ihren Azure-Ressourcen sammeln:

* Nach dem Erstellen einer Anfrage kann diese Option nicht deaktiviert werden.

* Wenn Sie die Option beim Erstellen einer Anfrage deaktiviert haben, können Sie sie nach dem Erstellen der Anfrage wieder aktivieren.

    1. Wählen Sie auf der Seite **Alle Supportanfragen** die Supportanfrage aus.
    
    1. Wählen Sie auf der Seite **Supportanfrage** die Option **Berechtigung erteilen** und anschließend **Ja** und **OK** aus.
    
        :::image type="content" source="media/how-to-manage-azure-support-request/grant-permission-manage.png" alt-text="Erteilen von Berechtigungen für Diagnoseinformationen":::

## <a name="upload-files"></a>Hochladen von Dateien

Sie können mithilfe der Dateiupload-Option Diagnosedateien oder andere Dateien hochladen, die Sie als relevant für eine Supportanfrage erachten.

1. Wählen Sie auf der Seite **Alle Supportanfragen** die Supportanfrage aus.

1. Navigieren Sie auf der Seite **Supportanfrage** zu Ihrer Datei, und wählen Sie dann **Upload** aus. Wiederholen Sie den Vorgang, wenn mehrere Dateien hochgeladen werden sollen.

    :::image type="content" source="media/how-to-manage-azure-support-request/file-upload.png" alt-text="Hochladen einer Datei":::

### <a name="file-upload-guidelines"></a>Richtlinien für den Dateiupload

Befolgen Sie diese Richtlinien, wenn Sie die Dateiupload-Option verwenden:

* Um Ihre Privatsphäre zu schützen, fügen Sie keine persönlichen Informationen in Ihren Upload ein.
* Der Dateiname darf nicht länger als 110 Zeichen sein.
* Sie können nicht mehrere Dateien hochladen.
* Dateien dürfen nicht größer als 4 MB sein.
* Alle Dateien müssen ein Suffix aufweisen, z. B. *.docx* oder *.xlsx*. In der folgenden Tabelle sind die für den Upload zulässigen Dateinamenerweiterungen aufgeführt.

| 0-9, A-C    | D – G   | H-M         | N-P   | R-T      | U-W        | X-Z     |
|-------------|-------|-------------|-------|----------|------------|---------|
| .7z         | .dat  | .hwl        | .odx  | .rar     | .tdb       | .xlam   |
| .a          | .db   | .ics        | .oft  | RDL     | .tdf       | .xlr    |
| .abc        | .DMP  | .ini        | .old  | .rdlc    | .text      | .xls    |
| .adm        | .do_  | .java       | .one  | .re_     | .thmx      | .xlsb   |
| .aspx       | .doc  | .jpg        | .osd  | .reg     | .tif       | .xlsm   |
| .ATF        | .docm | .LDF        | .OUT  | .remove  | .trc       | .xlsx   |
| .b          | .docx | .letterhead | .p1   | .ren     | .TTD       | .xlt    |
| .ba_        | .dotm | .lnk        | .pcap | .rename  | .tx_       | .xltx   |
| .bak        | .dotx | .lo_        | .pdb  | .rft     | .txt       | .xml    |
| .bat        | .dtsx | .log        | .pdf  | .rpt     | .uccapilog | XMLA   |
| .blg        | .eds  | .lpk        | .piz  | .rte     | .uccplog   | .xps    |
| .CA_        | .emf  | .manifest   | .pmls | .rtf     | .udcx      | .xsd    |
| .CAB        | .eml  | .master     | .png  | .run     | .vb_       | .xsn    |
| .cap        | .emz  | .mdmp       | .potx | .saz     | .vbs_      | .xxx    |
| .catx       | .err  | .mof        | .ppt  | SQL     | .vcf       | .z_     |
| .CFG        | .etl  | .mp3        | .pptm | .sqlplan | .vsd       | .z01    |
| .compressed | .evt  | .mpg        | .pptx | .stp     | .wdb       | .z02    |
| .Config     | .evtx | .ms_        | .prn  | .svclog  | .wks       | .zi     |
| .cpk        | .EX   | .msg        | .psf  |   -       | .wma       | .zi_    |
| .cpp        | .ex_  | .msi        | .pst  |  -        | .wmv       | .zip    |
| .cs         | .ex0  | .mso        | .pub  | -         | .wmz       | .zip_   |
| .CSV        | .FRD  | .msu        | -      |-          | .wps       | .zipp   |
| .cvr        | .gif  | .nfo        | -      |-          | .wpt       | .zipped |
| -            | .guid | -            | -      | -         | .wsdl      | .zippy  |
| -            | .gz   | -            | -      | -         | .wsp       | .zipx   |
| -            | -      | -            | -      | -         | .wtl       | .zit    |
| -            | -      | -            | -      | -         |     -       | .zix    |
| -            | -      | -            | -      | -         |  -          | .zzz    |

## <a name="reopen-a-closed-request"></a>Erneutes Öffnen einer geschlossenen Anfrage

Wenn Sie eine geschlossene Supportanfrage erneut öffnen müssen, erstellen Sie eine [neue Nachricht](#send-a-message). Dadurch wird die Anfrage automatisch erneut geöffnet.

## <a name="next-steps"></a>Nächste Schritte

[Erstellen einer Azure-Supportanfrage](how-to-create-azure-support-request.md)

[Azure-Supportticket-REST-API](/rest/api/support)
