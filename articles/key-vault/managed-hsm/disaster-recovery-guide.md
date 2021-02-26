---
title: 'Vorgehensweise bei einer Azure-Dienstunterbrechung mit Auswirkungen auf „Verwaltetes HSM“: Azure Key Vault | Microsoft-Dokumentation'
description: Es wird beschrieben, wie Sie bei einer Azure-Dienstunterbrechung vorgehen, die sich auf „Verwaltetes HSM“ auswirkt.
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 8c284e9993002f6e05e41ca00d00b388feef8f82
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100376002"
---
# <a name="managed-hsm-disaster-recovery"></a>Notfallwiederherstellung für „Verwaltetes HSM“

Unter Umständen müssen Sie ein genaues Replikat Ihres HSM erstellen, wenn das Original aus den folgenden Gründen verloren gegangen oder nicht verfügbar ist:

- Es wurde gelöscht und anschließend bereinigt.
- Ein schwerwiegender Fehler in der Region hat dazu geführt, dass alle Mitgliedspartitionen zerstört wurden.

Sie können die HSM-Instanz in derselben oder einer anderen Region neu erstellen, wenn Sie über Folgendes verfügen:
- Die [Sicherheitsdomäne](security-domain.md) des Quell-HSM.
- Die privaten Schlüssel (mindestens Quorumanzahl), mit denen die Sicherheitsdomäne verschlüsselt wird.
- Die neueste vollständige HSM-[Sicherung](backup-restore.md) vom Quell-HSM.

Hier sind die Schritte des Verfahrens für die Notfallwiederherstellung angegeben:

1. Erstellen einer neuen HSM-Instanz
1. Aktivieren der „Sicherheitsdomänenwiederherstellung“ Ein neues RSA-Schlüsselpaar (Austauschschlüssel für Sicherheitsdomäne) wird für die Sicherheitsdomänenübertragung generiert und als Antwort gesendet. Diese wird als „SecurityDomainExchangeKey“ (öffentlicher Schlüssel) heruntergeladen.
1. Erstellen Sie die „Datei für die Sicherheitsdomänenübertragung“, und laden Sie sie anschließend hoch. Sie benötigen die privaten Schlüssel, mit denen die Sicherheitsdomäne verschlüsselt wird. Die privaten Schlüssel werden lokal verwendet und bei diesem Prozess nie an einen anderen Ort übertragen.
1. Erstellen Sie eine Sicherung des neuen HSM. Vor einer Wiederherstellung ist auch dann eine Sicherung erforderlich, wenn das HSM leer ist. Sicherungen ermöglichen einen einfachen Rollback.
1. Wiederherstellen der letzten HSM-Sicherung vom Quell-HSM

Mit diesen Schritten können Sie den Inhalt des HSM manuell in einer anderen Region replizieren. Der HSM-Name (und der Dienstendpunkt-URI) lautet anders. Daher müssen Sie möglicherweise die Anwendungskonfiguration ändern, damit diese Schlüssel an einem anderen Speicherort verwendet werden können.

## <a name="create-a-new-managed-hsm"></a>Erstellen eines neuen verwalteten HSM

Verwenden Sie den Befehl `az keyvault create`, um ein verwaltetes HSM zu erstellen. Das Skript verfügt über drei erforderliche Parameter: einen Ressourcengruppennamen, einen HSM-Namen und den geografischen Standort.

Zum Erstellen einer Ressource vom Typ „Verwaltetes HSM“ müssen Sie Folgendes eingeben:

- Den Namen des HSM.
- Die Ressourcengruppe, in die es unter Ihrem Abonnement eingefügt wird.
- Den Azure-Standort.
- Eine Liste mit den anfänglichen Administratoren.

Im Beispiel unten wird ein HSM mit dem Namen **ContosoMHSM** in der Ressourcengruppe **ContosoResourceGroup** am Standort **USA, Osten 2** erstellt. Hierbei ist **der derzeit angemeldete Benutzer** der einzige Administrator.

