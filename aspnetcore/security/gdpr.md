---
title: Поддержка Общий регламент по защите данных (GDPR) в ASP.NET Core
author: rick-anderson
description: Узнайте, как получить доступ к точкам расширения GDPR в веб-приложении ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
uid: security/gdpr
ms.openlocfilehash: 1086c22c2f3c27373d8cb779f4b1d8eb6792ec2e
ms.sourcegitcommit: 2fa0ffe82a47c7317efc9ea908365881cbcb8ed7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/17/2019
ms.locfileid: "69572876"
---
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a>Поддержка ЕС Общий регламент по защите данных (GDPR) в ASP.NET Core

Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

ASP.NET Core предоставляет интерфейсы API и шаблоны, помогающие удовлетворить некоторые из требований [ес общий регламент по защите данных (GDPR)](https://www.eugdpr.org/) :

::: moniker range=">= aspnetcore-3.0"

* Шаблоны проектов включают точки расширения и разметку создана, которые можно заменить на конфиденциальность и политику использования файлов cookie.
* Страница Pages */privacy. cshtml* или views */Home/privacy. cshtml* предоставляет страницу для детализации политики конфиденциальности сайта.

Чтобы включить функцию разрешения файлов cookie по умолчанию, как в шаблонах ASP.NET Core 2,2 в создаваемом шаблоне ASP.NET Core 3,0, выполните следующие действия.

* Добавьте `using Microsoft.AspNetCore.Http` в список директив using.
* Добавьте [кукиеполициоптионс](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) в `Startup.ConfigureServices` и [усекукиеполици](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) в `Startup.Configure`:

  [!code-csharp[Main](gdpr/sample/RP3.0/Startup.cs?name=snippet1&highlight=12-19,38)]

* Добавьте в файл *_layout. cshtml* полное согласие на файлы cookie:

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_Layout.cshtml?name=snippet&highlight=4)]

* Добавьте в проект файл  *кукиеконсентпартиал.cshtml:\_*

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_CookieConsentPartial.cshtml)]

