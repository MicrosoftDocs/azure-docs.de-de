---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 11/04/2020
ms.author: inhenkel
ms.custom: portal
ms.openlocfilehash: c3a27218f03980e9b42a4a56fa739203c43754a0
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "96013274"
---
<!--Set the encryption on storage account in the portal-->

## <a name="set-the-encryption-on-a-storage-account"></a>Festlegen der Verschlüsselung für ein Speicherkonto

1. Geben Sie im Azure-Portal den Namen des Speicherkontos, das Sie verschlüsseln möchten, in das Feld **Suche** am oberen Bildschirmrand ein.  Daraufhin werden unterhalb des Suchfelds Treffer angezeigt.
1. Wählen Sie das gewünschte Speicherkonto aus. Der Speicherkontobildschirm wird angezeigt.
1. Wählen Sie **Verschlüsselung** aus.
1. Wählen Sie von Microsoft verwaltete Schlüssel oder kundenseitig verwaltete Schlüssel aus.

### <a name="use-microsoft-managed-keys"></a>Verwenden der von Microsoft verwalteten Schlüssel

Standardmäßig werden Daten im Speicherkonto mit von Microsoft verwalteten Schlüsseln verschlüsselt.

### <a name="use-customer-managed-keys"></a>Verwenden kundenseitig verwalteter Schlüssel

1. Wählen Sie **Von Kunden verwaltete Schlüssel** aus.
1. Wählen Sie entweder **Schlüssel-URI eingeben** oder **Aus Schlüsseltresor auswählen** aus.
    1. Geben Sie bei Verwendung von **Schlüssel-URI eingeben** den Schlüssel-URI in das entsprechende Feld ein, und wählen Sie das Abonnement aus. (Gegebenenfalls wurde es bereits automatisch ausgewählt.)
    1. Wählen Sie bei Verwendung von **Aus Schlüsseltresor auswählen** die Option **Schlüsseltresor und Schlüssel auswählen** aus. Der Bildschirm zum Auswählen des Schlüssels aus Azure Key Vault wird angezeigt.
1. Wählen Sie die Instanz von **Key Vault** aus, die Sie verwenden möchten, und wählen Sie entweder einen Schlüssel aus, der bereits in Ihrem Schlüsseltresor vorhanden ist, oder **erstellen Sie einen neuen Schlüssel**.
    1. Wenn Sie sich für die Erstellung eines neuen Schlüssels entscheiden, wählen Sie in der Dropdownliste **Optionen** entweder **Generieren** oder **Importieren** aus. Sie können nur RSA-Schlüssel importieren.
    1. Wenn Sie einen neuen Schlüssel generieren möchten, geben Sie dem Schlüssel im Feld **Name** einen Namen, und wählen Sie dann den Schlüsseltyp aus:
        1. RSA – Schlüsselgrößen:  2.048, 3.072 oder 4.096. Diese Option wird von den meisten Kunden verwendet.
        1. EC – Elliptic Curve-Namen: P-256, P-384, P-521 oder P-256K
        1. Falls gewünscht, können Sie das Aktivierungs- und das Ablaufdatum des Schlüssels festlegen.
        1. Wählen Sie **Ja** aus, um die automatische Schlüsselrotation zu aktivieren.
        1. Klicken Sie auf **Erstellen**.
    1. Wenn Sie einen Schlüssel importieren möchten, wählen Sie die hochzuladende Datei aus, indem Sie auf eine beliebige Stelle im Feld **Datei auswählen** klicken.
        1. Geben Sie dem Schlüssel im Feld **Name** einen Namen.
        1. Falls gewünscht, können Sie das Aktivierungs- und das Ablaufdatum des Schlüssels festlegen.
        1. Wählen Sie **Ja** aus, um die automatische Schlüsselrotation zu aktivieren.
        1. Klicken Sie auf **Erstellen**.
    1. Wählen Sie **Auswählen** aus, um diesen Schlüssel für die Verschlüsselung Ihres Speicherkontos auszuwählen. Daraufhin gelangen Sie wieder zum Verschlüsselungsbildschirm.
1. **WICHTIG!** Wählen Sie **Speichern** aus, um Ihre Verschlüsselungseinstellungen zu speichern. Andernfalls wird die gesamte soeben vorgenommene Konfiguration verworfen.
