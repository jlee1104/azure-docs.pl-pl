---
title: Uzyskaj token w aplikacji sieci Web, która wywołuje interfejsy API sieci Web — Microsoft Identity platform | Azure
description: Dowiedz się, jak uzyskać token dla aplikacji sieci Web, która wywołuje interfejsy API sieci Web
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: abf7d800eda376c21dfdd672032ddb65e27355be
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759078"
---
# <a name="a-web-app-that-calls-web-apis-acquire-a-token-for-the-app"></a>Aplikacja sieci Web, która wywołuje interfejsy API sieci Web: uzyskiwanie tokenu dla aplikacji

Obiekt aplikacji klienta został skompilowany. Teraz użyjesz go, aby uzyskać token do wywoływania interfejsu API sieci Web. W ASP.NET lub ASP.NET Core wywoływanie interfejsu API sieci Web odbywa się na kontrolerze:

- Uzyskaj token dla interfejsu API sieci Web za pomocą pamięci podręcznej tokenów. Aby uzyskać ten token, należy wywołać metodę `AcquireTokenSilent`.
- Wywołaj chroniony interfejs API, przekazując do niego token dostępu jako parametr.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Metody kontrolera są chronione przez atrybut `[Authorize]`, który wymusza uwierzytelnienie użytkowników w celu korzystania z aplikacji sieci Web. Oto kod, który wywołuje Microsoft Graph:

```csharp
[Authorize]
public class HomeController : Controller
{
 readonly ITokenAcquisition tokenAcquisition;

 public HomeController(ITokenAcquisition tokenAcquisition)
 {
  this.tokenAcquisition = tokenAcquisition;
 }

 // Code for the controller actions (see code below)

}
```

Usługa `ITokenAcquisition` jest wstrzykiwana przez ASP.NET przy użyciu iniekcji zależności.

Oto uproszczony kod dla akcji `HomeController`, który pobiera token do wywołania Microsoft Graph:

```csharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenOnBehalfOfUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
 string json = await client.GetStringAsync(url);
}
```

Aby lepiej zrozumieć kod wymagany dla tego scenariusza, zobacz krok 2 ([2-1 — wywołania aplikacji sieci Web Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)) w samouczku [MS-Identity-aspnetcore-webapp-samouczka](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) .

Istnieją inne złożone odmiany, takie jak:

- Wywoływanie kilku interfejsów API.
- Przetwarzanie przyrostowej zgody i dostępu warunkowego.

Te zaawansowane kroki przedstawiono w rozdziale 3 samouczka [3-webapp-wiele interfejsów API](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/3-WebApp-multi-APIs) .

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Kod dla ASP.NET jest podobny do kodu pokazanego dla ASP.NET Core:

- Akcja kontrolera, chroniona przez atrybut [autoryzuje], wyodrębnia identyfikator dzierżawy i identyfikator użytkownika `ClaimsPrincipal` elementu członkowskiego kontrolera. (ASP.NET używa `HttpContext.User`.)
- Z tego miejsca kompiluje obiekt MSAL.NET `IConfidentialClientApplication`.
- Na koniec wywołuje metodę `AcquireTokenSilent` poufnej aplikacji klienckiej.

# <a name="javatabjava"></a>[Java](#tab/java)

W przykładzie Java kod wywołujący interfejs API jest w metodzie getUsersFromGraph w [AuthPageController. Java # L62](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L62).

Metoda próbuje wywołać `getAuthResultBySilentFlow`. Jeśli użytkownik musi wyrazić zgodę na więcej zakresów, kod przetwarza obiekt `MsalInteractionRequiredException`, aby zakwestionować użytkownika.

```java
@RequestMapping("/msal4jsample/graph/me")
public ModelAndView getUserFromGraph(HttpServletRequest httpRequest, HttpServletResponse response)
        throws Throwable {

    IAuthenticationResult result;
    ModelAndView mav;
    try {
        result = authHelper.getAuthResultBySilentFlow(httpRequest, response);
    } catch (ExecutionException e) {
        if (e.getCause() instanceof MsalInteractionRequiredException) {

            // If the silent call returns MsalInteractionRequired, redirect to authorization endpoint
            // so user can consent to new scopes.
            String state = UUID.randomUUID().toString();
            String nonce = UUID.randomUUID().toString();

            SessionManagementHelper.storeStateAndNonceInSession(httpRequest.getSession(), state, nonce);

            String authorizationCodeUrl = authHelper.getAuthorizationCodeUrl(
                    httpRequest.getParameter("claims"),
                    "User.Read",
                    authHelper.getRedirectUriGraph(),
                    state,
                    nonce);

            return new ModelAndView("redirect:" + authorizationCodeUrl);
        } else {

            mav = new ModelAndView("error");
            mav.addObject("error", e);
            return mav;
        }
    }

    if (result == null) {
        mav = new ModelAndView("error");
        mav.addObject("error", new Exception("AuthenticationResult not found in session."));
    } else {
        mav = new ModelAndView("auth_page");
        setAccountInfo(mav, httpRequest);

        try {
            mav.addObject("userInfo", getUserInfoFromGraph(result.accessToken()));

            return mav;
        } catch (Exception e) {
            mav = new ModelAndView("error");
            mav.addObject("error", e);
        }
    }
    return mav;
}
// Code omitted here
```

# <a name="pythontabpython"></a>[Python](#tab/python)

W przykładzie języka Python kod, który wywołuje Microsoft Graph, znajduje się w [App. PR # L53-L62](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L53-L62).

Kod próbuje uzyskać token z pamięci podręcznej tokenów. Następnie, po ustawieniu nagłówka autoryzacji, wywoła internetowy interfejs API. Jeśli nie można uzyskać tokenu, podpisuje użytkownika ponownie.

```python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service.
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wywoływanie interfejsu API sieci Web](scenario-web-app-call-api-call-api.md)
