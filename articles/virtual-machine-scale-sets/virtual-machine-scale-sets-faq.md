---
title: Häufig gestellte Fragen zu Azure-VM-Skalierungsgruppen
description: Hier erhalten Sie Antworten auf die am häufigsten gestellten Fragen zu VM-Skalierungsgruppen in Azure.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: faq
ms.date: 06/30/2020
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 8170cfcbbf200c6ba5030aff5716f46b537d8c97
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016708"
---
# <a name="azure-virtual-machine-scale-sets-faqs"></a>Häufig gestellte Fragen zu Azure-VM-Skalierungsgruppen

Hier erhalten Sie Antworten auf häufig gestellte Fragen zu VM-Skalierungsgruppen in Azure.

## <a name="top-frequently-asked-questions-for-scale-sets"></a>Die am häufigsten gestellten Fragen zu Skalierungsgruppen

### <a name="how-many-vms-can-i-have-in-a-scale-set"></a>Wie viele virtuelle Computer können in einer Skalierungsgruppe enthalten sein?

Eine Skalierungsgruppe kann 0 bis 1.000 virtuelle Computer (Plattformimages) bzw. 0 bis 600 virtuelle Computer (benutzerdefinierte Images) enthalten.

### <a name="are-data-disks-supported-within-scale-sets"></a>Werden Datenträger in Skalierungsgruppen unterstützt?

Ja. Für eine Skalierungsgruppe kann eine Konfiguration für angefügte Datenträger definiert werden, die für alle virtuellen Computer der Gruppe gilt. Weitere Informationen finden Sie unter [Azure-VM-Skalierungsgruppen und angefügte Datenträger](virtual-machine-scale-sets-attached-disks.md). Beispiele für andere Optionen zum Speichern von Daten:

* Azure-Dateien (SMB-freigegebene Laufwerke)
* Betriebssystemlaufwerk
* Temporäres Laufwerk (lokal; nicht durch Azure Storage gesichert)
* Azure-Datendienst (z.B. Azure-Tabellen, Azure-Blobs)
* Externer Datendienst (z.B. Remotedatenbank)

### <a name="which-azure-regions-support-scale-sets"></a>In welchen Azure-Regionen werden Skalierungsgruppen unterstützt?

Alle Regionen unterstützen Skalierungsgruppen.

### <a name="how-do-i-create-a-scale-set-by-using-a-custom-image"></a>Wie erstelle ich eine Skalierungsgruppe mit einem benutzerdefinierten Image?

Erstellen und erfassen Sie ein VM-Image, und verwenden Sie es dann als Quelle für Ihre Skalierungsgruppe. Ein Tutorial zum Erstellen und Verwenden eines benutzerdefinierten VM-Images finden Sie unter [Azure CLI](tutorial-use-custom-image-cli.md) oder [Azure PowerShell](tutorial-use-custom-image-powershell.md).

### <a name="if-i-reduce-my-scale-set-capacity-from-20-to-15-which-vms-are-removed"></a>Welche virtuellen Computer werden entfernt, wenn ich meine Kapazität für Skalierungsgruppen von 20 auf 15 verringere?

VMs werden standardmäßig gleichmäßig aus den Skalierungsgruppen der Verfügbarkeitszonen (wenn die Skalierungsgruppe in zonaler Konfiguration bereitgestellt wurde) und Fehlerdomänen entfernt, um die Verfügbarkeit zu maximieren. Die virtuellen Computer mit den höchsten IDs werden zuerst entfernt.

Sie können die Reihenfolge zur Entfernung der VM ändern, indem Sie eine [Richtlinie für horizontales Herunterskalieren](virtual-machine-scale-sets-scale-in-policy.md) für die Skalierungsgruppe festlegen.

### <a name="what-if-i-then-increase-the-capacity-from-15-to-18"></a>Was passiert, wenn ich dann die Kapazität von 15 auf 18 erhöhe?

Wenn Sie die Kapazität auf 18 erhöhen, werden drei neue VMs erstellt. Die VM-Instanz-ID wird jeweils ausgehend vom vorherigen höchsten Wert erhöht (Beispiel: 20, 21, 22). Die VMs werden gleichmäßig auf die Fehlerdomänen verteilt.

### <a name="when-im-using-multiple-extensions-in-a-scale-set-can-i-enforce-an-execution-sequence"></a>Kann ich bei Verwendung mehrerer Erweiterungen in einer Skalierungsgruppe eine Ausführungsreihenfolge erzwingen?

Ja, Sie können die [Erweiterungssequenzierung](virtual-machine-scale-sets-extension-sequencing.md) für Skalierungsgruppen verwenden.

### <a name="do-scale-sets-work-with-azure-availability-sets"></a>Funktionieren Skalierungsgruppen mit Azure-Verfügbarkeitsgruppen?

Eine regionale (nicht auf eine Zone beschränkte) Skalierungsgruppe verwendet *Platzierungsgruppen*, die als implizite Verfügbarkeitsgruppe mit fünf Fehlerdomänen und fünf Updatedomänen konfiguriert werden können. Skalierungsgruppen mit mehr als 100 VMs erstrecken sich auf mehrere Platzierungsgruppen. Weitere Informationen zu Platzierungsgruppen finden Sie unter [Verwenden umfangreicher VM-Skalierungsgruppen](virtual-machine-scale-sets-placement-groups.md). Eine Verfügbarkeitsgruppe mit virtuellen Computern kann sich im gleichen virtuellen Netzwerk befinden wie eine Skalierungsgruppe mit virtuellen Computern. Oftmals werden virtuelle Steuerknotencomputer (für die häufig eine eindeutige Konfiguration erforderlich ist) in einer Verfügbarkeitsgruppe und Datenknoten in der Skalierungsgruppe platziert.

### <a name="do-scale-sets-work-with-azure-availability-zones"></a>Funktionieren Skalierungsgruppen mit Azure-Verfügbarkeitszonen?

Ja. Weitere Informationen hierzu finden Sie in der [Dokumentation zu Skalierungsgruppen über verschiedene Verfügbarkeitszonen hinweg](./virtual-machine-scale-sets-use-availability-zones.md).


## <a name="autoscale"></a>Autoscale

### <a name="what-are-best-practices-for-azure-autoscale"></a>Was sind die bewährten Methoden für die automatische Skalierung in Azure?

Bewährte Methoden für die automatische Skalierung finden Sie unter [Empfohlene Methoden für die automatische Skalierung von VMs, VM-Skalierungsgruppen und Clouddiensten](../azure-monitor/platform/autoscale-best-practices.md).

### <a name="where-do-i-find-metric-names-for-autoscaling-that-uses-host-based-metrics"></a>Wo finde ich Metriknamen für die automatische Skalierung mit hostbasierten Metriken?

Metriknamen für die automatische Skalierung mit hostbasierten Metriken finden Sie unter [Unterstützte Metriken von Azure Monitor](../azure-monitor/platform/metrics-supported.md).

### <a name="are-there-any-examples-of-autoscaling-based-on-an-azure-service-bus-topic-and-queue-length"></a>Gibt es Beispiele für die automatische Skalierung auf der Grundlage eines Azure Service Bus-Themas und der Warteschlangenlänge?

Ja. Beispiele für die automatische Skalierung auf der Grundlage eines Azure Service Bus-Themas und der Warteschlangenlänge finden Sie unter [Allgemeine Metriken für die automatische Skalierung in Azure Monitor](../azure-monitor/platform/autoscale-common-metrics.md).

Verwenden Sie für eine Service Bus-Warteschlange den folgenden JSON-Code:

```json
"metricName": "MessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

Verwenden Sie für eine Speicherwarteschlange den folgenden JSON-Code:

```json
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

