---
title: RelyingParty — Azure Active Directory B2C | Microsoft Docs
description: Określ element RelyingParty zasad niestandardowych w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/24/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 90ac6f35cafbe63e8c6cdb77450089d00c0e3099
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79264351"
---
# <a name="relyingparty"></a>RelyingParty

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Element **RelyingParty** określa podróż użytkownika, aby wymusić bieżące żądanie do Azure Active Directory B2C (Azure AD B2C). Określa także listę oświadczeń, które są wymagane przez aplikację jednostki uzależnionej (RP) jako część wystawionego tokenu. Aplikacja RP, taka jak aplikacja sieci Web, aplikacji mobilnej lub klasycznej, wywołuje plik zasad RP. Plik zasad RP wykonuje określone zadanie, takie jak logowanie, Resetowanie hasła lub edytowanie profilu. Wiele aplikacji może korzystać z tych samych zasad RP, a pojedyncza aplikacja może korzystać z wielu zasad. Wszystkie aplikacje RP otrzymują ten sam token z oświadczeniami, a użytkownik przechodzi przez tę samą podróż użytkownika.

Poniższy przykład przedstawia element **RelyingParty** w pliku zasad *B2C_1A_signup_signin* :

```XML
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="your-tenant.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://your-tenant.onmicrosoft.com/B2C_1A_signup_signin">

  <BasePolicy>
    <TenantId>your-tenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
      <SingleSignOn Scope="TrustFramework" KeepAliveInDays="7"/>
      <SessionExpiryType>Rolling</SessionExpiryType>
      <SessionExpiryInSeconds>300</SessionExpiryInSeconds>
      <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="your-application-insights-key" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
      </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Description>The policy profile</Description>
      <Protocol Name="OpenIdConnect" />
      <Metadata>collection of key/value pairs of data</Metadata>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
  ...
```

Opcjonalny element **RelyingParty** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| DefaultUserJourney | 1:1 | Domyślna podróż użytkownika dla aplikacji RP. |
| UserJourneyBehaviors | 0:1 | Zakres zachowań podróży użytkownika. |
| TechnicalProfile | 1:1 | Profil techniczny obsługiwany przez aplikację RP. Profil techniczny zawiera kontrakt dla aplikacji RP do kontaktu Azure AD B2C. |

## <a name="defaultuserjourney"></a>DefaultUserJourney

Element `DefaultUserJourney` określa odwołanie do identyfikatora podróży użytkownika, która jest zwykle zdefiniowana w zasadach podstawowych lub zasad rozszerzenia. W poniższych przykładach przedstawiono drogę rejestracji lub logowania użytkownika określoną w elemencie **RelyingParty** :

Zasady *B2C_1A_signup_signin* :

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

*B2C_1A_TrustFrameWorkBase* lub *B2C_1A_TrustFrameworkExtensionPolicy*:

```XML
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
  ...
```

Element **DefaultUserJourney** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| ReferenceId | Tak | Identyfikator podróży użytkownika w zasadach. Aby uzyskać więcej informacji, zobacz [podróże użytkownika](userjourneys.md) |

## <a name="userjourneybehaviors"></a>UserJourneyBehaviors

Element **UserJourneyBehaviors** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| SingleSignOn | 0:1 | Zakres działania dotyczącego logowania jednokrotnego (SSO) w trakcie podróży użytkownika. |
| SessionExpiryType |0:1 | Zachowanie podczas uwierzytelniania sesji. Możliwe wartości: `Rolling` lub `Absolute`. Wartość `Rolling` (domyślnie) wskazuje, że użytkownik pozostanie zalogowany, o ile użytkownik jest stale aktywny w aplikacji. Wartość `Absolute` wskazuje, że użytkownik jest zmuszony do ponownego uwierzytelnienia po upływie czasu określonego przez okres istnienia sesji aplikacji. |
| SessionExpiryInSeconds | 0:1 | Okres istnienia pliku cookie sesji B2C's usługi Azure AD został określony jako liczba całkowita przechowywana w przeglądarce użytkownika po pomyślnym uwierzytelnieniu. |
| JourneyInsights | 0:1 | Klucz Instrumentacji usługi Azure Application Insights, który ma być używany. |
| ContentDefinitionParameters | 0:1 | Lista par wartości klucza do dołączenia do identyfikatora URI ładowania definicji zawartości. |
|ScriptExecution| 0:1| Obsługiwane tryby wykonywania [języka JavaScript](javascript-samples.md) . Możliwe wartości: `Allow` lub `Disallow` (wartość domyślna).

### <a name="singlesignon"></a>SingleSignOn

