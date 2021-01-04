---
title: Beheben von Problemen bei der Anmeldung bei der Registrierung
description: Symptome, Ursachen und Lösungen allgemeiner Probleme bei der Anmeldung bei einer Azure Container Registry
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 5499c64bef8ce36a5f622c4d847b417ef49a5a03
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93379501"
---
# <a name="troubleshoot-registry-login"></a>Beheben von Problemen mit der Registrierungsanmeldung

Dieser Artikel hilft Ihnen bei der Behebung von Problemen, die bei der Anmeldung bei einer Azure Container Registry auftreten können. 

## <a name="symptoms"></a>Symptome

Mindestens eines der folgenden Probleme kann ein Symptom sein:

* Anmeldung in der Registrierung mit `docker login`, `az acr login` oder beidem nicht möglich.
* Anmeldung bei der Registrierung ist nicht möglich, und Sie erhalten den Fehler `unauthorized: authentication required` oder `unauthorized: Application not registered with AAD`.
* Anmeldung bei der Registrierung ist nicht möglich, und Sie erhalten den Azure CLI-Fehler `Could not connect to the registry login server`.
* Images können nicht gepusht oder gepullt werden, und Sie erhalten den Docker-Fehler `unauthorized: authentication required`.
* Zugriff auf die Registrierung über Azure Kubernetes Service, Azure DevOps oder einen anderen Azure-Dienst ist nicht möglich.
* Auf die Registrierung kann nicht zugegriffen werden, und Sie erhalten den Fehler `Error response from daemon: login attempt failed with status: 403 Forbidden` (weitere Informationen finden Sie unter [Beheben von Netzwerkproblemen mit der Registrierung](container-registry-troubleshoot-access.md)).
* Der Zugriff auf oder das Anzeigen von Registrierungseinstellungen im Azure-Portal oder das Verwalten der Registrierung mithilfe der Azure CLI ist nicht möglich.

## <a name="causes"></a>Ursachen