Ersetzen Sie die Beispielwerte durch die URIs (Uniform Resource Identifiers) Ihrer Ressource.


### <a name="should-i-autoscale-by-using-host-based-metrics-or-a-diagnostics-extension"></a>Soll ich für die automatische Skalierung hostbasierte Metriken oder eine Diagnoseerweiterung verwenden?

Sie können eine Einstellung zur automatischen Skalierung auf einem virtuellen Computer erstellen, um Metriken zu verwenden, die auf der Hostebene oder auf dem Gastbetriebssystem basieren.

Eine Liste mit unterstützten Metriken finden Sie unter [Allgemeine Metriken für die automatische Skalierung in Azure Monitor](../azure-monitor/platform/autoscale-common-metrics.md).

Ein vollständiges Beispiel für VM-Skalierungsgruppen finden Sie unter [Konfiguration der erweiterten automatischen Skalierung mithilfe von Resource Manager-Vorlagen für VM-Skalierungsgruppen](../azure-monitor/platform/autoscale-virtual-machine-scale-sets.md).

In dem Beispiel werden die CPU-Metrik auf der Hostebene und die Metrik für die Nachrichtenanzahl verwendet.



### <a name="how-do-i-set-alert-rules-on-a-virtual-machine-scale-set"></a>Wie lege ich Warnungsregeln für eine VM-Skalierungsgruppe fest?

