---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 11/25/2018
ms.author: crdun
ms.openlocfilehash: deb94cab97bd9a402676cdc5c0239da8d07ed8b2
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178779"
---
Im vorhergehenden Beispiel wurde eine Standardanmeldung gezeigt, bei der der Client bei jedem Start der App sowohl den Identitätsanbieter als auch den Azure-Back-End-Dienst kontaktieren muss. Diese Methode ist allerdings ineffizient und kann zu Problemen bei der Nutzung führen, wenn viele Kunden die App gleichzeitig starten möchten. Daher empfiehlt es sich, das vom Azure-Dienst zurückgegebene Authentifizierungstoken zwischenzuspeichern und vor einer anbieterbasierten Anmeldung zu verwenden.

> [!NOTE]
> Für die Zwischenspeicherung des vom Azure-Back-End-Dienst zurückgegebenen Authentifizierungstokens spielt es keine Rolle, ob Sie die clientverwaltete oder die dienstverwaltete Authentifizierung verwenden. In diesem Lernprogramm wird die dienstverwaltete Authentifizierung verwendet.
>
>

1. Öffnen Sie die Datei "ToDoActivity.java", und fügen Sie die folgenden import-Anweisungen ein:

    ```java
    import android.content.Context;
    import android.content.SharedPreferences;
    import android.content.SharedPreferences.Editor;
    ```

2. Fügen Sie die folgenden Mitglieder zur `ToDoActivity` -Klasse hinzu:

    ```java
    public static final String SHAREDPREFFILE = "temp";
    public static final String USERIDPREF = "uid";
    public static final String TOKENPREF = "tkn";
    ```

3. Fügen Sie der Datei „ToDoActivity.java“ die folgende Definition für die `cacheUserToken`-Methode hinzu.

    ```java
    private void cacheUserToken(MobileServiceUser user)
    {
        SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
        Editor editor = prefs.edit();
        editor.putString(USERIDPREF, user.getUserId());
        editor.putString(TOKENPREF, user.getAuthenticationToken());
        editor.commit();
    }
    ```

    Diese Methode speichert die Benutzer-ID und das Token in einer als privat gekennzeichneten Einstellungsdatei. Dadurch wird der Zugriff auf den Cache geschützt, sodass andere Apps auf dem Gerät nicht auf das Token zugreifen können. Die Einstellung ist für die App in einer Sandbox isoliert. Trotzdem ist es möglich, dass jemand, der Zugriff auf das Gerät erlangt hat, auf andere Weise auf den Tokencache zugreift.

   > [!NOTE]
   > Falls es sich um besonders sensible Daten handelt und die Gefahr besteht, dass andere Benutzer auf das Gerät zugreifen, können Sie das Token zu seinem Schutz zusätzlich verschlüsseln. Eine absolut sichere Lösung geht allerdings über den Rahmen dieses Tutorial hinaus und hängt zudem von Ihren Sicherheitsanforderungen ab.
   >
   >

4. Fügen Sie der Datei „ToDoActivity.java“ die folgende Definition für die `loadUserTokenCache`-Methode hinzu.

    ```java
    private boolean loadUserTokenCache(MobileServiceClient client)
    {
        SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
        String userId = prefs.getString(USERIDPREF, null);
        if (userId == null)
            return false;
        String token = prefs.getString(TOKENPREF, null);
        if (token == null)
            return false;

        MobileServiceUser user = new MobileServiceUser(userId);
        user.setAuthenticationToken(token);
        client.setCurrentUser(user);

        return true;
    }
    ```

5. Ersetzen Sie in der Datei *ToDoActivity.java* die Methoden `authenticate` und `onActivityResult` durch die folgenden, die einen Tokencache verwenden. Wechseln Sie den Login-Anbieter, wenn Sie ein anderes Konto als Google verwenden möchten.

    ```java
    private void authenticate() {
        // We first try to load a token cache if one exists.
        if (loadUserTokenCache(mClient))
        {
            createTable();
        }
        // If we failed to load a token cache, sign in and create a token cache
        else
        {
            // Sign in using the Google provider.
            mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
        }
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        // When request completes
        if (resultCode == RESULT_OK) {
            // Check the request code matches the one we send in the sign-in request
            if (requestCode == GOOGLE_LOGIN_REQUEST_CODE) {
                MobileServiceActivityResult result = mClient.onActivityResult(data);
                if (result.isLoggedIn()) {
                    // sign-in succeeded
                    createAndShowDialog(String.format("You are now signed in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
                    cacheUserToken(mClient.getCurrentUser());
                    createTable();
                } else {
                    // sign-in failed, check the error message
                    String errorMessage = result.getErrorMessage();
                    createAndShowDialog(errorMessage, "Error");
                }
            }
        }
    }
    ```

6. Erstellen Sie die App und testen Sie die Authentifizierung mit einem gültigen Konto. Führen Sie sie mindestens zweimal aus. Bei der ersten Ausführung sollten Sie eine Aufforderung zur Anmeldung und Erstellung des Tokencaches erhalten. Danach wird bei jeder Ausführung versucht, den Tokencache für die Authentifizierung zu laden. Nun sollte keine Anmeldung mehr erforderlich sein.
