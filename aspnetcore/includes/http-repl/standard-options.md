* `-F|--no-formatting`

  <span data-ttu-id="9adb0-101">Флаг, при установке которого форматирование HTTP-ответа не применяется.</span><span class="sxs-lookup"><span data-stu-id="9adb0-101">A flag whose presence suppresses HTTP response formatting.</span></span>

* `-h|--header`

  <span data-ttu-id="9adb0-102">Задает заголовок HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="9adb0-102">Sets an HTTP request header.</span></span> <span data-ttu-id="9adb0-103">Поддерживаются следующие два формата значений:</span><span class="sxs-lookup"><span data-stu-id="9adb0-103">The following two value formats are supported:</span></span>

  * `{header}={value}`
  * `{header}:{value}`

* `--response`

  <span data-ttu-id="9adb0-104">Указывает файл, в который должен быть записан весь HTTP-ответ (включая заголовки и текст).</span><span class="sxs-lookup"><span data-stu-id="9adb0-104">Specifies a file to which the entire HTTP response (including headers and body) should be written.</span></span> <span data-ttu-id="9adb0-105">Например, `--response "C:\response.txt"`.</span><span class="sxs-lookup"><span data-stu-id="9adb0-105">For example, `--response "C:\response.txt"`.</span></span> <span data-ttu-id="9adb0-106">Если файл не существует, он создается.</span><span class="sxs-lookup"><span data-stu-id="9adb0-106">The file is created if it doesn't exist.</span></span>

* `--response:body`

  <span data-ttu-id="9adb0-107">Указывает файл, в который должен быть записан текст HTTP-ответа.</span><span class="sxs-lookup"><span data-stu-id="9adb0-107">Specifies a file to which the HTTP response body should be written.</span></span> <span data-ttu-id="9adb0-108">Например, `--response:body "C:\response.json"`.</span><span class="sxs-lookup"><span data-stu-id="9adb0-108">For example, `--response:body "C:\response.json"`.</span></span> <span data-ttu-id="9adb0-109">Если файл не существует, он создается.</span><span class="sxs-lookup"><span data-stu-id="9adb0-109">The file is created if it doesn't exist.</span></span>

* `--response:headers`

  <span data-ttu-id="9adb0-110">Указывает файл, в который должны быть записаны заголовки HTTP-ответа.</span><span class="sxs-lookup"><span data-stu-id="9adb0-110">Specifies a file to which the HTTP response headers should be written.</span></span> <span data-ttu-id="9adb0-111">Например, `--response:headers "C:\response.txt"`.</span><span class="sxs-lookup"><span data-stu-id="9adb0-111">For example, `--response:headers "C:\response.txt"`.</span></span> <span data-ttu-id="9adb0-112">Если файл не существует, он создается.</span><span class="sxs-lookup"><span data-stu-id="9adb0-112">The file is created if it doesn't exist.</span></span>

* `-s|--streaming`

  <span data-ttu-id="9adb0-113">Флаг, при установке которого включается потоковая передача HTTP-ответа.</span><span class="sxs-lookup"><span data-stu-id="9adb0-113">A flag whose presence enables streaming of the HTTP response.</span></span>