Metrikwarnungen für VM-Skalierungsgruppen können über PowerShell oder über die Azure-Befehlszeilenschnittstelle erstellt werden. Weitere Informationen finden Sie unter [Azure Monitor – PowerShell-Schnellstartbeispiele](../azure-monitor/samples/powershell-samples.md#create-metric-alerts) und unter [Azure Monitor – Schnellstartbeispiele für die plattformübergreifende CLI](../azure-monitor/samples/cli-samples.md#work-with-alerts).

Die Zielressourcen-ID (TargetResourceId) der VM-Skalierungsgruppe sieht wie folgt aus:

/subscriptions/IhreAbonnementID/resourceGroups/<Ihre Ressourcengruppe>/providers/Microsoft.Compute/virtualMachineScaleSets/<Name Ihrer VM-Skalierungsgruppe>

Als Metrik, für die eine Warnung festgelegt werden soll, können Sie einen beliebigen VM-Leistungsindikator auswählen. Weitere Informationen finden Sie im Artikel [Allgemeine Metriken für die automatische Skalierung in Azure Monitor](../azure-monitor/platform/autoscale-common-metrics.md) unter [Gastbetriebssystem-Metriken für Resource Manager-basierte virtuelle Windows-Computer](../azure-monitor/platform/autoscale-common-metrics.md#guest-os-metrics-for-resource-manager-based-windows-vms) und [Gastbetriebssystem-Metriken für virtuelle Linux-Computer](../azure-monitor/platform/autoscale-common-metrics.md#guest-os-metrics-linux-vms).

### <a name="how-do-i-set-up-autoscale-on-a-virtual-machine-scale-set-by-using-powershell"></a>Wie richte ich die automatische Skalierung für eine VM-Skalierungsgruppe mithilfe von PowerShell ein?

Informationen zum Einrichten der automatischen Skalierung für eine VM-Skalierungsgruppe mithilfe von PowerShell finden Sie unter dem Thema [Automatische Skalierung einer VM-Skalierungsgruppe](tutorial-autoscale-powershell.md). Außerdem können Sie die automatische Skalierung mit [Azure CLI](tutorial-autoscale-cli.md) und [Azure-Vorlagen](tutorial-autoscale-template.md) konfigurieren.


### <a name="if-i-have-stopped-deallocated-a-vm-is-that-vm-started-as-part-of-an-autoscale-operation"></a>Wird eine VM, wenn ich sie beendet (d.h. deren Zuordnung aufgehoben) habe, bei einer automatischen Skalierung erneut gestartet?

Nein. Wenn Regeln für die automatische Skalierung zusätzliche VM-Instanzen als Teil einer Skalierungsgruppe erfordern, wird eine neue VM-Instanz erstellt. VM-Instanzen, die beendet (bzw. deren Zuordnung aufgehoben) wurden, werden nicht im Rahmen einer automatischen Skalierung gestartet. Die beendeten (freigegebenen) VMs können jedoch im Zuge einer automatischen Skalierung gelöscht werden, die die Anzahl der Instanzen skaliert, so wie jede VM-Instanz in der Reihenfolge der VM-Instanz-ID gelöscht werden kann.



## <a name="certificates"></a>Zertifikate

### <a name="how-do-i-securely-ship-a-certificate-to-the-vm"></a>Wie übertrage ich ein Zertifikat sicher auf den virtuellen Computer?

Um ein Zertifikat sicher auf den virtuellen Computer zu übertragen, können Sie ein Kundenzertifikat aus dem Schlüsseltresor des Kunden direkt in einem Windows-Zertifikatspeicher installieren.

Verwenden Sie dazu den folgenden JSON-Code:

```json
"secrets": [
    {
        "sourceVault": {
            "id": "/subscriptions/{subscriptionid}/resourceGroups/myrg1/providers/Microsoft.KeyVault/vaults/mykeyvault1"
        },
        "vaultCertificates": [
            {
                "certificateUrl": "https://mykeyvault1.vault.azure.net/secrets/{secretname}/{secret-version}",
                "certificateStore": "certificateStoreName"
            }
        ]
    }
]
```

Der Code ist für Windows und Linux geeignet.

Weitere Informationen finden Sie unter [Create or update a set](/rest/api/compute/virtualmachinescalesets/createorupdate) (Erstellen oder Aktualisieren einer Gruppe).


### <a name="how-do-i-use-self-signed-certificates-provisioned-for-azure-service-fabric-clusters"></a>Wie verwende ich selbstsignierte Zertifikate, die für Azure Service Fabric-Cluster bereitgestellt werden?
Verwenden Sie für das neueste Beispiel die folgende Azure-CLI-Anweisung innerhalb von Azure Shell, und lesen Sie die Dokumentation zum Service Fabrics CLI-Modulbeispiel, die über stdout gedruckt wird:

```azurecli
az sf cluster create -h
```

Selbstsignierte Zertifikate können nicht für verteilte Vertrauensstellungen verwendet werden, die von einer Zertifizierungsstelle bereitgestellt werden, und sie sollten nicht für Service Fabric Cluster verwendet werden, die zum Hosten von Produktionslösungen von Unternehmen bestimmt sind; weitere Anleitungen zur Service Fabric-Sicherheit finden Sie in [Azure Service Fabric Security Best Practices](../security/fundamentals/service-fabric-best-practices.md) (Azure Service Fabric-Sicherheit – bewährte Methoden) und [Service Fabric cluster security scenarios](../service-fabric/service-fabric-cluster-security.md) (Service Fabric Cluster-Sicherheitsszenarien).

### <a name="can-i-specify-an-ssh-key-pair-to-use-for-ssh-authentication-with-a-linux-virtual-machine-scale-set-from-a-resource-manager-template"></a>Kann ich ein SSH-Schlüsselpaar für die SSH-Authentifizierung mit einer Linux-VM-Skalierungsgruppe über eine Resource Manager-Vorlage angeben?

Ja. Die REST-API für **osProfile** gleicht der standardmäßigen VM-REST-API.

Schließen Sie **osProfile** in Ihre Vorlage ein:

```json
"osProfile": {
    "computerName": "[variables('vmName')]",
    "adminUsername": "[parameters('adminUserName')]",
    "linuxConfiguration": {
        "disablePasswordAuthentication": "true",
        "ssh": {
            "publicKeys": [
                {
                    "path": "[variables('sshKeyPath')]",
                    "keyData": "[parameters('sshKeyData')]"
                }
            ]
        }
    }
}
```

Dieser JSON-Block wird in [dieser Azure-Schnellstartvorlage](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json) verwendet.

Weitere Informationen finden Sie unter [Create or update a set](/rest/api/compute/virtualmachinescalesets/createorupdate#linuxconfiguration) (Erstellen oder Aktualisieren einer Gruppe).

### <a name="how-do-i-remove-deprecated-certificates"></a>Wie entferne ich veraltete Zertifikate?

Ein veraltetes Zertifikat können Sie entfernen, indem Sie es aus der Liste mit den Tresorzertifikaten entfernen. Belassen Sie alle Zertifikate, die auf dem Computer bleiben sollen, in der Liste. Dadurch wird das Zertifikat nicht von allen Ihren virtuellen Computern entfernt. Auch wird das Zertifikat nicht zu neuen virtuellen Computern hinzugefügt, die in der VM-Skalierungsgruppe erstellt werden.

Wenn Sie das Zertifikat von vorhandenen virtuellen Computern entfernen möchten, verwenden Sie eine benutzerdefinierte Skripterweiterung, die die Zertifikate manuell aus Ihrem Zertifikatspeicher entfernt.

### <a name="how-do-i-inject-an-existing-ssh-public-key-into-the-virtual-machine-scale-set-ssh-layer-during-provisioning"></a>Wie kann ich einen vorhandenen öffentlichen SSH-Schlüssel während der Bereitstellung in die SSH-Schicht der VM-Skalierungsgruppe einfügen?

Wenn Sie für die virtuellen Computer nur mit einem öffentlichen SSH-Schlüssel bereitstellen, müssen Sie die öffentlichen Schlüssel nicht in Key Vault speichern. Öffentliche Schlüssel sind nicht geheim.

Sie können öffentliche SSH-Schlüssel bei der Erstellung eines virtuellen Linux-Computers im Nur-Text-Format angeben:

```json
"linuxConfiguration": {
    "ssh": {
        "publicKeys": [
            {
                "path": "path",
                "keyData": "publickey"
            }
        ]
    }
}
```

linuxConfiguration-Elementname | Erforderlich | type | BESCHREIBUNG
--- | --- | --- | ---
ssh | Nein | Collection | Gibt die SSH-Schlüsselkonfiguration für ein Linux-Betriebssystem an.
path | Ja | String | Gibt den Linux-Dateipfad für die SSH-Schlüssel oder das Zertifikat an.
keyData | Ja | String | Gibt einen Base64-codierten öffentlichen SSH-Schlüssel an.

Ein Beispiel finden Sie in der [GitHub-Schnellstartvorlage „101-vm-sshkey“](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).

### <a name="when-i-run-update-azvmss-after-adding-more-than-one-certificate-from-the-same-key-vault-i-see-the-following-message"></a>Wenn ich `Update-AzVmss` ausführe, nachdem ich mehrere Zertifikate aus dem gleichen Schlüsseltresor hinzugefügt habe, erhalte ich die folgende Meldung:

>Update-AzVmss: Die Liste „secret“ enthält wiederholte Instanzen von „/subscriptions/\<my-subscription-id>/resourceGroups/internal-rg-dev/providers/Microsoft.KeyVault/vaults/internal-keyvault-dev“, was nicht zulässig ist.

Dieser Fall kann eintreten, wenn Sie versuchen, den gleichen Tresor erneut hinzuzufügen, anstatt ein neues Zertifikat für den vorhandenen Quelltresor zu verwenden. Die Befehl `Add-AzVmssSecret` funktioniert nicht ordnungsgemäß, wenn Sie zusätzliche Geheimnisse hinzufügen.

Aktualisieren Sie die Liste „$vmss.properties.osProfile.secrets[0].vaultCertificates“, um weitere Geheimnisse aus dem gleichen Schlüsseltresor hinzuzufügen.

Die erwartete Eingabestruktur finden Sie unter [Create or update a set](/rest/api/compute/virtualmachinescalesets/createorupdate) (Erstellen oder Aktualisieren einer Gruppe).

Suchen Sie im Schlüsseltresor das Geheimnis im VM-Skalierungsgruppenobjekt. Fügen Sie anschließend Ihren Zertifikatsverweis (URL und Name des Geheimnisspeichers) der dem Tresor zugeordneten Liste hinzu.

> [!NOTE]
> Derzeit können Zertifikate nicht mithilfe der VM-Skalierungsgruppen-API von virtuellen Computern entfernt werden.
>

Neue virtuelle Computer verfügen nicht über das alte Zertifikat. Virtuelle Computer, die über das Zertifikat verfügen und bereits bereitgestellt wurden, besitzen allerdings das alte Zertifikat.

### <a name="can-i-push-certificates-to-the-virtual-machine-scale-set-without-providing-the-password-when-the-certificate-is-in-the-secret-store"></a>Kann ich Zertifikate mithilfe von Push ohne Angabe des Kennworts an die VM-Skalierungsgruppe übertragen, wenn sich das Zertifikat im Geheimnisspeicher befindet?

In Skripts müssen keine hartcodierten Kennwörter verwendet werden. Sie können dynamisch Kennwörter mit den Berechtigungen abrufen, die Sie zum Ausführen des Bereitstellungsskripts verwenden. Wenn Sie über ein Skript verfügen, das ein Zertifikat aus dem Geheimnisspeicher-Schlüsseltresor verschiebt, gibt der Geheimnisspeicherbefehl `get certificate` auch das Kennwort der PFX-Datei aus.

### <a name="how-does-the-secrets-property-of-virtualmachineprofileosprofile-for-a-virtual-machine-scale-set-work-why-do-i-need-the-sourcevault-value-when-i-have-to-specify-the-absolute-uri-for-a-certificate-by-using-the-certificateurl-property"></a>Wie funktioniert die Secrets-Eigenschaft von „virtualMachineProfile.osProfile“ für eine VM-Skalierungsgruppe? Warum benötige ich den sourceVault-Wert, wenn ich den absoluten URI für ein Zertifikat mithilfe der certificateUrl-Eigenschaft angeben muss?

In der Secrets-Eigenschaft des Betriebssystemprofils muss ein WinRM-Zertifikatsverweis (Windows Remote Management) vorhanden sein.

Die Angabe des Quelltresors dient zur Erzwingung von ACL-Richtlinien (Access Control List; Zugriffssteuerungsliste) aus dem Azure Cloud Service-Modell eines Benutzers. Ohne Angabe des Quelltresors könnten Benutzer über einen Anbieter von Computeressourcen (Compute Resource Provider, CRP) auch ohne entsprechende Berechtigungen Geheimnisse in einem Schlüsseltresor bereitstellen oder darauf zugreifen. ACLs sind sogar für nicht vorhandene Ressourcen vorhanden.

Wenn Sie eine falsche Quelltresor-ID und eine gültige Schlüsseltresor-URL angeben, tritt beim Abfragen des Vorgangs ein Fehler auf.

### <a name="if-i-add-secrets-to-an-existing-virtual-machine-scale-set-are-the-secrets-injected-into-existing-vms-or-only-into-new-ones"></a>Wenn ich einer vorhandenen VM-Skalierungsgruppe Geheimnisse hinzufüge, werden diese in vorhandene virtuelle Computer eingefügt oder nur in neue?

Zertifikate werden allen Ihren virtuellen Computern hinzugefügt. Das gilt auch für bereits vorhandene virtuelle Computer. Wenn die upgradePolicy-Eigenschaft Ihrer VM-Skalierungsgruppe auf **Manuell** festgelegt ist, wird das Zertifikat dem virtuellen Computer hinzugefügt, wenn Sie ein manuelles Update auf dem virtuellen Computer ausführen.

### <a name="where-do-i-put-certificates-for-linux-vms"></a>Wohin platziere ich Zertifikate für virtuelle Linux-Computer?

Eine Anleitung zum Bereitstellen von Zertifikaten für virtuelle Linux-Computer finden Sie unter [Deploy Certificates to VMs from customer-managed Key Vault](/archive/blogs/kv/deploy-certificates-to-vms-from-customer-managed-key-vault) (Bereitstellen von Zertifikaten für virtuelle Computer über eine vom Kunden verwaltete Key Vault-Instanz).

### <a name="how-do-i-add-a-new-vault-certificate-to-a-new-certificate-object"></a>Wie füge ich einem neuen Zertifikatobjekt ein neues Tresorzertifikat hinzu?

Sehen Sie sich das folgende PowerShell-Beispiel an, wenn Sie einem vorhandenen Geheimnis ein Tresorzertifikat hinzufügen möchten. Verwenden Sie nur ein einzelnes Geheimnisobjekt.

```powershell
$newVaultCertificate = New-AzVmssVaultCertificateConfig -CertificateStore MY -CertificateUrl https://sansunallapps1.vault.azure.net:443/secrets/dg-private-enc/55fa0332edc44a84ad655298905f1809

$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($newVaultCertificate)

Update-AzVmss -VirtualMachineScaleSet $vmss -ResourceGroup $rg -Name $vmssName
```

### <a name="what-happens-to-certificates-if-you-reimage-a-vm"></a>Was geschieht mit Zertifikaten, wenn Sie für einen virtuellen Computer ein Reimaging durchführen?

Wenn Sie für einen virtuellen Computer ein Reimaging durchführen, werden die Zertifikate gelöscht. Beim Reimaging wird der gesamte Betriebssystemdatenträger gelöscht.

### <a name="what-happens-if-you-delete-a-certificate-from-the-key-vault"></a>Was geschieht, wenn Sie ein Zertifikat aus dem Schlüsseltresor löschen?

Wenn das Geheimnis aus dem Schlüsseltresor gelöscht wird und Sie anschließend für alle Ihre virtuellen Computer `stop deallocate` ausführen und diese dann neu starten, tritt ein Fehler auf. Der Grund für den Fehler: Der CRP muss das Geheimnis aus dem Schlüsseltresor abrufen, das ist aber nicht möglich. In diesem Szenario können Sie die Zertifikate aus dem VM-Skalierungsgruppenmodell löschen.

Kundengeheimnisse werden von der CRP-Komponente nicht speichert. Wenn Sie `stop deallocate` für alle virtuellen Computer in der VM-Skalierungsgruppe ausführen, wird der Cache gelöscht. In diesem Szenario werden die Geheimnisse aus dem Schlüsseltresor abgerufen.

Beim horizontalen Hochskalieren tritt dieses Problem nicht auf, da eine zwischengespeicherte Kopie des Geheimnisses in Azure Service Fabric (Einzelfabric-Mandantenmodell) vorhanden ist.

### <a name="why-do-i-have-to-specify-the-certificate-version-when-i-use-key-vault"></a>Warum muss ich bei Verwendung von Key Vault die Zertifikatversion angeben?

Die Angabe der Zertifikatversion ist bei Verwendung von Key Vault erforderlich, um dem Benutzer gegenüber deutlich zu machen, welches Zertifikat auf den virtuellen Computern bereitgestellt wird.

Wenn Sie einen virtuellen Computer erstellen und anschließend Ihr Geheimnis im Schlüsseltresor aktualisieren, wird das neue Zertifikat nicht auf Ihre virtuellen Computer heruntergeladen. Ihre virtuellen Computer scheinen jedoch darauf zu verweisen, und neue virtuelle Computer erhalten das neue Geheimnis. Um dies zu vermeiden, müssen Sie eine Geheimnisversion verweisen.

### <a name="my-team-works-with-several-certificates-that-are-distributed-to-us-as-cer-public-keys-what-is-the-recommended-approach-for-deploying-these-certificates-to-a-virtual-machine-scale-set"></a>Mein Team arbeitet mit mehreren Zertifikaten, die uns als öffentliche CER-Schlüssel zur Verfügung gestellt werden. Wie stelle ich diese Zertifikate am besten für eine VM-Skalierungsgruppe bereit?

Wenn Sie öffentliche CER-Schlüssel für eine VM-Skalierungsgruppe bereitstellen möchten, können Sie eine PFX-Datei erstellen, die nur CER-Dateien enthält. Verwenden Sie hierzu `X509ContentType = Pfx`. Laden Sie beispielsweise die CER-Datei als Objekt vom Typ „x509Certificate2“ in C# oder PowerShell, und rufen Sie dann die Methode auf.

Weitere Informationen finden Sie unter [X509Certificate.Export Method (X509ContentType, String)](/dotnet/api/system.security.cryptography.x509certificates.x509certificate.export?view=netcore-3.1#system_security_cryptography_x509certificates_x509certificate_export_system_security_cryptography_x509certificates_x509contenttype_system_string_) (X509Certificate.Export-Methode (X509ContentType, String)).

### <a name="how-do-i-pass-in-certificates-as-base64-strings"></a>Wie übergebe ich Zertifikate als base64-Zeichenfolgen?

Sie können das Übergeben eines Zertifikats als Base64-Zeichenfolge emulieren, indem Sie die neueste URL mit Versionsangabe in einer Resource Manager-Vorlage extrahieren. Schließen Sie in Ihre Resource Manager-Vorlage die folgende JSON-Eigenschaft ein:

```json
"certificateUrl": "[reference(resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults/secrets', parameters('vaultName'), parameters('secretName')), '2015-06-01').secretUriWithVersion]"
```

### <a name="do-i-have-to-wrap-certificates-in-json-objects-in-key-vaults"></a>Muss ich Zertifikate in Schlüsseltresoren mit JSON-Objekten umschließen?

In VM-Skalierungsgruppen und auf virtuellen Computern müssen Zertifikate mit JSON-Objekten umschlossen werden.

Wir unterstützen auch den Inhaltstyp „application/x-pkcs12“.

CER-Dateien werden derzeit nicht unterstützt. Zu verwendende CER-Dateien müssen in PFX-Container exportiert werden.



## <a name="compliance-and-security"></a>Konformität und Sicherheit

### <a name="are-virtual-machine-scale-sets-pci-compliant"></a>Sind VM-Skalierungsgruppen PCI-konform?

Bei VM-Skalierungsgruppen handelt es sich um eine dünne API-Schicht über dem CRP. Beide Komponenten sind Teil der Computeplattform in der Azure-Dienststruktur.

VM-Skalierungsgruppen sind hinsichtlich der Konformität ein grundlegender Bestandteil der Azure-Computeplattform. Sie teilen sich Team, Tools, Prozesse, Bereitstellungsmethode, Sicherheitskontrollen, JIT-Kompilierung, Überwachung, Warnungen usw. mit dem CRP. VM-Skalierungsgruppen sind PCI-konform (Payment Card Industry), da der CRP Teil des aktuellen PCI DSS-Nachweises (Data Security Standard) ist.

Weitere Informationen finden Sie im [Microsoft Trust Center](https://www.microsoft.com/TrustCenter/Compliance/PCI).

### <a name="does-managed-identities-for-azure-resources-work-with-virtual-machine-scale-sets"></a>Können [verwaltete Identitäten für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/overview.md) zusammen mit einer VM-Skalierungsgruppe verwendet werden?

Ja. Einige MSI-Beispielvorlagen finden Sie in den Azure-Schnellstartvorlagen für [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi) und [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi).

## <a name="deleting"></a>Wird gelöscht

### <a name="will-the-locks-i-set-in-place-on-virtual-machine-scale-set-instances-be-respected-when-deleting-instances"></a>Werden die Sperren, die ich für VM-Skalierungsgruppeninstanzen festgelegt habe, beim Löschen von Instanzen beachtet?

Im Azure-Portal haben Sie die Möglichkeit, eine einzelne Instanz zu löschen oder mehrere Instanzen auszuwählen und gleichzeitig in einem Schritt zu löschen. Wenn Sie versuchen, eine einzelne gesperrte Instanz zu löschen, wird die Sperre beachtet, sodass Sie die Instanz nicht löschen können. Wenn Sie mehrere Instanzen auswählen und eine dieser Instanzen gesperrt wurde, werden die Sperren nicht beachtet, sodass alle ausgewählten Instanzen gelöscht werden.

In der Azure-Befehlszeilenschnittstelle können Sie nur eine einzelne Instanz löschen. Wenn Sie versuchen, eine einzelne gesperrte Instanz zu löschen, wird die Sperre beachtet, sodass Sie die betreffende Instanz nicht löschen können.

## <a name="extensions"></a>Erweiterungen

### <a name="how-do-i-delete-a-virtual-machine-scale-set-extension"></a>Wie lösche ich eine VM-Skalierungsgruppenerweiterung?

Verwenden Sie zum Löschen einer VM-Skalierungsgruppenerweiterung das folgende PowerShell-Beispiel:

```powershell
$vmss = Get-AzVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName"

$vmss=Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "extensionName"

Update-AzVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" -VirtualMacineScaleSet $vmss
```

Den Wert für „extensionName“ finden Sie in `$vmss`.

### <a name="is-there-a-virtual-machine-scale-set-template-example-that-integrates-with-azure-monitor-logs"></a>Gibt es ein Beispiel für eine VM-Skalierungsgruppenvorlage, die in Azure Monitor-Protokolle integriert ist?

Ein Beispiel für eine VM-Skalierungsgruppenvorlage, die in Azure Monitor-Protokolle integriert ist, finden Sie unter [Deploy Azure Service Fabric Cluster and enable monitoring using Azure Monitor logs](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/ServiceFabric) (Bereitstellen eines Azure Service Fabric-Clusters und Aktivieren der Überwachung mithilfe von Azure Monitor-Protokollen) im zweiten Beispiel.

### <a name="how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set"></a>Wie füge ich allen virtuellen Computern in meiner VM-Skalierungsgruppe eine Erweiterung hinzu?

Wenn die Aktualisierungsrichtlinie auf **Automatisch** festgelegt ist, werden durch die erneute Bereitstellung der Vorlage mit den neuen Erweiterungseigenschaften alle virtuellen Computer aktualisiert.

Wenn die Aktualisierungsrichtlinie auf **Manuell** festgelegt ist, aktualisieren Sie zunächst die Erweiterung, und aktualisieren Sie anschließend manuell alle Instanzen auf Ihren virtuellen Computern.

### <a name="if-the-extensions-associated-with-an-existing-virtual-machine-scale-set-are-updated-are-existing-vms-affected"></a>Hat die Aktualisierung der Erweiterungen, die einer vorhandenen VM-Skalierungsgruppe zugeordnet sind, Auswirkungen auf bereits vorhandene virtuelle Computer?

Wenn die Erweiterungsdefinition im VM-Skalierungsgruppenmodell aktualisiert wird und die upgradePolicy-Eigenschaft auf **Automatisch** festgelegt ist, werden die virtuellen Computer aktualisiert. Ist die upgradePolicy-Eigenschaft auf **Manuell** festgelegt, werden Erweiterungen als nicht dem Modell entsprechend gekennzeichnet.

### <a name="are-extensions-run-again-when-an-existing-machine-is-service-healed-or-reimaged"></a>Werden Erweiterungen erneut ausgeführt, wenn ein vorhandener Computer vom Dienst repariert oder ein Reimaging für ihn durchgeführt wird?

Wenn ein vorhandener virtueller Computer vom Dienst repariert wird, wird dies wie ein Neustart behandelt, und die Erweiterungen werden nicht erneut ausgeführt. Wird ein Reimaging für eine VM durchgeführt, ist der Prozess ähnlich wie das Ersetzen des Betriebssystemlaufwerks durch das Quellimage. Sämtliche Spezialisierungen aus dem neuesten Modell (etwa Erweiterungen) werden erneut ausgeführt.

### <a name="how-do-i-join-a-virtual-machine-scale-set-to-an-active-directory-domain"></a>Wie füge ich eine VM-Skalierungsgruppe einer Active Directory-Domäne hinzu?

Eine VM-Skalierungsgruppe kann einer Active Directory-Domäne durch Definieren einer Erweiterung hinzugefügt werden.

Verwenden Sie zum Definieren einer Erweiterung die JsonADDomainExtension-Eigenschaft:

```json
"extensionProfile": {
    "extensions": [
        {
            "name": "joindomain",
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "JsonADDomainExtension",
                "typeHandlerVersion": "1.3",
                "settings": {
                    "Name": "[parameters('domainName')]",
                    "OUPath": "[variables('ouPath')]",
                    "User": "[variables('domainAndUsername')]",
                    "Restart": "true",
                    "Options": "[variables('domainJoinOptions')]"
                },
                "protectedsettings": {
                    "Password": "[parameters('domainJoinPassword')]"
                }
            }
        }
    ]
}
```

### <a name="my-virtual-machine-scale-set-extension-is-trying-to-install-something-that-requires-a-reboot"></a>Meine VM-Skalierungsgruppenerweiterung versucht, etwas installieren, das einen Neustart erfordert.

Wenn Ihre VM-Skalierungsgruppenerweiterung versucht, etwas installieren, das einen Neustart erfordert, können Sie die Erweiterung „Azure Automation Desired State Configuration (Automation DSC)“ verwenden. Bei Verwendung des Betriebssystems Windows Server 2012 R2 führt Azure das WMF 5.0-Setup (Windows Management Framework) aus, startet neu und setzt dann die Konfiguration fort.

### <a name="how-do-i-turn-on-antimalware-in-my-virtual-machine-scale-set"></a>Wie aktiviere ich Antischadsoftware in meiner VM-Skalierungsgruppe?

Antischadsoftware können Sie in Ihrer VM-Skalierungsgruppe mithilfe des folgenden PowerShell-Beispiels aktiveren:

```powershell
$rgname = 'autolap'
$vmssname = 'autolapbr'
$location = 'eastus'

# Retrieve the most recent version number of the extension.
$allVersions= (Get-AzVMExtensionImage -Location $location -PublisherName "Microsoft.Azure.Security" -Type "IaaSAntimalware").Version
$versionString = $allVersions[($allVersions.count)-1].Split(".")[0] + "." + $allVersions[($allVersions.count)-1].Split(".")[1]

$VMSS = Get-AzVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname
echo $VMSS
Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "IaaSAntimalware" -Publisher "Microsoft.Azure.Security" -Type "IaaSAntimalware" -TypeHandlerVersion $versionString
Update-AzVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $VMSS
```

### <a name="how-do-i-execute-a-custom-script-thats-hosted-in-a-private-storage-account"></a>Wie führe ich ein benutzerdefiniertes Skript aus, das in einem privaten Speicherkonto gehostet wird?

Zum Ausführen eines benutzerdefinierten Skripts, das in einem privaten Speicherkonto gehostet wird, müssen Sie geschützte Einstellungen mit dem Speicherkontoschlüssel und -name einrichten. Weitere Informationen finden Sie unter [Benutzerdefinierte Skripterweiterung](../virtual-machines/extensions/custom-script-windows.md?toc=/azure/virtual-machines/windows/toc.json#property-managedidentity).

## <a name="passwords"></a>Kennwörter

### <a name="how-do-i-reset-the-password-for-vms-in-my-virtual-machine-scale-set"></a>Wie setze ich das Kennwort für virtuelle Computer in meiner VM-Skalierungsgruppe zurück?

Es gibt zwei Hauptmethoden zum Ändern des Kennworts für virtuelle Computer in Skalierungsgruppen.

- Ändern Sie das Modell der VM-Skalierungsgruppe direkt. Verfügbar mit API 2017-12-01 oder höher.

    Aktualisieren Sie die Administratoranmeldeinformationen direkt im Skalierungsgruppenmodell (z. B. mit dem Azure-Ressourcen-Explorer, mit PowerShell oder mit CLI). Sobald die Skalierungsgruppe aktualisiert wurde, werden für alle neuen virtuellen Computer die neuen Anmeldeinformationen verwendet. Vorhandene virtuelle Computer erhalten die neuen Anmeldeinformationen nur, wenn für sie ein Reimaging durchgeführt wird.

- Setzen Sie das Kennwort mit den Zugriffserweiterungen für virtuelle Computer zurück. Achten Sie darauf, den Kennwortanforderungen zu folgen, wie [hier](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm) beschrieben.

    Verwenden Sie das folgende PowerShell-Beispiel:

    ```powershell
    $vmssName = "myvmss"
    $vmssResourceGroup = "myvmssrg"
    $publicConfig = @{"UserName" = "newuser"}
    $privateConfig = @{"Password" = "********"}

    $extName = "VMAccessAgent"
    $publisher = "Microsoft.Compute"
    $vmss = Get-AzVmss -ResourceGroupName $vmssResourceGroup -VMScaleSetName $vmssName
    $vmss = Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion "2.0" -AutoUpgradeMinorVersion $true
    Update-AzVmss -ResourceGroupName $vmssResourceGroup -Name $vmssName -VirtualMachineScaleSet $vmss
    ```

## <a name="networking"></a>Netzwerk

### <a name="is-it-possible-to-assign-a-network-security-group-nsg-to-a-scale-set-so-that-it-applies-to-all-the-vm-nics-in-the-set"></a>Kann einer Skalierungsgruppe eine Netzwerksicherheitsgruppe (NSG) zugewiesen werden, sodass diese für alle VM-NICs in der Gruppe gilt?

Ja. Eine Netzwerksicherheitsgruppe kann direkt auf eine Skalierungsgruppe angewendet werden, indem im Abschnitt „networkInterfaceConfigurations“ des Netzwerkprofils darauf verwiesen wird. Beispiel:

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "nic1",
            "properties": {
                "primary": "true",
                "ipConfigurations": [
                    {
                        "name": "ip1",
                        "properties": {
                            "subnet": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                            },
                            "loadBalancerInboundNatPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                                }
                            ],
                            "loadBalancerBackendAddressPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                                }
                            ]
                        }
                    }
                ],
                "networkSecurityGroup": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('nsgName'))]"
                }
            }
        }
    ]
}
```

### <a name="how-do-i-do-a-vip-swap-for-virtual-machine-scale-sets-in-the-same-subscription-and-same-region"></a>Wie führe ich ein VIP-Swap für VM-Skalierungsgruppen im gleichen Abonnement und in der gleichen Region aus?

Wenn Sie zwei VM-Skalierungsgruppen mit Front-Ends von Azure Load Balancer haben und diese sich im gleichen Abonnement und der gleichen Region befinden, können Sie die Zuordnung der öffentlichen IP-Adressen aufheben und der jeweils anderen zuordnen. Ein Beispiel finden Sie unter [VIP Swap: Blue-green deployment in Azure Resource Manager](https://msftstack.wordpress.com/2017/02/24/vip-swap-blue-green-deployment-in-azure-resource-manager/) (VIP-Tausch: Blaugrünbereitstellung in Azure Resource Manager). Dies führt jedoch wahrscheinlich zu Verzögerungen, während Ressourcen auf Netzwerkebene zugeordnet werden bzw. während ihre Zuordnungen aufgehoben werden. Eine schnellere Option ist die Verwendung von Azure Application Gateway mit zwei Back-End-Pools und einer Routingregel. Alternativ können Sie Ihre Anwendung mit [Azure App Service](https://azure.microsoft.com/services/app-service/) hosten. Dieser Dienst bietet Unterstützung für das schnelle Wechseln zwischen Staging- und Produktionsslots.

### <a name="how-do-i-specify-a-range-of-private-ip-addresses-to-use-for-static-private-ip-address-allocation"></a>Wie gebe ich einen Bereich privater IP-Adressen für die statische private IP-Adresszuordnung an?

IP-Adressen werden in einem von Ihnen angegebenen Subnetz ausgewählt.

Die IP-Adresszuordnungsmethode für VM-Skalierungsgruppen ist immer dynamisch. Das bedeutet aber nicht, dass sich diese IP-Adressen ändern können. In diesem Fall bedeutet „dynamisch“ lediglich, dass Sie die IP-Adresse nicht in einer PUT-Anforderung angeben. Verwenden Sie zum Angeben der statischen Gruppe das Subnetz.

### <a name="how-do-i-deploy-a-virtual-machine-scale-set-to-an-existing-azure-virtual-network"></a>Wie stelle ich eine VM-Skalierungsgruppe für ein vorhandenes virtuelles Azure-Netzwerk bereit?

Informationen zum Bereitstellen einer VM-Skalierungsgruppe für ein vorhandenes virtuelles Azure-Netzwerk finden Sie unter [Deploy a VM Scale Set into an existing vnet](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-existing-vnet) (Bereitstellen einer VM-Skalierungsgruppe für ein vorhandenes VNET).

### <a name="can-i-use-scale-sets-with-accelerated-networking"></a>Kann ich Skalierungsgruppen mit beschleunigten Netzwerken verwenden?

Ja. Um beschleunigte Netzwerke zu verwenden, legen Sie „enableAcceleratedNetworking“ in den Einstellungen „networkInterfaceConfigurations“ Ihrer Skalierungsgruppe auf „TRUE“ fest. Beispiel:

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "niconfig1",
            "properties": {
                "primary": true,
                "enableAcceleratedNetworking" : true,
                "ipConfigurations": [
                ]
            }
        }
    ]
}
```

