---
title: 'Bereitstellen des Blaupausenbeispiels „ISO 27001: ASE-/SQL-Workload“'
description: 'Bereitstellungsschritte für das Blaupausenbeispiel „ISO 27001: App Service-Umgebungs-/SQL-Datenbank-Workload“, einschließlich Details zum Blaupausenartefaktparameter'
ms.date: 02/05/2021
ms.topic: sample
ms.openlocfilehash: 60867c117c9f1fda6f8ba33333c19a7bd764d219
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627482"
---
# <a name="deploy-the-iso-27001-app-service-environmentsql-database-workload-blueprint-sample"></a>Bereitstellung des Blaupausenbeispiels „ISO 27001: App Service-Umgebungs-/SQL-Datenbank-Workload“

Zum Bereitstellen des Azure Blueprints-Beispiels für die Blaupause „ISO 27001: App Service-Umgebungs-/SQL-Datenbank-Workload“ müssen die folgenden Schritte ausgeführt werden:

> [!div class="checklist"]
> - Bereitstellen des Blaupausenbeispiels [ISO 27001: Gemeinsame Dienste](../iso27001-shared/index.md)
> - Erstellen einer neuen Blaupause mithilfe des Beispiels
> - Markieren Ihrer Kopie des Beispiels als **Veröffentlicht**
> - Zuweisen Ihrer Kopie der Blaupause zu einem vorhandenen Abonnement

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free) erstellen, bevor Sie beginnen.

## <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Bereitstellen des Blaupausenbeispiels „ISO 27001: Gemeinsame Dienste“

Damit dieses Blaupausenbeispiel bereitgestellt werden kann, muss zunächst das Blaupausenbeispiel [ISO 27001: Gemeinsame Dienste](../iso27001-shared/index.md) im Zielabonnement bereitgestellt werden. Ohne eine erfolgreiche Bereitstellung des Blaupausenbeispiels „ISO 27001: Gemeinsame Dienste“ fehlen in diesem Blaupausenbeispiel Infrastrukturabhängigkeiten, sodass bei der Bereitstellung Fehler auftreten.

> [!IMPORTANT]
> Dieses Blaupausenbeispiel muss im gleichen Abonnement wie das Blaupausenbeispiel [ISO 27001: Gemeinsame Dienste](../iso27001-shared/index.md) zugewiesen werden.

## <a name="create-blueprint-from-sample"></a>Erstellen einer Blaupause mithilfe des Beispiels

Implementieren Sie zuerst das Blaupausenbeispiel, indem Sie mithilfe des Beispiels eine neue Blaupause in Ihrer Umgebung erstellen.

1. Wählen Sie **Alle Dienste** im linken Bereich aus. Suchen Sie nach **Blaupausen**, und wählen Sie die Option aus.

1. Klicken Sie links auf der Seite **Erste Schritte** unter _Blaupause erstellen_ auf die Schaltfläche **Erstellen**.

1. Suchen Sie nach dem Blaupausenbeispiel **ISO 27001: ASE-/SQL-Workload** unter _Weitere Beispiele_, und wählen Sie **Dieses Beispiel verwenden** aus.

1. Geben Sie die _Grundlagen_ des Blaupausenbeispiels ein:

   - **Name der Blaupause**: Geben Sie einen Namen für Ihre Kopie des Blaupausenbeispiels „ISO 27001: ASE-/SQL-Workload“ ein.
   - **Definitionsspeicherort**: Klicken Sie auf die Schaltfläche mit den Auslassungspunkten, und wählen Sie die Verwaltungsgruppe aus, in der Sie Ihre Kopie des Beispiels speichern möchten.

1. Wählen Sie oben auf der Seite die Registerkarte _Artefakte_ oder unten auf der Seite die Option **Weiter: Artefakte** aus.

1. Überprüfen Sie die Liste der Artefakte, aus denen das Blaupausenbeispiel besteht. Viele der Artefakte enthalten Parameter, die Sie später definieren. Wählen Sie **Entwurf speichern** aus, nachdem Sie das Blaupausenbeispiel überprüft haben.

## <a name="publish-the-sample-copy"></a>Veröffentlichen der Kopie des Beispiels

