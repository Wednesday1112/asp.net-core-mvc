# asp.net-core-mvc
## View
### ViewData
controller
```cs
public IActionResult index()
{
    ViewData["要傳的變數"] = 要傳得值;

    return View();
}
```
view\controllerName\index
```cs
@ViewData["要傳的變數"] //Razor語法，想要在這用c#語法或變數時要加@
```
### ViewBag
controller
```cs
public IActionResult index()
{
    ViewBag.要傳的變數 = 要傳得值;

    return View();
}
```
view\controllerName\index
```cs
@ViewBag.要傳的變數 //Razor語法，想要在這用c#語法或變數時要加@
```
### 他們同個變數會共用
controller
```cs
public IActionResult index()
{
    ViewData["title"] = "這是 ViewDate 設定的值";
    ViewBag.Title = "這是 ViewBag 設定的值"; //這裡 T 我用大寫

    return View();
}
```
view\controllerName\index
```cshtml
@ViewData["title"]
<br/>
@ViewBag.Title
```
結果兩個變數被視為同一個
<img width="675" height="218" alt="image" src="https://github.com/user-attachments/assets/e637c555-66a4-49a4-b7d1-dcc55a29e829" />
### 方便性
ViewData 跟 ViewBag 不用事先宣告，可以直接使用，但會造成程式碼雜亂不好維護，所以要少用，或用在簡單的地方，例如<title/>
! 注意 ! ViewData 稍微嚴謹一點
```
public IActionResult Index()
{
    ViewData["number1"] = 10;
    int ViewDataNum = 10 + (int)ViewData["number1"]; //ViewData 比較嚴謹，要給型別

    ViewBag.number2 = 10;
    int ViewBagNum = 10 + ViewBag.number2;

    return View();
}
```
### 生命週期
兩個的生命週期只有一個 request，下一個 request 又是新的變數，例如我在另一個 view 使用 @ViewData["number1"]，雖然我有先載入 index 頁面，但另一個 view 就是另一個 request 了，所以另一個 view 印不出 ViewData["number1"] 的值
###TempData
用起來跟 ViewData 差不多
```cs
public IActionResult index()
{
    TempData["Temp"] = "這是 Temp 設定的值";

    return View();
}
```
但他的生命週期是使用過一次才會消失，例如我在另一個 view 使用 @TempData["Temp"]，然後先載入 index 頁面(index 的 view 沒有印 TempData)，在載入另一個 view，TempData就出來了，但我重新整理後又不見了，因為 TempData 使用過一次就會消失，除非再載入 index 一次
<img width="807" height="277" alt="image" src="https://github.com/user-attachments/assets/f23d1b81-129b-4bed-a222-1d92fd2615ce" />