### <a name="how-can-i-configure-the-dns-servers-used-by-a-scale-set"></a>Wie kann ich den DNS-Server konfigurieren, der von einer Skalierungsgruppe verwendet wird?

Um eine VM-Skalierungsgruppe mit einer benutzerdefinierten DNS-Konfiguration zu erstellen, fügen Sie ein dnsSettings-JSON-Paket im Abschnitt „networkInterfaceConfigurations“ zur Skalierungsgruppe hinzu. Beispiel:

```json
    "dnsSettings":{
        "dnsServers":["10.0.0.6", "10.0.0.5"]
    }
```

### <a name="how-can-i-configure-a-scale-set-to-assign-a-public-ip-address-to-each-vm"></a>Wie kann ich eine Skalierungsgruppe so konfigurieren, dass sie jeder VM eine öffentliche IP-Adresse zuordnet?

Um eine VM-Skalierungsgruppe zu erstellen, die jeder VM eine öffentliche IP-Adresse zuweist, stellen Sie sicher, dass die API-Version der Ressource „Microsoft.Compute/virtualMachineScaleSets“ „2017-03-30“ ist, und fügen Sie ein _publicipaddressconfiguration_-JSON-Paket im Abschnitt „ipConfigurations“ der Skalierungsgruppe hinzu. Beispiel:

