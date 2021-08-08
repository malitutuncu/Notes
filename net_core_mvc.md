# MVC

Mvc bir model-view-controller mimarisel desendir(Architectural pattern)

Veri ile gösterim arasında bir soyutlama esas alınır.

asp.net mvc ise mvc kalıbını benimsemiş bir mimari geliştirmiştir.





# API

api Application Programming Interface uygulama programlama arayüzüdür.



# ASP.NET MVC

asp.net core uygulamaları özünde bir console uygulamasıdır. 

asp.net core kendi dahilinde kendi sunucusunu ayaga kaldırır.(program.cs)

asp.net core da host ayaga kalkarkan startup.cs i kullanır

startup cs temel configurasyon sınıfıdır.



# ASP.NET CORE MVC Pipeline

Request------Middleware---Routing-----Controller Initilization----Action Method Execution ---- Result Execution----View Rendering--------Response

### Action Types

- **ViewResult** response olarak bir bir view dosyası (.cshtml) render etmemizi saglayan action turudur. Bir sayfayı döndürür. _ViewStart.cshtml dosyasını baz alır

- **PartialViewResult** Bir view(.cshtml) render etmemizi saglayan bir action turudur. client tabanlı ajax isteklerinde kullanıma yatkındır.Bir sayfasının tamamını degil bir sayfanın bir parcasını degistirir. _ViewStart.cshtml dosyasını baz almaz
- **JsonResult** datayı JSON turunde donduren bir action tipidir(content-type:application-json)
- **EmptyResult** Gelen istege herhangi bir responsedondurmek istemedigimiz kullandıgımız action turudur.
- **ContentResult** İstek neticesinde cevap olarak düz bir metin donmemizi saglayan action turudur(content-type:application-text)
- **ViewComponentResult** İstege cevap olarak bir viewComponent donmemizi saglayan action turudur
- **ActionResult** genel



### NonAction & NonController

- **NonAction** Controller icerisinde tanımlanmıs metodların sadece is mantıgı amaclı kullanılmasını istedigimizde yani dısarıdan gelen requestin alınmasını engellemek istiyorsak ilgili metodun bir action olmadıgı bildirmek icin kullanılır.
- **NonController** bir controllerin dısarıdan gelen istekleri karsılamasını istemiyorsak NonController ile isaretleriz



### ViewBag, ViewData, TempData

- **ViewBag** Viewe gonderilecek datayı dinamik bir sekilde tanımlanan degiskenle tasımamızı saglayan kontroldur. türü runtime da belirli olacak sekilde tasır. ViewBag actionlar arası veri tasıyamaz.
- **ViewData** ViewBag gibi actiondaki datayı viewe tasımamızı saglar. viewdata veriyi object olarak tasır. ViewData actionlar arası veri tasıyamaz
- **TempData** ViewData daki gibi actiondaki datayı viewe tasımamızı saglar. Tempdata actionlar arası veri tasıyabilir.Verileri tarayıcının cookie sinde tutar



### Tuple kullanımı

- viev i me gonderecegım data iki tane complex tip iceriyorsa(user,product) bunun icin bir viewModel acip icerisine koyup o viewmodeli viewime gonderebilirim yada tuple ile gonderebilirim.

- Bind mekanızmasında tuple bir nesne kullanıyorsak bu tuple nesnnesinin icerisinde classların verilmesi gerekir, null olamaz.. get için kendisi olusturmaz. Controllerda metodda create etmem gerekiyor.

  ```c#
  public IActionResult CreateProduct()
  {
     var tuple = (new Product(), new User());
     return View(tuple)
  }
  ```

- View de model  kullanımı

  ```c#
  @model (Product, User)
  ```

   (bu sekilde kullanirsam @model.Item1, @model.Item2 olarak kullanırım)

  buna anlamlı isim vermek istersek

  ```html
  @model (Product product, User user)
  ```

  bu sekilde kullanırsam @model.product, @model.user olarak kullanırım

- Post edildiginde controllerda bunu yakalamam icin tuple icerisindeki her bir nesneyi ayrı ayrı parametre olarak gecmeliyim ve bunları tupleda hangi nesneye bind edildigini bildirmeliyim. Burada tuple kullandıgım nesneler takma isim versemde tuple soldan saga item1,item2 olacak sekilde isimlendirmem gerekiyor

  ```c#
  [HttpPost]
  public IActionResult CreateProduct([Bind(Prefix = "Item1")]Product product, [Bind(Prefix = "Item1")] User user)
  {
     
     return View(tuple)
  }
  ```

  

### Razor

@ operatoru razor operatorudur.

- **Razor Yorum Satırı**   :  @* Yorum *@

- **Razor degisken olusturma**

  ```c#
  @{
  	string metin = "abc"
  	Console.WriteLine(metin);
  }
  ```

  

- **Razor ile parcalı scope yapısı** : Razorda scope lar c# tarafındaki gibi derlenmediginden baska bir scope altında digerinde tanımladıgım degiskeni kullanabilirim. razordaki scoplar derlendiginde tek bir scope olarak derlenir

  ```c#
  @{
     string metin = "abc";
  }
  
  .......
  
  @{
  	Console.WriteLine(metin);
  }
  ```

  

- **Razor icerisinde HTML Kullanımı**

  ```c#
@{
      <div></div>
}
  ```
  
- **Razor icerisinde <text> etiketi kullanımı**

  ```c#
  @{
    if (true)
    {
        <text>Evet</text>
    }
    else
    {
        <text>Hayir</text>
    }
    
  
  
  }
  ```

- **Razor ile Tek satırlık islemler**

  ```c#
  <h3>@(5+4)</h3>
  <h3>@(33 % 5 == 3 ? "Evet" : "Hayır")</h3>
  <h3>@metin</h3>
  <h3>@(metin)</h3> 
  ```

- **Koşul yapıları** : if-foreach gibi yapılarda @{ } razor scopa ihtiyac olmadan direk olarak  @ ile baslayarak islem yapabiliriz

  ```c#
  @if (@model.pasif)
  {
  	<h3>pasif</h3>
  }
  else
  {
  	<h3>aktif</h3>
  }
  ```

  ```c#
  @foreach(var item in model.products)
  {
  	...
  }
  ```

  

