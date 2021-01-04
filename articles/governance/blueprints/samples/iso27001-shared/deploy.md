---
title: 'Bereitstellen des Blaupausenbeispiels „ISO 27001: Gemeinsame Dienste“'
description: 'Bereitstellungsschritte für das Blaupausenbeispiel „ISO 27001: Gemeinsame Dienste“, einschließlich Details zum Blaupausenartefaktparameter.'
ms.date: 11/02/2020
ms.topic: sample
ms.openlocfilehash: acce9ecf906430047fa1e687f719fd4e04daf756
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420278"
---
# <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Bereitstellen des Blaupausenbeispiels „ISO 27001: Gemeinsame Dienste“

Folgende Schritte sind erforderlich, um das Azure Blueprints-Blaupausenbeispiel „ISO 27001: Gemeinsame Dienste“ bereitzustellen:

> [!div class="checklist"]
> - Erstellen einer neuen Blaupause mithilfe des Beispiels
> - Markieren Ihrer Kopie des Beispiels als **Veröffentlicht**
> - Zuweisen Ihrer Kopie der Blaupause zu einem vorhandenen Abonnement

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free) erstellen, bevor Sie beginnen.

## <a name="create-blueprint-from-sample"></a>Erstellen einer Blaupause mithilfe des Beispiels

Implementieren Sie zuerst das Blaupausenbeispiel, indem Sie mithilfe des Beispiels eine neue Blaupause in Ihrer Umgebung erstellen.

1. Wählen Sie **Alle Dienste** im linken Bereich aus. Suchen Sie nach **Blaupausen** , und wählen Sie die Option aus.

1. Klicken Sie links auf der Seite **Erste Schritte** unter _Blaupause erstellen_ auf die Schaltfläche **Erstellen**.

1. Suchen Sie nach dem Blaupausenbeispiel **ISO 27001: Gemeinsame Dienste** unter _Weitere Beispiele_ , und klicken Sie auf **Dieses Beispiel verwenden**.

1. Geben Sie die _Grundlagen_ des Blaupausenbeispiels ein:

   - **Name der Blaupause** : Geben Sie einen Namen für Ihre Kopie des Blaupausenbeispiels „ISO 27001: Gemeinsame Dienste“ ein.
   - **Definitionsspeicherort** : Klicken Sie auf die Schaltfläche mit den Auslassungspunkten, und wählen Sie die Verwaltungsgruppe aus, in der Sie Ihre Kopie des Beispiels speichern möchten.

1. Wählen Sie oben auf der Seite die Registerkarte _Artefakte_ oder unten auf der Seite die Option **Weiter: Artefakte** aus.

1. Überprüfen Sie die Liste der Artefakte, aus denen das Blaupausenbeispiel besteht. Viele der Artefakte enthalten Parameter, die Sie später definieren. Wählen Sie **Entwurf speichern** aus, nachdem Sie das Blaupausenbeispiel überprüft haben.

## <a name="publish-the-sample-copy"></a>Veröffentlichen der Kopie des Beispiels

Ihre Kopie des Blaupausenbeispiels wurde jetzt in Ihrer Umgebung erstellt. Sie wird im Modus **Entwurf** erstellt und muss **veröffentlicht** werden, bevor sie zugewiesen und bereitgestellt werden kann. Die Kopie des Blaupausenbeispiels kann an die Umgebung und an Ihre Anforderungen angepasst werden. Durch diese Änderungen ist sie aber möglicherweise nicht mehr mit der Norm ISO 27001 konform.

1. Wählen Sie **Alle Dienste** im linken Bereich aus. Suchen Sie nach **Blaupausen** , und wählen Sie die Option aus.

1. Wählen Sie links die Seite **Blaupausendefinitionen** aus. Verwenden Sie die Filter, um Ihre Kopie des Blaupausenbeispiels zu suchen, und wählen Sie es aus.

