---
title: Aktualisieren eines Clusters, um den allgemeinen Namen des Zertifikats zu verwenden
description: Hier erfahren Sie, wie Sie ein Azure Service Fabric-Clusterzertifikat so konvertieren, dass keine fingerabdruckbasierten Deklarationen, sondern allgemeine Namen verwendet werden.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 013b8190390a4b05791b0a56072487f249956ec5
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92495207"
---
# <a name="convert-cluster-certificates-from-thumbprint-based-declarations-to-common-names"></a>Konvertieren von Clusterzertifikaten von der Verwendung fingerabdruckbasierter Deklarationen zur Verwendung allgemeiner Namen

Die Signatur eines Zertifikats (im Allgemeinen als Fingerabdruck bezeichnet) ist eindeutig. Ein durch einen Fingerabdruck deklariertes Clusterzertifikat bezieht sich auf eine bestimmte Instanz eines Zertifikats. Diese Besonderheit erschwert einen Zertifikatrollover und die Verwaltung allgemein. Jede Änderung erfordert die Orchestrierung von Upgrades des Clusters und der zugrunde liegenden Computerhosts.

Indem Sie die Zertifikatdeklarationen eines Azure Service Fabric-Clusters konvertieren, sodass diese nicht mehr auf Fingerabdrücken, sondern auf Deklarationen des allgemeinen Namens (Common Name, CN) des Zertifikatantragstellers basieren, vereinfachen Sie die Verwaltung erheblich. Insbesondere wird der Rollover eines Zertifikats vereinfacht, weil kein Clusterupgrade mehr erforderlich ist. In diesem Artikel wird beschrieben, wie Sie einen vorhandenen Cluster ohne Ausfallzeit in CN-basierte Deklarationen konvertieren.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="move-to-certificate-authority-signed-certificates"></a>Wechsel zu von einer Zertifizierungsstelle signierten Zertifikaten

Die Sicherheit eines Clusters, dessen Zertifikat durch einen Fingerabdruck deklariert ist, basiert auf der Tatsache, dass es unmöglich (oder zumindest berechnungstechnisch nicht durchführbar) ist, ein Zertifikat zu fälschen, das dieselbe Signatur besitzt wie ein anderes Zertifikat. In diesem Fall ist die Herkunft des Zertifikats weniger wichtig, daher sind selbstsignierte Zertifikate ausreichend.

Im Gegensatz dazu entspringt die Sicherheit eines Clusters, dessen Zertifikate durch einen allgemeinen Namen deklariert werden, dem impliziten Vertrauen, das der Clusterbesitzer in seinen Zertifikatanbieter setzt. Der Anbieter ist der PKI-Dienst (Public Key-Infrastruktur), der das Zertifikat ausgestellt hat. Das Vertrauen basiert u. a. auf den Zertifizierungsverfahren der PKI, der Überwachung und Abnahme der Betriebssicherheit des Anbieters durch andere vertrauenswürdige Parteien usw.

Der Clusterbesitzer muss außerdem genau wissen, welche Zertifizierungsstellen die Zertifikate ausstellen, da dies ein grundlegender Aspekt der Validierung von Zertifikaten anhand des Antragstellers ist. Dies bedeutet auch, dass selbstsignierte Zertifikate für diesen Zweck völlig ungeeignet sind. Buchstäblich jede Person kann ein Zertifikat mit einem bestimmten Antragsteller generieren.

Ein durch den allgemeinen Namen deklariertes Zertifikat wird in der Regel in folgenden Fällen als gültig betrachtet:

* Die Zertifikatkette kann erfolgreich erstellt werden.
* Der Antragsteller weist das erwartete CN-Element auf.
* Dem Aussteller (unmittelbar oder weiter oben in der Kette) wird von der Stelle vertraut, die die Validierung durchführt.

Service Fabric unterstützt die Deklaration von Zertifikaten anhand des CN auf zwei Arten:

* Mit *impliziten* Ausstellern. Dies bedeutet, dass die Kette in einem Vertrauensanker enden muss.
* Mit Ausstellern, die durch einen Fingerabdruck deklariert werden. Dies wird auch als „Thumbprint Pinning“ (Anheften des Fingerabdrucks) bezeichnet.