```azurecli-interactive
oid=$(az ad signed-in-user show --query objectId -o tsv)
az keyvault create --hsm-name "ContosoMHSM" --resource-group "ContosoResourceGroup" --location "East US 2" --administrators $oid
```

> [!NOTE]
> Die Ausführung des Befehls „Erstellen“ kann einige Minuten dauern. Nachdem der Vorgang erfolgreich abgeschlossen wurde und die Rückgabe erfolgt ist, können Sie Ihr HSM aktivieren.

In der Ausgabe dieses Befehls werden die Eigenschaften des verwalteten HSM angezeigt, das Sie erstellt haben. Die zwei wichtigsten Eigenschaften sind diese:

* **name:** Im Beispiel lautet der Name „ContosoMHSM“. Sie verwenden diesen Namen für andere Key Vault-Befehle.
* **hsmUri**: In diesem Beispiel lautet der URI https://contosohsm.managedhsm.azure.net. Von Anwendungen, die Ihr HSM über die zugehörige REST-API nutzen, muss dieser URI verwendet werden.

Ihr Azure-Konto verfügt nun über die Berechtigung zum Durchführen von Vorgängen für dieses verwaltete HSM. Derzeit ist noch keine andere Person autorisiert.

## <a name="activate-the-security-domain-recovery-mode"></a>Aktivieren des Wiederherstellungsmodus für die Sicherheitsdomäne

Beim regulären Erstellungsprozess führen wir an diesem Punkt die Initialisierung und den Download einer neuen Sicherheitsdomäne durch. Da wir aber eine Notfallwiederherstellung durchführen, fordern wir das HSM zum Aktivieren des Wiederherstellungsmodus für Sicherheitsdomänen und Herunterladen eines Austauschschlüssels für die Sicherheitsdomäne auf. Der Austauschschlüssel für die Sicherheitsdomäne ist ein öffentlicher RSA-Schlüssel, der zum Verschlüsseln der Sicherheitsdomäne vor dem Hochladen in das HSM verwendet wird. Der entsprechende private Schlüssel ist innerhalb des HSM geschützt, damit der Inhalt Ihrer Sicherheitsdomäne während der Übertragung sicher ist.

```azurecli-interactive
az keyvault security-domain init-recovery --hsm-name ContosoMHSM2 --sd-exchange-key ContosoMHSM2-SDE.cer
```

## <a name="upload-security-domain-to-destination-hsm"></a>Hochladen der Sicherheitsdomäne auf das Ziel-HSM

Für diesen Schritt benötigen Sie Folgendes:
- Den Austauschschlüssel für die Sicherheitsdomäne, den Sie im vorherigen Schritt heruntergeladen haben.
- Die Sicherheitsdomäne des Quell-HSM.
- Die privaten Schlüssel (mindestens Quorumanzahl), die zum Verschlüsseln der Sicherheitsdomäne verwendet wurden.

Mit dem Befehl `az keyvault security-domain upload` wird Folgendes durchgeführt:

- Entschlüsseln der Sicherheitsdomäne des Quell-HSM mit den von Ihnen bereitgestellten privaten Schlüsseln 
- Erstellen eines Blobs für den Upload der Sicherheitsdomäne, das mit dem heruntergeladenen Sicherheitsdomänen-Austauschschlüssel aus dem vorherigen Schritt verschlüsselt ist
- Hochladen des Blobs für den Upload der Sicherheitsdomäne auf das HSM, um die Wiederherstellung der Sicherheitsdomäne abzuschließen

Im Beispiel unten verwenden wir die Sicherheitsdomäne aus **ContosoMHSM** und die beiden entsprechenden privaten Schlüssel und laden sie in das **ContosoMHSM2** hoch, das auf eine Sicherheitsdomäne wartet. 

