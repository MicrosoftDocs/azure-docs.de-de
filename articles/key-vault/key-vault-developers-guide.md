---
title: Entwicklerhandbuch zu Azure-Schlüsseltresor
description: Mit Azure-Schlüsseltresor können Entwickler kryptografische Schlüssel in der Microsoft Azure-Umgebung verwalten.
services: key-vault
author: msmbaldwin
manager: barbkess
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: mbaldwin
ms.openlocfilehash: 72ec3080658b98376952f72f746c1b53fdf7de77
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64704343"
---
# <a name="azure-key-vault-developers-guide"></a>Entwicklerhandbuch zu Azure-Schlüsseltresor

Mithilfe des Schlüsseltresors können Sie in Ihren Anwendungen sicher auf vertrauliche Daten zugreifen:

- Schlüssel und geheime Schlüssel werden geschützt, ohne dass Sie den Code selbst schreiben müssen, und können auf einfache Weise in Ihren Anwendungen verwendet werden.
- Ihre Kunden können die Verantwortung für ihre eigenen Schlüssel übernehmen und diese selbst verwalten, sodass Sie sich auf die Bereitstellung der Hauptfunktionen der Software konzentrieren können. Auf diese Weise müssen Ihre Anwendungen weder die Verantwortung noch die Haftung für die Mandantenschlüssel und geheimen Mandantenschlüssel Ihrer Kunden übernehmen.
- Ihre Anwendung kann Signatur- und Verschlüsselungsschlüssel verwenden, wobei die Schlüssel außerhalb der Anwendung verwaltet werden, damit die Lösung auch für eine geografisch verteilte App geeignet ist.
- Mit der Key Vault-Version vom September 2016 können Ihre Anwendungen nun auch Key Vault-Zertifikate verwalten. Weitere Informationen finden Sie im Artikel [Informationen zu Schlüsseln, Geheimnissen und Zertifikaten](/rest/api/keyvault/about-keys--secrets-and-certificates).

Allgemeine Informationen zu Azure Key Vault finden Sie unter [Was ist der Schlüsseltresor?](key-vault-whatis.md).

## <a name="public-previews"></a>Öffentliche Vorschauversionen

Wir veröffentlichen in regelmäßigen Abständen eine öffentliche Vorschau des neuen Key Vault-Features. Testen Sie diese, und teilen Sie uns Ihre Meinung über die folgende E-Mail-Adresse für Feedback mit: azurekeyvault@microsoft.com.

### <a name="storage-account-keys---july-10-2017"></a>Speicherkontoschlüssel – 10. Juli 2017

>[!NOTE]
>Für dieses Update von Azure Key Vault ist nur das Feature **Speicherkontoschlüssel** in der Vorschau enthalten.