### UrlHelpers, HtmlHelpers,TagHelper

Helperslar yardımcı metodlardır

UrlHelpers url de yardımcı metodlar, HtmlHelpers htmlde yardımcı metodlar

#### **UrlHelpers**

- **Metodlar**

  - **Action** : Verilen Controller ve Action'a ait url olusturmayı saglayan metot, Action yapısı sistemdeki rotanın semasına göre olusturur.

    ```c#
    Url.Action("index","product", new { id=5 })
    //Olusturulan Url : product/index/5
    ```

  - **ActionLink** : Verilen Controller ve Action'a ait url oluşturmayı sağlayan metod. url in tamamını olusturur.

    ```c#
    Url.ActionLink("index","product", new { id=5 })
    //Olusturulan Url : https://localhost:5001/product/index/5
    ```

  - **Content** Genellikle CSS ve Script gibi dosya dizinlerini programatik olarak tarif etmek için kullanmaktayız. UseStaticFiles middlerware ile gelen static dosya yapılandırması bunu üstlenir. Çok kullanılmaz

    ```c#
    Url.Content("~/sites.css")
    ```

  - **RouteUrl** Mimaride tanımlı olan Route isimleriyle uygun bir sekilde url olusturan metoddur

    ```c#
    Url.RouteUrl("Default")
    //url : /Product/GetProducts
    ```

- **Propertyler**

  - **ActionContext:** O anki url'e dair tum bilgilere erismemizi saglayan bir propertydir.

    Url.ActionContex ModelStateini, Route datalarını, requesti, response vs bir cok seye erismemizi saglar.



#### **HtmlHelper**

 Gunumuzde cok kullanılmıyor. TagHelperlar cok daha performanslı. 

- Html etiketlerini server tabanlı olusturmamızı saglayan yardımcı metodları bulunmaktar 

- Hedeflenen .cshtml dosyalarını render etmemizi saglamakta 

- O anki contexte dair bilgiler edinmemizi saglamakta

- Veri tasıma kontrollerine erismemizi saglamaktadır.

- **Metodlar**

  - **Html.Partial** Hedef Viewi render etmemizi saglayan bir fonksiyondur. Render edilen view'e ilgili action'dan model/data gönderebilmektedir

    ```c#
    <div style="border-top-color:ActiveBorder">
    	@Html.Partial("~/Views/Product/Partials/ListPartial.cshtml")
    </div>
    ```

    Default olarak controllerdan gonderilen modeli kullanır. Örnegin: Controllerdan A viewine X modelini gönderiyorsam, ve icerisinde APartial.cshtml i partial view i cagırıyorsam direk olarak APartial viewine de X modeli gider. baska bir model kullanmasını istiyorsam A viewi icerisinde iken kullanmak istedigim modeli gonderirim. 2.parametresinde bunu alır

    ```c#
    <div style="border-top-color:ActiveBorder">
    	@Html.Partial("~/Views/Product/Partials/ListPartial.cshtml", new OrnekModel())
        //yada elde varsa direk kullanabilirim
    </div>
    ```

  - **Html.RenderPartial** Html.Partial gibi calısır farkı Html.Partial scope icerisinde kullanılmıyorken RenderPartial scope icerisinde kullanılır. Bununda sebebi Html.Partial geriye string dondururken Html.RenderPartial void dondurur. Html.Partial a gore daha hızlıdır.

    ```c#
    <div style="border-top-color:ActiveBorder">
    	@{ Html.RenderPartial("~/Views/Product/Partials/ListPartial.cshtml"); }
    </div>
    ```

  - **Html.ActionLink** Url olusturur. Url.ActionLinkle aynı işlevi yapar farkı <a> tagındaki html cıktısını olusturur.

    ```c#
    @Html.ActionLink("anasayfa","Index","Home")
    //Cıktısı : <a href="/Anasayfa">Anasayfa</a>
    ```

    

  - **Html.Form** Kullanıcıyla etkilesime girmemizi saglayan form ve input nesnelerini olusturmamızı saglayan metodlardır. Örnegin:

    ```c#
     @Html.BeginForm()
     @Html.Checkbox("cb") // <input id="cb" name="cb" type="checkbox" value="true">
     @Html.Textbox("txt") // <input id="txt" name="txt" type="text" value>
    ```

    Günümüzde cok fazla kullanılmamaktadır. Maliyetlidir. Mantık olarakta html i kodlamamız gerekirken boyle bir surecte html cıktılarının yukunude servere vermek mantıksızdır.

- **Propertyler**

  - ViewContext
  - TempData
  - ViewData
  - ViewBag

#### Custom HtmlHelper Fonksiyonu

@Html.TextBox ı ornek alacak olursak overload larında buna value , extra attiributler verebildigimizi goruruz

```c#
@Html.TextBox("txtAdi",null,new { style= "background-color:red" })
```

Bunu global olarak yapmak istiyorsam @Html.TextBox nereden turuyorsa (IHtmlHelper) Extension yazmam gerekli. Geri donecegim degerde @Html.Textbox geriye ne donuyorsa o olacaktır (IHtmlContent)

```c#
public static class Extensions
{
    public static IHtmlContent CustomTextBox(this IHtmlHelper, string name, string value)
    =>
        htmlHelper.TexBox(name,value,new { 
            style= "background-color:red",
            @class="form-input",
            a = "a",
            b = "b"
        });
}
```

Bunu kullanırken

```c#
@Html.CustomTextBox("txtCustomAdi",null)
```

Not: Birseyin customize edilmis halini extension metodlarla yapabiliriz

### TagHelpers

HtmlHelper ların yerine gelen yeni yapılanmalardır.

TagHelpers, daha okunabilir, anlaşılabilir ve kolay geliştirilebilir bir view inşa etmemizi saglayan HtmlHelper yerine gelen yapılardır.

- TagHelper'lar viewlerde ki kod maliyetini düsürür(daha az kod)
- HtmlHelpers gibi html nesnelerini generate edilmesini servera getirdiği yuku ortandan kaldırır

Kullanabilmemiz icin 

```c#
@addTagHelpers*, Microsoft.AspNetCore.Mvc.TagHelpers // entegre etmemiz gerek
```