* Docker ist in Ihrer Umgebung nicht ordnungsgemäß: [Lösung](#check-docker-configuration).
* Die Registrierung ist nicht vorhanden, oder der Name ist falsch: [Lösung](#specify-correct-registry-name).
* Die Registrierungsanmeldeinformationen sind nicht gültig: [Lösung](#confirm-credentials-to-access-registry).
* Die Anmeldeinformationen sind für Push-, Pull- oder Azure Resource Manager-Vorgänge nicht autorisiert: [Lösung](#confirm-credentials-are-authorized-to-access-registry).
* Die Anmeldeinformationen sind abgelaufen: [Lösung](#check-that-credentials-arent-expired).

## <a name="further-diagnosis"></a>Weitere Diagnose 

Führen Sie den Befehl [az acr check-health](/cli/azure/acr#az-acr-check-health) aus, um weitere Informationen zur Integrität der Registrierungsumgebung abzurufen und optional Zugriff auf eine Zielregistrierung zu erhalten. Beispielsweise können Sie Docker-Konfigurationsfehler oder Azure Active Directory-Anmeldeprobleme diagnostizieren. 

Befehlsbeispiele finden Sie unter [Überprüfen der Integrität einer Azure Container Registry](container-registry-check-health.md). Wenn Fehler gemeldet werden, überprüfen Sie die [Fehlerreferenz](container-registry-health-error-reference.md) und die folgenden Abschnitte für empfohlene Lösungen.

> [!NOTE]
> Einige Authentifizierungs- oder Autorisierungsfehler können auch auftreten, wenn es Firewall- oder Netzwerkkonfigurationen gibt, die Zugriff auf die Registrierung verhindern. Weitere Informationen finden Sie unter [Beheben von Netzwerkproblemen mit der Registrierung](container-registry-troubleshoot-access.md).

## <a name="potential-solutions"></a>Mögliche Lösungen

### <a name="check-docker-configuration"></a>Überprüfen der Docker-Konfiguration

Die meisten Azure Container Registry-Authentifizierungsflows erfordern eine lokale Docker-Installation, damit Sie sich bei Ihrer Registrierung für Vorgänge wie das Pushen und Pullen von Images authentifizieren können. Vergewissern Sie sich, dass der Docker CLI-Client und -Daemon (Docker-Engine) in Ihrer Umgebung ausgeführt werden. Sie benötigen die Docker-Clientversion 18.03 oder höher.

Verwandte Links:

* [Übersicht über die Authentifizierung](container-registry-authentication.md#authentication-options)
* [Häufig gestellte Fragen (FAQ) zur Containerregistrierung](container-registry-faq.md)

### <a name="specify-correct-registry-name"></a>Angeben des richtigen Registrierungsnamens

Wenn Sie `docker login` verwenden, geben Sie den vollständigen Anmeldeservernamen der Registrierung an, z. B. *myregistry.azurecr.io*. Stellen Sie sicher, dass Sie nur Kleinbuchstaben verwenden. Beispiel:

```console
docker login myregistry.azurecr.io
```

Wenn Sie [az acr login](/cli/azure/acr#az-acr-login) mit einer Azure Active Directory-Identität verwenden, [melden Sie sich zuerst bei der Azure CLI an](/cli/azure/authenticate-azure-cli), und geben dann den Azure-Ressourcennamen der Registrierung an. Der Ressourcenname ist der Name, der beim Erstellen der Registrierung bereitgestellt wird, z. B. *myregistry* (ohne Domänensuffix). Beispiel:

```azurecli
az acr login --name myregistry
```

Verwandte Links:

* [az acr login erfolgreich, aber für Docker tritt ein Fehler auf: "Nicht autorisiert: Authentifizierung erforderlich"](container-registry-faq.md#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required )

### <a name="confirm-credentials-to-access-registry"></a>Bestätigen der Anmeldeinformationen für den Zugriff auf die Registrierung

Überprüfen Sie die Gültigkeit der Anmeldeinformationen, die Sie für Ihr Szenario verwenden, oder die für Sie von einem Registrierungsbesitzer bereitgestellt wurden. Einige mögliche Probleme:

* Wenn Sie einen Active Directory-Dienstprinzipal verwenden, stellen Sie sicher, dass Sie die richtigen Anmeldeinformationen im Active Directory-Mandanten verwenden:
  * User Name (Benutzername): Anwendungs-ID des Dienstprinzipals (auch als *Client-ID* bezeichnet)
  * Password (Kennwort): Kennwort des Dienstprinzipals (auch als *geheimer Clientschlüssel* bezeichnet)
* Wenn Sie einen Azure-Dienst wie Azure Kubernetes Service oder Azure DevOps für den Zugriff auf die Registrierung verwenden, bestätigen Sie die Registrierungskonfiguration für Ihren Dienst.
* Wenn Sie `az acr login` mit der `--expose-token`-Option ausgeführt haben, die die Registrierungsanmeldung ohne den Docker-Daemon ermöglicht, stellen Sie sicher, dass Sie sich mit dem Benutzernamen `00000000-0000-0000-0000-000000000000` authentifizieren.
* Wenn Ihre Registrierung für [anonymen Pullzugriff](container-registry-faq.md#how-do-i-enable-anonymous-pull-access) konfiguriert ist, können vorhandene Docker-Anmeldeinformationen, die von einer früheren Docker-Anmeldung gespeichert wurden, den anonymen Zugriff verhindern. Führen Sie `docker logout` aus, bevor Sie einen anonymen Pullvorgang auf die Registrierung anwenden.

Verwandte Links:

* [Übersicht über die Authentifizierung](container-registry-authentication.md#authentication-options)
* [Individuelle Anmeldung bei Azure AD](container-registry-authentication.md#individual-login-with-azure-ad)
* [Anmelden mit dem Dienstprinzipal](container-registry-auth-service-principal.md)
* [Anmelden mit der verwalteten Identität](container-registry-authentication-managed-identity.md)
* [Anmelden mit einem Token mit Repositorygültigkeitsbereich](container-registry-repository-scoped-permissions.md)
* [Anmelden mit dem Administratorkonto](container-registry-authentication.md#admin-account)
* [Azure AD-Authentifizierungs- und -Autorisierungsfehlercodes](../active-directory/develop/reference-aadsts-error-codes.md)
* [az acr Login](/cli/azure/acr#az-acr-login)-Referenz

### <a name="confirm-credentials-are-authorized-to-access-registry"></a>Bestätigen, dass die Anmeldeinformationen für den Zugriff auf die Registrierung autorisiert sind

Überprüfen Sie die Registrierungsberechtigungen, die mit den Anmeldeinformationen verknüpft sind, z. B. die Azure-Rolle `AcrPull` zum Pullen von Images aus der Registrierung und die Rolle `AcrPush` zum Pushen von Images. 

Zugriff auf eine Registrierung im Portal oder die Registrierungsverwaltung über die Azure CLI erfordert mindestens die Rolle `Reader` oder entsprechende Berechtigungen, um Azure Resource Manager-Vorgänge auszuführen.

Wenn Ihre Berechtigungen vor Kurzem geändert wurden, um den Zugriff auf die Registrierung über das Portal zuzulassen, müssen Sie möglicherweise eine Inkognito- oder private Sitzung in Ihrem Browser verwenden, um Probleme aufgrund veralteter Browsercaches oder Cookies zu vermeiden.

Sie oder ein Registrierungsbesitzer müssen über ausreichende Berechtigungen im Abonnement verfügen, um Rollenzuweisungen hinzuzufügen oder zu entfernen.

Verwandte Links:

* [Azure Container Registry: Rollen und Berechtigungen](container-registry-roles.md)
* [Anmelden mit einem Token mit Repositorygültigkeitsbereich](container-registry-repository-scoped-permissions.md)
* [Hinzufügen oder Entfernen von Azure-Rollenzuweisungen über das Azure-Portal](../role-based-access-control/role-assignments-portal.md)
* [Erstellen einer Azure AD-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff über das Portal](../active-directory/develop/howto-create-service-principal-portal.md)
* [Erstellen eines neuen Anwendungsgeheimnisses](../active-directory/develop/howto-create-service-principal-portal.md#option-2-create-a-new-application-secret)
* [Azure AD-Authentifizierungs- und Autorisierungscodes](../active-directory/develop/reference-aadsts-error-codes.md)

### <a name="check-that-credentials-arent-expired"></a>Überprüfen, ob Anmeldeinformationen abgelaufen sind

Token und Active Directory-Anmeldeinformationen können nach festgelegten Zeiträumen ablaufen und somit Registrierungszugriff verhindern. Zum Aktivieren des Zugriffs müssen Anmeldeinformationen möglicherweise zurückgesetzt oder neu generiert werden.

* Wenn eine einzelne AD-Identität, eine verwaltete Identität oder ein Dienstprinzipal für die Registrierungsanmeldung verwendet wird, läuft das AD-Token nach 3 Stunden ab. Melden Sie sich erneut bei der Registrierung an.  
* Wenn Sie einen AD-Dienstprinzipal mit einem abgelaufenen geheimen Clientschlüssel verwenden, muss ein Abonnementbesitzer oder Kontoadministrator Anmeldeinformationen zurücksetzen oder einen neuen Dienstprinzipal generieren.
* Bei Verwendung eines [Tokens mit Repositorygültigkeitsbereich](container-registry-repository-scoped-permissions.md) muss ein Registrierungsbesitzer möglicherweise ein Kennwort zurücksetzen oder ein neues Token generieren.

Verwandte Links:

* [Zurücksetzen von Anmeldeinformationen für Dienstprinzipal](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset)
* [Erneutes Generieren von Tokenkennwörtern](container-registry-repository-scoped-permissions.md#regenerate-token-passwords)
* [Individuelle Anmeldung bei Azure AD](container-registry-authentication.md#individual-login-with-azure-ad)

## <a name="advanced-troubleshooting"></a>Erweiterte Problembehandlung

Wenn [Sammlung von Ressourcenprotokollen](container-registry-diagnostics-audit-logs.md) in der Registrierung aktiviert ist, überprüfen Sie das Protokoll „ContainterRegistryLoginEvents“. In diesem Protokoll werden Registrierungauthentifizierungsereignisse und der -status gespeichert, einschließlich der eingehenden Identität und IP-Adresse. Fragen Sie das Protokoll nach [Authentifizierungsfehlern für die Registrierung](container-registry-diagnostics-audit-logs.md#registry-authentication-failures) ab. 

Verwandte Links:

* [Protokolle für die Diagnoseauswertung und -überwachung](container-registry-diagnostics-audit-logs.md)
* [Häufig gestellte Fragen (FAQ) zur Containerregistrierung](container-registry-faq.md)
* [Bewährte Methoden für Azure Container Registry](container-registry-best-practices.md)

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie Ihr Problem hier nicht lösen können, sehen Sie sich die folgenden Optionen an.

* Weitere Themen zur Problembehandlung der Registrierung sind:
  * [Beheben von Netzwerkproblemen mit der Registrierung](container-registry-troubleshoot-access.md)
  * [Beheben von Problemen mit der Registrierungsleistung](container-registry-troubleshoot-performance.md)
* [Optionen für Support durch die Community](https://azure.microsoft.com/support/community/)
* [Microsoft Q&A (Fragen und Antworten)](/answers/products/)
* [Öffnen eines Supporttickets](https://azure.microsoft.com/support/create-ticket/): Basierend auf den von Ihnen bereitgestellten Informationen kann eine schnelle Diagnose für Authentifizierungsfehler in Ihrer Registrierung ausgeführt werden.