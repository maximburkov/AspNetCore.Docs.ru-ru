---
title: Использование grunt в ASP.NET Core
author: rick-anderson
description: Использование grunt в ASP.NET Core
ms.author: riande
ms.date: 12/05/2019
uid: client-side/using-grunt
ms.openlocfilehash: e516b85da7e94d0c93be642086fede0a11fea3c2
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74879796"
---
# <a name="use-grunt-in-aspnet-core"></a>Использование grunt в ASP.NET Core

Grunt — это средство выполнения задач JavaScript, которое автоматизирует скрипты минификации, компиляцию TypeScript, качества кода "Lint", предварительные процессоры CSS и практически любую рутинную работу, которая необходима для поддержки разработки клиентов. Grunt полностью поддерживается в Visual Studio.

В этом примере в качестве начальной точки используется пустой проект ASP.NET Core, чтобы продемонстрировать, как автоматизировать процесс сборки клиента с нуля.

В завершенном примере очищается целевой каталог развертывания, объединяются файлы JavaScript, проверяется качество кода, сжимается содержимое файлов JavaScript и развертывается в корне веб-приложения. Мы будем использовать следующие пакеты:

* **grunt**: пакет средства выполнения задач grunt.

* **grunt-от участников сообщества-Clean**: подключаемый модуль, удаляющий файлы или каталоги.

* **grunt-от участников сообщества-жшинт**— подключаемый модуль, который проверяет качество кода JavaScript.

* **grunt-от участников сообщества-Concat**: подключаемый модуль, объединяющий файлы в один файл.

* **grunt-от участников сообщества-углифи**— подключаемый модуль, который уменьшает JavaScript для уменьшения размера.

* **grunt-от участников сообщества-Watch**: подключаемый модуль, отслеживающий активность файлов.

## <a name="preparing-the-application"></a>Подготовка приложения

Чтобы начать, настройте новое пустое веб-приложение и добавьте файлы примеров TypeScript. Файлы TypeScript автоматически компилируются в JavaScript с помощью параметров по умолчанию Visual Studio и будут рассматриваться как необработанные материалы для обработки с использованием grunt.

1. В Visual Studio создайте новый `ASP.NET Web Application`.

2. В диалоговом окне **Новый проект ASP.NET** выберите ASP.NET Core **пустой** шаблон и нажмите кнопку ОК.

3. В обозреватель решений проверьте структуру проекта. Папка `\src` содержит пустые узлы `wwwroot` и `Dependencies`.

    ![пустое веб-решение](using-grunt/_static/grunt-solution-explorer.png)

4. Добавьте новую папку с именем `TypeScript` в каталог проекта.

5. Перед добавлением файлов убедитесь, что в Visual Studio установлен параметр "компилировать при сохранении" для файлов TypeScript. Последовательно выберите **сервис** > **Параметры** > **текстовый редактор** > **проект** **typescript** > .

    ![параметры, устанавливающие автоматическую компиляцию файлов TypeScript](using-grunt/_static/typescript-options.png)

6. Щелкните правой кнопкой мыши каталог `TypeScript` и выберите в контекстном меню команду **добавить > новый элемент** . Выберите элемент **файл JavaScript** и назовите файл *предпочтений. TS* (Обратите внимание на расширение \*. TS). Скопируйте строку кода TypeScript ниже в файл (при сохранении будет создан новый файл *предпочтений. js* с исходным кодом JavaScript).

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. Добавьте второй файл в каталог **TypeScript** и назовите его `Food.ts`. Скопируйте приведенный ниже код в файл.

    ```typescript
    class Food {
      constructor(name: string, calories: number) {
        this._name = name;
        this._calories = calories;
      }

      private _name: string;
      get Name() {
        return this._name;
      }

      private _calories: number;
      get Calories() {
        return this._calories;
      }

      private _taste: Tastes;
      get Taste(): Tastes { return this._taste }
      set Taste(value: Tastes) {
        this._taste = value;
      }
    }
    ```

## <a name="configuring-npm"></a>Настройка NPM

Затем настройте NPM для загрузки grunt и grunt-Tasks.

1. В обозреватель решений щелкните правой кнопкой мыши проект и выберите в контекстном меню команду **добавить > новый элемент** . Выберите элемент **файл конфигурации NPM** , оставьте имя файла *Package. JSON*по умолчанию и нажмите кнопку **добавить** .