Ihre Kopie des Blaupausenbeispiels wurde jetzt in Ihrer Umgebung erstellt. Sie wird im Modus **Entwurf** erstellt und muss **veröffentlicht** werden, bevor sie zugewiesen und bereitgestellt werden kann. Die Kopie des Blaupausenbeispiels kann an die Umgebung und an Ihre Anforderungen angepasst werden. Durch diese Änderungen ist sie aber möglicherweise nicht mehr mit der Norm ISO 27001 konform.

1. Wählen Sie **Alle Dienste** im linken Bereich aus. Suchen Sie nach **Blaupausen**, und wählen Sie die Option aus.

1. Wählen Sie links die Seite **Blaupausendefinitionen** aus. Verwenden Sie die Filter, um Ihre Kopie des Blaupausenbeispiels zu suchen, und wählen Sie es aus.

1. Wählen Sie oben auf der Seite die Option **Blaupause veröffentlichen** aus. Geben Sie auf der neuen Seite rechts für Ihre Kopie des Blaupausenbeispiels eine **Version** an. Diese Eigenschaft ist hilfreich, wenn Sie später Änderungen vornehmen. Geben Sie **Änderungshinweise** ein, z. B. „Erste mit dem Blaupausenbeispiel ‚ISO 27001‘ veröffentlichte Version“. Wählen Sie dann unten auf der Seite **Veröffentlichen** aus.

## <a name="assign-the-sample-copy"></a>Zuweisen der Kopie des Beispiels

Nachdem die Kopie des Blaupausenbeispiels erfolgreich **veröffentlicht** wurde, kann sie einem Abonnement innerhalb der Verwaltungsgruppe, in der sie gespeichert wurde, zugewiesen werden. In diesem Schritt werden Parameter angegeben, damit jede Bereitstellung der Kopie des Blaupausenbeispiels eindeutig ist.

1. Wählen Sie **Alle Dienste** im linken Bereich aus. Suchen Sie nach **Blaupausen**, und wählen Sie die Option aus.

1. Wählen Sie links die Seite **Blaupausendefinitionen** aus. Verwenden Sie die Filter, um Ihre Kopie des Blaupausenbeispiels zu suchen, und wählen Sie es aus.

1. Wählen Sie oben auf der Seite mit der Blaupausendefinition die Option **Blaupause zuweisen** aus.

