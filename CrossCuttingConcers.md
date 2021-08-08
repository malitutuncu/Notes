# Cross Cutting Concerns

Uygulamayı dikine kesen durumlar diyebiliriz.

Örneğin : Validation , Cache,  Log, Performance , Autherization , Transaction birer ornektir.

Business içerisinde UrunEkle metodum oldugunu dusunursun bu metodun icerisinde validasyon kontrolü yaparsam solid e aykırı davranmıs olurum.

UrunEkle metodunun sadece Urun eklemesi gerekir. UrunEkle metodumun icerisinde validation yapmamam gerekli.

Bunlarıda solide uygun olması icin AOP(Aspect Oriented Programming) yaklaşımıyla yapabilirim. AOP Cross cutting concerns ler için kullanılır.

Cross cutting concernsler dışında da kullanılmamalıdır.

Örnek:

Fatura ve Fatura satırlarını kaydettigimizi dusunelim. Veri tabanında Fatura ve FaturaSatir diye tablolarımız mevcut. Fatura classımız FaturaSatir clasını liste olarak tutuyor.

Kurallara uymadıgımız zaman boyle bir durum karsılımıza cıkar

```c#
public void UrunEkle(Fatura Fatura)
{
​	/* 
	#-Validasyon Yap
    -CariId bos ise	 -> Cari seçmek zorundasınız
    -Satır sayısı 0 ise -> Faturaya satir girilmek zorundadır
    -Foreach FaturaSatir
    	-StokId bos ise -> satırlarda stok olmak zorundadır vb durumlar
  #-Transaction yonetimi yap
  	-FaturaEkle(icerisinde satırlar var)
  	-EvrakNoDegistir(Faturanın evrak numarasını takip ettiğimizi dusunelim)
  #-UrunEkle
  #-Cacheleme yap
  */
}
```

Kurallara uydugumuzda

```c#
[ValidasyonYap]
[CachelemeYap]
[TransactionYonet]
public void UrunEkle(Fatura Fatura)
{
  //Urun Ekle
}
```

Yani aop ile belirli islemleri bir operasyonun icerisine koyulması yerine basında ve sonunda calistirilması surecidir.

AOP araclarından birkacı Autofac,ninject vb dir.



# Validation

Validation kuralları icin ornek olarak FluentValidation kutuphanesinden yararlanacagız validasyonumuzu yazacagız ve sonra bunu aop yaklasımı ile projemizde kullanacagız.

Fluent validationu projemize ekledikten sonra

```c#
public class FaturaValidator : AbstractValidator<Fatura>
{
  
}
```