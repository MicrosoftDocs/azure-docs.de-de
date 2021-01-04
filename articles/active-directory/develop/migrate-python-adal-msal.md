---
title: Leitfaden für die Migration von ADAL zu MSAL für Python | Azure
titleSuffix: Microsoft identity platform
description: Erfahren Sie, wie Sie Ihre Python-App mit ADAL (Azure Active Directory Authentication Library) zu MSAL (Microsoft Authentication Library) für Python migrieren.
services: active-directory
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Python
ms.workload: identity
ms.date: 11/11/2019
ms.author: rayluo
ms.reviewer: marsma, rayluo, nacanuma
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 731c608b8ac280a3a3c4e77c3a94533d258cb85d
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96618041"
---
# <a name="adal-to-msal-migration-guide-for-python"></a>Leitfaden für die Migration von ADAL zu MSAL für Python

In diesem Artikel werden die Änderungen beschrieben, die Sie durchführen müssen, um eine App, die ADAL (Azure Active Directory Authentification Library) verwendet, zu MSAL (Microsoft Authentication Library) zu migrieren.

## <a name="difference-highlights"></a>Wichtige Unterschiede

ADAL arbeitet mit dem Azure Active Directory v1.0-Endpunkt (Azure AD). MSAL (Microsoft Authentication Library) arbeitet mit Microsoft Identity Platform (ehemals bekannt als Azure Active Directory v2.0-Endpunkt). Microsoft Identity Platform weist einige Unterschiede im Vergleich zu Azure AD v1.0 auf:

Unterstützt:
  - Geschäfts-, Schul- oder Unikonten (von Azure AD bereitgestellte Konten)
  - Persönliche Konten (z. B. Outlook.com oder Hotmail.com)
  - Kunden mit eigenen E-Mail-Adressen oder Social Media-Konten (z. B. LinkedIn, Facebook, Google) über das Azure AD B2C-Angebot

- Kompatible Standards:
  - OAuth v2.0
  - OpenID Connect (OIDC)

Weitere Informationen finden Sie unter [Neuerungen des Microsoft Identity Platform (v 2.0)-Endpunkts](../azuread-dev/azure-ad-endpoint-comparison.md).

### <a name="scopes-not-resources"></a>Geltungsbereiche im Gegensatz zu Ressourcen

ADAL Python ruft Token für Ressourcen ab, während MSAL Python Token für Geltungsbereiche abruft. Die API-Oberfläche in MSAL Python weist keinen Ressourcenparameter mehr auf. Sie müssten Geltungsbereiche in Form einer Liste von Zeichenfolgen bereitstellen, welche die gewünschten Berechtigungen und Ressourcen deklarieren, die angefordert werden. Einige Beispiele für Geltungsbereiche finden Sie unter [Geltungsbereiche von Microsoft Graph](/graph/permissions-reference).

Sie können der Ressource das Bereichssuffix `/.default` hinzufügen, um die Migration Ihrer Apps vom v1.0-Endpunkt (ADAL) zum Microsoft Identity Platform-Endpunkt (MSAL) zu unterstützen. Beispielsweise entspricht dem Ressourcenwert `https://graph.microsoft.com` der entsprechende Bereichswert `https://graph.microsoft.com/.default`.  Wenn sich die Ressource nicht im URL-Format befindet, aber eine Ressourcen-ID im Format `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX` vorliegt, können Sie weiterhin den Bereichswert `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX/.default` verwenden.

Weitere Informationen zu den verschiedenen Bereichstypen finden Sie in den Artikeln [Berechtigungen und Zustimmung im Microsoft Identity Platform-Endpunkt](./v2-permissions-and-consent.md) und [Geltungsbereiche für eine Web-API, die v1.0-Token akzeptiert](./msal-v1-app-scopes.md).

### <a name="error-handling"></a>Fehlerbehandlung

ADAL (Azure Active Directory Authentication Library) für Python verwendet die Ausnahme `AdalError`, um anzugeben, dass ein Problem aufgetreten ist. MSAL für Python verwendet dafür in der Regel Fehlercodes. Weitere Informationen finden Sie unter [MSAL für Python – Fehlerbehandlung](./msal-handling-exceptions.md?tabs=python).

### <a name="api-changes"></a>API-Änderungen

In der folgenden Tabelle finden Sie eine API in ADAL für Python und eine, die stattdessen in MSAL für Python verwendet wird:

