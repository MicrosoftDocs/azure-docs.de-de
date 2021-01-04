---
title: Behandeln von Problemen mit Schlüsseltresor-Zugriffsrichtlinien
description: Behandeln von Problemen mit Schlüsseltresor-Zugriffsrichtlinien
author: sebansal
ms.author: sebansal
ms.date: 08/10/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: f9bbf6e9d1d932f7225807613b789821c1462932
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94441850"
---
# <a name="troubleshooting-azure-key-vault-access-policy-issues"></a>Behandeln von Problemen mit Schlüsseltresor-Zugriffsrichtlinien

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="how-can-i-identify-how-and-when-key-vaults-are-accessed"></a>Wie kann ich ermitteln, wie und wann auf Schlüsseltresore zugegriffen wird?

Nachdem Sie einen oder mehrere Schlüsseltresore erstellt haben, möchten Sie vermutlich überwachen, wie, wann und von wem auf die Schlüsseltresore zugegriffen wird. Zur Überwachung können Sie die Protokollierung für Azure Key Vault aktivieren. Eine Schrittanleitung zum Aktivieren der Protokollierung finden Sie [hier](./logging.md).

### <a name="how-can-i-monitor-vault-availability-service-latency-periods-or-other-performance-metrics-for-key-vault"></a>Wie kann ich die Tresorverfügbarkeit, Dienstwartezeiten oder andere Leistungsmetriken für einen Schlüsseltresor überwachen?

Wenn Sie damit beginnen, Ihren Dienst zu skalieren, steigt die Anzahl von Anforderungen, die an Ihren Schlüsseltresor gesendet werden. Dies kann die Latenz Ihrer Anforderungen erhöhen und in extremen Fällen zu einer Drosselung Ihrer Anforderungen führen – was sich auf die Leistung Ihres Diensts auswirkt. Sie können Schlüsseltresor-Leistungsmetriken überwachen und Warnungen für bestimmte Schwellenwerte konfigurieren. Eine ausführliche Anleitung zum Konfigurieren der Überwachung finden Sie [hier](./alert.md).

### <a name="i-am-not-able-to-modify-access-policy-how-can-it-be-enabled"></a>Ich kann die Zugriffsrichtlinie nicht ändern. Wie kann sie aktiviert werden?
Der Benutzer muss über ausreichende AAD-Berechtigungen verfügen, um die Zugriffsrichtlinie zu ändern. In diesem Fall muss der Benutzer über die höhere Rolle „Mitwirkender“ verfügen.

### <a name="i-am-seeing-unkwown-policy-error-what-does-that-mean"></a>Mir wird der Fehler „Unbekannte Richtlinie“ angezeigt. Was bedeutet das?
Es gibt zwei verschiedene Gründe dafür, dass im Abschnitt „Unbekannt“ eine Zugriffsrichtlinie angezeigt wird:
* Es gibt einen Benutzer, der zuvor Zugriff hatte, und aus irgendeinem Grund ist der Benutzer nicht vorhanden.
* Eine Zugriffsrichtlinie wird über PowerShell, aber für die Anwendungsobjekt-ID und nicht für den Dienstprinzipal hinzugefügt.

### <a name="how-can-i-assign-access-control-per-key-vault-object"></a>Wie kann ich die Zugriffssteuerung pro Schlüsseltresorobjekt zuweisen? 