* Выберите версию ASP.NET Core 2,2 в этой статье, чтобы узнать о функции согласия файлов cookie.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* Шаблоны проектов включают точки расширения и разметку создана, которые можно заменить на конфиденциальность и политику использования файлов cookie.
* Функция согласия файлов cookie позволяет запрашивать (и контролировать) согласие от пользователей на хранение персональных данных. Если пользователь не предоставил разрешения на сбор данных, а приложение имеет [чеккконсентнидед](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) `true`, некритические файлы cookie не отправляются в браузер.
* Файлы cookie могут быть помечены как основные. Основные файлы cookie отправляются в браузер, даже если пользователь не отправил и не отслеживается.
* [TempData и сеансовые файлы cookie](#tempdata) не работают, если отслеживание отключено.
* Страница [Управление удостоверениями](#pd) содержит ссылку для скачивания и удаления данных пользователя.

[Пример приложения](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) позволяет тестировать большинство точек расширения GDPR и API, добавленных в шаблоны ASP.NET Core 2,1. Инструкции по тестированию см. в файле [сведений](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) .

[Просмотреть или скачать образец кода](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a>ASP.NET Core поддержка GDPR в коде, созданном шаблоном

Razor Pages и проекты MVC, созданные с помощью шаблонов проектов, включают следующую поддержку GDPR:

* [Кукиеполициоптионс](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) и [усекукиеполици](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) `Startup` задаются в классе.
* [Частичное представление](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)  *кукиеконсентпартиал.cshtml.\_* В этот файл входит кнопка **принять** . Когда пользователь нажимает кнопку **Accept (принять** ), предоставляется согласие на хранение файлов cookie.
* Страница Pages */privacy. cshtml* или views */Home/privacy. cshtml* предоставляет страницу для детализации политики конфиденциальности сайта. Файл *кукиеконсентпартиал. cshtml создает ссылку на страницу «конфиденциальность». \_*
* Для приложений, созданных с учетными записями отдельных пользователей, страница Управление предоставляет ссылки для скачивания и удаления [персональных данных пользователя](#pd).

### <a name="cookiepolicyoptions-and-usecookiepolicy"></a>Кукиеполициоптионс и Усекукиеполици

[Кукиеполициоптионс](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) инициализируются в `Startup.ConfigureServices`:

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

[Усекукиеполици](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) вызывается в `Startup.Configure`:

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="_cookieconsentpartialcshtml-partial-view"></a>\_Частичное представление Кукиеконсентпартиал. cshtml

Частичное представление  *кукиеконсентпартиал.cshtml:\_*

[!code-html[](gdpr/sample/RP2.2/Pages/Shared/_CookieConsentPartial.cshtml)]

Это частичное:

* Получает состояние отслеживания для пользователя. Если приложение настроено для обязательного согласия, пользователь должен согласиться перед тем, как можно будет относиться к файлам cookie. Если требуется согласие, панель согласия файлов cookie зафиксирована в верхней части панели навигации, созданной с помощью  *\_файла Layout. cshtml* .
* Предоставляет элемент HTML `<p>` для суммирования политики конфиденциальности и использования файлов cookie.
* Содержит ссылку на страницу конфиденциальности или представление, где можно подробно описать политику конфиденциальности сайта.

## <a name="essential-cookies"></a>Основные файлы cookie

Если не предоставлено согласие на хранение файлов cookie, в браузер отправляются только файлы cookie, отмеченные как ключевые. Следующий код создает файл cookie, необходимый для:

[!code-csharp[Main](gdpr/sample/RP2.2/Pages/Cookie.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-cookies-arent-essential"></a>Файлы cookie состояния поставщика TempData и сеанса не являются важными

Файл cookie [поставщика TempData](xref:fundamentals/app-state#tempdata) не является обязательным. Если отслеживание отключено, то поставщик TempData не работает. Чтобы включить поставщик TempData при отключении отслеживания, пометьте файл cookie TempData как важный в `Startup.ConfigureServices`:

[!code-csharp[Main](gdpr/sample/RP2.2/Startup.cs?name=snippet1)]

Файлы cookie [состояния сеанса](xref:fundamentals/app-state) не являются обязательными. Состояние сеанса не работает, если отслеживание отключено. Следующий код создает файлы cookie для сеанса:

[!code-csharp[](gdpr/sample/RP2.2/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a>Персональные данные

ASP.NET Core приложения, созданные с учетными записями отдельных пользователей, включают код для загрузки и удаления персональных данных.

Выберите имя пользователя, а затем выберите **личные данные**:

![Страница «Управление личными данными»](gdpr/_static/pd.png)

Примечания.

* Сведения о создании `Account/Manage` кода см. в разделе [удостоверение шаблона](xref:security/authentication/scaffold-identity).
* Ссылки **Delete** и **download** действуют только для данных удостоверений по умолчанию. Приложения, которые создают настраиваемые пользовательские данные, должны быть расширены для удаления и скачивания пользовательских данных. Дополнительные сведения см. [в разделе Добавление, скачивание и удаление настраиваемых данных пользователя в удостоверение](xref:security/authentication/add-user-data).
* Сохраненные токены для пользователя, хранящиеся в таблице `AspNetUserTokens` базы данных Identity, удаляются, когда пользователь удаляется через каскадное поведение удаления из-за [внешнего ключа](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).
* [Проверка подлинности внешнего поставщика](xref:security/authentication/social/index), например Facebook и Google, недоступна до принятия политики "файлы cookie".

::: moniker-end

## <a name="encryption-at-rest"></a>Шифрование неактивных

Некоторые базы данных и механизмы хранилища позволяют шифровать неактивных. Шифрование при хранении:

* Автоматически шифрует сохраненные данные.
* Шифруется без настройки, программирования или другой работы для программного обеспечения, обращающегося к данным.
* — Самый простой и надежный вариант.
* Позволяет базе данных управлять ключами и шифрованием.

Например:

* Microsoft SQL и Azure SQL предоставляют [прозрачное шифрование данных](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).
* [По умолчанию SQL Azure шифрует базу данных](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* [Большие двоичные объекты Azure, файлы, таблицы и хранилища очередей шифруются](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/)по умолчанию.

Для баз данных, которые не предоставляют встроенное шифрование, вы можете использовать шифрование дисков для обеспечения такой же защиты. Например:

* [BitLocker для Windows Server](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* Linux:
  * [екриптфс](https://launchpad.net/ecryptfs)
  * [Енкфс](https://github.com/vgough/encfs).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Microsoft.com/GDPR](https://www.microsoft.com/trustcenter/Privacy/GDPR)
* [GDPR — Добавление кнопки "отозвать согласие" в ASP.NET Core](https://www.joeaudette.com/blog/2018/08/28/gdpr---adding-a-revoke-consent-button-in-aspnet-core)