Diese Vorschau enthält unser neues Speicherkontoschlüssel-Feature, das über diese Schnittstellen verfügbar ist: [.NET/C#](/dotnet/api/microsoft.azure.keyvault/), [REST](/rest/api/keyvault/) und [PowerShell](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault). 

Weitere Informationen zum neuen Speicherkontoschlüssel-Feature finden Sie unter [Azure Key Vault storage account keys overview](key-vault-ovw-storage-keys.md) (Übersicht zu Azure Key Vault-Speicherkontoschlüsseln).

## <a name="videos"></a>Videos

Dieses Video zeigt Ihnen, wie Sie selbst einen Schlüsseltresor erstellen und diesen in der Beispielanwendung „Hello Key Vault“ verwenden.

- [Key Vault-Entwickler: Schnellstarthandbuch](https://channel9.msdn.com/Blogs/Azure/Azure-Key-Vault-Developer-Quick-Start/player)

Im Video erwähnte Ressourcen:

- [Azure PowerShell](https://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)
- [Azure Key Vault-Beispielcode](https://go.microsoft.com/fwlink/?LinkId=521527&clcid=0x409)

## <a name="creating-and-managing-key-vaults"></a>Erstellen und Verwalten von Schlüsseltresoren

Azure Key Vault bietet eine Möglichkeit zum sicheren Speichern von Anmeldeinformationen und anderen Schlüsseln und Geheimnissen. Um diese abrufen zu können, muss sich Ihr Code jedoch bei Key Vault authentifizieren. Mithilfe der verwalteten Identitäten für Azure-Ressourcen kann dieses Problem leichter gelöst werden, indem für Azure-Dienste eine automatisch verwaltete Identität in Azure Active Directory (Azure AD) bereitgestellt wird. Sie können diese Identität für die Authentifizierung bei jedem Dienst verwenden, der die Azure AD-Authentifizierung einschließlich von Key Vault unterstützt. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein. 

Weitere Informationen zu verwalteten Identitäten für Azure-Ressourcen finden Sie unter [Verwaltete Identitäten – Übersicht](../active-directory/managed-identities-azure-resources/overview.md). Weitere Informationen zur Arbeit mit AAD finden Sie im Artikel [Integrieren von Anwendungen in Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md).

Bevor Sie mit Schlüsseln, Geheimnissen oder Zertifikaten in Ihrem Schlüsseltresor arbeiten können, müssen Sie diesen über die CLI, PowerShell, Azure Resource Manager-Vorlagen oder REST erstellen und verwalten. Die jeweilige Vorgehensweise wird in den folgenden Artikeln beschrieben:

- [Erstellen und Verwalten von Schlüsseltresoren über die CLI](key-vault-manage-with-cli2.md)
- [Erstellen und Verwalten von Schlüsseltresoren mit PowerShell](key-vault-overview.md)
- [Erstellen eines Schlüsseltresors und Hinzufügen eines Geheimnisses über eine Azure Resource Manager-Vorlage](../azure-resource-manager/resource-manager-template-keyvault.md)
- [Erstellen und Verwalten von Schlüsseltresoren über REST](/rest/api/keyvault/)


## <a name="coding-with-key-vault"></a>Programmieren mit dem Schlüsseltresor

Das Key Vault-Verwaltungssystem für Programmierer besteht aus mehreren Schnittstellen. Dieser Abschnitt enthält Links zu allen Programmier- und Skriptsprachen und zu einigen Codebeispielen. 

### <a name="supported-programming-and-scripting-languages"></a>Unterstützte Programmier- und Skriptsprachen

#### <a name="rest"></a>REST

Über die REST-Schnittstelle können Sie auf sämtliche Key Vault-Ressourcen wie Schlüsseltresore, Schlüssel, Geheimnisse zugreifen. 

[Referenz für die Key Vault-REST-API](/rest/api/keyvault/).

#### <a name="net"></a>.NET

[.NET-API-Referenz für Key Vault](/dotnet/api/microsoft.azure.keyvault).

Weitere Informationen über die .NET SDK-Version 2.x finden Sie in den [Anmerkungen zu dieser Version](key-vault-dotnet2api-release-notes.md).

#### <a name="java"></a>Java

[Java SDK für Key Vault](/java/api/overview/azure/keyvault)

#### <a name="nodejs"></a>Node.js

Die Key Vault-Verwaltungs-API und die Key Vault-Objekt-API sind in Node.js getrennt. In den folgenden Übersichtsartikeln erhalten Sie weitere Informationen zu beiden. 

[Azure Key Vault-Module für Node.js](/nodejs/api/overview/azure/key-vault)

#### <a name="python"></a>Python

[Azure Key Vault-Bibliotheken für Python](/python/api/overview/azure/key-vault)

#### <a name="azure-cli-2"></a>Azure CLI 2

[Azure CLI für Key Vault](/cli/azure/keyvault)

#### <a name="azure-powershell"></a>Azure PowerShell 

[Azure PowerShell für Key Vault](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)

### <a name="quick-start-guides"></a>Schnellstarthandbücher

- [Erstellen eines Schlüsseltresors](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)
- [Erste Schritte mit Key Vault in Node.js](https://github.com/Azure-Samples/key-vault-node-getting-started)

### <a name="code-examples"></a>Codebeispiele

Vollständige Beispiele für die Verwendung von Key Vault mit Ihren Anwendungen finden Sie hier:

- [Azure Key Vault-Codebeispiele](https://azure.microsoft.com/resources/samples/?service=key-vault) – Codebeispiele für Azure Key Vault. 
- [Verwenden von Azure Key Vault in einer Webanwendung](quick-create-net.md) In diesem Tutorial erfahren Sie, wie Sie Azure Key Vault in einer Webanwendung in Azure verwenden. 

## <a name="how-tos"></a>Vorgehensweisen

Die folgenden Artikel und Szenarien bieten aufgabenspezifische Anleitungen für die Arbeit mit Azure Key Vault:

- [Ändern der Mandanten-ID des Schlüsseltresors nach einer Abonnementverschiebung:](key-vault-subscription-move-fix.md) Wenn Sie Ihr Azure-Abonnement aus Mandant A in Mandant B verschieben, können die Prinzipale (Benutzer und Anwendungen) in Mandant B nicht auf Ihre vorhandenen Schlüsseltresore zugreifen. Gehen Sie wie folgt vor, um dies zu beheben.
- [Zugreifen auf Schlüsseltresore hinter der Firewall:](key-vault-access-behind-firewall.md) Für den Zugriff auf einen Schlüsseltresor muss Ihre Schlüsseltresor-Clientanwendung auf mehrere Endpunkte für unterschiedliche Funktionen zugreifen können.
- [Generieren und Übertragen von HSM-geschützten Schlüsseln für Azure Key Vault](key-vault-hsm-protected-keys.md) : Verwenden Sie diesen Artikel zum Planen, Generieren und anschließenden Übertragen Ihrer eigenen HSM-geschützten Schlüssel für die Nutzung mit Azure Key Vault.
- [Übergeben sicherer Werte während der Bereitstellung:](../azure-resource-manager/resource-manager-keyvault-parameter.md) Wenn Sie während der Bereitstellung einen sicheren Wert (z.B. ein Kennwort) als Parameter übergeben müssen, können Sie diesen Wert als Geheimnis in Azure Key Vault speichern und in anderen Resource Manager-Vorlagen auf ihn verweisen.
- [Erweiterbare Schlüsselverwaltung mit Azure Key Vault (SQL Server)](https://msdn.microsoft.com/library/dn198405.aspx) : Der SQL Server-Connector für Azure Key Vault ermöglicht SQL Server und SQL-in-a-VM die Nutzung des Diensts Azure Key Vault als EKM-Anbieter (Extensible Key Management), um die Verschlüsselungsschlüssel für Anwendungslinks zu schützen – transparente Datenverschlüsselung, Verschlüsseln von Sicherungen und Verschlüsselung auf Spaltenebene.
- [Bereitstellen von Zertifikaten für VMs aus Key Vault](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) : Eine in einer VM in Azure ausgeführte Cloudanwendung benötigt ein Zertifikat. Wie wird dieses Zertifikat derzeit für eine solche VM bereitgestellt?
- [Einrichten des Schlüsseltresors mit End-to-End-Schlüsselrotation und Überwachung](key-vault-key-rotation-log-monitoring.md) bietet eine exemplarische Vorgehensweise zum Einrichten der Schlüsselrotation und Protokollüberwachung mit Azure Key Vault.
- [Deploying Azure Web App Certificate through Key Vault]( https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/) (Bereitstellen von Azure-Web-App-Zertifikaten über Key Vault) enthält schrittweise Anleitungen zum Bereitstellen von Zertifikaten, die im Schlüsseltresor gespeichert sind, im Rahmen des [App Service Certificate](https://azure.microsoft.com/blog/internals-of-app-service-certificate/)-Angebots.
- [Erteilen von Berechtigungen für viele Anwendungen für den Zugriff auf einen Schlüsseltresor](key-vault-group-permissions-for-apps.md): Die Key Vault-Zugriffssteuerungsrichtlinie unterstützt bis zu 1024 Einträge. Sie können aber eine Azure Active Directory-Sicherheitsgruppe erstellen. Fügen Sie dieser Sicherheitsgruppe alle zugeordneten Dienstprinzipale hinzu, und gewähren Sie dieser Sicherheitsgruppe dann Zugriff auf Key Vault.
- Aufgabenspezifische Anleitungen zum Integrieren und Verwenden von Schlüsseltresoren mit Azure finden Sie in den [Beispielen für Azure Resource Manager-Vorlagen für Key Vault von Ryan Jones](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
- [Verwenden des vorläufigen Löschens in Key Vault mit der CLI](key-vault-soft-delete-cli.md) enthält Informationen zur Verwendung und zum Lebenszyklus eines Schlüsseltresors und verschiedener Schlüsseltresorobjekte, für den bzw. die vorläufiges Löschen aktiviert ist.
- [Verwenden des vorläufigen Löschens in Key Vault mit PowerShell](key-vault-soft-delete-powershell.md) enthält Informationen zur Verwendung und zum Lebenszyklus eines Schlüsseltresors und verschiedener Schlüsseltresorobjekte, für den bzw. die vorläufiges Löschen aktiviert ist.

## <a name="integrated-with-key-vault"></a>Key Vault-Integration

Diese Artikel enthalten Informationen zu anderen Szenarien und Diensten, in denen Key Vault oder eine Key Vault-Integration zum Einsatz kommt.

- Bei der [Azure Disk Encryption](../security/azure-security-disk-encryption.md) werden das Branchenstandardfeature [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) von Windows und das Feature [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) von Linux verwendet, um Volumeverschlüsselung für das Betriebssystem und die Datenträger bereitzustellen. Die Lösung ist in Azure Key Vault integriert, damit Sie die Schlüssel für die Datenträgerverschlüsselung und die geheimen Schlüssel unter Ihrem Schlüsseltresorabonnement steuern und verwalten können. Gleichzeitig können Sie sicherstellen, dass alle Daten auf den Datenträgern von virtuellen Computern im Ruhezustand in Ihrem Azure-Speicher verschlüsselt sind.
- [Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md) bietet eine Option zur Verschlüsselung von Daten, die im Konto gespeichert sind. Für die Schlüsselverwaltung bietet Data Lake Store zwei Modi zur Verwaltung Ihrer Master-Verschlüsselungsschlüssel (Master Encryption Keys, MEKs), die zum Entschlüsseln von Daten erforderlich sind, die im Data Lake Store gespeichert sind. Sie können die MEKs entweder von Data Lake Store für sich verwalten lassen, oder Sie verwenden das Azure Key Vault-Konto und bleiben im Besitz der MEKs. Beim Erstellen eines Data Lake Store-Kontos geben Sie die Art der Schlüsselverwaltung an.
- [Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) ermöglicht Ihnen, Ihren eigenen Mandantenschlüssel zu verwalten. Anstatt z.B. Ihren Mandantenschlüssel von Microsoft verwalten zu lassen (Standard), können Sie Ihren eigenen Mandantenschlüssel verwalten, um bestimmte Vorschriften zu erfüllen, die in Ihrer Organisation gelten. Das Verwalten Ihres eigenen Mandantenschlüssels wird auch als „Bring Your Own Key“ (BYOK) bezeichnet.

## <a name="key-vault-overviews-and-concepts"></a>Key Vault-Übersichten und -Konzepte

- [Übersicht über die Azure Key Vault-Funktion für vorläufiges Löschen](key-vault-ovw-soft-delete.md) beschreibt ein Feature, dass die Wiederherstellung gelöschter Objekte unabhängig davon ermöglicht, ob die Löschung versehentlich oder absichtlich erfolgte.
- [Anleitung zur Drosselung von Azure Key Vault](key-vault-ovw-throttling.md) informiert Sie über die grundlegenden Konzepte der Drosselung und bietet einen Ansatz für Ihre App.
- [Key Vault storage account keys overview](key-vault-ovw-storage-keys.md) (Übersicht zu Azure-Speicherkontoschlüsseln) beschreibt die Key Vault-Integration in Azure-Speicherkontoschlüssel.
- [Sicherheitsumgebungen und geografische Grenzen von Azure Key Vault](key-vault-ovw-security-worlds.md) beschreibt die Beziehungen zwischen Regionen und Sicherheitsbereichen.

## <a name="social"></a>Soziale Netzwerke

- [Key Vault Blog](https://aka.ms/kvblog)
- [Key Vault Forum](https://aka.ms/kvforum)

## <a name="supporting-libraries"></a>Unterstützung von Bibliotheken

- Die [Microsoft Azure Key Vault-Kernbibliothek](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) bietet die Schnittstellen **IKey** und **IKeyResolver**, um Schlüssel von Bezeichnern zu ermitteln und mit diesen Schlüsseln Vorgänge auszuführen.
- [Microsoft Azure-Schlüsseltresorerweiterungen](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) bieten erweiterte Funktionen für den Azure-Schlüsseltresor.