1. Geben Sie die Parameterwerte für die Blaupausenzuweisung an:

   - Grundlagen

     - **Abonnements**: Wählen Sie mindestens eines der Abonnements in der Verwaltungsgruppe aus, in der Sie die Kopie des Blaupausenbeispiels gespeichert haben. Wenn Sie mehrere Abonnements auswählen, wird für jedes Abonnement eine Zuweisung mit den eingegebenen Parametern erstellt.
     - **Zuweisungsname:** Der Name wird basierend auf dem Namen der Blaupause vorab ausgefüllt.
       Sie können ihn nach Bedarf ändern oder unverändert übernehmen.
     - **Standort**: Wählen Sie eine Region aus, in der die verwaltete Identität erstellt werden soll. Azure Blueprint verwendet diese verwaltete Identität zum Bereitstellen aller Artefakte in der zugewiesenen Blaupause. Weitere Informationen finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Version der Blaupausendefinition:** Wählen Sie eine **veröffentlichte** Version Ihrer Kopie des Blaupausenbeispiels aus.

   - Zuweisung sperren

     Wählen Sie die Blaupausensperreinstellung für die Umgebung aus. Weitere Informationen finden Sie unter [Grundlegendes zur Ressourcensperre in Azure Blueprint](../../concepts/resource-locking.md).

   - Verwaltete Identität

     Behalten Sie die Standardeinstellung _Vom System zugewiesen_ für die verwaltete Identität bei.

   - Blaupausenparameter

     Die in diesem Abschnitt definierten Parameter werden in vielen der Artefakte in der Blaupausendefinition verwendet, um Konsistenz zu gewährleisten.

     - **Name der Organisation:** Geben Sie einen Kurznamen für Ihre Organisation ein. Diese Eigenschaft wird in erster Linie zum Benennen von Ressourcen verwendet.
     - **Abonnement-ID für gemeinsame Dienste:** ID des Abonnements, dem das Blaupausenbeispiel [ISO 27001: Gemeinsame Dienste](../iso27001-shared/index.md) zugewiesen wird.
     - **Adresspräfix für Standardsubnetz:** Die CIDR-Notation für das Standardsubnetz des virtuellen Netzwerks.
       Der Standardwert ist _10.1.0.0/16_.
     - **Standort für Workload:** Bestimmt den Standort, an dem die Artefakte bereitgestellt werden. Nicht alle Dienste sind an allen Standorten verfügbar. Artefakte, die diese Dienste bereitstellen, enthalten eine Parameteroption für den Standort, an dem das entsprechende Artefakt bereitgestellt wird.

   - Artefaktparameter

     Die in diesem Abschnitt definierten Parameter gelten für das Artefakt, unter dem sie definiert werden. Diese Parameter sind [dynamische Parameter](../../concepts/parameters.md#dynamic-parameters), da sie während der Zuweisung der Blaupause definiert werden. Eine vollständige Liste der Artefaktparameter und die zugehörigen Beschreibungen finden Sie in der [Tabelle der Artefaktparameter](#artifact-parameters-table).

1. Nachdem Sie alle Parameter eingegeben haben, wählen Sie unten auf der Seite die Option **Zuweisen** aus. Die Blaupausenzuweisung wird erstellt, und die Artefaktbereitstellung wird gestartet. Die Bereitstellung dauert ungefähr eine Stunde. Um den Status der Bereitstellung zu überprüfen, öffnen Sie die Blaupausenzuweisung.

> [!WARNING]
> Der Azure Blueprints-Dienst und die integrierten Blaupausenbeispiele sind **kostenlos**. Azure-Ressourcen werden [nach Produkt abgerechnet](https://azure.microsoft.com/pricing/). Verwenden Sie den [Preisrechner](https://azure.microsoft.com/pricing/calculator/) zum Schätzen der Kosten für die Ausführung der Ressourcen, die mit diesem Blaupausenbeispiel bereitgestellt werden.

## <a name="artifact-parameters-table"></a>Tabelle der Artefaktparameter

Die folgende Tabelle enthält eine Aufstellung der Parameter des Blaupausenartefakts:

|Artefaktname|Artefakttyp|Parametername|BESCHREIBUNG|
|-|-|-|-|
|Log Analytics-Ressourcengruppe|Resource group|Name|**Gesperrt:** Verkettet den **Namen der Organisation** mit `-workload-log-rg`, sodass die Ressourcengruppe eindeutig ist.|
|Log Analytics-Ressourcengruppe|Resource group|Standort|**Gesperrt:** Verwendet den Blaupausenparameter.|
|Log Analytics-Vorlage|Resource Manager-Vorlage|Dienstebene|Legt den Tarif des Log Analytics-Arbeitsbereichs fest. Der Standardwert ist _PerNode_.|
|Log Analytics-Vorlage|Resource Manager-Vorlage|Protokollaufbewahrung in Tagen|Datenaufbewahrung in Tagen. Der Standardwert ist _365_.|
|Log Analytics-Vorlage|Resource Manager-Vorlage|Standort|Die bei der Erstellung des Log Analytics-Arbeitsbereichs verwendete Region. Der Standardwert ist _USA, Westen 2_.|
|Netzwerkressourcengruppe|Resource group|Name|**Gesperrt:** Verkettet den **Namen der Organisation** mit `-workload-net-rg`, sodass die Ressourcengruppe eindeutig ist.|
|Netzwerkressourcengruppe|Resource group|Standort|**Gesperrt:** Verwendet den Blaupausenparameter.|
|Netzwerksicherheitsgruppen-Vorlage|Resource Manager-Vorlage|Protokollaufbewahrung in Tagen|Datenaufbewahrung in Tagen. Der Standardwert ist _365_.|
|Virtual Network- und Routingtabellenvorlage|Resource Manager-Vorlage|Private IP-Adresse von Azure Firewall|Konfiguriert die private IP-Adresse von [Azure Firewall](../../../../firewall/overview.md). Sollte Teil der CIDR-Notation sein, die in _ISO 27001: Gemeinsame Dienste_ im Artefaktparameter **Adresspräfix für Azure Firewall-Subnetz** definiert ist. Der Standardwert ist _10.0.4.4_.|
|Virtual Network- und Routingtabellenvorlage|Resource Manager-Vorlage|Abonnement-ID für gemeinsame Dienste|Wert, der zum Aktivieren des VNET-Peerings zwischen einer Workload und gemeinsamen Diensten verwendet wird.|
|Virtual Network- und Routingtabellenvorlage|Resource Manager-Vorlage|Adresspräfix für Virtual Network|Die CIDR-Notation für das virtuelle Netzwerk. Der Standardwert ist _10.1.0.0/16_.|
|Virtual Network- und Routingtabellenvorlage|Resource Manager-Vorlage|Adresspräfix für Standardsubnetz|Die CIDR-Notation für das Standardsubnetz des virtuellen Netzwerks. Der Standardwert ist _10.1.0.0/16_.|
|Virtual Network- und Routingtabellenvorlage|Resource Manager-Vorlage|ADDS-IP-Adresse|IP-Adresse der ersten ADDS-VM. Dieser Wert wird als benutzerdefinierter VNET-DNS verwendet.|
|Schlüsseltresor-Ressourcengruppe|Resource group|Name|**Gesperrt:** Verkettet den **Namen der Organisation** mit `-workload-kv-rg`, sodass die Ressourcengruppe eindeutig ist.|
|Schlüsseltresor-Ressourcengruppe|Resource group|Standort|**Gesperrt:** Verwendet den Blaupausenparameter.|
|Key Vault-Vorlage|Resource Manager-Vorlage|AAD-Objekt-ID|Der AAD-Objektbezeichner des Kontos, das Zugriff auf die Key Vault-Instanz benötigt. Hat keinen Standardwert und darf nicht leer sein. Diesen Wert finden Sie im Azure-Portal, indem Sie unter _Dienste_ die Option „Benutzer“ suchen und auswählen. Verwenden Sie das Feld _Name_, um den Kontonamen zu filtern und das entsprechende Konto auszuwählen. Wählen Sie auf der Seite _Benutzerprofil_ das Symbol „Klicken Sie zum Kopieren“ neben der _Objekt-ID_ aus.|
|Key Vault-Vorlage|Resource Manager-Vorlage|Protokollaufbewahrung in Tagen|Datenaufbewahrung in Tagen. Der Standardwert ist _365_.|
|Key Vault-Vorlage|Resource Manager-Vorlage|Key Vault-SKU|Gibt die SKU der erstellten Key Vault-Instanz an. Der Standardwert ist _Premium_.|
|Key Vault-Vorlage|Resource Manager-Vorlage|Azure SQL Server-Administratorbenutzername|Der Benutzername für den Zugriff auf Azure SQL Server. Muss mit dem Eigenschaftswert in **Vorlage für Azure SQL-Datenbank** übereinstimmen. Der Standardwert ist _sql-admin-user_.|
|Azure SQL-Datenbank-Ressourcengruppe|Resource group|Name|**Gesperrt:** Verkettet den **Namen der Organisation** mit `-workload-azsql-rg`, sodass die Ressourcengruppe eindeutig ist.|
|Azure SQL-Datenbank-Ressourcengruppe|Resource group|Standort|**Gesperrt:** Verwendet den Blaupausenparameter.|
|Vorlage für Azure SQL-Datenbank|Resource Manager-Vorlage|Azure SQL Server-Administratorbenutzername|Benutzername für Azure SQL Server. Muss mit dem Eigenschaftswert in **Key Vault-Vorlage** übereinstimmen. Der Standardwert ist _sql-admin-user_.|
|Vorlage für Azure SQL-Datenbank|Resource Manager-Vorlage|Azure SQL Server-Administratorkennwort (Key Vault-Ressourcen-ID)|Die Ressourcen-ID der Key Vault-Instanz. Verwenden Sie „/ subscription/{subscriptionId}/resourceGroups/{orgName}-workload-kv/providers/Microsoft.KeyVault/vaults/{orgName}-workload-kv“, und ersetzen Sie `{subscriptionId}` durch Ihre Abonnement-ID und `{orgName}` durch den Blaupausenparameter **Name der Organisation**.|
|Vorlage für Azure SQL-Datenbank|Resource Manager-Vorlage|Azure SQL Server-Administratorkennwort (Key Vault-Geheimnisname)|Benutzername des SQL Server-Administrators. Muss mit dem Wert in der Eigenschaft **Azure SQL Server-Administratorbenutzername** von **Key Vault-Vorlage** übereinstimmen.|
|Vorlage für Azure SQL-Datenbank|Resource Manager-Vorlage|Protokollaufbewahrung in Tagen|Datenaufbewahrung in Tagen. Der Standardwert ist _365_.|
|Vorlage für Azure SQL-Datenbank|Resource Manager-Vorlage|AAD-Administratorobjekt-ID|AAD-Objekt-ID des Benutzers, der als Active Directory-Administrator zugewiesen wird. Hat keinen Standardwert und darf nicht leer sein. Diesen Wert finden Sie im Azure-Portal, indem Sie unter _Dienste_ die Option „Benutzer“ suchen und auswählen. Verwenden Sie das Feld _Name_, um den Kontonamen zu filtern und das entsprechende Konto auszuwählen. Wählen Sie auf der Seite _Benutzerprofil_ das Symbol „Klicken Sie zum Kopieren“ neben der _Objekt-ID_ aus.|
|Vorlage für Azure SQL-Datenbank|Resource Manager-Vorlage|AAD-Administratoranmeldung|Derzeit können Microsoft-Konten (z. B. live.com oder outlook.com) nicht als Administrator festgelegt werden. Nur Benutzer und Sicherheitsgruppen in Ihrer Organisation können als Administrator festgelegt werden. Hat keinen Standardwert und darf nicht leer sein. Diesen Wert finden Sie im Azure-Portal, indem Sie unter _Dienste_ die Option „Benutzer“ suchen und auswählen. Verwenden Sie das Feld _Name_, um den Kontonamen zu filtern und das entsprechende Konto auszuwählen. Kopieren Sie den _Benutzernamen_ auf der Seite _Benutzerprofil_.|
|Ressourcengruppe für App Service-Umgebung|Resource group|Name|**Gesperrt:** Verkettet den **Namen der Organisation** mit `-workload-ase-rg`, sodass die Ressourcengruppe eindeutig ist.|
|Ressourcengruppe für App Service-Umgebung|Resource group|Standort|**Gesperrt:** Verwendet den Blaupausenparameter.|
|Vorlage für App Service-Umgebung|Resource Manager-Vorlage|Domänenname|Der Name der mit dem Beispiel erstellten Active Directory-Instanz. Der Standardwert ist _contoso.com_.|
|Vorlage für App Service-Umgebung|Resource Manager-Vorlage|ASE-Standort|Standort der App Service-Umgebung. Der Standardwert ist _USA, Westen 2_.|
|Vorlage für App Service-Umgebung|Resource Manager-Vorlage|Application Gateway-Protokollaufbewahrung in Tagen|Datenaufbewahrung in Tagen. Der Standardwert ist _365_.|

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich mit den Schritten zum Bereitstellen des Blaupausenbeispiels „ISO 27001: App Service-Umgebungs-/SQL-Datenbank-Workload“ vertraut gemacht haben, finden Sie nun in den folgenden Artikeln Informationen zur Architektur und Steuerungszuordnung:

> [!div class="nextstepaction"]
> [Beispiel: Blaupause „ISO 27001: App Service-Umgebungs-/SQL-Datenbank-Workload“ – Übersicht](./index.md)
> [Beispiel: Blaupause „ISO 27001: App Service-Umgebungs-/SQL-Datenbank-Workload“ – Steuerungszuordnung](./control-mapping.md)

Weitere Artikel zu Blaupausen und ihrer Nutzung:

- Erfahren Sie mehr über den [Lebenszyklus von Blaupausen](../../concepts/lifecycle.md).
- Machen Sie sich mit der Verwendung [statischer und dynamischer Parameter](../../concepts/parameters.md) vertraut.
- Erfahren Sie, wie Sie die [Abfolge von Blaupausen](../../concepts/sequencing-order.md) anpassen können.
- Erfahren Sie, wie Sie [Ressourcen in Blaupausen sperren](../../concepts/resource-locking.md) können.
- Lernen Sie, wie Sie [vorhandene Zuweisungen aktualisieren](../../how-to/update-existing-assignments.md).