- **Form TagHelper**

  Form nesnesi olustururken kullandıgımız tag helperimiz

  ```html
  <form action="/" method="post"></form>
  ```

- **input  TagHelper**

  ```html
  <input type="type" name="name" value="" />
  ```

- **select TagHelper**

  ```html
  <select>
  	<option value="value">text</option>
  </select>
  ```

- **Cache TagHelper**

  Burada cache tagHelperların cache alır

  ```html
  <cache>
      Cache : @DateTime.Now
  </cache>
  @DateTime.Now
  ```

  Uygulamayı calıstırdıgımızda

  ```
  Cache : 24.12.2020 11:55:00
  24.12.2020 11:55:00
  ```

  cıktısını goruruz, yeniledigimizde ise cachelenmeyen tarafın ilerledigini goruruz

  ```
  Cache : 24.12.2020 11:55:00
  24.12.2020 11:57:00
  ```

- **Environment TagHelper**

  Bulundugumuz ortamlara gore kodun isleyisini yonlendirebiliyoruz

  ```html
  <environment names="Development">
      <p>Development ortamı</p>
  </environment>
  <environment names="Production,Staging">
      <p>Production veya staging ortamı</p>
  </environment>
  ```

- **Image TagHelper**

  Tarayıcı resim,css,js dosyalarını bir kere serverdan aldıgında bunları cacheler. örnegin resimi tarayıcı cacheledi resimi degistirsek bile tarayıcı cacheden kullanacagı icin resim degismeyecektir boyle bir durumda ETag kullanılır. image TagHelper bunu bunyesinde bulundurur. boylece cachelenen dosyanın ismi degismesede icerigi degistigi takdirde etag uzerinden bu degisikligi fark ederek dosyayı serverdan tekrar talep eder.

  ```html
  <img src="~/ornek.jpg" asp-append-version="true"/>
  ```

  asp-append-version dedigimizde ETag yapılanması otomatik olarak saglanmıs olacaktır

- **Partial TagHelper**

  HtmlHelperdaki gibi Partial olarak calisir.

  ```html
  <partial name="~/Views/Product/Partials/ExamplePartial.cshtml"/>
  ```

- **Remove TagHelper**

  *Sayfa seviyesinde* Tag helper kullanmadıgımız sayfada ,  tag helperı silmek icin

  ```c#
  @removeTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

- *Tag Seviyesinde* Tag helper kullanmadıgımız zaman !(Ünlem isareti yeterlidir)

  ```html
  <form asp-action="Index"><!form> //burada tag helper gecerlidir
  <!form asp-action="Index"><!form> //burada tag helper gecerli degildir
  ```



#### Custom TagHelper

- Tekrar eden calısmaları TagHelper kullanarak cozebiliriz.

- TagHelperlari HtmlHelperlar gibi extension olarak gelistirmiyoruz. TagHelperlar birer sınıftırlar tag helper olusturmak icin bir sınıf olusturmak gerekiyor.

- 

Örnegin

Email adlı tag helperimi olusturdugumda bunu view tarafında

```html
@addTagHelper *, OrnekMvc //projemdeki butun tag helperları getir, EmailTagHelper kullanabilmek icin

<email></email>
```

**TagHelperKullanımı**

- TagHelper sınıflarının adları aslında tagHelper kendisinin adlarıdır.Olusturulurken sonuna TagHelper koymak guzeldir

  - Sınıf adlarımızda buyuk harfler varsa bunlarıda kuculdur ve arasında -(tire) isareti koyar

    Ornegin : CustomTextBox view tarafında <custom-text-box> olur

  - Yazdıgımız TagHelperin adını ozellestirmek icin [HtmlTargetElement("TagHelperAdi")]

- Bir sınıfın tag helper olması icin TagHelper  sınfından turemis olması gerekmektedir.
- Yeni olusturulan tagHelperi kullanmak icin  yada tagHelperda bir degisiklik yapıldıgı zaman uygulamayı bir kere derlemek gerekir
- Olusturdugumuz tagHelperları kutuphanelerini eklememiz gerekiyor
- Yazılan taghelperin calısması icin *Process* metodunun override edilmesi gerekiyor. Process metodunda
  -  *context* parametresi ilgili taghelperi getirmektedir.Attributes, uniqueid vs..
  -  *output* parametresi ise ilgili attributun cıktısını bizlere vermektedir.
  - output.TagName = "a" (a tagi) cıktı olarak taghelperin ne verecegini bildiriyoruz
  - output.Attributes.Add(name, deger) = Attributesler ekyebiliriz
  - output.Content.Append() - ilgili nesnenin icerigini verebiliyoruz
- tag icerisine yazılan degerler (deneme="abc") yazdıgımız taghelper classının icerisine property olarak yazılırsa direk olarak propertlere aktarılır (public string deneme {get; set;})

Ornek

```c#
public class EmailTagHelper : TagHelper
{
    public string Mail { get; set; }
    public string Display { get; set; }
    public override void Process(TagHelperContext context, TagHelperOutput output)
    {
        output.TagName = "a";
        output.Attributes.Add("href", $"mailto:{Mail}");
        output.Content.Append(Display);
    }
}
```

Kullanımı

```html
@addTagHelper *, WebApplication1 // Uygulama adı icerisinde tum taghelperları getir istersek global olarak import.cs den yapabiliriz