```json
    "publicipaddressconfiguration": {
        "name": "pub1",
        "properties": {
        "idleTimeoutInMinutes": 15
        }
    }
```

### <a name="can-i-configure-a-scale-set-to-work-with-multiple-application-gateways"></a>Kann ich eine Skalierungsgruppe für die Verwendung mit mehreren Application Gateway-Instanzen konfigurieren?

Ja. Sie können die Ressourcen-IDs für mehrere Application Gateway-Back-End-Adresspools zur Liste _applicationGatewayBackendAddressPools_ im Abschnitt _ipConfigurations_ des Skalierungsgruppen-Netzwerkprofils hinzufügen.

## <a name="scale"></a>Skalieren

### <a name="in-what-case-would-i-create-a-virtual-machine-scale-set-with-fewer-than-two-vms"></a>In welchem Fall erstelle ich eine VM-Skalierungsgruppe mit weniger als zwei virtuellen Computern?

Ein Grund für die Erstellung einer VM-Skalierungsgruppe mit weniger als zwei virtuellen Computern wäre die Verwendung der elastischen Eigenschaften einer VM-Skalierungsgruppe. So können Sie beispielsweise eine VM-Skalierungsgruppe ohne virtuelle Computer bereitstellen, um Ihre Infrastruktur zu definieren, ohne die laufenden Kosten für virtuelle Computer zu bezahlen. Wenn Sie dann für die Bereitstellung von virtuellen Computern bereit sind, können Sie die Kapazität der VM-Skalierungsgruppe auf die Anzahl von Produktionsinstanzen erhöhen.

