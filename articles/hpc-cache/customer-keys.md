---
title: Verwenden von kundenseitig verwalteten Schlüsseln zum Verschlüsseln von Daten in Azure HPC Cache
description: Vorgehensweise zum Verwenden von Azure Key Vault mit Azure HPC Cache, um den Zugriff auf Verschlüsselungsschlüssel zu kontrollieren, anstatt die von Microsoft verwalteten Standardverschlüsselungsschlüssel zu verwenden.
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/20/2020
ms.author: v-erkel
ms.openlocfilehash: 90af33a01450002c7d36a4ab4cf4a3da647068c5
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96444571"
---
# <a name="use-customer-managed-encryption-keys-for-azure-hpc-cache"></a>Verwenden von kundenseitig verwalteten Verschlüsselungsschlüsseln für Azure HPC Cache

Sie können Azure Key Vault verwenden, um den Besitz der Schlüssel zu steuern, die zum Verschlüsseln Ihrer Daten in Azure HPC Cache verwendet werden. In diesem Artikel wird erläutert, wie kundenseitig verwaltete Schlüssel für die Verschlüsselung von Cachedaten verwendet werden.

> [!NOTE]
> Alle in Azure gespeicherten Daten, einschließlich auf Cachedatenträgern, werden ruhend standardmäßig mithilfe der von Microsoft verwalteten Schlüssel verschlüsselt. Sie müssen die Schritte in diesem Artikel nur ausführen, wenn Sie die zum Verschlüsseln Ihrer Daten verwendeten Schlüssel verwalten möchten.

Azure HPC Cache wird darüber hinaus durch [VM-Hostverschlüsselung](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data) auf den verwalteten Datenträgern geschützt, die Ihre zwischengespeicherten Daten enthalten, auch wenn Sie einen Kundenschlüssel für die Cachedatenträger hinzufügen. Durch das Hinzufügen eines kundenseitig verwalteten Schlüssels für die doppelte Verschlüsselung wird für Kunden mit hohen Sicherheitsanforderungen die Sicherheit erhöht. Ausführliche Informationen finden Sie unter [Serverseitige Verschlüsselung von Azure Disk Storage](../virtual-machines/disk-encryption.md).