```azurecli-interactive
az keyvault security-domain upload --hsm-name ContosoMHSM2 --sd-exchange-key ContosoMHSM-SDE.cer --sd-file ContosoMHSM-SD.json --sd-wrapping-keys cert_0.key cert_1.key
```

Nun verfügen das Quell-HSM (ContosoMHSM) und das Ziel-HSM (ContosoMHSM2) über die gleiche Sicherheitsdomäne. Wir können jetzt eine vollständige Sicherung vom Quell-HSM auf das Ziel-HSM durchführen.

## <a name="create-a-backup-as-a-restore-point-of-your-new-hsm"></a>Erstellen einer Sicherung (als Wiederherstellungspunkt) Ihres neuen HSM

Es ist immer ratsam, vor dem Durchführen einer vollständigen HSM-Wiederherstellung eine vollständige Sicherung durchzuführen. Sie verfügen dann über einen Wiederherstellungspunkt, falls es bei der Wiederherstellung zu einem Fehler kommt.

Zum Erstellen einer HSM-Sicherung benötigen Sie Folgendes:
- Ein Speicherkonto, unter dem die Sicherung gespeichert wird.
- Einen Blobspeichercontainer in diesem Speicherkonto, in dem vom Sicherungsprozess ein neuer Ordner zum Speichern der verschlüsselten Sicherung erstellt wird.

Im Beispiel unten verwenden wir den Befehl `az keyvault backup` für die HSM-Sicherung im Speichercontainer **mhsmbackupcontainer**, der sich im Speicherkonto **ContosoBackup** befindet. Wir erstellen ein SAS-Token, das innerhalb von 30 Minuten abläuft, und stellen es zum Schreiben der Sicherung für das verwaltete HSM bereit.

```azurecli-interactive
end=$(date -u -d "500 minutes" '+%Y-%m-%dT%H:%MZ')
skey=$(az storage account keys list --query '[0].value' -o tsv --account-name ContosoBackup)
az storage container create --account-name  mhsmdemobackup --name mhsmbackupcontainer  --account-key $skey
sas=$(az storage container generate-sas -n mhsmbackupcontainer --account-name ContosoBackup --permissions crdw --expiry $end --account-key $skey -o tsv)
az keyvault backup start --hsm-name ContosoMHSM2 --storage-account-name ContosoBackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas

```

## <a name="restore-backup-from-source-hsm"></a>Wiederherstellen einer Sicherung vom Quell-HSM

Für diesen Schritt benötigen Sie Folgendes:

- Das Speicherkonto und den Blobcontainer, in dem die Sicherungen des Quell-HSM gespeichert werden.
- Den Namen des Ordners, aus dem Sie die Sicherung wiederherstellen möchten. Wenn Sie reguläre Sicherungen erstellen, enthält dieser Container viele Ordner.


```azurecli-interactive
end=$(date -u -d "500 minutes" '+%Y-%m-%dT%H:%MZ')
skey=$(az storage account keys list --query '[0].value' -o tsv --account-name ContosoBackup)
sas=$(az storage container generate-sas -n mhsmdemobackupcontainer --account-name ContosoBackup --permissions rl --expiry $end --account-key $skey -o tsv)
az keyvault restore start --hsm-name ContosoMHSM2 --storage-account-name ContosoBackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas --backup-folder mhsm-ContosoMHSM-2020083120161860
```

Sie haben nun einen vollständigen Prozess für die Notfallwiederherstellung erstellt. Der Inhalt des Quell-HSM bei Erstellung der Sicherung wird auf das Ziel-HSM kopiert, einschließlich aller Schlüssel, Versionen, Attribute, Tags und Rollenzuweisungen.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr zur Sicherheitsdomäne unter [Informationen zur Sicherheitsdomäne für „Verwaltetes HSM“](security-domain.md).
- Halten Sie sich an die [bewährten Methoden für „Verwaltetes HSM“](best-practices.md).
