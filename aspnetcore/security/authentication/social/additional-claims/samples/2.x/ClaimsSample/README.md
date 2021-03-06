# <a name="additional-claims-sample-app"></a>Дополнительные утверждения примера приложения

Этот пример демонстрирует способы:

* Получает имя и Фамилия пользователя от Google и сохраняет имя утверждения со значениями, предоставляемый Google.
* Store маркер доступа Google в пользователя `AuthenticationProperties`.

Чтобы использовать пример приложения:

1. Регистрация приложения и получить допустимый идентификатор клиента и секрет клиента для проверки подлинности Google. Дополнительные сведения см. в разделе [установки внешней учетной записи Google](https://docs.microsoft.com/aspnet/core/security/authentication/social/google-logins).
1. Указание идентификатора клиента и секрет клиента для приложения в [GoogleOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) из `Startup.ConfigureServices`.
1. Запустите приложение и запрос страницы Мои утверждения. Если пользователь не вошел в систему, которую перенаправляет пользователя приложение в Google. Войдите с помощью Google. Google перенаправляет пользователя обратно к приложению (`/MyClaims`). Пользователь проходит проверку подлинности и загрузке страницы Мои утверждения. Заданное имя и фамилия утверждения присутствуют в разделе **утверждения пользователей** со значениями, предоставляемый Google. Маркер доступа отображается в столбце **свойства проверки подлинности**.