<email mail="malitutuncu@gmail.com" display="M.Ali'nin maili" ></email>
```



### Model Binding

- Bir viewde controller girmezssek o viewin bulundugu controlu baz alır
- Bir web uygulamasında form tetiklendiginde inputların hepsinin degeri hedef endpointe gonderilir
- View tarafında input adına verdigim degeri controllerda post metodunda parametre adı olarak input adına verdigim degeri verirsem otomatik yakalar(veri alma yontemlerinden birisi)
- form icerisinde inputların name leri ile onu karsılayacak olan contoller icerisindeki post metodundaki parametre class ise ve bu class icerisinde property isimleri inputların name leri ile aynı ise bunlar otomatik olarak bind edilir
- input taglarıne isim vermek yerine view tarafında bir model tutarsam asp-for ile bu modeldeki propertyleri baglarsam ve onu karsılayan post metodunda bu modeli parametre olarak alırsam otomatik olarak bind edilir.
- Controllerde Get metodunda bos bir view donersem sayfa daki model otomatik create edilir ve post edildiginde degerleri elde ederiz. eger get metodunda bir modeli create edersem nesnelere o baglanır



### Kullanıcıdan veri alma yontemleri

- En güzeli model-binding ile degerleri almaktır

- **IFormCollection** : Controllerde post metodumda IFormCollection u parametre olarak gecersem form icerisindeki tum inputların degerlerine ulasabilirim

  ```c#
  public IActionResult CreateUser(IFormCollection datas)
  {
      var value1 = datas["txtValue1"].ToString(); //input name = txtValue1
      return View(;
  }
  ```

- **QueryString :** Guvenlik gerektirmeyen bilgilerin url uzerinden tasınmasıdır. requestin turu onemli değildir.

  ```html
  https://localhost:44338/product/listele?sayfa=2
  ```

  ```c#
  public IActionResult Liste(string sayfa)
  {
      //diger baska bir sekilde
      var deger = Request.Query["sayfa"].ToString()
  	....
  }
  ```

  Otomatik olarak verileri alırım
  
- **Route Parameter :** startup icersinde rotalar da parametreler belirleyebiliyorum. rotalardan custom parametrelerde belirleyebiliyorum.

- Query stringe gore bir nebze daha guvenlidir. QueryStringde : /user?name=mehmet   Router Parametre : /user/mehmet

- Default olarak Controller/Action/id olarak tanımlıdır.

  ```c#
  // https://localhost:44338/product/listele/2 bu sekilde bir istek yapıldıgında otomatik olarak yakalar
  public IActionResult Listele(string id)
  {
  ....
  }
  ```

- Startup.cs de custom route olusturabiliriz.

  ```c#
  public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
  {
      ...
      app.UseEndpoints(endpoints =>
      {
  		endpoints.MapControllerRoute("CustomRoute","{controller=Home}/{action=index}/{deger1}/{deger2}/{id}")
      });
  }
  ```

  ```c#
  // https://localhost:44338/product/listele/ahmet/mehmet/2 bu sekilde bir istek yapıldıgında otomatik olarak yakalar. Url basvurusunda sıralama onemlidir. ancak controllerdaki metodda onemli degildir
  public IActionResult Listele(string id, string deger1, string deger2)
  {
   //burada otomatik olarak binding islemi yapılır
   //id = 2 , deger1 = ahmet, deger2 = mehmet degerini alır
  }
  
  //aynı sekilde istek yapıldıgında bunu bir class olarakta karsılayabiliriz. classın property isimleri ile url de tanımlı olan degiskenlerle aynı olmalıdır. buradada otomatik bindig islemi yapılır
  public class Data
  {
      public int id {get; set;}
      public string Deger1 {get; set;}
      public string Deger2 {get; set;}
  }
  public IActionResult Listele(Data data)
  {
  	...
  }
  ```

  Bu degerleri tag helperlarla da yollayabiliriz. ayrıca rotada olmayan datalarda query string olarak gonderilir

  ```html
  <a asp-action="Listele" asp-controller="Product" asp-route-deger1="ahmet" asp-route-deger2="mehmet" asp-route-id="5" asp-route-x="x"></a>
  ```

- **Header :** Header dan da veriyi alabilirim. Header da türkce, arapca, japonca vs kullanılmaz sadece latin alfabesinde olanlar kullanılır

- **Ajax :** ajax ile bir data post edildiginde conrollerde metodda parametre olarak gectigimizde mvc mimarisi datayı otomatik eslestirir.



### Validation

- Client side validation, server side validation

- if else ile yapılan validasyon kuralları çöp kod yazmamıza sebep olur

- **Data Annotation ile validasyon**

  ```c#
  public class Movie
  {
      [Required]
      [StringLength(100)]
      public string Title { get; set; }
  }
  ```

  Controllerda

  ```c#
    if (!ModelState.IsValid)    
    {        
    	return View();   
    }
  ```

  Viewde input nesnesinin altına asp-validation-for tagıyla sapan eklersek otomatik onun altında yazdıracaktır

  ```html
  <span asp-validation-for="Title"></span>
  ```

  tum hataları beraber gostermek icin

  ```html
  <div asp-validation-summary="All">
      
  </div>
  ```

- **ModelMetadataType ile Validation**

  Class icerisinde data validationları tutmak okunaklı azaltır, solide uygun degil. Bunun icin ilgili nesneye bir metada sınıfı olusturup validation ları orada tanımlayabiliriz. boylelikle dtoyu yada entityi sadelestirmis oluyoruz

  ```c#
  public class MovieMetaData
  {
      [Required]
      [StringLength(100)]
      public string Title { get; set; }
  }
  ```

  Daha sonra sınıfımıza gelip ModelMetadataType attributu ile metadasını bildiriyoruz

  ```c#
  [ModelMetadataType(typeof(MovieMetaData))]
  public class Movie
  {
      public string Title { get; set; }
  }
  ```

- **Fluent Validation**



### Serverdaki Validationlari dinamik olarak client tabanlı olarak uygulamak

Gerekli olanlar

- jquery
- jquery-validate
- jquery-validation-unobtrusive

Bunları projeme import ediyorum. binding kullandıgım yerlerde otomatik olarak validation lar tasınıyor



### Layout

- Tüm sayfalarda ortak olan kullanımları bir merkeze alma mantıgıdır.

- Views klasoru altında Shared klasorunda _Layout.cshtml adında bulunur

- @RenderBody() -> degiskenlik gosteren alandır. content - yani sayfalarımız

- RenderSection layoutta parca olarak calıstırılır. viewde gonderdigimde layouttaki sectionun tanımlandıgı yere yerlestirilir

- sayfada harici bir layout kullanırken  belirtebiliriz

  ```c#
  {
     Layout = null;
  }
  ```

  

#### _ViewStart

- Tum viewlerde kullanılması gereken ortak calısmaların yapıldıgı viewdir.

- Views klasorunde _ViewStart.cshtml olarak olusturulması gerekir
- Layout tanımlaması burada yapılır

#### _ViewImports

- razor sayfaları icin namespacesler, kutuphaneler, taghelperlar vs tanımlanır. heryerde gecerli olur
- Views klasorunde _ViewImports.cshtml olarak bulunması gerekir



### Partial View

- Moduler tasarımda her parcanın ayrı ayrı tasarlanıp ihtiyac duydugunda cagrılmasıdır.

- Partial viewlere _ ile baslamak guzeldir

- Ozunde bir view dosyasıdır

- Kullanımı

  ```c#
  Html.PartialView(partial_viewin_dizini)
  ```

  yada

  ```c#
  <partial name="partial_view_dizini"/>
  ```

- **Partial View Veri Gonderme**

  - Bir view e gonderdigim veriler icerisinde kullandıgı partial viewe otomatik tasınır.
  - verinin tamamını kullanacaksam gelen veriyi aynı tipte model olarak karsılamam yeterli
  - Eger modelin icinde bir parcayı partial viewe gondereceksem sayfaya farklı bir sekilde o veriyi tasıyıp partial viewe modelini verebilirim. boylelikle tum sayfanın degil parcasını model olarak partial viewe gecirrim

  ```c#
  <partial name="partial_view_dizini" model=@ViewBag.Data/>
  ```

  - Partial viewden *RenderSectiona* herhangi bir deger gonderilemez



### View Component

- Partial viewe veri controllerdan gelirken, View component sadece model den veriyi alıp kullanabilir.

- Partialda ihtiyacı olan dataları Controller uzerinden sagladıgı icin maliyeti arttırır, controlleri yormaktadır. 

- ViewComponentte kendi dahilinde veriyi ceker, kendi cs dosyasından dataları elde eder. boylece boylece controllerdaki maliyeti ortadan kaldırır

- ViewComponentin bir kısmı .cshtml(gorsel) bir kısmı cs(backend) dosyasıdır.

- ViewComponents klasoru bunun icin uygundur. isimlendirmede sonuna ViewComponent demek guzeldir.

- ViewComponent olusturmak icin 

  - bir class olusturuyoruz bunu ViewComponentten kalıtıyoruz

  - Viewcomponent render edildiginde icerisinde calısmasını istedigimiz kodları Invoke metodu olusturmalı bunun icerisine yerlestirmeliyiz. Donus tipi IViewComponentResult olmalıdır. Invoke metodunda veri tabnına baglanıp datayı alırız. burada dependency injection calısabilir.

    ```c#
  public class PersonelViewComponent : ViewComponent
    {
      public IViewComponentResult Invoke()
        {
            List<Personel> datas = new List<Personel>{
                new Personel {Adi= "Ahmet", Soyadi="aaaa"},
                new Personel {Adi="Mehmet", Soyadi="bbb"}
            }
            return View(datas)
         }
    }
    ```
  
    
  
  - **Viewi ise** 
  
    - *1.olarak* : O anda kullanıldıgı klasorun altında Components die bir klasor arar. Onun altındada hangi viewComponentse o isim altında bir klasor arar ve herhangi bir view ismi belirtilmediyse Default.cshtml dosyasını arar. Kullanılan her sayfanın altına Components/ViewComponentAdi/Default.cshtml olması gerekiyor
  
      ```c#
      -Home
      	-Components
      		-Personeller
      			-Default.cshtml
      ```
      
    - 2.olarak Shared altında Components klasoru altında ViewComponentin adına denk gelen klasoru arar. isim belirtilmedigi takdirde Default.cshtml arar
    
      ```
      -Shared
      	-Components
      		-Personeller
      			-Default.cshtml
      ```
    
    - ```html
      @model List<Personel>
      
      @foreach(var personel in Model)
      {
      	<p>
              @personel.Adi - @personel.Soyadi
          </p>    
      }
      ```
  
- View componenti kullanmak icin

  ```c#
  @await Component.InvokeAsync("Personeller")
  ```

- ViewComponent Gelen Requestleri karsılayamaz

- ViewComponente Render edilirken deger gonderilebilir

  ```c#
  @await Component.InvokeAsync("Personeller", new { id = 5 })
  ```

  - View Component direk bunu parametre olarak yakalar

    ```c#
    public class PersonelViewComponent : ViewComponent
    {
        public IViewComponentResult Invoke(int id)
        {
    		....
        }
    }
    ```

- ViewComponentin bir view component olmadıgını bildirmek icin *NonViewComponent* attribute ile isaretlenir



### Route Yapılanması

- Route : Gelen istegin hani rotaya ggidecegini belirleyen sablayonlardır

- **MapDefaultControllerRoute** : Mvc mimarisinde default olan routle eslestirir.(controller/action-id)

- **MapControllerRoute :** Custom rotalar olusturmamızı saglar.

  - Her custom route un uniq bir adı olur

    ```c#
    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllerRoute(
            name: "default2",
            pattern: "{controller=Home}/{action=Index}/{id?}");
    });
    ```
    ```c#
    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllerRoute(
            name: "default2",
            pattern: "anasayfa",
            new {controller="Home", action="Index"});
    });
    // /anasayfa die bir rota olusturdum burada anasayfa ya giderse Home controllerinde Index actionu calısır.
    ```

- Birden cok custom route oldugunda en genel olan startpup dosyasında en asagıda olması gerekir

- Bir rota olustururken mvc bakar benım olusturacagım rota ya en uygun custom rota sablonu hangisiyse onu uygular.

- **Route Constraints** rotadaki degiskenlerin tiplerini sınırlandırma islemine denir.

  - Rota sablonunda tipini belirtebilirim. yazılmadıgın da string olarak kabul edilir

    ```c#
    {id: int?} //int, bool, datetime,decimal, double,float, guid olabilir
    ```

  - kac karakter olması gerektigini belirleyebiliyorum, maxlength, min, max kullanılabilir

    ```c#
    {id:length(11)}
    ```

- **Custom Contrait olusturma**

  - Clasımız IRouteContrait den implement edilmesi gerekiyor

    ```c#
    public class CustomConstraint : IRouteConstraint
    {
    	public bool Match(....)
        {
            return true; //herseyi kabul eder
        }
    }
    ```

  - Ayarlanması icin startup da bilidiriyorum

    ```c#
    services.Configure<RouteOptions>(options => options.ConstraintMap.Add("custom",typeof(CustomConstraint)))
    ```

    Kullanımı

    ```c#
    {id:custom}
    ```

- **Custom Route Handle :** Herhangi bir belirlenmis rotanın semasının controllerdan degilde baska sekilde karsılanıp responsunun donulmesidir

  - Genel gecer (crud, authentication vs) islemlerde buna gerek yoktur. spesifik islemlerde olusturulur. Map fonksiyonuna ismini ve ikinci parametresi bir delegate alır baktıgımız async olan void donen parametre olarak context olan bir delegattir.

  - Not : Delegate lerde ismi haric metod imzası aynı olmalıdır

    ```c#
    public class ExampleHandler
    {
    	public RequestDelegate Handler()
        {
            //async olan void donen context parametresi alan bir metod
            return async context => 
            {
               await context.Response.WriteAsync("merhaba");
            }
        }
    }
    ```

  - Stratup da kullanımı

    ```c#
    endpoint.Map("example", new ExampleHandler().Handler() )
    ```

  - **Custom Route ile Resim Boyutlandırma**

    - Örnek olarak serverda wwwroot altında bulunan bir resmi boyutlandırıyoruz. resmi istediginde orjinal boyutuyla , width - height belirtirsede o boyutlarda veriyoruz.

      ```c#
      public class ImageHandler
      {
      	public RequestDelegate Handler(string filePath)
          {
              return async context => 
              {
                 FileInfo fileInfo = new FileInfo($"{filePath}\\{context.Request.RouteValues["imageName"].ToString()}");
                  using MagickImage magick = new MagickImage(fileInfo);
                  
                  int width = magick.Width, height = magick.Height;
                  
                  
                  if (!string.IsNullOrEmpty(c.Request.Query["w"].ToString()))
                      width =int.Parse(c.Request.Query["w"].ToString());
                  
                  if (!string.IsNullOrEmpty(c.Request.Query["h"].ToString()))
                      height =int.Parse(c.Request.Query["h"].ToString());
                  
                  magick.Resize(width,height);
                  
                  var buffer = magick.ToByteArray();
                  context.Response.Clear();
                  context.Response.ContentType = string.Concat("image/", fileInfo.Extension.Replace(".",""));
                  
                  await context.Response.Body.WriteAsync(buffer,0,buffer.Length);
                  await context.Response.WriteAsync(filePath)
              }
          }
      }
      ```

    - startupta kullanımı

      ```c#
      endpoint.Map("image/{imageName}", new ImageHandler().Handler(env.WebRootPath) )
      //env.WebRootPath bana wwwroot klasorunun pathini verir
      ```

      https://localhost:5001\image/tavuk.png

      https://localhost:5001\image/tavuk.png?w=500&h=250

    

### Middleware

- Gelen islemin request - response arasına girip islem yapmaya middleware denir

- Middware sarmal bir sekilde tetiklenir

- bir middle war bitince digerine gecmez. middleware logic ini isletir daha sonrakine devreder  boyle en sondaki middleware isi bittiginde geriye dogru birmeye baslar. 

- ornegin Mid1 den sonra Mid2 ondan sonra Mid3 islenir bu durumda

  request - Mid1, Mid2, Mid3  - response

  Mid1 logic isler - Mid2 ye gecer - Mid2 logic isler - Mid 3 e gecer Mid 3 logic isler , isi biter doner, Mid2 ye ugrar bitirir, Mid1 e ugrar en sonunda response doner

- Startu configure metodu icerisinde middleware lar cagrılır ve tetiklenme sırası onemlidir.

- asp.net core mimarisinde middlewarelar Use ile baslar. Bir middware adında Use ile baslamak guzeldir.

- **Hazır Middlewareler** : cekirdekte olup biz kullandıgımızda devreye girecek icini doldurdugumuz middlewareler.

  - **Run Middleware i :** 

    - Run fonksiyonu kendisinden sonra gelen middleware i tetiklemez. 

    - Akısı kesecektir.buna Short Circuit (Kısa devre denir).

    - O middlewareden itibaren geri donmeye baslar.

      ```c#
      ...
      --burada middleware ler var
      app.Run(async c => {await c.Response.WriteAsync("middleware1") });
      app.Run(async c => {await c.Response.WriteAsync("middleware2") });
      asp.UseMiddleware3;
      asp.UseMiddleware3;
      ```

      Burada "middleware1" responsu doner ve biter

  - **Use Middleware i**

    - Devreye girdikten sonra sıradaki middware cagıran, normal middware islevi bittikten sonra geriye donup devam eden bir yapıya sahiptir.

      ```c#
      app.Use(async (context, next)) =>
      {
          //Burada next kendisinden sonraki middleware onu arada calıstırıyor
      	Console.WriteLine("Start Use Middleware");
          await next.Invoke();
        	Console.WriteLine("Stop Use Middleware");
      }
      
      app.Run(async c => 
      {
          Console.WriteLine("Start Run Middleware");        
      });
      
      app.UseHttpsRedirection();
      
      ```

      Burada ekrana yazar

      ```c#
      Start Use Middleware
      Start Run Middleware
      Stop Use Middleware
      ```

  - **Map Middleware i**

    - Gelen requestin pathine gore middleware calıstırmak istersem Map middleware i kullanırım

      ```c#
      app.Map("/weatherforecast", builder =>
      {
         builder.Run(async c => await c.Response.WriteAsync("Run middlewari tetiklendi"))         
      });
      
      app.Map("/home", builder =>
      {
      	builder.Use(async (context, next) =>
      	{
              Console.WriteLine("Start Use Middleware");
              await next.Invoke();
              Console.WriteLine("Stop Use Middleware");
          });	            
      });
      
      //Burada /weatherforecast urline ve /home urline geldiginde farklı middlewareler calıstırdım.
      ```

  - **MapWhen Middleware i** 

    - Map metodu ile sadece requestin yapıldıgı patha gore filtreleme yapılırken MapWhen metodu ile gelen requestin herhangi bir ozelligine gore filtreleme islemi gerceklestirilebilir.

      ```c#
      app.MapWhen(c => c.Request.Method == "GET", builder =>
      {
      	builder.Use(async (context, next)) =>
      	{
      		 Console.WriteLine("Start Use Middleware");
      		 await next.Invoke();
      		 Console.WriteLine("Start Use Middleware");
      	}
      })
      ```

- **Custom Middleware olusturma**

  - Custom middleware yazdıgımda bunu extension olarak tasarlayıp IApplicationBuilder a yazabilirim. boylelikle net core mimarisine uygun olur.

  - Authentication, rol yonetimi vs ozel custom middleware lere ihtiyacım olabilir.

  - Örnegin

    ```c#
    public class HelloMiddleware
    {
    	RequestDelegate _next;
    	// buradaki next gelecek middleware bunu mimari otomatik olarak dolduracaktır
    	public HelloMiddleware(RequestDelegate next)
    	{
    		_next = next;
    	}
    	
    	public async Task Invoke(HttpContext httpContext)
    	{
    		Console.WriteLine("Start CustomMiddleware");
    		await _next.Invoke(httpContext);
    		Console.WriteLine("Finish CustomMiddleware");
    	}
    }
    ```

    Yazdıgım middleware kullanan IApplicationBuildera Extension olarak yazacagım

    ```c#
    public static class Extensions
    {
    	public static IApplicationBuilder UseHello(this IApplicationBuilder builder)
    	{
    		return builder.UseMiddleware<HelloMiddleware>();
    	}
    }
    ```

    Sonra bunu startup.cs de kullanacagım

    ```c#
    public void Configure(IApplicationBuilder app, IWebHostenvironment env)
    {
    ....
    app.UseHello();
    ....
    }
    ```



### Dependency Injection

- Bagımlılık olusturacak parcaların ayrılıp, sınıflara bunun enjekte edilmesidir.
- Bagımlılıkları soyutlamak demektir(Interface ile)

### Inversion of Control

- Bazı durumlarda cok fazla dependency injection kodu yazılması gerekecektir, bu kod karmasasına neden olacagı icin bunu otomatiklestiren bir yapı kurmamız gerekir. bu yapıya Inversion Of Control (Ioc) denir.

- **Asp.Net Core da icerisinde bulunan Ioc container**

  - İcerisine koyulacak degerleri/nesneleri uc farklı davranısla alabilmektedir.
  - **Singleton :** Uygulama bazlı tekil nesne olusur. Tum taleplere o nesneyi gonderir.
  - **Scoped :** Her request basına bir nesne uretir ve o request pipeline nındaki tum isteklere gonderir.
  - **Transient :** Her requestin her talebine karsılık bir nesne uretir ve gonderir.

- **Build in IService Collection**

  - service collection icerisine istedigim nesneleri olarak ekleyip sonra provider ile geri o nesneleri geri elde edebiliyorum

  - **Add fonksiyonu**

    ConsoleLog  clasım var ve console log tutuyor.ConsoleLog  u her kullandıgımda new lemek yerine IserviceCollectiona ekleyebirim. 

    Cok effectif degildir

    ```c#
    IServiceCollection services = new ServiceCollection(); // build in Ioc
    services.Add(new ServiceDescriptor(typeof(ConsoleLog ),new ConsoleLog ()));
    //Burada bu tipini ve instance ini verdim. daha sonra bu tipte bana bir nesne ver dedigimde instance ni olusturup bana geri verecek
    //IServiceCollection startup dosyasında ConfigureServices icerisinde otomatik olarak enjectedir.
    ```

    Cagırmak icin

    ```c#
    ServiceProvider provider = services.BuildServiceProvider(); //provider
    provider.GerService<ConsoleLog>();
    ```

    Not: Add fonksiyonu ile eklediklerimiz default olarak singleton dır. Bunu degistirmek icin interface ler ile yapılandırmam gerek.

  - Singleton, Scoped, Transiet : 

    ```c#
    //Vermiş oldugumuz neyne neyse ondan bir tane nesne  olusturur.
    services.AddSingleton<ConsoleLog>();
    services.AddScoped<ConsoleLog>();
    services.AddTransiet<ConsoleLog>();
    
    //Eklenen nesnenin constructer i parametreli ise  söyle bir kullanım gereklidir
    services.AddSingleton<ConsoleLog>(p => new Console(5));
    services.AddScoped<ConsoleLog>(p => new Console(5));
    services.AddTransiet<ConsoleLog>(p => new Console(5));
    ```

- **Nesne Bildirimlerinde Uyulması Gereken Abstraction Mantıgı**

  - Nesneleri bir Interface ye tabi tutarım

  - Ornegin ConsoleLog sınıfım ve TextLog sınıfım ILog interfacesini implemente ettigimde

    ```c#
    //Constructor parametresiz ise
    services.AddScoped<ILog, ConsoleLog>();
    //Konsola log almasını istemiyor texte log almasını istiyorsam
    //services.AddScoped<ILog, TextLog>(); demem yeterli
    
    //Constructor parametreli ise
    services.AddScoped<ILog>(p => new ConsoleLog(5));
    ```

    Böylece tek yerden bir degisimle butun projede loglama sistemini degistirebilirim..Artık sınıflarımda ILog interfacesini talep edebilirim .

    - **Constructordan talep etme**

      ```c#
      public OrnekController : Controller
      {
      	private readonly ILog _log;
      	
      	public OrnekController(ILog log) // Burada Ioc nesnemi doldurur
      	{
      		_log = log;
      	}
      }
      ```

    - **Action(method) bazlı talep etme**

      ```c#
      public OrnekController : Controller
      {
      	private readonly ILog _log;
      	
          //Burada degeri sen servicesdan yani containerdan getir
       	public IActionResult Index([FromServices]ILog log)
          {
              
          }
      }
      ```

    - **Viewde nesne talep etme**

      ```c#
      @inject ILog log
      ```

      

## Area

Area : bir web uygulamasnda, farklı işlevsellikleri ayırmak için kullanılan özelliktir. Bu farklı işlevsellikleri için farklı katmanda, bir route ayarlamamızı sağlayan o işlevselligi ayrıca yönetmemizi saglayan yapılanmadır.

- Herbir area icerisinde View,Controller,Controller ve model katmanı barındırabilir
- Area yönetim panellerinde , işlevsel modullerde, uygulamada mantıksal olarak ayrılabilen ust duzey islevsel bilesenlerde kullanılabilir.

Area olusturmak icin Areas adında kok dizine bir klasor acıp sag tıklarım Add-Area -Mvc area diyerek ekliyorum

- Area icerisindeki controller area attribute u ile isaretlenmelidir.(Ana dizindeki controllerle area icerisindeki controller aynı isme sahip olabilir. boyle olursa uygulama ayaga kalktıgında hangi controllere gidecek bilemez, bunu engellemek icin)

  ```c#
  [Area("admin")]
  public class HomeController : Controller
{
  	public IActionResult Index()
      {
          return View();
      }
  }
  
  //ornegin buradaki action a ulasmak icin localhost:5000/admin/home/index seklinde
  ```
  
- **MapcontrollerRoute**

  Her bir area icerisindeki controller lara erisim icin farklı bir route saglamaktadır.Bu route ların tarafımızca tasarlanması gerekiyor. Default area rotası belirlememizi saglar.

  ```c#
  endpoints.MapControllerRoute(
  	name : "areaDefault",
  	pattern : "{area:exists}/{controller=Home}/{action=Index}/{id?}" 
  )
  ```

  burada area_adi/controller_adi/action_adi/id seklinde default bir route belirledim.

- **MapAreaControllerRoute**

  MapControllerRoute : Default area rotası belirlememizi saglar.

  MapAreaControllerRouter ise bir area'ya ait/özel rota belirlememizi saglar.

  ```c#
  app.useEndpoints(endpoints => {
     endpoints.MapAreaControllerRoute(
     		name: "AdminArea",
     		areaName: "admin",
     		pattern: "admin/{controller=Home}/{action=Index}/{id?}"
     )
  })
      
  //Burada admin area adina sahip [Area("admin")] tum contollerların ozel olarak böyle route verebilirim.
  ```
```
  
