---
title: Введение в удостоверение на ASP.NET Core
author: rick-anderson
description: Используйте удостоверение с приложением ASP.NET Core. Узнайте, как устанавливать требования к паролю (Рекуиредигит, Рекуиредленгс, Рекуиредуникуечарс и др.).
ms.author: riande
ms.date: 01/15/2020
uid: security/authentication/identity
ms.openlocfilehash: 98fee261a741a20eed181ca5b9a4ebb693deeb63
ms.sourcegitcommit: cbd30479f42cbb3385000ef834d9c7d021fd218d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76146515"
---
# <a name="introduction-to-identity-on-aspnet-core"></a>Введение в удостоверение на ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

Удостоверение ASP.NET Core:

* — Это API, поддерживающий функцию входа в пользовательский интерфейс.
* Управляет пользователями, паролями, данными профилирования, ролями, утверждениями, маркерами, подтверждением электронной почты и т. д.

Пользователи могут создать учетную запись с данными для входа, хранящимися в удостоверении, или использовать внешнего поставщика входа. Поддерживаемые внешние поставщики входа включают [Facebook, Google, учетную запись Майкрософт и Twitter](xref:security/authentication/social/index).

[Исходный код удостоверения](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) доступен на сайте GitHub. [Удостоверение формирования шаблонов](xref:security/authentication/scaffold-identity) и просмотр созданных файлов для проверки взаимодействия шаблона с удостоверением.

Удостоверение обычно настраивается с помощью SQL Server базы данных для хранения имен пользователей, паролей и данных профилей. Кроме того, можно использовать еще одно постоянное хранилище, например хранилище таблиц Azure.

В этом разделе вы узнаете, как использовать удостоверение для регистрации, входа и выхода пользователя. Более подробные инструкции по созданию приложений, использующих удостоверение, см. в разделе дальнейшие действия в конце этой статьи.

[Платформа Microsoft Identity Platform](/azure/active-directory/develop/) :

* Развитие платформы разработки Azure Active Directory (Azure AD).
* Не связано с удостоверением ASP.NET Core.

[!INCLUDE[](~/includes/IdentityServer4.md)]

