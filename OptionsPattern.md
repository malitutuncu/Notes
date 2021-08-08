“appsettings.json” içerisinde bilgileri dependency injection ile alabilmekteyiz bunun icin microsoft bize Option patternden yaralanıyoruz.

Asagidaki gibi bir appsetting.json dosyamız oldugunu dusunelim

```json
{
  "TokenSettings": {
    "Key": "Key123123123",
    "Issuer": "Issuer123123",
    "Audience": "Audience123123",
    "DurationInMinutes": 60
  },
  "AllowedHosts": "*"
}
```

Bunun icin TokenSettings e karsılık gelen bir sınıf olusturuyoruz

```c#
public class TokenSettings
{
    public string Key { get; set; }
    public string Issuer { get; set; }
    public string Audience { get; set; }
    public double DurationInMinutes { get; set; }
}
```
Class icerisindeki property isimleri ile json icerisindeki isimlerdirmeler aynı olmalıdır.

ve startup.cs icerisinde ConfigureServices icerisinde 

```c#
services.Configure<JWTSettings>(configuration.GetSection("JWTSettings"));
```

boylelikle appsettings icerisindeki verilerimi clasıma aktarmıs oldum

Not: 

Bunun icin Microsoft.Extensions.Options.Configuration kutuphanesine ihtiyac var



Bunun DI olarak gecebilirim artık

```c#
public class Example
{
  private readonly JWTSettings _jwtSettings;
  public Example(IOptions<JWTSettings> jwtSettings)
  {
    _jwtSettings = jwtSettings.Value;
  }
  
  

}
```