1. Wählen Sie oben auf der Seite die Option **Blaupause veröffentlichen** aus. Geben Sie auf der neuen Seite rechts für Ihre Kopie des Blaupausenbeispiels eine **Version** an. Diese Eigenschaft ist hilfreich, wenn Sie später Änderungen vornehmen. Geben Sie **Änderungshinweise** ein, z. B. „Erste mit dem Blaupausenbeispiel ‚ISO 27001‘ veröffentlichte Version“. Wählen Sie dann unten auf der Seite **Veröffentlichen** aus.

## <a name="assign-the-sample-copy"></a>Zuweisen der Kopie des Beispiels

Nachdem die Kopie des Blaupausenbeispiels erfolgreich **veröffentlicht** wurde, kann sie einem Abonnement innerhalb der Verwaltungsgruppe, in der sie gespeichert wurde, zugewiesen werden. In diesem Schritt werden Parameter angegeben, damit jede Bereitstellung der Kopie des Blaupausenbeispiels eindeutig ist.

1. Wählen Sie **Alle Dienste** im linken Bereich aus. Suchen Sie nach **Blaupausen** , und wählen Sie die Option aus.

1. Wählen Sie links die Seite **Blaupausendefinitionen** aus. Verwenden Sie die Filter, um Ihre Kopie des Blaupausenbeispiels zu suchen, und wählen Sie es aus.

1. Wählen Sie oben auf der Seite mit der Blaupausendefinition die Option **Blaupause zuweisen** aus.

