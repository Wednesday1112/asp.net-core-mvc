# asp.net core mvc 自學紀錄
- [View](#view)
  - [ViewData](#viewdata)
  - [ViewBag](viewdbag)
  - [他們同個變數會共用](#他們同個變數會共用)
  - [方便性](#方便性)
  - [生命週期](#生命週期)
  - [TempData](#tempdata)
  - [架構的執行順序](#架構的執行順序)
  - [_ViewStart](#viewstart)
- [Razor 基本語法](#razor-基本語法)
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
```cs
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
### 架構的執行順序
request > Controller > View > Layout(主版型)
### _ViewStart
設定主板型(版型放在View\Shared)
View\_ViewStart
```cshtml
@{
    var controller = ViewContext.RouteData.Values["controller"].ToString();

    if(controller == "Demo")
    {
        Layout = "_DemoLayout";
    }
    else
    {
        Layout = "_Layout";
    }
}
```
View\Shared\_DemoLayout
```cdhtml
<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">Demo</a> //讓左上角標題變Demo
```
<img width="1346" height="371" alt="image" src="https://github.com/user-attachments/assets/8c5a974e-3fee-4b2b-9398-5852723bf9cb" /><br/>
也可以再放一個 _ViewStart 在要改版型的 controller 的 view 資料夾裡，這樣會先執行外面的 _ViewStart，再執行裡面的 _ViewStart，裡面蓋掉外面的，所以最後 Demo 的版型會以裡面的為主<br/>
<img width="213" height="524" alt="image" src="https://github.com/user-attachments/assets/c6885f5d-0f66-4fe8-b4fc-ab1b1c8abe74" /><br/>
不想用 _ViewStart，只想要某個 view 用其他版型，可以直接在 index 裡使用
View\Demo\index
```cshtml
@{
    Layout = "_DemoLayout"; //Layout = null; 代表不套版型
}

@{
    ViewData["Title"] = "Home Page";
}

<div class="text-center">
    <h1 class="display-4">Welcome</h1>
    <p>Learn about <a href="https://learn.microsoft.com/aspnet/core">building Web apps with ASP.NET Core</a>.</p>
</div>
```
## Razor 基本語法
```cshtml
@{
    string title = "在 @{ } 中可以使用 c# 語法";
    int number = 5;
}
@title : @number
```
<img width="630" height="255" alt="image" src="https://github.com/user-attachments/assets/b497172f-c4d0-48b2-be01-e15b9e2b2acc" /><br/>
if 判斷式
```cshtml
@if(number > 0)
{
    //在這邊還是寫程式碼區塊
    <div>如果今天要在這裡面顯示文字到網頁上，就寫在html標籤裡面即可</div>
    <text>如果怕破壞格式不想用html標籤，可以用text，則不會留下痕跡</text>
}
else
{
    <div>number 小於等於 0</div>
}
```
text 沒有留下痕跡<br/>
<img width="1599" height="643" alt="image" src="https://github.com/user-attachments/assets/2c5e3c21-2139-4efb-bb7f-b2b3e30db1af" /><br/>
如果要在畫面上打小老鼠
```cshtml
<div>@@</div>//這樣會顯示一個@
```
<img width="493" height="193" alt="image" src="https://github.com/user-attachments/assets/9f25900e-1d3a-4809-af22-77a4840ffc05" /><br/>
或是
```cshtml
@{
    string 小老鼠 = "@"; //可以用中文當變數，超酷
}
<!-- 想要在@後面加文字 -->
<div>@(小老鼠)大老鼠</div>
```
<img width="537" height="230" alt="image" src="https://github.com/user-attachments/assets/fcea51af-f939-41c0-9d39-e3ac11e23815" />