Ein weiterer Grund für die Erstellung einer VM-Skalierungsgruppe mit weniger als zwei virtuellen Computern wäre, wenn Sie sich weniger Gedanken um die Verfügbarkeit machen, sondern eher auf die Verwendung einer Verfügbarkeitsgruppe mit diskreten virtuellen Computern Wert legen. VM-Skalierungsgruppen ermöglichen die Verwendung undifferenzierter, austauschbarer Computeeinheiten. Diese Einheitlichkeit ist ein wichtiges Unterscheidungsmerkmal von VM-Skalierungsgruppen im Vergleich zu Verfügbarkeitsgruppen. Bei vielen zustandslosen Workloads werden keine einzelnen Einheiten nachverfolgt. Sie können auf eine einzelne Computeeinheit herunterskalieren, wenn die Workload zurückgeht, und wieder auf viele Computeeinheiten hochskalieren, wenn sich die Workload erhöht.

### <a name="how-do-i-change-the-number-of-vms-in-a-virtual-machine-scale-set"></a>Wie ändere ich die Anzahl von virtuellen Computern in einer VM-Skalierungsgruppe?

Um die Anzahl der virtuellen Computer in einer VM-Skalierungsgruppe im Azure-Portal zu ändern, klicken Sie im Eigenschaftsbereich der VM-Skalierungsgrupe auf das Blatt „Skalierung“, und verwenden Sie den Schieberegler.