Weitere Informationen finden Sie unter [Auf dem allgemeinen Namen basierende Zertifikatvalidierungsdeklarationen](cluster-security-certificates.md#common-name-based-certificate-validation-declarations).

Um einen Cluster mithilfe eines per Fingerabdruck deklarierten, selbstsignierten Zertifikats zur Verwendung eines allgemeinen Namens zu konvertieren, muss das Ziel – also das von einer Zertifizierungsstelle signierte Zertifikat – zunächst per Fingerabdruck im Cluster eingeführt werden. Nur dann ist eine Konvertierung von der Verwendung eines Fingerabdrucks zur Verwendung eines allgemeinen Namens möglich.

Zu Testzwecken *könnte* ein selbstsigniertes Zertifikat per CN deklariert werden. Dies funktioniert aber nur, wenn der Aussteller mit seinem eigenen Fingerabdruck verknüpft ist. Aus Sicherheitsperspektive entspricht dies ungefähr dem Deklarieren desselben Zertifikats per Fingerabdruck. Eine erfolgreiche Konvertierung dieser Art garantiert keine erfolgreiche Konvertierung vom Fingerabdruck zum allgemeinen Namen mit einem von einer Zertifizierungsstelle signierten Zertifikat. Es empfiehlt sich, die Konvertierung mit einem ordnungsgemäßen, von einer Zertifizierungsstelle signierten Zertifikat zu testen. Für diesen Test gibt es einige kostenlose Optionen.

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>Hochladen des Zertifikats und Installieren in der Skalierungsgruppe

In Azure umfasst der empfohlene Mechanismus zum Abrufen und Bereitstellen von Zertifikaten Azure Key Vault und die zugehörigen Tools. Ein Zertifikat, das der Clusterzertifikatdeklaration entspricht, muss auf jedem Knoten der VM-Skalierungsgruppen bereitgestellt werden, aus denen der Cluster besteht. Weitere Informationen finden Sie unter [Wie übertrage ich ein Zertifikat sicher auf den virtuellen Computer?](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-securely-ship-a-certificate-to-the-vm).

Es ist wichtig, dass sowohl aktuelle als auch zukünftig zu verwendende Clusterzertifikate auf den virtuellen Computern aller Knotentypen des Clusters installiert werden, bevor Sie Änderungen an den Zertifikatdeklarationen des Clusters vornehmen. Der gesamte Prozess von der Ausstellung des Zertifikats bis hin zur Bereitstellung auf einem Service Fabric-Knoten wird detailliert unter [Der Weg eines Zertifikats](cluster-security-certificate-management.md#the-journey-of-a-certificate) erläutert.

## <a name="bring-the-cluster-to-an-optimal-starting-state"></a>Versetzen des Clusters in einen optimalen Anfangszustand

Die Konvertierung einer Zertifikatdeklaration von der Verwendung von Fingerabdrücken zur Verwendung von allgemeinen Namen wirkt sich auf Folgendes aus:

- Die Art und Weise, in der die einzelnen Knoten im Cluster ihre Anmeldeinformationen finden und für andere Knoten darstellen.
- Die Art und Weise, in der die einzelnen Knoten beim Einrichten einer sicheren Verbindung die Anmeldeinformationen ihrer Pendants überprüfen.

Lesen Sie die [Regeln für die Zertifikatkonfiguration](cluster-security-certificates.md#certificate-configuration-rules) für beide Konfigurationen, bevor Sie fortfahren. Der wichtigste Aspekt bei der Konvertierung von Fingerabdrücken zu allgemeinen Namen ist Folgendes: Sowohl Knoten, für die bereits ein Upgrade durchgeführt wurde, als auch Knoten, für dies noch nicht erfolgt ist (also Knoten, die zu verschiedenen Upgradedomänen gehören), müssen jederzeit während des Upgradeprozesses in der Lage sein, eine erfolgreiche gegenseitige Authentifizierung durchzuführen. Um dieses Ziel zu erreichen, empfiehlt es sich, das Zielzertifikat in einem ersten Upgrade per Fingerabdruck zu deklarieren. Danach erfolgt in einem nachfolgenden Upgrade der Übergang zu CN. Wenn sich der Cluster bereits in einem empfohlenen Anfangszustand befindet, können Sie diesen Abschnitt überspringen.

Es gibt mehrere gültige Anfangszustände für eine Konvertierung. Für alle Zustände gilt die gleiche Voraussetzung: Der Cluster muss zu Beginn des Upgrades auf CN bereits das (per Fingerabdruck deklarierte) Zielzertifikat verwenden. In diesem Artikel werden die Zertifikate `GoalCert`, `OldCert1` und `OldCert2` betrachtet.

#### <a name="valid-starting-states"></a>Gültige Anfangszustände

- `Thumbprint: GoalCert, ThumbprintSecondary: None`
- `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`, wobei `GoalCert` ein späteres `NotAfter`-Datum aufweist als `OldCert1`
- `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`, wobei `GoalCert` ein späteres `NotAfter`-Datum aufweist als `OldCert1`

Wenn sich Ihr Cluster in keinem der zuvor beschriebenen gültigen Zustände befindet, finden Sie am Ende dieses Artikels Informationen dazu, wie Sie einen solchen Zustand erreichen.

## <a name="select-the-desired-cn-based-certificate-validation-scheme"></a>Auswählen des gewünschten CN-basierten Schemas für die Zertifikatüberprüfung

Wie bereits beschrieben, unterstützt Service Fabric die Deklaration von Zertifikaten per CN mit einem impliziten Vertrauensanker oder durch explizites Anheften der Fingerabdrücke des Ausstellers. Weitere Informationen finden Sie unter [Auf dem allgemeinen Namen basierende Zertifikatvalidierungsdeklarationen](cluster-security-certificates.md#common-name-based-certificate-validation-declarations).

Sie müssen die Unterschiede und Auswirkungen der verschiedenen Mechanismen genau kennen. Aus syntaktischer Sicht wird die Auswahl durch den Wert des Parameters `certificateIssuerThumbprintList` bestimmt. Ein leerer Parameter bedeutet, dass eine vertrauenswürdige Stammzertifizierungsstelle (Vertrauensanker) verwendet wird. Eine Reihe von Fingerabdrücken dagegen beschränkt die zulässigen direkten Aussteller von Clusterzertifikaten.

   > [!NOTE]
   > Im Feld `certificateIssuerThumbprint` können Sie die erwarteten direkten Zertifikataussteller angeben, die vom allgemeinen Namen des Antragstellers deklariert werden. Zulässige Werte sind ein oder mehrere durch Trennzeichen getrennte SHA-1-Fingerabdrücke. Diese Aktion stärkt die Zertifikatüberprüfung.
   >
   > Wenn keine Aussteller angegeben sind oder die Liste leer ist, wird das Zertifikat für die Authentifizierung akzeptiert, sofern die Kette erstellt werden kann. Dann endet das Zertifikat in einem Stamm, dem vom Validierungssteuerelement vertraut wird. Wenn mindestens ein Fingerabdruck eines Ausstellers angegeben ist, wird das Zertifikat akzeptiert, sofern der Fingerabdruck des direkten Ausstellers – wie aus der Kette extrahiert – mit einem der in diesem Feld angegebenen Werte übereinstimmt. Das Zertifikat wird akzeptiert, unabhängig davon, ob dem Stamm vertraut wird oder nicht.
   >
   > Eine PKI verwendet möglicherweise andere Zertifizierungsstellen (so genannte *Aussteller* ), um Zertifikate mit einem bestimmten Antragsteller zu signieren. Aus diesem Grund ist es wichtig, alle erwarteten Fingerabdrücke von Ausstellern für diesen Antragsteller anzugeben. Anders gesagt: Die Verlängerung eines Zertifikats wird nicht unbedingt vom selben Aussteller signiert wie das Zertifikat, das verlängert wird.
   >
   > Die Angabe des Ausstellers gilt als Best Practice. Auch wenn kein Aussteller angegeben wird, funktioniert dieses Verfahren bei Zertifikatketten bis zu einem vertrauenswürdigen Stamm. Dieses Verhalten unterliegt jedoch Einschränkungen, und es wird möglicherweise in naher Zukunft eingestellt. Cluster, die in Azure bereitgestellt, mit durch eine private PKI ausgestellten X.509-Zertifikaten geschützt und per Antragsteller deklariert werden, können möglicherweise durch Service Fabric nicht überprüft werden (zur Kommunikation zwischen Cluster und Dienst). Für eine Überprüfung muss die Zertifikatrichtlinie der PKI ermittelbar, verfügbar und zugänglich sein.

## <a name="update-the-clusters-azure-resource-manager-template-and-deploy"></a>Aktualisieren und Bereitstellen der Azure Resource Manager-Vorlage für den Cluster

Verwalten Sie Ihre Service Fabric-Cluster mit Azure Resource Manager-Vorlagen (ARM). Eine Alternative, bei der ebenfalls JSON-Artefakte verwendet werden, ist der [Azure-Ressourcen-Explorer (Vorschau)](https://resources.azure.com). Das Azure-Portal bietet derzeit keine äquivalenten Funktionen.

Wenn die ursprüngliche Vorlage für einen vorhandenen Cluster nicht verfügbar ist, kann eine gleichwertige Vorlage im Azure-Portal abgerufen werden. Wechseln Sie zu der Ressourcengruppe, die den Cluster enthält, und wählen Sie aus dem Menü **Automatisierung** auf der linken Seite die Option **Vorlage exportieren** aus. Wählen Sie dann die gewünschten Ressourcen aus. Es müssen mindestens die VM-Skalierungsgruppe bzw. die Clusterressourcen exportiert werden. Die generierte Vorlage kann ebenfalls heruntergeladen werden. Diese Vorlage muss eventuell geändert werden, bevor sie vollständig bereitgestellt werden kann. Möglicherweise entspricht die Vorlage auch nicht exakt der ursprünglichen. Sie spiegelt den aktuellen Zustand der Clusterressource wider.

Folgende Änderungen sind notwendig:

- Aktualisieren Sie die Definition der Service Fabric-Knotenerweiterung (unter den VM-Ressourcen). Wenn der Cluster mehrere Knotentypen definiert, müssen Sie die Definition jeder entsprechenden VM-Skalierungsgruppe aktualisieren.
- Aktualisieren Sie die Clusterressourcendefinition.

Im Folgenden finden Sie einige detaillierte Beispiele.

### <a name="update-the-virtual-machine-scale-set-resources"></a>Aktualisieren der Ressourcen der VM-Skalierungsgruppen
Von:
```json
"virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            ...
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            ...
                            "certificate": {
                                "thumbprint": "[parameters('certificateThumbprint')]",
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        ...
                    }
                },
```
Nach:
```json
"virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            ...
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            ...
                            "certificate": {
                                "commonNames": [
                                    "[parameters('certificateCommonName')]"
                                ],
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        ...
                    }
                },
```

### <a name="update-the-cluster-resource"></a>Aktualisieren der Clusterressource

Fügen Sie in der Ressource **Microsoft.ServiceFabric/clusters** die Eigenschaft **certificateCommonNames** mit der Einstellung **commonNames** hinzu, und entfernen Sie die Eigenschaft **certificate** vollständig, einschließlich sämtlicher Einstellungen.

Von:
```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            ...
        ],
        "properties": {
            "addonFeatures": [
                ...
            ],
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
```
Nach:
```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            ...
        ],
        "properties": {
            "addonFeatures": [
                ...
            ],
            "certificateCommonNames": {
                "commonNames": [
                    {
                        "certificateCommonName": "[parameters('certificateCommonName')]",
                        "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprintList')]"
                    }
                ],
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
```

Weitere Informationen finden Sie unter [Bereitstellen eines Service Fabric-Clusters mit allgemeinem Zertifikatnamen anstelle eines Fingerabdrucks](./service-fabric-create-cluster-using-cert-cn.md).

## <a name="deploy-the-updated-template"></a>Bereitstellen der aktualisierten Vorlage

Wenn Sie alle Änderungen vorgenommen haben, stellen Sie die aktualisierte Vorlage erneut bereit.

```powershell
$groupname = "sfclustertutorialgroup"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="achieve-a-valid-starting-state-for-converting-a-cluster-to-cn-based-certificate-declarations"></a>Erreichen eines gültigen Anfangszustands für die Konvertierung eines Clusters zu CN-basierten Zertifikatdeklarationen

| Anfangsstatus | Upgrade 1 | Upgrade 2 |
| :--- | :--- | :--- |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` und `GoalCert` weisen ein späteres `NotAfter`-Datum auf als `OldCert1` | `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` und `OldCert1` weisen ein späteres `NotAfter`-Datum auf als `GoalCert` | `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1` | `Thumbprint: GoalCert, ThumbprintSecondary: None` |
| `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`, wobei `OldCert1` ein späteres `NotAfter`-Datum aufweist als `GoalCert` | Upgrade auf `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`, wobei `OldCert1` ein späteres `NotAfter`-Datum aufweist als `GoalCert` | Upgrade auf `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: OldCert2` | Entfernen von `OldCert1` oder `OldCert2`, um den Zustand `Thumbprint: OldCertx, ThumbprintSecondary: None` zu erreichen | Fortfahren mit dem neuen Anfangszustand |

Eine Anleitung zu diesen Upgrades finden Sie unter [Verwalten von Zertifikaten in einem Azure Service Fabric-Cluster](service-fabric-cluster-security-update-certs-azure.md).

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über [Clustersicherheit](service-fabric-cluster-security.md).
* Erfahren Sie, wie Sie einen [Rollover für ein Clusterzertifikat anhand des allgemeinen Namens ausführen](service-fabric-cluster-rollover-cert-cn.md).
* Erfahren Sie, wie Sie einen [Cluster für einen automatischen Rollover ohne Benutzereingriff konfigurieren](cluster-security-certificate-management.md).

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