- **Arealar arası bağlantı olusturma**

  HtmlHelperlarda 

  ```c#
  @Html.ActionLink("Yönetim", "Index", "Home", new {area = "yonetim"})
  @Url.Action("Index","Controller",new {area = "yonetim"})
```

  TagHelperlarda

  ```c#
  <a asp-action="Index" asp-controller="Home" asp-area="yonetim">Yönetim</a>
  ```

- **Arealar arası veri taşıma**

  örnegin admin ve fatura area larım var. admin areasında home contolleri index metodundan fatura areası home controlleri index metoduna  veri tasımak istiyorsam tempData kullanabilirim

  Admin area

  ```c#
  [Area("admin")]
  public class HomeController : Controller
  {
  	public IActionResult Index()
  	{
  		TempData["data"] = "deneme 1 2 3"	;
  		return RedirectToAction("Index","Home",new {area = "fatura"})
  	}
  }
  ```

  ```c#
  [Area("admin")]
  public class HomeController : Controller
  {
  	public IActionResult Index()
  	{
  		var data = TempData["data"];
  		return View();
  	}
  }
  ```



### ViewModel

- Kullanıcıya sunulacak verinin view e bekledigi sekilde tasarlanmıs halidir.
- Veriyi gorunumde anlamlı hale getirir.
- İşlevsel fonksiyonlar barındırabilir.
- icerisinde bir veya birden cok DTO olabilir.
- DTO ya gore daha komplextir.
- PersonelCreateVM, PersonelListVM