Element **SingleSignon** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Zakres | Tak | Zakres zachowania logowania jednokrotnego. Możliwe wartości: `Suppressed`, `Tenant`, `Application`lub `Policy`. Wartość `Suppressed` wskazuje, że zachowanie jest pomijane. Na przykład w przypadku sesji logowania jednokrotnego dla użytkownika nie jest utrzymywana żadna sesja, a użytkownik jest zawsze monitowany o wybranie dostawcy tożsamości. Wartość `TrustFramework` wskazuje, że zachowanie jest stosowane dla wszystkich zasad w strukturze zaufania. Na przykład użytkownik przechodzenia przez dwie przejazdy zasad dla struktury zaufania nie jest monitowany o wybranie dostawcy tożsamości. Wartość `Tenant` wskazuje, że zachowanie jest stosowane do wszystkich zasad w dzierżawie. Na przykład użytkownik przechodzenia przez dwie przejazdy zasad dla dzierżawy nie jest monitowany o wybranie dostawcy tożsamości. Wartość `Application` wskazuje, że zachowanie jest stosowane do wszystkich zasad dla aplikacji wysyłającej żądanie. Na przykład użytkownik przechodzenia przez dwie przejazdy zasad dla aplikacji nie jest monitowany o wybranie dostawcy tożsamości. Wartość `Policy` wskazuje, że zachowanie dotyczy tylko zasad. Na przykład użytkownik przechodzący przez dwie przedziały zasad dla struktury zaufania jest monitowany o wybranie dostawcy tożsamości podczas przełączania między zasadami. |
| KeepAliveInDays | Tak | Określa, jak długo użytkownik jest zalogowany. Ustawienie wartości 0 powoduje wyłączenie funkcji KMSI. Aby uzyskać więcej informacji, zobacz temat nie [wylogowuj mnie](custom-policy-keep-me-signed-in.md). |
|EnforceIdTokenHintOnLogout| Nie|  Wymuś przekazania wcześniej wystawionego tokenu ID do punktu końcowego wylogowania jako wskazówkę dotyczącą bieżącej uwierzytelnionej sesji użytkownika końcowego z klientem. Możliwe wartości: `false` (wartość domyślna) lub `true`. Aby uzyskać więcej informacji, zobacz [Logowanie w sieci Web za pomocą usługi OpenID Connect Connect](openid-connect.md).  |


## <a name="journeyinsights"></a>JourneyInsights

Element **JourneyInsights** zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| TelemetryEngine | Tak | Wartość musi być `ApplicationInsights`. |
| InstrumentationKey | Tak | Ciąg, który zawiera klucz Instrumentacji dla elementu Application Insights. |
| Deweloper | Tak | Możliwe wartości: `true` lub `false`. Jeśli `true`, Application Insights przyspiesza dane telemetryczne za pomocą potoku przetwarzania. To ustawienie jest dobre w przypadku programowania, ale jest ograniczone do dużych woluminów, szczegółowe dzienniki działań są przeznaczone tylko do pomocy w opracowywaniu zasad niestandardowych. Nie używaj trybu deweloperskiego w środowisku produkcyjnym. Dzienniki zbierają wszystkie oświadczenia wysyłane do i od dostawców tożsamości podczas opracowywania. Jeśli jest używany w środowisku produkcyjnym, programista przyjmuje odpowiedzialność za dane OSOBowe (informacje z możliwością zidentyfikowaną prywatnie) zebrane w dzienniku usługi App Insights. Te szczegółowe dzienniki są zbierane tylko wtedy, gdy ta wartość jest ustawiona na `true`.|
| ClientEnabled | Tak | Możliwe wartości: `true` lub `false`. Jeśli `true`, program wyśle Application Insights skrypt po stronie klienta do śledzenia widoku strony i błędów po stronie klienta. |
| ServerEnabled | Tak | Możliwe wartości: `true` lub `false`. Jeśli `true`, program wysyła istniejący kod JSON UserJourneyRecorder jako zdarzenie niestandardowe do Application Insights. |
| TelemetryVersion | Tak | Wartość musi być `1.0.0`. |

Aby uzyskać więcej informacji, zobacz [zbieranie dzienników](troubleshoot-with-application-insights.md)

## <a name="contentdefinitionparameters"></a>ContentDefinitionParameters

Korzystając z zasad niestandardowych w Azure AD B2C, można wysłać parametr w ciągu zapytania. Przekazanie parametru do punktu końcowego HTML pozwala na dynamiczną zmianę zawartość strony. Na podstawie parametru przekazywanego z aplikacji internetowej lub aplikacji mobilnej można na przykład zmienić obraz tła na stronie rejestracji lub logowania usługi Azure AD B2C. Azure AD B2C przekazuje parametry ciągu zapytania do dynamicznego pliku HTML, takiego jak plik aspx.

Poniższy przykład przekazuje parametr o nazwie `campaignId` z wartością `hawaii` w ciągu zapytania:

