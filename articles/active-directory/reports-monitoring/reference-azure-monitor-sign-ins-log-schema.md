---
title: Inloggnings logg schema i Azure Monitor | Microsoft Docs
description: Beskriv Azure AD-inloggningens logg schema för användning i Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d7c9713f27643e792ea381e1a2419cbc4b67a99
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82129208"
---
# <a name="interpret-the-azure-ad-sign-in-logs-schema-in-azure-monitor"></a>Tolka schemat för inloggnings loggar för Azure AD i Azure Monitor

I den här artikeln beskrivs inloggnings logg schema för Azure Active Directory (Azure AD) i Azure Monitor. De flesta av de uppgifter som är relaterade till inloggningar tillhandahålls under `records` objektets *egenskap* attribut.


```json
{ 
    "time": "2019-03-12T16:02:15.5522137Z", 
    "resourceId": "/tenants/<TENANT ID>/providers/Microsoft.aadiam",
    "operationName": "Sign-in activity", 
    "operationVersion": "1.0", 
    "category": "SignInLogs", 
    "tenantId": "<TENANT ID>", 
    "resultType": "50140", 
    "resultSignature": "None", 
    "resultDescription": "This error occurred due to 'Keep me signed in' interrupt when the user was signing-in.", 
    "durationMs": 0, 
    "callerIpAddress": "<CALLER IP ADDRESS>", 
    "correlationId": "a75a10bd-c126-486b-9742-c03110d36262", 
    "identity": "Timothy Perkins", 
    "Level": 4, 
    "location": "US", 
    "properties": 
        {
            "id":"0231f922-93fa-4005-bb11-b344eca03c01",
            "createdDateTime":"2019-03-12T16:02:15.5522137+00:00",
            "userDisplayName":"Timothy Perkins",
            "userPrincipalName":"<USER PRINCIPAL NAME>",
            "userId":"<USER ID>",
            "appId":"<APPLICATION ID>",
            "appDisplayName":"Azure Portal",
            "ipAddress":"<IP ADDRESS>",
            "status":
            {
                "errorCode":50140,
                "failureReason":"This error occurred due to 'Keep me signed in' interrupt when the user was signing-in."
            },
            "clientAppUsed":"Browser",
            "deviceDetail":
            {
                "operatingSystem":"Windows 10",
                "browser":"Chrome 72.0.3626"
            },
            "location":
                {
                    "city":"Bellevue",
                    "state":"Washington",
                    "countryOrRegion":"US",
                    "geoCoordinates":
                    {
                        "latitude":45,
                        "longitude":122
                    }
                },
            "correlationId":"a75a10bd-c126-486b-9742-c03110d36262",
            "conditionalAccessStatus":"notApplied",
            "appliedConditionalAccessPolicies":
            [
                {
                    "id":"ae11ffaa-9879-44e0-972c-7538fd5c4d1a",
                    "displayName":"Hr app access policy",
                    "enforcedGrantControls":
                    [
                        "Mfa"
                    ],
                    "enforcedSessionControls":
                    [
                    ],
                    "result":"notApplied"
                },
                {
                    "id":"b915a70b-2eee-47b6-85b6-ff4f4a66256d",
                    "displayName":"MFA for all but global support access",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
                {
                    "id":"830f27fa-67a8-461f-8791-635b7225caf1",
                    "displayName":"Header Based Application Control",
                    "enforcedGrantControls":["Mfa"],
                    "enforcedSessionControls":[],
                    "result":"notApplied"
                },
                {
                    "id":"8ed8d7f7-0a2e-437b-b512-9e47bed562e6",
                    "displayName":"MFA for everyones",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
                {
                    "id":"52924e0f-798b-4afd-8c42-49055c7d6395",
                    "displayName":"Device compliant",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
             ],
            "isInteractive":true,
            "tokenIssuerType":"AzureAD",
            "authenticationProcessingDetails":[],
            "networkLocationDetails":[],
            "processingTimeInMilliseconds":0,
            "riskDetail":"hidden",
            "riskLevelAggregated":"hidden",
            "riskLevelDuringSignIn":"hidden",
            "riskState":"none",
            "riskEventTypes":[],
            "resourceDisplayName":"windows azure service management api",
            "resourceId":"797f4846-ba00-4fd7-ba43-dac1f8f63013",
            "authenticationMethodsUsed":[]
        }
}
```


## <a name="field-descriptions"></a>Fältbeskrivningar