### <a name="how-do-i-define-custom-alerts-for-when-certain-thresholds-are-reached"></a>Wie definiere ich benutzerdefinierte Warnungen für das Erreichen bestimmter Schwellenwerte?

Warnungen für angegebene Schwellenwerte können mit einer gewissen Flexibilität gehandhabt werden. So können Sie beispielsweise benutzerdefinierte Webhooks definieren. Das folgende Webhookbeispiel stammt aus einer Resource Manager-Vorlage:

```json
{
    "type": "Microsoft.Insights/autoscaleSettings",
    "apiVersion": "[variables('insightsApi')]",
    "name": "autoscale",
    "location": "[parameters('resourceLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]"
    ],
    "properties": {
        "name": "autoscale",
        "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
        "enabled": true,
        "notifications": [
            {
                "operation": "Scale",
                "email": {
                    "sendToSubscriptionAdministrator": true,
                    "sendToSubscriptionCoAdministrators": true,
                    "customEmails": [
                        "youremail@address.com"
                    ]
                },
                "webhooks": [
                    {
                        "serviceUri": "<service uri>",
                        "properties": {
                            "key1": "custommetric",
                            "key2": "scalevmss"
                        }
                    }
                ]
            }
        ]
    }
}
```


## <a name="patching-and-operations"></a>Patching und Verfahren

### <a name="can-i-create-a-scale-set-in-an-existing-resource-group"></a>Kann ich eine Skalierungsgruppe in einer vorhandenen Ressourcengruppe erstellen?

Ja, Sie können eine Skalierungsgruppe in einer vorhandenen Ressourcengruppe erstellen.

### <a name="can-i-move-a-scale-set-to-another-resource-group"></a>Kann ich eine Skalierungsgruppe in eine andere Ressourcengruppe verschieben?

Ja, Sie können Skalierungsgruppenressourcen in ein neues Abonnement oder eine neue Ressourcengruppe verschieben.

### <a name="how-to-i-update-my-virtual-machine-scale-set-to-a-new-image-how-do-i-manage-patching"></a>Wie kann ich meine VM-Skalierungsgruppe auf ein neues Image aktualisieren? Wie verwalte das Patchen?

Informationen zum Aktualisieren Ihrer VM-Skalierungsgruppe auf ein neues Image sowie zum Verwalten des Patchens finden Sie unter [Upgraden einer VM-Skalierungsgruppe](./virtual-machine-scale-sets-upgrade-scale-set.md).

### <a name="can-i-use-the-reimage-operation-to-reset-a-vm-without-changing-the-image-that-is-i-want-reset-a-vm-to-factory-settings-rather-than-to-a-new-image"></a>Kann ich einen virtuellen Computer mittels Reimaging zurücksetzen, ohne das Image zu ändern? (Ich möchte einen virtuellen Computer auf die Werkseinstellungen zurücksetzen, nicht auf ein neues Image.)

Ja. Sie können einen virtuellen Computer mittels Reimaging zurücksetzen, ohne das Image zu ändern. Falls Ihre VM-Skalierungsgruppe allerdings auf ein Plattformimage mit `version = latest` verweist, kann Ihr virtueller Computer auf ein höheres Betriebssystemimage aktualisiert werden, wenn Sie das `reimage` aufrufen.

### <a name="is-it-possible-to-integrate-scale-sets-with-azure-monitor-logs"></a>Ist es möglich, Skalierungsgruppen in Azure Monitor-Protokolle zu integrieren?

Ja, Sie können dazu die Azure Monitor-Erweiterung auf den virtuellen Computern der Skalierungsgruppen installieren. Hier folgt ein Beispiel für die Azure-Befehlszeilenschnittstelle:

```azurecli
az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group Team-03 --vmss-name nt01 --settings "{'workspaceId': '<your workspace ID here>'}" --protected-settings "{'workspaceKey': '<your workspace key here'}"
```

Die erforderlichen Werte für „workspaceId“ und „workspaceKey“ finden Sie im Log Analytics-Arbeitsbereich im Azure-Portal. Klicken Sie auf der Übersichtsseite auf die Kachel „Einstellungen“. Klicken Sie oben auf die Registerkarte „Verbundene Datenquellen“.