Diese Funktion ist nur in bestimmten Azure-Regionen verfügbar, in denen auch Azure HPC Cache verfügbar ist. Ausführliche Informationen finden Sie in der Liste [Regionale Verfügbarkeit](hpc-cache-overview.md#region-availability).

Das Aktivieren der kundenseitig verwalteten Schlüsselverschlüsselung für Azure HPC Cache umfasst drei Schritte:

1. Richten Sie einen Azure Key Vault zum Speichern der Schlüssel ein.
1. Wählen Sie beim Erstellen des Azure HPC Cache die kundenseitig verwaltete Schlüsselverschlüsselung aus, und geben Sie den zu verwendenden Schlüsseltresor sowie den Schlüssel an.
1. Nachdem der Cache erstellt wurde, autorisieren Sie ihn für den Zugriff auf den Schlüsseltresor.

Die Verschlüsselung ist erst vollständig eingerichtet, nachdem Sie sie aus dem neu erstellten Cache heraus autorisiert haben (Schritt 3). Dies liegt daran, dass Sie die Identität des Caches an den Schlüsseltresor übergeben müssen, um sie zu einem autorisierten Benutzer zu machen. Sie können dies erst nach dem Erstellen des Caches vornehmen, da die Identität erst vorhanden ist, nachdem der Cache erstellt wurde.

Nach dem Erstellen des Caches können Sie nicht mehr zwischen von Kunden und von Microsoft verwalteten Schlüsseln wechseln. Wenn Ihr Cache jedoch kundenseitig verwaltete Schlüssel verwendet, können Sie den Verschlüsselungsschlüssel, die Schlüsselversion und den Schlüsseltresor nach Bedarf [ändern](#update-key-settings).

## <a name="understand-key-vault-and-key-requirements"></a>Grundlegendes zum Schlüsseltresor und den Schlüsselanforderungen

Der Schlüsseltresor und Schlüssel müssen diese Anforderungen erfüllen, um mit Azure HPC Cache arbeiten zu können.

Schlüsseltresoreigenschaften:

* **Abonnement**: Verwenden Sie dasselbe Abonnement, das für den Cluster verwendet wird.
* **Region**: Der Schlüsseltresor muss sich in derselben Region wie der Azure HPC Cache befinden.
* **Tarif**: Der Tarif „Standard“ ist für die Verwendung mit Azure HPC Cache ausreichend.
* **Vorläufiges Löschen**: Azure HPC Cache aktiviert vorläufiges Löschen, wenn es nicht bereits im Schlüsseltresor konfiguriert ist.
* **Löschschutz**: Löschschutz muss aktiviert sein.
* **Zugriffsrichtlinie**: Standardeinstellungen sind ausreichend.
* **Netzwerkkonnektivität:** Azure HPC Cache muss unabhängig von Ihren ausgewählten Endpunkteinstellungen auf den Schlüsseltresor zugreifen können.

Schlüsseleigenschaften:

* **Schlüsseltyp**: RSA
* **RSA-Schlüsselgröße**: 2048
* **Aktiviert**: Ja

Schlüsseltresor-Zugriffsberechtigungen:

* Der Benutzer, der den Azure HPC Cache erstellt, muss über Berechtigungen verfügen, die der Rolle [Key Vault-Mitwirkender](../role-based-access-control/built-in-roles.md#key-vault-contributor) entsprechen. Dieselben Berechtigungen sind zum Einrichten und Verwalten von Azure Key Vault erforderlich.

  Weitere Informationen finden Sie unter [Sicherer Zugriff auf einen Schlüsseltresor](../key-vault/general/secure-your-key-vault.md).

## <a name="1-set-up-azure-key-vault"></a>1. Einrichten von Azure Key Vault

Sie können einen Schlüsseltresor und Schlüssel einrichten, bevor Sie den Cache erstellen, oder Sie machen dies im Rahmen der Erstellung des Caches. Stellen Sie sicher, dass diese Ressourcen den [oben](#understand-key-vault-and-key-requirements) angeführten Anforderungen entsprechen.

Zum Zeitpunkt der Cacheerstellung müssen Sie einen Tresor, Schlüssel und eine Schlüsselversion angeben, die für die Verschlüsselung des Caches verwendet werden sollen.

Details finden Sie in der [Azure Key Vault-Dokumentation](../key-vault/general/overview.md).

> [!NOTE]
> Azure Key Vault muss dasselbe Abonnement verwenden und sich in derselben Region wie Azure HPC Cache befinden. Stellen Sie sicher, dass die von Ihnen gewählte Region die [Funktion „Kundenseitig verwaltete Schlüssel“ unterstützt](hpc-cache-overview.md#region-availability).

## <a name="2-create-the-cache-with-customer-managed-keys-enabled"></a>2. Erstellen des Caches mit aktivierten kundenseitig verwalteten Schlüsseln

Sie müssen die Verschlüsselungsschlüsselquelle angeben, wenn Sie Ihren Azure HPC Cache erstellen. Befolgen Sie die Anweisungen unter [Erstellen eines Azure HPC Cache](hpc-cache-create.md), und geben Sie den Schlüsseltresor und Schlüssel auf der Seite **Datenträger-Verschlüsselungsschlüssel** an. Sie können einen neuen Schlüsseltresor und Schlüssel während der Erstellung des Caches erstellen.

> [!TIP]
> Wenn die Seite **Datenträger-Verschlüsselungsschlüssel** nicht angezeigt wird, stellen Sie sicher, dass sich Ihr Cache in einer der unterstützten Regionen befindet.

Der Benutzer, der den Cache erstellt, muss über Berechtigungen verfügen, die der Rolle [Key Vault-Mitwirkender](../role-based-access-control/built-in-roles.md#key-vault-contributor) oder höher entsprechen.

1. Klicken Sie auf die Schaltfläche, um privat verwaltete Schlüssel zu aktivieren. Nachdem Sie diese Einstellung geändert haben, werden die Schlüsseltresoreinstellungen angezeigt.

1. Klicken Sie auf **Schlüsseltresor auswählen**, um die Seite für die Schlüsselauswahl zu öffnen. Wählen oder erstellen Sie den Schlüsseltresor und Schlüssel zum Verschlüsseln von Daten auf den Datenträgern dieses Caches.

   Wenn Ihr Azure Key Vault nicht in der Liste angezeigt wird, überprüfen Sie diese Anforderungen:

   * Befindet sich der Cache im selben Abonnement wie der Schlüsseltresor?
   * Befindet sich der Cache in derselben Region wie der Schlüsseltresor?
   * Besteht Netzwerkkonnektivität zwischen dem Azure-Portal und dem Schlüsseltresor?

1. Wählen Sie nach der Auswahl eines Tresors den einzelnen Schlüssel aus den verfügbaren Optionen aus, oder erstellen Sie einen neuen Schlüssel. Bei dem Schlüssel muss es sich um einen 2.048-Bit-RSA-Schlüssel handeln.

1. Geben Sie die Version für den ausgewählten Schlüssel an. Weitere Informationen zur Versionsverwaltung finden Sie in der [Azure Key Vault-Dokumentation](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning).

Fahren Sie mit den restlichen Spezifikationen fort, und erstellen Sie den Cache, wie unter [Erstellen eines Azure HPC Cache](hpc-cache-create.md) beschrieben.

## <a name="3-authorize-azure-key-vault-encryption-from-the-cache"></a>3. Autorisieren der Azure Key Vault-Verschlüsselung aus dem Cache
<!-- header is linked from create article, update if changed -->

Nach ein paar Minuten wird der neue Azure HPC Cache in Ihrem Azure-Portal angezeigt. Wechseln Sie zur Seite **Übersicht**, um ihn für den Zugriff auf Ihren Azure Key Vault zu autorisieren und die kundenseitig verwaltete Schlüsselverschlüsselung zu aktivieren.

> [!TIP]
> Der Cache wird möglicherweise in der Ressourcenliste angezeigt, bevor die „Bereitstellung läuft“-Nachrichten gelöscht werden. Überprüfen Sie die Ressourcenliste nach einer oder zwei Minuten, anstatt auf eine Erfolgsbenachrichtigung zu warten.

Dieser zweistufige Prozess ist erforderlich, weil die Azure HPC Cache-Instanz eine Identität benötigt, die zur Autorisierung an den Azure Key Vault übergeben werden kann. Die Cacheidentität ist erst nach Abschluss der anfänglichen Erstellungsschritte vorhanden.

> [!NOTE]
> Sie müssen die Verschlüsselung innerhalb von 90 Minuten nach dem Erstellen des Caches autorisieren. Wenn Sie diesen Schritt nicht abschließen, kommt es zum Timeout und Fehlschlagen des Caches. Ein fehlgeschlagener Cache muss neu erstellt werden, da er nicht repariert werden kann.

Der Cache weist den Status **Warten auf Schlüssel** aus. Klicken Sie oben auf der Seite auf die Schaltfläche **Verschlüsselung aktivieren**, um den Cache für den Zugriff auf den angegebenen Schlüsseltresor zu autorisieren.

![Screenshot der Seite „Cacheübersicht“ im Portal mit Hervorhebung der Schaltfläche „Verschlüsselung aktivieren“ (oberste Zeile) und Status: Warten auf Schlüssel](media/waiting-for-key.png)

Klicken Sie auf **Verschlüsselung aktivieren**, und klicken Sie dann auf die Schaltfläche **Ja**, um den Cache für die Verwendung des Verschlüsselungsschlüssels zu autorisieren. Diese Aktion aktiviert außerdem vorläufiges Löschen und Löschschutz (falls nicht bereits aktiviert) für den Schlüsseltresor.

![Screenshot der Seite „Cacheübersicht“ im Portal mit einer Bannermeldung am oberen Rand, die den Benutzer auffordert, die Verschlüsselung zu aktivieren, indem er auf „Ja“ klickt.](media/enable-keyvault.png)

Nachdem der Cache den Zugriff auf den Schlüsseltresor angefordert hat, kann er die Datenträger, auf denen zwischengespeicherte Daten gespeichert werden, erstellen und verschlüsseln.

Nachdem Sie die Verschlüsselung autorisiert haben, durchläuft Azure HPC Cache mehrere weitere Minuten der Einrichtung, um die verschlüsselten Datenträger und die zugehörige Infrastruktur zu erstellen.

## <a name="update-key-settings"></a>Aktualisieren von Schlüsseleinstellungen

Sie können den Schlüsseltresor, den Schlüssel oder die Schlüsselversion für Ihren Cache im Azure-Portal ändern. Klicken Sie auf den Link für die **Verschlüsselungs** einstellungen des Caches, um die Seite mit den **Kundenschlüsseleinstellungen** zu öffnen.

Sie können bei einem Cache nicht zwischen kundenseitig und systemseitig verwalteten Schlüsseln wechseln.

![Screenshot der Seite „Kundenschlüsseleinstellungen“, die über das Klicken auf „Einstellungen“ > „Verschlüsselung“ auf der Seite „Cache“ im Azure-Portal angezeigt wurde.](media/change-key-click.png)

Klicken Sie auf den Link **Schlüssel ändern**, und klicken Sie dann auf **Schlüsseltresor, Schlüssel oder Version ändern**, um die Schlüsselauswahl zu öffnen.

![Screenshot der Seite „Auswählen des Schlüssels aus Azure Key Vault“ mit drei Dropdown-Auswahlfeldern zur Auswahl von Schlüsseltresor, Schlüssel und Version.](media/select-new-key.png)

Schlüsseltresore, die sich im selben Abonnement und in derselben Region wie dieser Cache befinden werden in der Liste angezeigt.

Nachdem Sie die neuen Werte für die Verschlüsselungsschlüssel ausgewählt haben, klicken Sie auf **Auswählen**. Eine Bestätigungsseite mit den neuen Werten wird angezeigt. Klicken Sie auf **Speichern**, um die Auswahl abzuschließen.

![Screenshot der Bestätigungsseite mit der Schaltfläche „Speichern“ oben links.](media/save-key-settings.png)

## <a name="read-more-about-customer-managed-keys-in-azure"></a>Weitere Informationen zu kundenseitig verwalteten Schlüsseln in Azure

In diesen Artikeln wird erläutert, wie Sie Azure Key Vault und kundenseitig verwaltete Schlüssel zum Verschlüsseln von Daten in Azure verwenden:

* [Übersicht über die Azure Storage-Verschlüsselung](../storage/common/storage-service-encryption.md)
* [Datenträgerverschlüsselung mit kundenseitig verwalteten Schlüsseln:](../virtual-machines/disk-encryption.md#customer-managed-keys) Dokumentation zur Verwendung von Azure Key Vault und verwalteten Datenträgern, die als Szenario Azure HPC Cache ähnelt

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie den Azure HPC Cache erstellt und die Key Vault-basierte Verschlüsselung autorisiert haben, fahren Sie mit der Einrichtung Ihres Caches fort, indem Sie ihm Zugriff auf Ihre Datenquellen gewähren.

* [Hinzufügen von Speicherzielen](hpc-cache-add-storage.md)