1. Geben Sie die Parameterwerte für die Blaupausenzuweisung an:

   - Grundlagen

     - **Abonnements** : Wählen Sie mindestens eines der Abonnements in der Verwaltungsgruppe aus, in der Sie die Kopie des Blaupausenbeispiels gespeichert haben. Wenn Sie mehrere Abonnements auswählen, wird für jedes Abonnement eine Zuweisung mit den eingegebenen Parametern erstellt.
     - **Zuweisungsname:** Der Name wird basierend auf dem Namen der Blaupause vorab ausgefüllt.
       Sie können ihn nach Bedarf ändern oder unverändert übernehmen.
     - **Standort** : Wählen Sie eine Region aus, in der die verwaltete Identität erstellt werden soll. Azure Blueprint verwendet diese verwaltete Identität zum Bereitstellen aller Artefakte in der zugewiesenen Blaupause. Weitere Informationen finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Version der Blaupausendefinition:** Wählen Sie eine **veröffentlichte** Version Ihrer Kopie des Blaupausenbeispiels aus.

   - Zuweisung sperren

     Wählen Sie die Blaupausensperreinstellung für die Umgebung aus. Weitere Informationen finden Sie unter [Grundlegendes zur Ressourcensperre in Azure Blueprint](../../concepts/resource-locking.md).

   - Verwaltete Identität

     Behalten Sie die Standardeinstellung _Vom System zugewiesen_ für die verwaltete Identität bei.

   - Blaupausenparameter

     Die in diesem Abschnitt definierten Parameter werden in vielen der Artefakte in der Blaupausendefinition verwendet, um Konsistenz zu gewährleisten.

     - **Name der Organisation:** Geben Sie einen Kurznamen für Ihre Organisation ein. Diese Eigenschaft wird in erster Linie zum Benennen von Ressourcen verwendet.
     - **Adresspräfix für Subnetz gemeinsamer Dienste:** Dieser Parameter stellt den CIDR-Notationswert dar, um die bereitgestellten Ressourcen zu vernetzen.
     - **Standort für gemeinsame Dienste:** Bestimmt den Standort, an dem die Artefakte bereitgestellt werden. Nicht alle Dienste sind an allen Standorten verfügbar. Artefakte, die diese Dienste bereitstellen, enthalten eine Parameteroption für den Standort, an dem das entsprechende Artefakt bereitgestellt wird.
     - **Zulässiger Standort (Richtlinie: Blueprint Initiative für ISO 27001)** : Wert, der die zulässigen Standorte für Ressourcengruppen und Ressourcen angibt.
     - **Log Analytics-Arbeitsbereich für VM-Agents (Richtlinie: Blueprint Initiative für ISO 27001)** : Gibt die Ressourcen-ID eines Arbeitsbereichs an. Dieser Parameter verwendet eine `concat`-Funktion, um die Ressourcen-ID zu erstellen.

   - Artefaktparameter

     Die in diesem Abschnitt definierten Parameter gelten für das Artefakt, unter dem sie definiert werden. Diese Parameter sind [dynamische Parameter](../../concepts/parameters.md#dynamic-parameters), da sie während der Zuweisung der Blaupause definiert werden. Eine vollständige Liste der Artefaktparameter und die zugehörigen Beschreibungen finden Sie in der [Tabelle der Artefaktparameter](#artifact-parameters-table).

1. Nachdem Sie alle Parameter eingegeben haben, wählen Sie unten auf der Seite die Option **Zuweisen** aus. Die Blaupausenzuweisung wird erstellt, und die Artefaktbereitstellung wird gestartet. Die Bereitstellung dauert ungefähr eine Stunde. Um den Status der Bereitstellung zu überprüfen, öffnen Sie die Blaupausenzuweisung.

> [!WARNING]
> Der Azure Blueprints-Dienst und die integrierten Blaupausenbeispiele sind **kostenlos**. Azure-Ressourcen werden [nach Produkt abgerechnet](https://azure.microsoft.com/pricing/). Verwenden Sie den [Preisrechner](https://azure.microsoft.com/pricing/calculator/) zum Schätzen der Kosten für die Ausführung der Ressourcen, die mit diesem Blaupausenbeispiel bereitgestellt werden.

## <a name="artifact-parameters-table"></a>Tabelle der Artefaktparameter

Die folgende Tabelle enthält eine Aufstellung der Parameter des Blaupausenartefakts:

|Artefaktname|Artefakttyp|Parametername|BESCHREIBUNG|
|-|-|-|-|
|\[Vorschau\]: Log Analytics-Agent für Linux-VM-Skalierungsgruppen bereitstellen|Richtlinienzuweisung|Optional: Liste der VM-Images mit unterstütztem Linux-Betriebssystem zum Hinzufügen zum Bereich|(Optional) Der Standardwert lautet _["none"]_ (keine).|
|\[Vorschau\]: Bereitstellen des Log Analytics-Agents für Linux-VMs|Richtlinienzuweisung|Optional: Liste der VM-Images mit unterstütztem Linux-Betriebssystem zum Hinzufügen zum Bereich|(Optional) Der Standardwert lautet _["none"]_ (keine).|
|\[Vorschau\]: Log Analytics-Agent für Windows-VM-Skalierungsgruppen bereitstellen|Richtlinienzuweisung|Optional: Liste der VM-Images mit unterstütztem Windows-Betriebssystem zum Hinzufügen zum Bereich|(Optional) Der Standardwert lautet _["none"]_ (keine).|
|\[Vorschau\]: Bereitstellen des Log Analytics-Agents für Windows-VMs|Richtlinienzuweisung|Optional: Liste der VM-Images mit unterstütztem Windows-Betriebssystem zum Hinzufügen zum Bereich|(Optional) Der Standardwert lautet _["none"]_ (keine).|
|Zulässige Ressourcentypen|Richtlinienzuweisung|Zulässige Ressourcentypen|Die Liste der Ressourcentypen, die bereitgestellt werden können. Diese Liste enthält alle drei Ressourcentypen, die in Shared Services bereitgestellt werden.|
|Allowed storage account SKUs (Zulässige Speicherkonto-SKUs)|Richtlinienzuweisung|Zulässige Speicher-SKUs|Liste der zulässigen Speicherkonto-SKUs der Diagnoseprotokolle. Der Standardwert lautet _["Standard_LRS"]_ .|
|Allowed virtual machine SKUs (Zulässige VM-SKUs)|Richtlinienzuweisung|Liste der für die Bereitstellung zulässigen VM-SKUs. Der Standardwert lautet _["Standard_DS1_v2", "Standard_DS2_v2"]_ .|
|Blueprint Initiative für ISO 27001|Richtlinienzuweisung|Ressourcentyp zur Überprüfung von Diagnoseprotokollen|Liste der Ressourcentypen, die überprüfen sollen, ob die Einstellung für das Diagnoseprotokoll nicht aktiviert ist. Die zulässigen Werte finden Sie unter [Unterstützte Dienste, Schemas und Kategorien für Azure-Diagnoseprotokolle](../../../../azure-monitor/platform/resource-logs-schema.md#service-specific-schemas).|
|Log Analytics-Ressourcengruppe|Resource group|Name|**Gesperrt:** Verkettet den **Namen der Organisation** mit `-sharedsvsc-log-rg`, sodass die Ressourcengruppe eindeutig ist.|
|Log Analytics-Ressourcengruppe|Resource group|Position|**Gesperrt:** Verwendet den Blaupausenparameter.|
|Log Analytics-Vorlage|Resource Manager-Vorlage|Dienstebene|Legt den Tarif des Log Analytics-Arbeitsbereichs fest. Der Standardwert ist _PerNode_.|
|Log Analytics-Vorlage|Resource Manager-Vorlage|Protokollaufbewahrung in Tagen|Datenaufbewahrung in Tagen. Der Standardwert ist _365_.|
|Log Analytics-Vorlage|Resource Manager-Vorlage|Standort|Die bei der Erstellung des Log Analytics-Arbeitsbereichs verwendete Region. Der Standardwert ist _USA, Westen 2_.|
|Netzwerkressourcengruppe|Resource group|Name|**Gesperrt:** Verkettet den **Namen der Organisation** mit `-sharedsvcs-net-rg`, sodass die Ressourcengruppe eindeutig ist.|
|Netzwerkressourcengruppe|Resource group|Position|**Gesperrt:** Verwendet den Blaupausenparameter.|
|Azure Firewall-Vorlage|Resource Manager-Vorlage|Private IP-Adresse von Azure Firewall|Konfiguriert die private IP-Adresse von [Azure Firewall](../../../../firewall/overview.md). Dieser Wert wird ebenso als Standardroutingtabelle für das Subnetz für gemeinsame Dienste verwendet. Er sollte Teil der CIDR-Notation sein, die im **Adresspräfix für Azure Firewall-Subnetz** definiert ist. Der Standardwert ist _10.0.4.4_.|
|Azure Firewall-Vorlage|Resource Manager-Vorlage|Protokollaufbewahrung in Tagen|Datenaufbewahrung in Tagen. Der Standardwert ist _365_.|
|Netzwerksicherheitsgruppen-Vorlage|Resource Manager-Vorlage|Protokollaufbewahrung in Tagen|Datenaufbewahrung in Tagen. Der Standardwert ist _365_.|
|Virtual Network- und Routingtabellenvorlage|Resource Manager-Vorlage|Adresspräfix für Virtual Network|Die CIDR-Notation für das virtuelle Netzwerk. Der Standardwert ist _10.0.0.0/16_.|
|Virtual Network- und Routingtabellenvorlage|Resource Manager-Vorlage|DDoS Protection in Virtual Network aktivieren|Konfiguriert den DDoS-Schutz für das virtuelle Netzwerk. Der Standardwert lautet _true_.|
|Virtual Network- und Routingtabellenvorlage|Resource Manager-Vorlage|Adresspräfix für Subnetz gemeinsamer Dienste|Die CIDR-Notation für das Subnetz für gemeinsame Dienste. Der Standardwert ist _10.0.0.0/24_.|
|Virtual Network- und Routingtabellenvorlage|Resource Manager-Vorlage|Adresspräfix für DMZ-Subnetz|Die CIDR-Notation für das DMZ-Subnetz. Der Standardwert ist _10.0.1.0/24_.|
|Virtual Network- und Routingtabellenvorlage|Resource Manager-Vorlage|Adresspräfix für Application Gateway-Subnetz|Die CIDR-Notation für das Application Gateway-Subnetz. Der Standardwert ist _10.0.2.0/24_.|
|Virtual Network- und Routingtabellenvorlage|Resource Manager-Vorlage|Adresspräfix für Subnetz des Virtual Network-Gateways|Die CIDR-Notation für das Subnetz des virtuellen Netzwerkgateways. Der Standardwert ist _10.0.3.0/24_.|
|Virtual Network- und Routingtabellenvorlage|Resource Manager-Vorlage|Adresspräfix für Azure Firewall-Subnetz|Die CIDR-Notation für das [Azure Firewall](../../../../firewall/overview.md)-Subnetz. Der Parameter der **privaten Azure Firewall-IP-Adresse** muss enthalten sein.|
|Schlüsseltresor-Ressourcengruppe|Resource group|Name|**Gesperrt:** Verkettet den **Namen der Organisation** mit `-sharedsvcs-kv-rg`, sodass die Ressourcengruppe eindeutig ist.|
|Schlüsseltresor-Ressourcengruppe|Resource group|Position|**Gesperrt:** Verwendet den Blaupausenparameter.|
|Key Vault-Vorlage|Resource Manager-Vorlage|Benutzername des Jumpbox-Administrators|Benutzername für die Jumpbox. Muss mit dem Eigenschaftswert in der **Jumpbox-Vorlage** übereinstimmen. Der Standardwert ist _jb-admin-user_.|
|Key Vault-Vorlage|Resource Manager-Vorlage|SSH-Schlüssel oder Kennwort des Jumpbox-Administrators|Schlüssel oder Kennwort für das Konto für die Jumpbox. Muss mit dem Eigenschaftswert in der **Jumpbox-Vorlage** übereinstimmen. Hat keinen Standardwert und darf nicht leer sein.|
|Key Vault-Vorlage|Resource Manager-Vorlage|Benutzername des Domänenadministrators|Benutzername für den Zugriff auf die Active Directory-VM und für das Einbinden anderer VMs in eine Domäne Muss mit dem Eigenschaftswert **Benutzername des Domänenadministrators** in der **Active Directory Domain Services-Vorlage** übereinstimmen. Der Standardwert ist _domain-admin-user_.|
|Key Vault-Vorlage|Resource Manager-Vorlage|Domänenadministratorkennwort|Benutzerkennwort des Domänenadministrators Hat keinen Standardwert und darf nicht leer sein.|
|Key Vault-Vorlage|Resource Manager-Vorlage|AAD-Objekt-ID|Der AAD-Objektbezeichner des Kontos, das Zugriff auf die Key Vault-Instanz benötigt. Hat keinen Standardwert und darf nicht leer sein. Diesen Wert finden Sie im Azure-Portal, indem Sie unter _Dienste_ die Option „Benutzer“ suchen und auswählen. Verwenden Sie das Feld _Name_ , um den Kontonamen zu filtern und das entsprechende Konto auszuwählen. Wählen Sie auf der Seite _Benutzerprofil_ das Symbol „Klicken Sie zum Kopieren“ neben der _Objekt-ID_ aus.  |
|Key Vault-Vorlage|Resource Manager-Vorlage|Protokollaufbewahrung in Tagen|Datenaufbewahrung in Tagen. Der Standardwert ist _365_.|
|Key Vault-Vorlage|Resource Manager-Vorlage|Key Vault-SKU|Gibt die SKU der erstellten Key Vault-Instanz an. Der Standardwert ist _Premium_.|
|Jumpbox-Ressourcengruppe|Resource group|Name|**Gesperrt:** Verkettet den **Namen der Organisation** mit `-sharedsvcs-jb-rg`, sodass die Ressourcengruppe eindeutig ist.|
|Jumpbox-Ressourcengruppe|Resource group|Position|**Gesperrt:** Verwendet den Blaupausenparameter.|
|Jumpbox-Vorlage|Resource Manager-Vorlage|Benutzername des Jumpbox-Administrators|Der Benutzername für den Zugriff auf Jumpbox-VMs. Muss mit dem Eigenschaftswert in **Key Vault-Vorlage** übereinstimmen. Der Standardwert ist _jb-admin-user_.|
|Jumpbox-Vorlage|Resource Manager-Vorlage|Jumpbox-Administratorkennwort (Key Vault-Ressourcen-ID)|Die Ressourcen-ID der Key Vault-Instanz. Verwenden Sie „/subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv“, und ersetzen Sie `{subscriptionId}` durch Ihre Abonnement-ID und `{orgName}` durch den Blaupausenparameter  **Name der Organisation**.|
|Jumpbox-Vorlage|Resource Manager-Vorlage|Jumpbox-Administratorkennwort (Key Vault-Geheimnisname)|Benutzername des Jumpbox-Administrators. Muss mit dem Wert in der Eigenschaft **Jumpbox-Administratorbenutzername** von **Key Vault-Vorlage** übereinstimmen.|
|Jumpbox-Vorlage|Resource Manager-Vorlage|Jumpbox-Betriebssystem|Bestimmt das Betriebssystem für die Jumpbox-VM. Der Standardwert ist _Windows_.|
|Active Directory Domain Services-Ressourcengruppe|Resource group|Name|**Gesperrt:** Verkettet den **Namen der Organisation** mit `-sharedsvcs-adds-rg`, sodass die Ressourcengruppe eindeutig ist.|
|Active Directory Domain Services-Ressourcengruppe|Resource group|Position|**Gesperrt:** Verwendet den Blaupausenparameter.|
|Active Directory Domain Services-Vorlage|Resource Manager-Vorlage|Benutzername des Domänenadministrators|Benutzername für die AD DS-Jumpbox. Muss mit dem Eigenschaftswert in **Key Vault-Vorlage** übereinstimmen. Der Standardwert ist _adds-admin-user_.|
|Active Directory Domain Services-Vorlage|Resource Manager-Vorlage|Domänenadministratorkennwort (Key Vault-Ressourcen-ID)|Die Ressourcen-ID der Key Vault-Instanz. Verwenden Sie „/subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv“, und ersetzen Sie `{subscriptionId}` durch Ihre Abonnement-ID und `{orgName}` durch den Blaupausenparameter  **Name der Organisation**.|
|Active Directory Domain Services-Vorlage|Resource Manager-Vorlage|Domänenadministratorkennwort (Key Vault-Geheimnisname)|Benutzername des Domänenadministrators. Muss mit dem Wert in der Eigenschaft **Benutzername des Domänenadministrators** von **Key Vault-Vorlage** übereinstimmen.|
|Active Directory Domain Services-Vorlage|Resource Manager-Vorlage|Domänenname|Der Name der mit dem Beispiel erstellten Active Directory-Instanz. Der Standardwert ist _contoso.com_.|
|Active Directory Domain Services-Vorlage|Resource Manager-Vorlage|Benutzername des Domänenadministrators|Benutzername für das AD-Administratorkonto und das Verknüpfen von Geräten mit der AD-Domäne. Muss mit dem Eigenschaftswert **AD admin username** (AD-Administratorbenutzername) in der **Key Vault-Vorlage** übereinstimmen. Der Standardwert ist _domain-admin-user_.|
|Active Directory Domain Services-Vorlage|Resource Manager-Vorlage|Domänenadministratorkennwort|Legt die Key Vault-Details zum Speichern des Kennworts fest. Hat keinen Standardwert und darf nicht leer sein.|

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich mit den Schritten zum Bereitstellen des Blaupausenbeispiels „ISO 27001: Gemeinsame Dienste“ vertraut gemacht haben, finden Sie nun in den folgenden Artikeln Informationen zur Architektur und Steuerungszuordnung:

> [!div class="nextstepaction"]
> [Übersicht über das Blaupausenbeispiel „ISO 27001: Gemeinsame Dienste“](./index.md)
> [ISO 27001 Shared Services blueprint - Control mapping (Blaupause „ISO 27001: Gemeinsame Dienste“, Steuerungszuordnung)](./control-mapping.md)

Weitere Artikel zu Blaupausen und ihrer Nutzung:

- Erfahren Sie mehr über den [Lebenszyklus von Blaupausen](../../concepts/lifecycle.md).
- Machen Sie sich mit der Verwendung [statischer und dynamischer Parameter](../../concepts/parameters.md) vertraut.
- Erfahren Sie, wie Sie die [Abfolge von Blaupausen](../../concepts/sequencing-order.md) anpassen können.
- Erfahren Sie, wie Sie [Ressourcen in Blaupausen sperren](../../concepts/resource-locking.md) können.
- Lernen Sie, wie Sie [vorhandene Zuweisungen aktualisieren](../../how-to/update-existing-assignments.md).