> [!NOTE]
> Wenn die Skalierungsgruppe _upgradePolicy_ auf „Manual“ festgelegt ist, müssen Sie die Erweiterung auf alle virtuellen Computer in der Gruppe anwenden, indem Sie für diese ein Upgrade durchführen. Für die Befehlszeilenschnittstelle wäre dies _az vmss update-instances_.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="troubleshooting"></a>Problembehandlung

### <a name="how-do-i-turn-on-boot-diagnostics"></a>Wie aktiviere ich die Startdiagnose?

Erstellen Sie zum Aktivieren der Startdiagnose zunächst ein Speicherkonto. Fügen Sie dann den folgenden JSON-Block unter **virtualMachineProfile** in die VM-Skalierungsgruppe ein, und aktualisieren Sie die VM-Skalierungsgruppe:

```json
"diagnosticsProfile": {
    "bootDiagnostics": {
        "enabled": true,
        "storageUri": "http://yourstorageaccount.blob.core.windows.net"
    }
}
```

Beim Erstellen eines neuen virtuellen Computers zeigt die InstanceView-Eigenschaft des virtuellen Computers die Details für den Screenshot usw. an. Hier sehen Sie ein Beispiel:

```json
"bootDiagnostics": {
    "consoleScreenshotBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.screenshot.bmp",
    "serialConsoleLogBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.serialconsole.log"
}
```

## <a name="virtual-machine-properties"></a>Eigenschaften für virtuelle Computer

### <a name="how-do-i-get-property-information-for-each-vm-without-making-multiple-calls-for-example-how-would-i-get-the-fault-domain-for-each-of-the-100-vms-in-my-virtual-machine-scale-set"></a>Wie erhalte ich Informationen zu den Eigenschaften für die einzelnen virtuellen Computer, ohne mehrere Aufrufe auszuführen? Wie kann ich also beispielsweise die Fehlerdomäne für jeden der 100 virtuellen Computer in meiner VM-Skalierungsgruppe abrufen?

Wenn Sie Informationen zu den Eigenschaften für die einzelnen virtuellen Computer abrufen möchten, ohne mehrere Aufrufe auszuführen, können Sie `ListVMInstanceViews` im Rahmen eines REST-API-Vorgangs vom Typ `GET` für den folgenden Ressourcen-URI aufrufen:

/subscriptions/<Abonnement-ID>/resourceGroups/<Name der Ressourcengruppe>/providers/Microsoft.Compute/virtualMachineScaleSets/<Name der Skalierungsgruppe>/virtualMachines?$expand=instanceView&$select=instanceView

### <a name="can-i-pass-different-extension-arguments-to-different-vms-in-a-virtual-machine-scale-set"></a>Kann ich unterschiedliche Erweiterungsargumente an verschiedene virtuelle Computer in einer VM-Skalierungsgruppe übergeben?

Nein. Es ist nicht möglich, unterschiedliche Erweiterungsargumente an verschiedene virtuelle Computer in einer VM-Skalierungsgruppe zu übergeben. Erweiterungen können aber unter Berücksichtigung der eindeutigen Eigenschaften des ausführenden virtuellen Computers (beispielsweise des Computernamens) aktiv werden. Darüber hinaus können Erweiterungen Instanzmetadaten von http://169.254.169.254 abfragen, um weitere Informationen zu dem virtuellen Computer zu erhalten.

### <a name="why-are-there-gaps-between-my-virtual-machine-scale-set-vm-machine-names-and-vm-ids-for-example-0-1-3"></a>Warum gibt es Lücken zwischen den Namen der virtuellen Computer meiner VM-Skalierungsgruppe und den IDs virtueller Computer? Beispiel: 0, 1, 3...

Die Lücken zwischen den Namen der virtuellen Computer Ihrer VM-Skalierungsgruppe und den IDs virtueller Computer sind darauf zurückzuführen, dass die Eigenschaft **overprovision** Ihrer VM-Skalierungsgruppe auf den Standardwert **true** festgelegt ist. Wenn die Überbereitstellung auf **true** festgelegt ist, werden mehr virtuelle Computer als angefordert bereitgestellt. Zusätzliche virtuelle Computer werden anschließend gelöscht. Dies erhöht zwar die Bereitstellungszuverlässigkeit, geht jedoch zulasten fortlaufender Namen und zusammenhängender Regeln für die Netzwerkadressenübersetzung (Network Address Translation, NAT).

Die Eigenschaft kann auf **false** festgelegt werden. Bei kleinen VM-Skalierungsgruppen hat das keine nennenswerten Auswirkungen auf die Bereitstellungszuverlässigkeit.

### <a name="what-is-the-difference-between-deleting-a-vm-in-a-virtual-machine-scale-set-and-deallocating-the-vm-when-should-i-choose-one-over-the-other"></a>Was ist der Unterschied zwischen dem Löschen eines virtuellen Computers in einer VM-Skalierungsgruppe und dem Aufheben der Zuordnung des virtuellen Computers? Wann sollte ich was wählen?

Der Hauptunterschied zwischen dem Löschen eines virtuellen Computers in einer VM-Skalierungsgruppe und dem Aufheben der Zuordnung des virtuellen Computers besteht darin, dass bei `deallocate` die virtuellen Festplatten (Virtual Hard Disks, VHDs) nicht gelöscht werden. Das Ausführen von `stop deallocate` ist mit Speicherkosten verbunden. Im Anschluss finden Sie Gründe für die Verwendung der einzelnen Verfahren:

- Sie möchten keine Computekosten mehr bezahlen, aber den Datenträgerzustand der virtuellen Computer beibehalten.
- Sie möchten eine Gruppe virtueller Computer schneller starten als sich eine VM-Skalierungsgruppe aufskalieren lässt.
  - Im Zusammenhang mit diesem Szenario: Sie haben ggf. eine eigene Engine für die automatische Skalierung erstellt und möchten eine schnellere End-to-End-Skalierung erreichen.
- Sie verfügen über eine VM-Skalierungsgruppe, die ungleichmäßig auf Fehler- oder Updatedomänen verteilt ist. Dieser Fall kann eintreten, wenn Sie selektiv virtuelle Computer gelöscht haben oder virtuelle Computer nach einer Überbereitstellung gelöscht wurden. Wenn Sie `stop deallocate` und anschließend `start` für die VM-Skalierungsgruppe ausführen, werden die virtuellen Computer gleichmäßig auf Fehler- oder Updatedomänen verteilt.

### <a name="how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance"></a>Wie erstelle ich eine Momentaufnahme einer VM-Skalierungsgruppeninstanz?
Erstellen Sie eine Momentaufnahme aus einer Instanz einer VM-Skalierungsgruppe.

```azurepowershell-interactive
$rgname = "myResourceGroup"
$vmssname = "myVMScaleSet"
$Id = 0
$location = "East US"

$vmss1 = Get-AzVmssVM -ResourceGroupName $rgname -VMScaleSetName $vmssname -InstanceId $Id     
$snapshotconfig = New-AzSnapshotConfig -Location $location -AccountType Standard_LRS -OsType Windows -CreateOption Copy -SourceUri $vmss1.StorageProfile.OsDisk.ManagedDisk.id
New-AzSnapshot -ResourceGroupName $rgname -SnapshotName 'mySnapshot' -Snapshot $snapshotconfig
```

Erstellen Sie einen verwalteten Datenträger aus der Momentaufnahme.

```azurepowershell-interactive
$snapshotName = "mySnapshot"
$snapshot = Get-AzSnapshot -ResourceGroupName $rgname -SnapshotName $snapshotName  
$diskConfig = New-AzDiskConfig -AccountType Premium_LRS -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
$osDisk = New-AzDisk -Disk $diskConfig -ResourceGroupName $rgname -DiskName ($snapshotName + '_Disk')
```