Informationen zur Verfügbarkeit des geheimnis-/schlüssel-/zertifikatspezifischen Zugriffssteuerungsfeatures finden Sie [hier](https://feedback.azure.com/forums/906355-azure-key-vault/suggestions/32213176-per-secret-key-certificate-access-control).

### <a name="how-can-i-provide-key-vault-authenticate-using-access-control-policy"></a>Wie kann ich die Schlüsseltresorauthentifizierung per Zugriffssteuerungsrichtlinie bereitstellen?

Die Authentifizierung einer cloudbasierten Anwendung bei Key Vault lässt sich am einfachsten mit einer verwalteten Identität bewerkstelligen. Einzelheiten hierzu finden Sie unter [Authentifizieren bei Azure Key Vault](authentication.md).
Wenn Sie eine lokale Anwendung erstellen, eine lokale Entwicklung durchführen oder eine verwaltete Identität aus anderen Gründen nicht verwenden können, können Sie stattdessen einen Dienstprinzipal manuell registrieren und mithilfe einer Zugriffssteuerungsrichtlinie Zugriff auf Ihren Schlüsseltresor bereitstellen. Informationen finden Sie unter [Zuweisen einer Zugriffssteuerungsrichtlinie](assign-access-policy-portal.md).

### <a name="how-can-i-give-the-ad-group-access-to-the-key-vault"></a>Wie kann ich der AD-Gruppe Zugriff auf den Schlüsseltresor gewähren?

Verwenden Sie entweder den Azure CLI-Befehl `az keyvault set-policy` oder das Azure PowerShell-Cmdlet „Set-AzKeyVaultAccessPolicy“, um der AD-Gruppe Berechtigungen für Ihre Key Vault-Instanz zu erteilen. Informationen dazu finden Sie unter [Zuweisen einer Zugriffsrichtlinie – CLI](assign-access-policy-cli.md) und [Zuweisen einer Zugriffsrichtlinie – PowerShell](assign-access-policy-powershell.md).

Für die Anwendung muss dem Schlüsseltresor darüber hinaus mindestens eine IAM-Rolle (Identity and Access Management, Identitäts- und Zugriffsverwaltung) zugewiesen werden. Andernfalls ist die Anmeldung nicht möglich, und es tritt ein Fehler aufgrund unzureichender Zugriffsberechtigungen für das Abonnement auf. Bei Azure AD-Gruppen mit verwalteten Identitäten dauert es möglicherweise bis zu acht Stunden, bis das Token aktualisiert ist und wirksam wird.

### <a name="how-can-i-redeploy-key-vault-with-arm-template-without-deleting-existing-access-policies"></a>Wie kann ich Key Vault per ARM-Vorlage erneut bereitstellen, ohne vorhandene Zugriffsrichtlinien zu löschen?

Derzeit werden bei der erneuten Key Vault-Bereitstellung alle Zugriffsrichtlinien in Key Vault gelöscht und durch die Zugriffsrichtlinie in der ARM-Vorlage ersetzt. Für Key Vault-Zugriffsrichtlinien steht keine inkrementelle Option zur Verfügung. Wenn die Zugriffsrichtlinien in Key Vault erhalten bleiben sollen, müssen Sie die vorhandene Zugriffsrichtlinien in Key Vault lesen und die ARM-Vorlage mit diesen Richtlinien auffüllen, um Ausfälle beim Zugriff zu vermeiden.

Eine weitere für dieses Szenario hilfreiche Option ist die Verwendung von Azure RBAC und Rollen als Alternative zu Zugriffsrichtlinien. Mit Azure RBAC können Sie den Schlüsseltresor erneut bereitstellen, ohne die Richtlinie erneut anzugeben. [Hier](./rbac-guide.md) finden Sie weitere Informationen zu dieser Lösung.

### <a name="recommended-troubleshooting-steps-for-following-error-types"></a>Empfohlene Schritte zur Problembehandlung bei folgenden Fehlertypen

* HTTP 401: Nicht authentifizierte Anforderung: [Schritte zur Problembehandlung](rest-error-codes.md#http-401-unauthenticated-request)
* HTTP 403: Unzureichende Berechtigungen: [Schritte zur Problembehandlung](rest-error-codes.md#http-403-insufficient-permissions)
* HTTP 429: Zu viele Anforderungen: [Schritte zur Problembehandlung](rest-error-codes.md#http-429-too-many-requests)
* Überprüfen Sie, ob Sie für den Schlüsseltresor über die Zugriffsberechtigung zum Löschen verfügen: Informationen dazu finden Sie unter [Zuweisen einer Zugriffsrichtlinie – CLI](assign-access-policy-cli.md), [Zuweisen einer Zugriffsrichtlinie – PowerShell](assign-access-policy-powershell.md) oder [Zuweisen einer Zugriffsrichtlinie – Portal](assign-access-policy-portal.md).
* Verwenden Sie das [Authentifizierungs-SDK](https://azure.github.io/azure-sdk/posts/2020-02-25/defaultazurecredentials.html), wenn bei Ihnen ein Problem mit der Authentifizierung für den Schlüsseltresor im Code besteht.

### <a name="what-are-the-best-practices-i-should-implement-when-key-vault-is-getting-throttled"></a>Welche bewährten Methoden sollten implementiert werden, wenn der Schlüsseltresor gedrosselt wird?
Bewährte Methoden für diesen Fall finden Sie [hier](overview-throttling.md#how-to-throttle-your-app-in-response-to-service-limits).

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich, wie Sie Fehler bei der Authentifizierung beim Schlüsseltresor beheben: [Leitfaden zur Problembehandlung für Key Vault](rest-error-codes.md).