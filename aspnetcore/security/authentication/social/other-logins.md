---
title: Provider di autenticazione OAuth esterni
author: rick-anderson
description: Individuare i provider di autenticazione OAuth esterni che funzionano con ASP.NET Core app.
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/otherlogins
ms.openlocfilehash: c61bbef26017f14df09f8ca6f494d29f79903b6b
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776461"
---
# <a name="external-oauth-authentication-providers"></a>Provider di autenticazione OAuth esterni

Di [Rick Anderson](https://twitter.com/RickAndMSFT), [Rastogi](https://github.com/rustd)e [Valeriy Novytskyy](https://github.com/01binary)

L'elenco seguente include i provider di autenticazione OAuth esterni comuni che funzionano con ASP.NET Core app. I pacchetti NuGet di terze parti, ad esempio quelli gestiti da [ASPNET-contrib](https://www.nuget.org/packages?q=owners%3Aaspnet-contrib+title%3AOAuth), possono essere usati per integrare i provider di autenticazione implementati dal team di ASP.NET Core.

* [LinkedIn](https://www.linkedin.com/developer/apps) ([istruzioni](https://developer.linkedin.com/docs/oauth2))

* [Instagram](https://www.instagram.com/developer/register/) ([istruzioni](https://www.instagram.com/developer/authentication/))

* [Reddit](https://www.reddit.com/login?dest=https%3A%2F%2Fwww.reddit.com%2Fprefs%2Fapps) ([istruzioni](https://github.com/reddit/reddit/wiki/OAuth2-Quick-Start-Example))

* [GitHub](https://github.com/login?return_to=https%3A%2F%2Fgithub.com%2Fsettings%2Fapplications%2Fnew) ([istruzioni](https://developer.github.com/v3/oauth/))

* [Yahoo](https://login.yahoo.com/config/login?src=devnet&.done=http%3A%2F%2Fdeveloper.yahoo.com%2Fapps%2Fcreate%2F) ([istruzioni](https://developer.yahoo.com/bbauth/user.html))

* [Tumblr](https://www.tumblr.com/oauth/apps) ([istruzioni](https://www.tumblr.com/docs/api/v2#auth))

* [Pinterest](https://www.pinterest.com/login/?next=http%3A%2F%2Fdevsite%2Fapps%2F) ([istruzioni](https://developers.pinterest.com/docs/api/overview/?))

* [Pocket](https://getpocket.com/developer/apps/new) ([istruzioni](https://getpocket.com/developer/docs/authentication))

* [Flickr](https://www.flickr.com/services/apps/create) ([istruzioni](https://www.flickr.com/services/api/auth.oauth.html))

* [Dribbling](https://dribbble.com/signup) ([istruzioni](https://developer.dribbble.com/v1/oauth/))

* [Vimeo](https://vimeo.com/join) ([istruzioni](https://developer.vimeo.com/api/authentication))

* [SoundCloud](https://soundcloud.com/you/apps/new) ([istruzioni](https://developers.soundcloud.com/blog/we-love-oauth-2))

* [VK](https://vk.com/apps?act=manage) ([istruzioni](https://vk.com/pages?oid=-17680044&p=Authorizing_Sites))

[!INCLUDE[Multiple authentication providers](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]
