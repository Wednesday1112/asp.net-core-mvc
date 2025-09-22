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
結果兩個變數被視為同一個<br/>
<img width="675" height="218" alt="image" src="https://github.com/user-attachments/assets/e637c555-66a4-49a4-b7d1-dcc55a29e829" />
### 方便性
ViewData 跟 ViewBag 不用事先宣告，可以直接使用，但會造成程式碼雜亂不好維護，所以要少用，或用在簡單的地方，例如<title/><br/>
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
他預設的生命週期是使用過一次才會消失，例如我在另一個 view 使用 @TempData["Temp"]，然後先載入 index 頁面(index 的 view 沒有印 TempData)，在載入另一個 view，TempData就出來了，但我重新整理後又不見了，因為 TempData 使用過一次就會消失，除非再載入 index 一次<br/>
先去 Home 再去 Privacy<br/>
<img width="807" height="277" alt="image" src="https://github.com/user-attachments/assets/f23d1b81-129b-4bed-a222-1d92fd2615ce" /><br/>
重新整理<br/>
<img width="781" height="297" alt="image" src="https://github.com/user-attachments/assets/ba460bd9-00f1-43ba-b1b9-4c2a4ced5c3c" /><br/>
但只要加上 TempData.Keep() 就能讓他再存在一次
```cs
public IActionResult Privacy()
{
    TempData.Keep("Temp");

    return View();
}
```
先去 Home 再去 Privacy，不管重新整理幾次他都還在，可是這時候如果我讓另一個頁面也使用 Temp，而且沒有 TempData.Keep()，他就只會出現一次，重新整理或回到 Privacy 都不會出現 Temp 了，除非回到 Home 重新設定<br/>
```cshtml
@TempData["Temp"]
```
View2 頁面<br/>
<img width="652" height="242" alt="image" src="https://github.com/user-attachments/assets/0c706422-db24-4a5f-b24a-386b032277aa" /><br/>
重新整理<br/>
<img width="778" height="320" alt="image" src="https://github.com/user-attachments/assets/74e2cdee-f6b9-4555-871b-631225377ce0" /><br/>
回到 Privacy 頁面<br/>
<img width="782" height="308" alt="image" src="https://github.com/user-attachments/assets/5993e569-8ade-484e-86bb-50e05b2aabe3" />