[Просмотр или скачивание образца кода](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([Загрузка)](xref:index#how-to-download-a-sample).

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a>Создание веб-приложения с проверкой подлинности

Создание ASP.NET Core проекта веб-приложения с учетными записями отдельных пользователей.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Выберите **файл** > **Новый** > **проект**.
* Выберите **Новое веб-приложение ASP.NET Core**. Присвойте проекту имя **APP1** , которое будет иметь то же пространство имен, что и загружаемый проект. Нажмите кнопку **ОК**.
* Выберите ASP.NET Core **веб-приложение**, а затем щелкните **изменить проверку подлинности**.
* Выберите **учетные записи отдельных пользователей** и нажмите кнопку **ОК**.

# <a name="net-core-clitabnetcore-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

Предыдущая команда создает веб-приложение Razor с помощью SQLite. Чтобы создать веб-приложение с помощью LocalDB, выполните следующую команду:

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

Созданный проект предоставляет [ASP.NET Core удостоверение](xref:security/authentication/identity) в виде [библиотеки классов Razor](xref:razor-pages/ui-class). Библиотека классов Razor Identity предоставляет конечные точки с областью `Identity`. Например:

* /идентити/аккаунт/логин
* /идентити/аккаунт/логаут
* /идентити/аккаунт/манаже

### <a name="apply-migrations"></a>Применение миграции

Примените миграции, чтобы инициализировать базу данных.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Выполните следующую команду в консоли диспетчера пакетов (PMC):

`PM> Update-Database`

# <a name="net-core-clitabnetcore-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli)

Миграция не требуется на этом этапе при использовании SQLite. Для LocalDB выполните следующую команду:

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a>Проверка регистра и вход в систему

Запустите приложение и зарегистрируйте пользователя. В зависимости от размера экрана может потребоваться выбрать переключатель навигации, чтобы просмотреть ссылки **Регистрация** и **Вход** .

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a>Настройка служб удостоверений

Службы добавляются в `ConfigureServices`. По стандартному шаблону сначала вызываются все методы `Add{Service}`, а затем все методы `services.Configure{Service}`.

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

Выделенный выше код настраивает удостоверение значениями параметров по умолчанию. Доступ к службам предоставляется приложению посредством [внедрения зависимостей](xref:fundamentals/dependency-injection).

Удостоверение включается путем вызова <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>. `UseAuthentication` добавляет по [промежуточного слоя](xref:fundamentals/middleware/index) проверки подлинности в конвейер запросов.

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

Созданное шаблоном приложение не использует [авторизацию](xref:security/authorization/secure-data). `app.UseAuthorization` включен, чтобы убедиться, что он добавлен в правильном порядке, если приложение добавляет авторизацию. `UseRouting`, `UseAuthentication`, `UseAuthorization`и `UseEndpoints` должны вызываться в порядке, показанном в предыдущем коде.

Дополнительные сведения о `IdentityOptions` и `Startup`см. в разделе <xref:Microsoft.AspNetCore.Identity.IdentityOptions> и [Запуск приложения](xref:fundamentals/startup).

## <a name="scaffold-register-login-and-logout"></a>Регистрация, вход и выход из шаблона формирования шаблонов

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Добавьте файлы Register, login и LogOut. Соблюдайте [идентификатор шаблона в проекте Razor с](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) инструкциями по авторизации для создания кода, приведенного в этом разделе.

# <a name="net-core-clitabnetcore-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli)

Если вы создали проект с именем " **APP1**", выполните следующие команды. В противном случае используйте правильное пространство имен для `ApplicationDbContext`.

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

В PowerShell используется точка с запятой в качестве разделителя команд. При использовании PowerShell заключите точку с запятой в список файлов или вставьте список файлов в двойные кавычки, как показано в предыдущем примере.

Дополнительные сведения об удостоверении формирования шаблонов см. в разделе [удостоверение шаблона в проекте Razor с авторизацией](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).

---

### <a name="examine-register"></a>Проверка регистра

Когда пользователь щелкает ссылку **Register** , вызывается действие `RegisterModel.OnPostAsync`. Пользователь создается с помощью [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) на объекте `_userManager`. `_userManager` предоставляется путем внедрения зависимостей):

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

Если пользователь создан, происходит вход пользователя путем вызова метода `_signInManager.SignInAsync`.

Инструкции по предотвращению немедленного входа при регистрации см. в статье [Подтверждение учетной записи](xref:security/authentication/accconfirm#prevent-login-at-registration) .

### <a name="log-in"></a>Вход

Форма входа отображается в следующих случаях:

* Выбрана ссылка для **входа** .
* Пользователь пытается получить доступ к странице с ограниченным доступом, которая не авторизована для доступа, **или** если система не прошла проверку подлинности.

При отправке формы на странице входа вызывается действие `OnPostAsync`. `PasswordSignInAsync` вызывается для объекта `_signInManager` (предоставленного путем внедрения зависимостей).

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

Базовый класс `Controller` предоставляет свойство `User`, доступ к которому можно получить из методов контроллера. Например, можно перечислить `User.Claims` и принимать решения об авторизации. Для получения дополнительной информации см. <xref:security/authorization/introduction>.

### <a name="log-out"></a>Выход

Ссылка **выхода** вызывает действие `LogoutModel.OnPost`. 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

В приведенном выше коде код `return RedirectToPage();` должен быть перенаправлением, чтобы браузер выполнит новый запрос и удостоверение для пользователя будет обновлено.

[Сигнаутасинк](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) очищает утверждения пользователя, хранящиеся в файле cookie.

Параметр POST указан в *страницах Pages/Shared/_LoginPartial. cshtml*:

[!code-csharp[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a>Проверить удостоверение

Шаблоны веб-проектов по умолчанию разрешают анонимный доступ к домашним страницам. Чтобы проверить удостоверение, добавьте [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

Если вы вошли в систему, выйдите из нее. Запустите приложение и щелкните ссылку **Конфиденциальность** . Вы перейдете на страницу входа.

### <a name="explore-identity"></a>Просмотр удостоверений

Более подробное изучение удостоверений:

* [Создание полного источника идентификатора пользовательского интерфейса](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* Изучите источник каждой страницы и пошаговое выполнение отладчика.

## <a name="identity-components"></a>Компоненты идентификации

Все пакеты NuGet, зависящие от удостоверения, включены в [ASP.NET Core общую платформу](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).

Основным пакетом для удостоверения является [Microsoft. AspNetCore. Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/). Этот пакет содержит основной набор интерфейсов для ASP.NET Core удостоверения и включается `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.

## <a name="migrating-to-aspnet-core-identity"></a>Миграция на ASP.NET Core удостоверение

Дополнительные сведения и рекомендации по переносу существующего хранилища удостоверений см. в статье [Миграция проверки подлинности и удостоверений](xref:migration/identity).

## <a name="setting-password-strength"></a>Установка силы пароля

См. раздел [Конфигурация](#pw) для примера, который устанавливает минимальные требования к паролю.

## <a name="adddefaultidentity-and-addidentity"></a>Адддефаултидентити и Аддидентити

<xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> было введено в ASP.NET Core 2,1. Вызов `AddDefaultIdentity` аналогичен вызову следующего:

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

Дополнительные сведения см. в разделе [адддефаултидентити Source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .

## <a name="prevent-publish-of-static-identity-assets"></a>Запретить публикацию ресурсов статических удостоверений

Чтобы запретить публикацию статических ресурсов удостоверений (таблиц стилей и файлов JavaScript для пользовательского интерфейса идентификации) в корневой веб-папке, добавьте в файл проекта приложения следующее свойство `ResolveStaticWebAssetsInputsDependsOn` и `RemoveIdentityAssets` целевой объект:

```xml
<PropertyGroup>
  <ResolveStaticWebAssetsInputsDependsOn>RemoveIdentityAssets</ResolveStaticWebAssetsInputsDependsOn>
</PropertyGroup>

<Target Name="RemoveIdentityAssets">
  <ItemGroup>
    <StaticWebAsset Remove="@(StaticWebAsset)" Condition="%(SourceId) == 'Microsoft.AspNetCore.Identity.UI'" />
  </ItemGroup>
</Target>
```

## <a name="next-steps"></a>Дальнейшие действия

* Сведения о настройке удостоверений с помощью SQLite см. в [этой статье о проблемах GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/5131) .
* [Настройка Identity](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

ASP.NET Core удостоверение — это система членства, которая добавляет функции входа в ASP.NET Core приложения. Пользователи могут создать учетную запись с данными для входа, хранящимися в удостоверении, или использовать внешнего поставщика входа. Поддерживаемые внешние поставщики входа включают [Facebook, Google, учетную запись Майкрософт и Twitter](xref:security/authentication/social/index).

Идентификацию можно настроить с помощью SQL Server базы данных для хранения имен пользователей, паролей и данных профилей. Кроме того, можно использовать еще одно постоянное хранилище, например хранилище таблиц Azure.

[Просмотр или скачивание образца кода](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([Загрузка)](xref:index#how-to-download-a-sample).

В этом разделе вы узнаете, как использовать удостоверение для регистрации, входа и выхода пользователя. Более подробные инструкции по созданию приложений, использующих удостоверение, см. в разделе дальнейшие действия в конце этой статьи.

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a>Адддефаултидентити и Аддидентити

<xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> было введено в ASP.NET Core 2,1. Вызов `AddDefaultIdentity` аналогичен вызову следующего:

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

Дополнительные сведения см. в разделе [адддефаултидентити Source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .

## <a name="create-a-web-app-with-authentication"></a>Создание веб-приложения с проверкой подлинности

Создание ASP.NET Core проекта веб-приложения с учетными записями отдельных пользователей.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Выберите **файл** > **Новый** > **проект**.
* Выберите **Новое веб-приложение ASP.NET Core**. Присвойте проекту имя **APP1** , которое будет иметь то же пространство имен, что и загружаемый проект. Нажмите кнопку **ОК**.
* Выберите ASP.NET Core **веб-приложение**, а затем щелкните **изменить проверку подлинности**.
* Выберите **учетные записи отдельных пользователей** и нажмите кнопку **ОК**.

# <a name="net-core-clitabnetcore-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

Созданный проект предоставляет [ASP.NET Core удостоверение](xref:security/authentication/identity) в виде [библиотеки классов Razor](xref:razor-pages/ui-class). Библиотека классов Razor Identity предоставляет конечные точки с областью `Identity`. Например:

* /идентити/аккаунт/логин
* /идентити/аккаунт/логаут
* /идентити/аккаунт/манаже

### <a name="apply-migrations"></a>Применение миграции

Примените миграции, чтобы инициализировать базу данных.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Выполните следующую команду в консоли диспетчера пакетов (PMC):

```PM> Update-Database```

# <a name="net-core-clitabnetcore-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a>Проверка регистра и вход в систему

Запустите приложение и зарегистрируйте пользователя. В зависимости от размера экрана может потребоваться выбрать переключатель навигации, чтобы просмотреть ссылки **Регистрация** и **Вход** .

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a>Настройка служб удостоверений

Службы добавляются в `ConfigureServices`. По стандартному шаблону сначала вызываются все методы `Add{Service}`, а затем все методы `services.Configure{Service}`.

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

Приведенный выше код настраивает удостоверение с использованием значений параметров по умолчанию. Доступ к службам предоставляется приложению посредством [внедрения зависимостей](xref:fundamentals/dependency-injection).

Удостоверение включается путем вызова [усеаусентикатион](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_). `UseAuthentication` добавляет по [промежуточного слоя](xref:fundamentals/middleware/index) проверки подлинности в конвейер запросов.

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

Дополнительные сведения см. в разделе [класс идентитйоптионс](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) и [Запуск приложения](xref:fundamentals/startup).

## <a name="scaffold-register-login-and-logout"></a>Регистрация, вход и выход из шаблона формирования шаблонов

Соблюдайте [идентификатор шаблона в проекте Razor с](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) инструкциями по авторизации для создания кода, приведенного в этом разделе.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Добавьте файлы Register, login и LogOut.

# <a name="net-core-clitabnetcore-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli)

Если вы создали проект с именем " **APP1**", выполните следующие команды. В противном случае используйте правильное пространство имен для `ApplicationDbContext`.

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

В PowerShell используется точка с запятой в качестве разделителя команд. При использовании PowerShell заключите точку с запятой в список файлов или вставьте список файлов в двойные кавычки, как показано в предыдущем примере.

---

### <a name="examine-register"></a>Проверка регистра

Когда пользователь щелкает ссылку **Register** , вызывается действие `RegisterModel.OnPostAsync`. Пользователь создается с помощью [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) на объекте `_userManager`. `_userManager` предоставляется путем внедрения зависимостей):

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

Если пользователь создан, происходит вход пользователя путем вызова метода `_signInManager.SignInAsync`.

**Примечание.** Раздел [подтверждение учетной записи](xref:security/authentication/accconfirm#prevent-login-at-registration) описывает, как предотвратить автоматический вход пользователя при регистрации.

### <a name="log-in"></a>Вход

Форма входа отображается в следующих случаях:

* Выбрана ссылка для **входа** .
* Пользователь пытается получить доступ к странице с ограниченным доступом, которая не авторизована для доступа, **или** если система не прошла проверку подлинности.

При отправке формы на странице входа вызывается действие `OnPostAsync`. `PasswordSignInAsync` вызывается для объекта `_signInManager` (предоставленного путем внедрения зависимостей).

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

Базовый класс `Controller` предоставляет свойство `User`, доступ к которому можно получить из методов контроллера. Например, можно перечислить `User.Claims` и принимать решения об авторизации. Для получения дополнительной информации см. <xref:security/authorization/introduction>.

### <a name="log-out"></a>Выход

Ссылка **выхода** вызывает действие `LogoutModel.OnPost`. 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

[Сигнаутасинк](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) очищает утверждения пользователя, хранящиеся в файле cookie.

Параметр POST указан в *страницах Pages/Shared/_LoginPartial. cshtml*:

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a>Проверить удостоверение

Шаблоны веб-проектов по умолчанию разрешают анонимный доступ к домашним страницам. Чтобы проверить удостоверение, добавьте [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) на страницу конфиденциальности.

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

Если вы вошли в систему, выйдите из нее. Запустите приложение и щелкните ссылку **Конфиденциальность** . Вы перейдете на страницу входа.

### <a name="explore-identity"></a>Просмотр удостоверений

Более подробное изучение удостоверений:

* [Создание полного источника идентификатора пользовательского интерфейса](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* Изучите источник каждой страницы и пошаговое выполнение отладчика.

## <a name="identity-components"></a>Компоненты идентификации

Все пакеты NuGet, зависящие от удостоверения, включены в пакет [Microsoft. AspNetCore. app метапакет](xref:fundamentals/metapackage-app).

Основным пакетом для удостоверения является [Microsoft. AspNetCore. Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/). Этот пакет содержит основной набор интерфейсов для ASP.NET Core удостоверения и включается `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.

## <a name="migrating-to-aspnet-core-identity"></a>Миграция на ASP.NET Core удостоверение

Дополнительные сведения и рекомендации по переносу существующего хранилища удостоверений см. в статье [Миграция проверки подлинности и удостоверений](xref:migration/identity).

## <a name="setting-password-strength"></a>Установка силы пароля

См. раздел [Конфигурация](#pw) для примера, который устанавливает минимальные требования к паролю.

## <a name="next-steps"></a>Дальнейшие действия

* Сведения о настройке удостоверений с помощью SQLite см. в [этой статье о проблемах GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/5131) .
* [Настройка Identity](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