| API – ADAL für Python  | API – MSAL für Python |
| ------------------- | ---------------------------------- |
| [AuthenticationContext](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext)  | [PublicClientApplication or ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__)  |
| –  | [get_authorization_request_url()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.get_authorization_request_url)  |
| [acquire_token_with_authorization_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_authorization_code) | [acquire_token_by_authorization_code()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_by_authorization_code) |
| [acquire_token()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token) | [acquire_token_silent()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_silent) |
| [acquire_token_with_refresh_token()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_refresh_token) | – |
| [acquire_user_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_user_code) | [initiate_device_flow()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.initiate_device_flow) |
| [acquire_token_with_device_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_device_code) und [cancel_request_to_get_token_with_device_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.cancel_request_to_get_token_with_device_code) | [acquire_token_by_device_flow()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow) |
| [acquire_token_with_username_password()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_username_password) | [acquire_token_by_username_password()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_username_password) |
| [acquire_token_with_client_credentials()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_credentials) und [acquire_token_with_client_certificate()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_certificate) | [acquire_token_for_client()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client) |
| – | [acquire_token_on_behalf_of()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) |
| [TokenCache()](https://adal-python.readthedocs.io/en/latest/#adal.TokenCache) | [SerializableTokenCache()](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) |
| – | Cache mit Persistenz, verfügbar über [MSAL-Erweiterungen](https://github.com/marstr/original-microsoft-authentication-extensions-for-python) |

## <a name="migrate-existing-refresh-tokens-for-msal-python"></a>Migrieren vorhandener Aktualisierungstoken für MSAL Python

In MSAL (Microsoft Authentication Library) wird das Konzept der Aktualisierungstoken abstrahiert. MSAL Python stellt standardmäßig einen Tokencache im Arbeitsspeicher bereit, sodass Sie keine Aktualisierungstoken speichern, suchen oder aktualisieren müssen. Benutzern werden auch weniger Anmeldeaufforderungen angezeigt, da Aktualisierungstoken in der Regel ohne Benutzereingriff aktualisiert werden können. Weitere Informationen zum Tokencache finden Sie unter [Benutzerdefinierte Tokencacheserialisierung in MSAL für Python](msal-python-token-cache-serialization.md).

Der folgende Code hilft Ihnen beim Migrieren Ihrer von einer anderen OAuth2-Bibliothek (einschließlich, aber nicht beschränkt auf ADAL Python) verwalteten Aktualisierungstoken, damit diese von MSAL für Python verwaltet werden. Ein Grund für die Migration dieser Aktualisierungstoken besteht darin, zu verhindern, dass sich vorhandene Benutzer beim Migrieren Ihrer App zu MSAL für Python erneut anmelden müssen.

Als Methode zum Migrieren eines Aktualisierungstokens wird MSAL für Python verwendet, um mithilfe des vorherigen Aktualisierungstokens ein neues Zugriffstoken abzurufen. Wenn das neue Aktualisierungstoken zurückgegeben wird, speichert MSAL für Python es im Cache.
Ab MSAL Python 1.3.0 stellen wir zu diesem Zweck in MSAL eine API bereit.
Siehe hierzu den folgenden Codeausschnitt, der [einem Beispiel für die abgeschlossene Migration von Aktualisierungstoken mit MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.3.0/sample/migrate_rt.py#L28-L67) entnommen wurde.

```python
import msal
def get_preexisting_rt_and_their_scopes_from_elsewhere():
    # Maybe you have an ADAL-powered app like this
    #   https://github.com/AzureAD/azure-activedirectory-library-for-python/blob/1.2.3/sample/device_code_sample.py#L72
    # which uses a resource rather than a scope,
    # you need to convert your v1 resource into v2 scopes
    # See https://docs.microsoft.com/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources
    # You may be able to append "/.default" to your v1 resource to form a scope
    # See https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#the-default-scope

    # Or maybe you have an app already talking to Microsoft identity platform v2,
    # powered by some 3rd-party auth library, and persist its tokens somehow.

    # Either way, you need to extract RTs from there, and return them like this.
    return [
        ("old_rt_1", ["scope1", "scope2"]),
        ("old_rt_2", ["scope3", "scope4"]),
        ]


# We will migrate all the old RTs into a new app powered by MSAL
app = msal.PublicClientApplication(
    "client_id", authority="...",
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# We choose a migration strategy of migrating all RTs in one loop
for old_rt, scopes in get_preexisting_rt_and_their_scopes_from_elsewhere():
    result = app.acquire_token_by_refresh_token(old_rt, scopes)
    if "error" in result:
        print("Discarding unsuccessful RT. Error: ", json.dumps(result, indent=2))

print("Migration completed")
```


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter [Vergleich von v1.0 und v2.0](../azuread-dev/azure-ad-endpoint-comparison.md).