### DTO

- Bir verinin transfer modellemesidir.(Genellikle veri tabanı nesnesi)
- Her hangi bir katmanda veri transferi icin kullanılabilir.Gorunum icin kullanılabilir.
- Herhangi bir  fonksiyonellik barındırmaz.
- Salt veriyi temsil eder.



### Sözleşme/Kontract

Backend dden bir dto gonderdigimizde client(react,vue,angular vs) gonderdigimiz data ya uygun bir model oluturur clientte buna sozlesme-kontract denilebilir.



### Data Dönüşümleri

- Manuel dönüştürme amele usulu

- **Implicit / bilinçsiz / gizli dönüşüm**

  ```c#
  public class Personel{
  	public int Id {get; set;}
  	public string Adi {get; set;}
  	public string Soyadi {get; set;}
  	
  	public static implicit operator PersonelCreateVm(Personel model)
  	{
  		return new PersonelCreateVM
  		{
  			Adi = model.Adi,
  			Soyadi = model.Soyadi
  		};
  	}
  }
  ```

  Kullanımı

  ```c#
  Personel personel = personelCreateVM;
  PersonelCreateVM vm = personel;
  ```

  

- **Explicit / açık/ bilinçli**

  ```c#
  public class Personel{
  	public int Id {get; set;}
  	public string Adi {get; set;}
  	public string Soyadi {get; set;}
  	
  	public static explicit operator PersonelCreateVm(Personel model)
  	{
  		return new PersonelCreateVM
  		{
  			Adi = model.Adi,
  			Soyadi = model.Soyadi
  		};
  	}
  }
  ```

  Kullanımı

  ```c#
  Personel p = (Personel)personelCreateVM;
  PersonelCreateVM p2 = (PersonelCreateVM)p;
  ```

- **Reflection ile**

  Reflection ilgili turun icerisinde ilgili memberlarında, fieldlarında degisiklik yapmaktır.
  
  Örnegin
  
  ```c#
  public static class TypeConversion
  {
  	public static TResult Conversion<T,TResult>(T Model) where TResult : class, new()
  	{
  		TResult result = new TResult();
          typeof(T).GetProperties().ToList().ForEach(p => {
              var propery = typeof(TResult).GetProperty(p.Name);
              property.SetValue(result, p.GetValue(model))
          });
          return result;
  	}
  }
  ```
  
   Kullanımı
  
  ```
  Personel p = TypeConverion.Conversion<PersonelCreateVM,Personel>(personelCreateVM);
  ```
  
- **AutoMapper ile**





### AppSettings.json

Asp.net core uygulamalarında yapılandırma araçlarından birisidir.

 eski asp.net te web.config in karsılıgı appSetting.json ve turevleri, global.asax ın karsılıgı ise startup dosyası

- appsettings.json | appsettings.{environment}.json
- secrets.json(secret manager tools)
- environment variables



- Uygulamayı ayaga kaldırdıgımızda appsetting.json ve turevlerinin icerikleri yani configurasyonlar onbellege kaydedilir.