| Fältnamn | Beskrivning |
|------------|-------------|
| Tid | Datumet och tiden, i UTC. |
| ResourceId | Värdet är omappat och du kan bortse från det här fältet.  |
| OperationName | För inloggningar är det här värdet alltid *inloggnings aktivitet*. |
| OperationVersion | Den REST API version som begärs av klienten. |
| Kategori | För inloggningar är det här värdet alltid *inloggnings*värde. | 
| TenantId | Klient-GUID som är associerat med loggarna. |
| ResultType | Resultatet av inloggnings åtgärden kan lyckas *eller Miss* *lyckas* . | 
| ResultSignature | Innehåller eventuell felkod för inloggnings åtgärden. |
| ResultDescription | Innehåller fel beskrivningen för inloggnings åtgärden. |
| riskDetail | riskDetail | Tillhandahåller orsaken bakom ett särskilt tillstånd för en riskfylld användare, inloggning eller risk identifiering. De möjliga värdena är: `none`, `adminGeneratedTemporaryPassword` `userPerformedSecuredPasswordChange` `userPerformedSecuredPasswordReset` `adminConfirmedSigninSafe` `aiConfirmedSigninSafe` `userPassedMFADrivenByRiskBasedPolicy` `adminConfirmedSigninCompromised`,,,,,,,, `unknownFutureValue` `adminDismissedAllRiskForUser` Värdet `none` innebär att ingen åtgärd har utförts för användaren eller inloggning hittills. <br>**Obs:** Information om den här egenskapen kräver en Azure AD Premium P2-licens. Andra licenser returnerar värdet `hidden`. |
| riskEventTypes | riskEventTypes | Typer av risk identifiering som är associerade med inloggningen. `unlikelyTravel`Möjliga värden är:, `anonymizedIPAddress` `maliciousIPAddress` `unfamiliarFeatures` `malwareInfectedIPAddress` `suspiciousIPAddress` `investigationsThreatIntelligence` `generic` `unknownFutureValue`,,,,,,, och. `leakedCredentials` |
| riskLevelAggregated | riskLevel | Aggregerad risk nivå. Möjliga värden är: `none`, `low` `medium` `high` `hidden`,,, och. `unknownFutureValue` Värdet `hidden` innebär att användaren eller inloggningen inte har Aktiver ats för Azure AD Identity Protection. **Obs:** Information om den här egenskapen är endast tillgänglig för Azure AD Premium P2-kunder. Alla andra kunder kommer att returneras `hidden`. |
| riskLevelDuringSignIn | riskLevel | Risk nivå under inloggningen. Möjliga värden är: `none`, `low` `medium` `high` `hidden`,,, och. `unknownFutureValue` Värdet `hidden` innebär att användaren eller inloggningen inte har Aktiver ats för Azure AD Identity Protection. **Obs:** Information om den här egenskapen är endast tillgänglig för Azure AD Premium P2-kunder. Alla andra kunder kommer att returneras `hidden`. |
| riskState | riskState | Rapporterar status för riskfylld användare, inloggning eller risk identifiering. Möjliga värden är: `none`, `confirmedSafe` `remediated` `dismissed` `atRisk` `confirmedCompromised`,,,,,. `unknownFutureValue` |
| . Durationms |  Värdet är omappat och du kan bortse från det här fältet. |
| CallerIpAddress | IP-adressen för klienten som gjorde begäran. | 
| CorrelationId | Det valfria GUID som skickas av klienten. Det här värdet kan hjälpa till att korrelera åtgärder på klient sidan med åtgärder på Server sidan, och det är användbart när du spårar loggar som omfattar tjänster. |
| Identitet | Identiteten från den token som angavs när du gjorde begäran. Det kan vara ett användar konto, ett system konto eller ett huvud namn för tjänsten. |
| Nivå | Tillhandahåller meddelande typen. För granskning är det alltid *information*. |
| Plats | Anger platsen för inloggnings aktiviteten. |
| Egenskaper | Visar en lista över alla egenskaper som är associerade med inloggningar. Mer information finns i [Microsoft Graph API-referens](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin). Schemat använder samma attributnamn som inloggnings resursen för läsbarhet.

## <a name="next-steps"></a>Nästa steg

* [Tolka schemat för gransknings loggar i Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Läs mer om Azures plattforms loggar](../../azure-monitor/platform/platform-logs-overview.md)