`https://login.microsoft.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?pB2C_1A_signup_signin&client_id=a415078a-0402-4ce3-a9c6-ec1947fcfb3f&nonce=defaultNonce&redirect_uri=http%3A%2F%2Fjwt.io%2F&scope=openid&response_type=id_token&prompt=login&campaignId=hawaii`

Element **ContentDefinitionParameters** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| ContentDefinitionParameter | 0: n | Ciąg zawierający parę wartości klucza, która jest dołączana do ciągu zapytania w identyfikatorze URI ładowania definicji zawartości. |

Element **ContentDefinitionParameter** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Name (Nazwa) | Tak | Nazwa pary klucz wartość. |

Aby uzyskać więcej informacji, zobacz [Konfigurowanie interfejsu użytkownika z zawartością dynamiczną przy użyciu zasad niestandardowych](custom-policy-ui-customization.md#configure-dynamic-custom-page-content-uri)

## <a name="technicalprofile"></a>TechnicalProfile

Element **profilu technicznym** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Identyfikator | Tak | Wartość musi być `PolicyProfile`. |

**Profilu technicznym** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| DisplayName | 1:1 | Ciąg, który zawiera nazwę profilu technicznego. |
| Opis | 0:1 | Ciąg, który zawiera opis profilu technicznego. |
| Protokół | 1:1 | Protokół używany w Federacji. |
| Metadane | 0:1 | Kolekcja *elementów* par klucz/wartość wykorzystywana przez protokół do komunikowania się z punktem końcowym w trakcie transakcji w celu skonfigurowania interakcji między jednostką uzależnioną i innymi uczestnikami społeczności. |
| OutputClaims | 1:1 | Lista typów roszczeń, które są pobierane jako dane wyjściowe w profilu technicznym. Każdy z tych elementów zawiera odwołanie do elementu **ClaimType** zdefiniowanego już w sekcji **ClaimsSchema** lub w zasadach, z których dziedziczy ten plik zasad. |
| SubjectNamingInfo | 1:1 | Nazwa podmiotu używana w tokenach. |

Element **Protocol** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Name (Nazwa) | Tak | Nazwa prawidłowego protokołu obsługiwanego przez Azure AD B2C, który jest używany jako część profilu technicznego. Możliwe wartości: `OpenIdConnect` lub `SAML2`. Wartość `OpenIdConnect` reprezentuje standard protokołu OpenID Connect Connect 1,0 zgodnie ze specyfikacją OpenID Connect Foundation. `SAML2` reprezentuje standard protokołu SAML 2,0 zgodnie ze specyfikacją języka Oasis. Nie używaj tokenu SAML w środowisku produkcyjnym. |

## <a name="outputclaims"></a>OutputClaims

Element **OutputClaims** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Oświadczenie outputclaim | 0: n | Nazwa oczekiwanego typu na liście obsługiwanych zasad, dla których zasubskrybuje się Jednostka uzależniona. To zgłoszenie służy jako dane wyjściowe profilu technicznego. |

Element **oświadczenie outputclaim** zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Tak | Odwołanie do elementu **ClaimType** zdefiniowanego już w sekcji **ClaimsSchema** w pliku zasad. |
| DefaultValue | Nie | Wartość domyślna, która może być używana, jeśli wartość jest pusta. |
| PartnerClaimType | Nie | Wysyła oświadczenia w innej nazwie, zgodnie z konfiguracją w definicji elementu ClaimType. |

### <a name="subjectnaminginfo"></a>SubjectNamingInfo

Element **SubjectNameingInfo** umożliwia kontrolowanie wartości podmiotu tokenu:
- **Token JWT** — `sub`. Jest to podmiot zabezpieczeń, w którym token potwierdza informacje, takie jak użytkownik aplikacji. Ta wartość jest niezmienna i nie można jej ponownie przypisać ani ponownie użyć. Może służyć do wykonywania bezpiecznych kontroli autoryzacji, na przykład gdy token jest używany w celu uzyskania dostępu do zasobu. Domyślnie, zgłoszenie podmiotu jest wypełniane IDENTYFIKATORem obiektu użytkownika w katalogu. Aby uzyskać więcej informacji, zobacz [Konfiguracja tokenu, sesji i logowania](session-behavior.md)jednokrotnego.
- **Token SAML** — element `<Subject><NameID>`, który identyfikuje element podmiotu.

Element **SubjectNamingInfo** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Claim | Tak | Odwołanie do **PartnerClaimType**zgłoszenia danych wyjściowych. Oświadczenia wyjściowe muszą być zdefiniowane w kolekcji **OutputClaims** zasad jednostki uzależnionej. |

Poniższy przykład pokazuje, jak zdefiniować jednostkę uzależnioną OpenID Connect Connect. Informacje o nazwie podmiotu są konfigurowane jako `objectId`:

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```
Token JWT zawiera `sub`ego z identyfikatorem użytkownika:

```JSON
{
  ...
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  ...
}
```