2. В файле *Package. JSON* в фигурных скобках объекта `devDependencies` введите «grunt». Выберите `grunt` из списка IntelliSense и нажмите клавишу ВВОД. Visual Studio поставит в кавычки имя пакета grunt и добавить двоеточие. Справа от двоеточия выберите последнюю стабильную версию пакета в верхней части списка IntelliSense (нажмите `Ctrl-Space`, если IntelliSense не отображается).

    ![grunt IntelliSense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > NPM использует [семантическое управление версиями](https://semver.org/) для Организации зависимостей. Семантическое управление версиями, также известное как SemVer, определяет пакеты со схемой нумерации \<основном >.\<дополнительный >. >\<исправлений. IntelliSense упрощает семантическое управление версиями, отображая лишь несколько распространенных вариантов. Верхний элемент в списке IntelliSense (0.4.5 в примере выше) считается последней стабильной версией пакета. Символ каретки (^) соответствует самой последней основной версии, а тильда (~) соответствует самой последней дополнительной версии. Ознакомьтесь со [справочником по анализатору версий NPM semver](https://www.npmjs.com/package/semver) , как описано в полной версии выразительности, предоставляемой semver.

3. Добавьте дополнительные зависимости для загрузки пакетов grunt-от участников сообщества-\* для *очистки*, *жшинт*, *concat*, *углифи*и *просмотра* , как показано в примере ниже. Версии не обязательно должны соответствовать примеру.

    ```json
    "devDependencies": {
      "grunt": "0.4.5",
      "grunt-contrib-clean": "0.6.0",
      "grunt-contrib-jshint": "0.11.0",
      "grunt-contrib-concat": "0.5.1",
      "grunt-contrib-uglify": "0.8.0",
      "grunt-contrib-watch": "0.6.1"
    }
    ```

4. Сохраните файл *Package. JSON* .

Пакеты для каждого `devDependencies` элемента будут скачаны вместе с любыми файлами, которые требуются для каждого пакета. Файлы пакета можно найти в каталоге *node_modules* , включив кнопку " **Показывать все файлы** " в **Обозреватель решений**.

![grunt node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> При необходимости можно вручную восстановить зависимости в **Обозреватель решений** , щелкнув `Dependencies\NPM` правой кнопкой мыши и выбрав пункт меню **восстановить пакеты** .

![восстановление пакетов](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a>Настройка grunt

Grunt настраивается с помощью манифеста с именем *грунтфиле. js* , который определяет, загружает и регистрирует задачи, которые могут выполняться вручную или настроены для автоматического запуска на основе событий в Visual Studio.

1. Щелкните правой кнопкой мыши проект и выберите **добавить** > **новый элемент**. Выберите шаблон элемента **файла JavaScript** , измените имя на *грунтфиле. js*и нажмите кнопку **добавить** .

1. Добавьте следующий код в *грунтфиле. js*. Функция `initConfig` задает параметры для каждого пакета, а оставшаяся часть модуля загружает и регистрирует задачи.

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. В функции `initConfig` добавьте параметры для задачи `clean`, как показано в примере *грунтфиле. js* ниже. Задача `clean` принимает массив строк каталога. Эта задача удаляет файлы из *wwwroot/lib* и удаляет весь каталог */TEMP* .

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. Под функцией `initConfig` добавьте вызов `grunt.loadNpmTasks`. Это сделает задачу готовой к запуску из Visual Studio.

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. Сохраните *грунтфиле. js*. Файл должен выглядеть примерно так, как показано на снимке экрана ниже.

    ![Начальная грунтфиле](using-grunt/_static/gruntfile-js-initial.png)

1. Щелкните правой кнопкой мыши *грунтфиле. js* и выберите **Task Runner Explorer (обозреватель выполнения задач** ) в контекстном меню. Откроется окно **Обозреватель выполнения задач** .

    ![меню обозревателя выполнения задач](using-grunt/_static/task-runner-explorer-menu.png)

1. Убедитесь, что `clean` отображается в разделе **задачи** в обозревателе средств выполнения **задач**.

    ![Список задач обозревателя выполнения задач](using-grunt/_static/task-runner-explorer-tasks.png)

1. Щелкните правой кнопкой мыши задачу очистить и выберите пункт **выполнить** в контекстном меню. В командном окне отображается ход выполнения задачи.

    ![Обозреватель выполнения задач "выполнить задачу очистки"](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > Нет файлов или каталогов для очистки. При желании вы можете вручную создать их в обозреватель решений а затем запустить задачу очистки в качестве теста.

1. В функции `initConfig` добавьте запись для `concat`, используя приведенный ниже код.

    Массив свойств `src` перечисляет файлы, которые нужно объединить, в том порядке, в котором они должны быть объединены. Свойство `dest` присваивает путь к созданному Объединенному файлу.

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > Свойство `all` в приведенном выше коде является именем целевого объекта. Целевые объекты используются в некоторых задачах grunt для разрешения нескольких сред сборки. Вы можете просматривать встроенные целевые объекты с помощью IntelliSense или назначать собственные.

1. Добавьте `jshint` задачу, используя приведенный ниже код.

    Служебная программа жшинт `code-quality` выполняется для каждого файла JavaScript, найденного во *временном* каталоге.

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > Параметр "-W069" является ошибкой, созданной жшинт, когда JavaScript использует синтаксис скобок для назначения свойства вместо точечной нотации, т. е. `Tastes["Sweet"]` вместо `Tastes.Sweet`. Параметр отключает предупреждение, разрешающее продолжение оставшейся части процесса.

1. Добавьте `uglify` задачу, используя приведенный ниже код.

    Задача уменьшает *Объединенный JS* -файл, находящийся в каталоге Temp, и создает файл результатов в wwwroot/lib, следуя стандартному соглашению об именовании *\<имя файла\>. min. js*.

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. При вызове `grunt.loadNpmTasks`, который загружает `grunt-contrib-clean`, включите тот же вызов жшинт, concat и углифи, используя приведенный ниже код.

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. Сохраните *грунтфиле. js*. Файл должен выглядеть примерно так, как показано в примере ниже.

    ![Полный пример файла grunt](using-grunt/_static/gruntfile-js-complete.png)

1. Обратите внимание, что список задачи **обозревателя выполнения задач** содержит `clean`, `concat`, `jshint` и `uglify` задач. Выполните каждую задачу по порядку и просмотрите результаты в **Обозреватель решений**. Каждая задача должна выполняться без ошибок.

    ![Обозреватель выполнения задач выполнение каждой задачи](using-grunt/_static/task-runner-explorer-run-each-task.png)

    Задача Concat создает новый *Объединенный JS* -файл и помещает его во временный каталог. Задача `jshint` просто выполняется и не создает выходные данные. Задача `uglify` создает новый *объединенный файл. min. js* и помещает его в *wwwroot/lib*. По завершении решение должно выглядеть примерно так, как показано на снимке экрана ниже:

    ![Обозреватель решений после всех задач](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > Дополнительные сведения о параметрах для каждого пакета см. в [https://www.npmjs.com/](https://www.npmjs.com/) и поиске имени пакета в поле поиска на главной странице. Например, можно найти пакет grunt-от участников сообщества-Clean, чтобы получить ссылку на документацию с описанием всех ее параметров.

### <a name="all-together-now"></a>Итоги

Используйте метод `registerTask()` grunt для выполнения ряда задач в определенной последовательности. Например, чтобы выполнить приведенные выше примеры шагов в разделе Порядок очистки > Concat-> жшинт-> углифи, добавьте приведенный ниже код в модуль. Код должен быть добавлен на тот же уровень, что и вызовы Лоаднпмтаскс (), за пределами Инитконфиг.

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

Новая задача отображается в обозревателе выполнения задач в разделе задачи псевдонима. Можно щелкнуть правой кнопкой мыши и запустить его так же, как и другие задачи. Задача `all` будет выполняться `clean`, `concat`, `jshint` и `uglify`по порядку.

![псевдонимы задач grunt](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a>Просмотр изменений

Задача `watch` следит за файлами и каталогами. Контрольное значение активирует задачи автоматически, если обнаруживает изменения. Добавьте приведенный ниже код в Инитконфиг для отслеживания изменений в файлах \*. js в каталоге TypeScript. При изменении файла JavaScript `watch` запустит задачу `all`.

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

Добавьте вызов `loadNpmTasks()`, чтобы отобразить задачу `watch` в обозревателе выполнения задач.

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

Щелкните правой кнопкой мыши задачу контрольные значения в обозревателе выполнения задач и выберите пункт запустить в контекстном меню. В командном окне, отображающем выполняемую задачу Watch, будет отображаться "ожидание..." Сообщение. Откройте один из файлов TypeScript, добавьте пробел, а затем сохраните файл. Это приведет к активации задачи наблюдения и активации выполнения других задач в указанном порядке. На следующем снимке экрана показан пример запуска.

![выходные данные выполняемых задач](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a>Привязка к событиям Visual Studio

Если вы не хотите запускать задачи вручную при каждой работе в Visual Studio, привяжите задачи **перед сборкой**, после событий **сборки**, **очистки**и **открытия проектов** .

Привяжите `watch`, чтобы он выполнялся при каждом открытии Visual Studio. В обозревателе выполнения задач щелкните правой кнопкой мыши задачу контрольные значения и выберите пункт **привязки** > **Открыть проект** в контекстном меню.

![Привязка задачи к открытию проекта](using-grunt/_static/bindings-project-open.png)

Выгрузите и перезагрузите проект. При повторной загрузке проекта задача Watch начинает выполняться автоматически.

## <a name="summary"></a>Сводка

Grunt — это мощный исполнитель задач, который можно использовать для автоматизации большинства задач сборки клиента. Grunt использует NPM для доставки своих пакетов, а также обеспечивает интеграцию средств с Visual Studio. Обозреватель выполнения задач Visual Studio обнаруживает изменения в файлах конфигурации и предоставляет удобный интерфейс для выполнения задач, просмотра выполняемых задач и привязки задач к событиям Visual Studio.
