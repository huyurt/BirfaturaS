#### 20 iş günü süren BirFatura stajımda, bana verilen görevler doğrultusunda kodladığım uygulamalar hakkında bilgilerin yer aldığı staj raporu (2018):
----

**İÇİNDEKİLER**

**KAYNAK KODDAN VERİ ELDE ETME (C#, Json, HtmlAgilityPack)**

**SUBDOMAIN TESPİT ETME (C#, HtmlAgilityPack)**

**BOT KULLANARAK WEB MADENCİLİĞİ (C#, Sqlite, Selenium)**

**BİRFATURA API TESTİ (C#, Rest, Json, ITextSharp)**

**KAYNAKLAR**

**EK – 1. Terimler**

**1.  KAYNAK KODDAN VERİ ELDE ETME**

**Amaç**

Bana verilen ilk görev, kuruluşun geliştirmiş olduğu Birfatura sanal mağazacılık
fatura entegrasyon sisteminde N11, GittiGidiyor ve Hepsiburada sanal mağazacılık
web sitelerinde satış yapan mağazaların profil sayfalarında ki mağaza performans
bilgilerini göstermek amacıyla, satıştaki bir ürününün url adresi üzerinden, o
ürünü satan mağazanın ismini elde edip, bu mağaza ismi üzerinden mağaza
profiline girerek müşterilerin mağazalara verdiği puanlara göre hesaplanan
mağaza performans bilgilerini elde etmek için .Net ortamında bir dinamik
bağlantı kitaplığı (dll) yazmam istendi. Bu kitaplık ile N11, GittiGidiyor veya
Hepsiburada sanal mağazacılık web sitelerinde mağazası olup, Birfatura
entegrasyon sistemini kullanan kişilerin anlık olarak performans bilgilerini
görüntüleyebilmesi amaçlandı.

![Mağaza Performan Akış Şeması.png](https://1.bp.blogspot.com/-tQ4CBBKKsyk/XM7bcLaGZRI/AAAAAAAAC4I/n32eHzdLFnc_7E6jCbAMEvA7128SXNrTwCLcBGAs/s1600/f324d0a9e792e0b4039a8da3ae726b60.png)

Şekil 3.1. Mağaza performans kitaplığının akış şeması

**Kullandığım Araçlar**

Bu uygulamayı geliştirirken, aşağıdaki araç ve kitaplıkları kullandım:

**Diller:** C\#, XPath, Json

**Geliştirme Ortamı:** Visual Studio 2017

**Kitaplıklar:** HtmlAgilityPack, Newtonsoft.Json

**Diğer:** Chrome web tarayıcısı, json2csharp.com

1.  **Uygulama**

Herhangi bir ürünün url adresi parametre olarak verildiğinde o web sayfasından
mağazanın profil sayfasını veren node bulunacak, bu node’dan elde edilen mağaza
adı parametre olarak verildiğinde mağazanın profil sayfasındaki mağaza
performans bilgileri bir liste yapısında saklanacak.

İlk olarak, Visual Studio 2017 de .Net Framework sınıf kitaplığı projesi
oluşturdum. Bu kitaplığı test edebilmek amacıyla aynı çözüm içerisinde .Net
Framework konsol uygulaması projesi oluşturdum. Bu iki projeyi birbirine
bağladım. Sonrasında, internette, web sayfasının kaynak kodunu alabileceğim bir
kitaplık aramaya başladım. Araştırmalarım sonucunda HtmlAgilityPack
kitaplığının, bunu yapabildiğini öğrendim. Sınıf kitaplığı projemde başvurulara
HtmlAgilityPack Nuget paketini ekledim. Daha sonra HtmlAgilityPack kitaplığını
nasıl kullanmam gerektiğini öğrenmek için kitaplığın resmi web sitesindeki
kullanım bilgilerini [2] inceledim.

Ürün url’ini parametre vererek HtmlWeb türünde değer döndüren bir metot ile web
sayfasının kaynak kodunu elde ettim.

HtmlWeb htmlWeb = new HtmlWeb();

return htmlWeb.Load(Url);

**N11 Mağaza Performansları**

Kaynak kodu elde ettikten sonra, istediğim bilgileri nasıl elde edeceğimi
öğrenmek için internette daha fazla araştırma yaptım [3]. Kaynak koddan,
mağazanın profiline girebileceğim url adresini bulunduran node’u aramam
gerekiyordu. Bunun için Chrome web tarayıcısından herhangi bir ürünün url’ine
girerek, mağaza isminin geçtiği yere sağ tıklayıp, ardından “İncele” ye bastım.
Buradaki a elementinin href özelliğini elde etmem gerekti. Bu değeri elde
edebilmem için a elementinin class veya id özelliğinin olması gerekiyordu. Bu
özellikleri olmadığı için, bu a elementini içeren class veya id özelliği olan
üst elemente gittim. class=”sallerTop” özelliği olan div elementini node olarak
seçtim. Node seçmek için div elementine sağ tıklayıp, XPath yolunu kopyaladım ve
üzerinde değişiklikler yaparak kullandım.

string XPathN11MagazaBul = "//div[\@class=\\"sallerTop\\"]";

foreach (HtmlNode node in kaynakKod.DocumentNode.SelectNodes(XPathN11MagazaBul))

{

>   magaza = node.SelectSingleNode(".//a").Attributes["href"].Value;

>   }

>   return magaza.Split('/').Last();

kodlarıyla string döndüren bir metot yazarak, mağazanın profiline girebilmemi
sağlayacak url adresini elde ettim.

![1](https://1.bp.blogspot.com/-mC9NAh5goVE/XM7baVgBuNI/AAAAAAAAC38/XAEaKXL4RbIQUot7uelzKLs1xDWY5VBrACLcBGAs/s1600/aad33d5c6a540098928579ff49b173e7.png)

Şekil 3.2. N11 web sitesinde mağaza profiline erişmek için kaynak koddan
aranacak node’un belirlenmesi

Mağaza ismini kullanarak, mağazanın profil sayfasına web tarayıcısı ile girdim.
Buradaki mağaza performanslarını elde edebilmem için aynı şekilde web sayfasının
kaynak kodundan seçmem gereken node’u belirledim. Bu node, mağaza performans
bilgilerini tablo halinde gösteren class="sellerReviews" özelliğine sahip div
elementi oldu.

![2](https://4.bp.blogspot.com/-PH9uZEcDxJ8/XM7bWcF3qrI/AAAAAAAAC3M/bdDVA2YDOjYIzTHl8J-zjPecKTiyV_tPQCLcBGAs/s1600/40a97cb4999e16d7f2eaebee7d6e69ef.png)

Şekil 3.3. N11 web sitesinde mağaza performanslarına erişmek için kaynak koddan
aranacak node’un belirlenmesi

Mağaza performanslarını, custom şeklinde bir list oluşturarak saklamaya karar
verdim. Bunun için ayrı bir sınıf dosyası oluşturdum.

public class MagazaPerformansListesi

{

>   public string Aciklama { get; set; }

>   public string Sonuc1 { get; set; }

>   public string Sonuc2 { get; set; }

>   public MagazaPerformansListesi(string Aciklama, string Sonuc1, string
>   Sonuc2)

>   {

>   this.Aciklama = Aciklama;

>   this.Sonuc1 = Sonuc1;

>   this.Sonuc2 = Sonuc2;

>   }

}

Daha sonra, seçtiğim node içindeki tablonun tr ve td elementlerindeki mağaza
performans verilerine erişebilmek için foreach ve for döngülerinden yararlanarak
bu bilgileri elde ettim. List için oluşturduğum nesneyi türeterek, bu bilgileri
listeye kaydettim.

public List\<MagazaPerformansListesi\> N11MagazaPerformansListesi { get; set; }

string[] xPath = { ".//tr[", "]/td[", "]" };

foreach (HtmlNode node in
kaynakKod.DocumentNode.SelectNodes("//div[\@class=\\"sellerReviews\\"]"))

{

>   for (int trIndeks = 2; trIndeks \<= 4; trIndeks++)

>   {

>   string aciklama = node.SelectSingleNode(xPath[0] + trIndeks + xPath[1] + 1 +
>   xPath[2]).InnerText.Trim();

>   string sonuc1 = node.SelectSingleNode(xPath[0] + trIndeks + xPath[1] + 2 +
>   xPath[2]).InnerText.Trim();

>   string sonuc2 = node.SelectSingleNode(xPath[0] + trIndeks + xPath[1] + 3 +
>   xPath[2]).InnerText.Trim();

>   MagazaPerformansListesi mList = new MagazaPerformansListesi(aciklama,
>   sonuc1, sonuc2);

>   N11MagazaPerformansListesi.Add(mList);

>   }

}

Bu şekilde N11 web sitesi için mağaza performans bilgilerini elde etmiş oldum.

**GittiGidiyor Mağaza Performansları**

GittiGidiyor sitesindeki mağazalar için, mağaza performans bilgilerine
ulaşabilmek adına herhangi bir ürünün sayfasına girdim ve mağaza profiline
erişebileceğim url adresini inceledim. Bu url, mağaza adının yazılı olduğu a
elementinin href özelliğinde bulunuyordu. a elementi herhangi bir class veya id
özelliğine sahip olmadığı için, a elementini içeren ve class="member-name"
özelliğine sahip span elementini node olarak seçtim.

![3](https://3.bp.blogspot.com/--7-3qUlHmdI/XM7bZXXisrI/AAAAAAAAC3w/PGmshGU50YcROG7pcBIJAFnk0lNpZ2regCLcBGAs/s1600/97cc8af6455afc2bc5e95b25ce4412f4.png)

Şekil 3.4. GittiGidiyor web sitesinde mağaza profiline erişmek için kaynak
koddan aranacak node’un belirlenmesi

N11 sitesinde mağaza profil url adresini bulduğum metot gibi,

string XPathGittiGidiyorMagazaBul = "//span[\@class=\\"member-name\\"]";

span elementinin XPath yolunu kullandım ve yine aynı şekilde foreach döngüsü
yardımıyla a elementinin href özelliğine eriştim. Bu şekilde mağaza profiline
ulaşabileceğim url adres bilgisine sahip oldum.

Mağaza profili url adres bilgisi ile, mağazanın profil sayfasına girdim. Yine
aynı şekilde mağaza performanslarının yazılı olduğu kısmı içeren
id="user-comment-rating-container" özelliğine sahip div elementini seçtim.

![4](https://2.bp.blogspot.com/-k_6OQBiftX8/XM7bb0Cwu_I/AAAAAAAAC4E/OgeEokXjJx4cfrB52oIDcMHp0s5UtnpFgCLcBGAs/s1600/e3f67a958589b4dffcf30ee6b74fdb92.png)

Şekil 3.5. GittiGidiyor web sitesinde mağaza performanslarına erişmek için
kaynak koddan aranacak node’un belirlenmesi

GittiGidiyor sitesinde, mağaza performans bilgilerinin her biri tablo yerine div
elementlerinde gösteriliyordu.

*Karşılaştığım zorluklar*

N11 web sitesindekine benzer şekilde id="user-comment-rating-container"
özelliğine sahip div elementini foreach döngüsüyle, içindeki div elementlerine
ulaşmaya çalıştığımda geriye NullReferenceException hatası döndü. Nedenini
anlamak için HtmlAgility’nin parse ettiği kaynak kodda ki div elementinin
içeriğini incelediğimde,

\<div id = "user-comment-rating-container" \>

>   \< div class="mt10 mrb10 loading-container"\>

>   \<img src = "//st.gittigidiyor.net/rsrc/mobil-express/img/preloader.gif"
>   width="105" height="25" align="center"\>

>   \</div\>

\</div\>

şeklinde olduğunu gördüm. div elementinin içinde, mağaza performanslarının
gösterildiği elementlerin yerine img elementi bulunuyordu. Nedenini bana
rehberlik yapan bilgisayar mühendisine sordum. Performans bilgileri isteğine,
cevabın web sayfası yüklendikten sonra gelmesinden kaynaklandığını ve bunun
WebSocket protokolü aracılığı ile gerçekleştiğini öğrendim.

Performans bilgilerini elde etme yolunun, web tarayıcısının gelen frameleri
gösterdiği Network sekmesinden bulunabileceğini öğrendim. Network sekmesinde
userCommentRatingAjax.php?memberId=1005504274 şeklinde web sayfasına mağazanın
id bilgisi (memberId) Http isteği get metodu ile gönderilerek, mağaza performans
bilgilerinin alınabildiğini gördüm.

![5](https://2.bp.blogspot.com/-Vv1V_cqxznk/XM7bbXsZEqI/AAAAAAAAC4A/5bq7vvePqYYHiMMKowjasRgMX_B-5tM3QCLcBGAs/s1600/e207968b1830902d4d75de5011756ada.png)

Şekil 3.6. GittiGidiyor web sitesinde mağaza performanslarına erişmek için
Network sekmesindeki framelerden gerekli dosyanın bulunması

Dosyayı açtığımda karşıma, mağaza bilgilerinin yazdığı bir Json verisi çıktı.
Mağaza performans bilgilerine erişmek için bu url adresini kullanmam gerektiği
için mağaza profilinin kaynak kodundan, mağaza id’sinin yazdığı
id="user-info-id" özelliğine sahip input elementinden value özelliğinin değerini
elde ederek, mağazanın id bilgisine sahip oldum.

string magazaId =
kaynakKod.GetElementbyId("user-info-id").GetAttributeValue("value", "");

![6](https://4.bp.blogspot.com/-yNYrGgnWToE/XM7bYVcTrwI/AAAAAAAAC3o/8pLFnHK49jAAzqIXPL0ZFY1Ya57dV23sACLcBGAs/s1600/89e60bb1b2745ec4fcdc98690f5426b3.png)

Şekil 3.7. GittiGidiyor web sitesi mağaza performansları Json verisi

Daha sonra mağaza performans bilgilerinin yazdığı Json yapısını nasıl kullanmam
gerektiğini öğrenmek için internette araştırma yaptım. Json’u parse edebilmek
için Newtonsoft.Json kitaplığını kullanmam gerektiğini öğrendim. Nuget paketi
olarak bu kitaplığı yükledim. Resmi web sitesindeki kullanım bilgilerini
inceledim [4]. Json verisini deserialize yaparak bir sınıfa atamam gerektiğini
öğrendim. Rehberlik yapan bilgisayar mühendisinden Json verisini sınıf yapısına
dönüştürebilen web sitelerinin olduğunu öğrendim ve json2csharp [5] web sitesini
kullanarak Json verisini sınıfa dönüştürdüm.

public class Result

{

>   public int questionNumber { get; set; }

>   public string criteria { get; set; }

>   public int count { get; set; }

>   public int score { get; set; }

>   public double average { get; set; }

}

public class RootObject

{

>   public List\<Result\> result { get; set; }

>   public object errorCode { get; set; }

>   public object errorMessage { get; set; }

}

Json verisini deserializeobject yaparak oluşturduğum sınıfa türettim. for
döngüsünü kullanarak oluşturduğum nesneden mağaza performans bilgilerini listeye
ekledim.

RootObject rootObject = JsonConvert.DeserializeObject\<RootObject\>(JsonString);

for (int i = 0; i \< rootObject.result.Count(); i++)

{

>   MagazaPerformansListesi mList = new
>   MagazaPerformansListesi(System.Text.RegularExpressions.Regex.Unescape(rootObject.result[i].criteria),
>   rootObject.result[i].count.ToString(),
>   rootObject.result[i].average.ToString());

>   GittiGidiyorMagazaPerformansListesi.Add(mList);

}

**Hepsiburada Mağaza Performansları**

Hepsiburada için ürün url adresinden, o ürünü satan tüm mağazalar gösterildiği
için, ürün url’sinden mağaza ismini bulma işlemini yapmamam söylendi.

Mağaza performans bilgilerini bulabilmek için mağaza ismi parametre alınarak, o
mağazanın profilinden performans bilgilerini elde etmeye çalıştım. N11 ve
GittiGidiyor mağazalarındakine benzer şekilde performans bilgilerine ulaşabilmek
için kaynak kodu inceledim ve class="merchant-rating lv-5" özelliğine sahip span
elementinde bu bilginin gösterildiğini gördüm.

string magazaPuani =
kaynakKod.DocumentNode.SelectSingleNode("//span[\@class=\\"merchant-rating
lv-5\\"]").InnerText;

Hepsiburada mağaza performans bilgilerini de listeye ekledikten sonra, kitaplığı
geliştirmeyi tamamladım.

**2.  SUBDOMAIN TESPİT ETME**

**Amaç**

Bana verilen ikinci görev, Birfatura entegrasyon sisteminin entegre olduğu sanal
mağazacılık sistemlerini kullanan müşterilere, Birfatura entegrasyon sistemini
haberdar etmek ve bu sayede müşteri kazanabilmek amacıyla sadece IdeaSoft
kullanan kişilerin domainlerini bulabilecek ve bunların gerçekten de (alan adı
transfer edilmiş olabilir) IdeaSoft sistemini kullanıp kullanmadığını tespit
edebilecek .Net ortamında bir dinamik bağlantı kitaplığı yazmam istendi.

IdeaSoft’u kullanan tahminen 6000 kişinin olduğu ve bunların domainleri tespit
edilerek, bu domainlerin üzerinden iletişim bilgilerinin elde edilmesi ile o
kişilerle iletişime geçilip, Birfatura entegrasyon sistemi için müşteri kazanmak
amaçlandı.

![7](https://1.bp.blogspot.com/-05xhObot130/XM7bYgIb9SI/AAAAAAAAC3s/mwK45gmouTEkwOJHXKiNCwKHpYfDKrAaACLcBGAs/s1600/9749288ca84db1060e446f1f31ab0822.png)

Şekil 4.1. Subdomain tespit etme kitaplığının akış şeması

**Kullandığım Araçlar**

Bu uygulamayı geliştirirken, aşağıdaki araç ve kitaplıkları kullandım:

**Diller:** C\#, XPath

**Geliştirme Ortamı:** Visual Studio 2017

**Kitaplıklar:** HtmlAgilityPack

**Diğer:** Chrome web tarayıcısı, dnsdumpster.com

**Uygulama**

Öncelikle, Visual Studio’da .Net Framework sınıf kitaplığı projesi oluşturdum.
Kitaplığı test edebilmek için konsol uygulaması projesi oluşturup, ikisini
birbirine bağladım. Yine domainlerin kaynak kodlarına erişebilmek için
HtmlAgilityPack kitaplığını Nuget paketi olarak projeye ekledim.

Rehberlik yapan bilgisayar mühendisi, IdeaSoft’un myideasoft.com domaininden
subdomainlerin bulunabildiğini söylediği için, ben de subdomainleri bulmak
amacıyla Google’da “*find subdomain on dns*” yazarak arama yaptım ve ilk web
sayfasına girdim. Buraya IdeaSoft’un subdomain sunucusunu yazdığımda herhangi
bir şey çıkmadı. Google’a dönüp ikinci web sayfasından [6] arama yaptığımda,
IdeaSoft’un subdomainlerini barındıran iki sunucusunun olduğunu ve bu
sunucularda barındırılan subdomainlerin listelenebildiğini gördüm. Buradaki
subdomainleri bir text dosyasına kopyaladığımda 5408 adet subdomain url adresi
elde ettim.

![7](https://1.bp.blogspot.com/-V-RaOsfKzgs/XM7bWnwDABI/AAAAAAAAC3U/IjKLrCO81G4Moaf62KRwcx84B_TnUyiQwCLcBGAs/s1600/6161bc890d4aa8e56a4bcf2ae8cba915.png)

Şekil 4.2. IdeaSoft subdomainlerinin tespit edilmesi

Text dosyasındaki url adreslerinden bazılarını Chrome ile açtığımda, IdeaSoft
sistemini kullanan web sayfaların en altında “*IdeaSoft® \| E-ticaret Paketleri
ile hazırlanmıştır.*” ya da “*Bu site, IdeaSoft® - Akıllı E-ticaret Paketleri
ile hazırlanmıştır.*” yazdığını gördüm. Yani IdeaSoft kullanan subdomainlerin
ortak özelliği, web sayfaların en altında “*IdeaSoft*” ve “*E-ticaret Paketleri
ile hazırlanmıştır*” textlerinin geçmesiydi.

Bu subdomain url adreslerini kullanabilmek için, text dosyasının içeriğini list
yapısında ki bir listeye kaydettim. Url listesini, for döngüsüyle elde ettiğim
her bir url adresinin kaynak kodlarını yine, bir önceki görevde öğrendiğim,
HtmlAgilityPack kitaplığı ile elde ettim. Her bir kaynak kodun içerisinde
aranacak anahtar stringleri, tespit ettiğim ortak textler olarak aldım ve kaynak
kodun içerisinde bu iki textin bulunduğu url adreslerini IdeaSoft’un subdomaini
olarak ayrı bir listeye kaydettim.

Bazı web sayfaları yapım aşamasında olduğu için anahtar kelimelerin
bulunmamasından dolayı bulunanların listesine eklenmediği halde, bu url
adreslerinin IdeaSoft da barındırıldığını fark ettim. Yapım aşamasında olan
domainlerin kaynak kodunda id=”yapimAsamasinda” özelliğine sahip div elementinin
olduğunu gördüm. Bu domain url adreslerini de ayrı bir listeye ekledim.

Son olarak elde ettiğim iki listenin içeriklerini ayrı ayrı text dosyalarına
kaydettim.

Özet olarak aşağıdaki kodlar ile hangi subdomainin IdeaSoft’u kullandığını,
hangisinin kullanmadığını, hangisinin yapım aşamasında olduğunu tespit etmiş
oldum.

string[] AnahtarKelimeler = new string[]{ "IdeaSoft" , "E-ticaret Paketleri ile
hazırlanmıştır" };

HtmlWeb htmlWeb = new HtmlWeb();

for (int i = 0; i \< UrlListesi.Count; i++)

{

>   HtmlDocument kaynakKod = htmlWeb.Load(UrlListesi[i].Url);

>   foreach (string s in AnahtarKelimeler)

>   {

>   if
>   (!kaynakKod.DocumentNode.SelectSingleNode("//body").InnerText.Contains(s))

>   {

>   bulundu = false;

>   break;

>   }

>   }

>   UrlListesi u = new UrlListesi(url);

>   if (!bulundu)

>   {

>   Bulundu.Remove(u);

>   if (kaynakKod.GetElementbyId("yapimAsamasinda").Name != null)

>   {

>   YapimAsamasinda.Add(u);

>   }

>   }

>   bulundu = true;

}

string[] b = Bulundu.ToArray(typeof(string)) as string[];

string[] y = YapimAsamasinda.ToArray(typeof(string)) as string[];

File.AppendAllLines(\@"BulunanMagazalar.txt", b);

File.AppendAllLines(\@"YapimAsamasindakiMagazalar.txt", y);

Sonuç olarak 5408 subdomainden 4418’inin IdeaSoft’u kullandığı, 231’inin yapım
aşamasında olduğu ve 759’unun IdeaSoft’u kullanmadığı sonucuna ulaştım. Bu ve
bir önceki görevin kodladığım kitaplık ve benden istenilen verileri, bana
rehberlik yapan bilgisayar mühendisine teslim ettim.

**3.  BOT KULLANARAK WEB MADENCİLİĞİ**

**Amaç**

N11 sanal mağazasını kullanan satıcıların iletişim bilgilerini bir veri tabanı
tablosuna kaydedip, bu iletişim bilgileri ile BirFatura entegrasyon sistemini
kullanmayan satıcılarla iletişime geçerek, kuruluşun müşteri kazanması amacıyla,
satıcıların mağaza bilgilerini N11 web sitesinden alıp, bu bilgileri veri
tabanına kaydeden .Net ortamında bir kitaplık yazmam istendi.

Elde edilecek iletişim bilgileri ile hem BirFatura sistemini kullanmayan N11
satıcıları tespit edilerek o satıcılara BirFatura sisteminin tanıtılması, hem de
BirFatura sistemini kullanan N11 satıcılarının iletişim bilgileri kuruluşun
kendi veri tabanında güncellenmesi, ayrıca haftada bir uygula çalıştırılarak
yeni açılmış mağazaların tespit edilmesiyle o kişilerle iletişime geçen ilk
fatura entegrasyon sistemi olmak amaçlandı.

**Kullandığım Araçlar**

Bu uygulamayı geliştirirken, aşağıdaki araç ve kitaplıkları kullandım:

**Diller:** C\#, XPath, SQL

**Geliştirme Ortamı:** Visual Studio 2017, Sqlite

**Kitaplıklar:** Selenium.WebDriver

**Diğer:** Chrome web tarayıcısı, DB Browser for Sqlite

**Uygulama**

Rehberlik yapan bilgisayar mühendisi, yapmam gereken işlemleri bana anlattı.
Bunlar:

-   Kayıtlı kullanıcı adı ve şifresi ile N11 web sitesine giriş yapılmalı

-   n11.com/magazalar web sayfasındaki tüm mağazaların listelendiği tablonun
    kaynak kodundan mağaza profiline girilebilen url adresleri alınmalı

-   Mağazanın profiline girip, profilinde gösterilen ilk ürüne tıklanmalı

-   Ürün sayfasında Hemen Al butonuna tıklanmalı

-   N11 web sitesinin yönlendireceği kullanıcı sepeti sayfasında Ödemeye Geç
    butonu tıklanmalı

-   N11 web sitesinin yönlendireceği ödeme sayfasında, Mesafeli Satış
    Sözleşmesi’ne tıklanmalı

-   Açılacak sözleşme sayfasındaki mağaza bilgileri veri tabanına kaydedilmeli

-   Kullanıcı sepetine girip, sepetteki ürün silinmeli

Bu işlemleri otomatikleştirebilmek için, bunu yapabileceğini bildiğim, daha
önceden geliştirdiğim başka bir projeyi geliştirme sürecinde karşıma çıkan
Selenium Browser Automation projesi aklıma geldi [7]. Bana rehberlik yapan
bilgisayar mühendisine böyle bir projenin olduğunu ve bu projeyi kullanarak
benden istediği görevi yerine getirebileceğimi anlattım. Bilgisayar mühendisinin
onaylaması sonrasında Selenium’u kullanabilmek için internette araştırma yaptım.
Selenium’un resmi web sitesindeki dokümanları inceledim [8]. Ayrıca YouTube’da
izlediğim bir videoda, Selenium’u kullanarak aranacak kelime ve istenen
bilgilerin XPath yolu parametre olarak verilip, otomatik bir şekilde Google
arama sonuçları listeleniyordu [9]. Bu videodan da yararlanarak .Net ortamında,
benzer bir şekilde, istediğim bilgileri elde edebileceğimi düşündüm.

Visual Studio’da sınıf kitaplığı projesi oluşturdum. Bunu test edebilmek için
konsol uygulaması projesi oluşturdum ve her ikisini birbirine bağladım.
HtmlAgilityPack, Selenium.WebDriver, Selenium.Support,
Selenium.WebDriver.ChromeDriver kitaplıklarını Nuget paketleri olarak yükledim.
Mağaza bilgilerini kaydedeceğim veri tabanı tablo yapısını belirlemek için, N11
web sitesinde yeni bir üyelik oluşturdum ve ürün satın alma adımlarını
uygulayarak bir mağazanın iletişim bilgilerine ulaştım. Böylece oluşturacağım
veri tabanı tablo yapısını belirledim.

![8](https://4.bp.blogspot.com/-hWmi9_9Ptc8/XM7bcJQ26GI/AAAAAAAAC4M/QC6zz-R9VX0RD5TqEoEQMH08fqudwZ6nQCLcBGAs/s1600/f33d7a7e9a52573e9191f917a9cfeaf1.png)

Şekil 5.1. Mağaza bilgileri veri tabanı tablo yapısı

Böylece kitaplığı geliştirmeden önce gerekli bilgileri elde etmiş oldum.
Kitaplığı geliştirmeye başladığımda ilk olarak, mağaza bilgilerini kaydedeceğim,
veri tabanına uygun, list yapısındaki bir liste için sınıf oluşturmak oldu.

public class MagazaBilgilerListesi

{

>   public string MagazaAdi { get; set; }

>   public string SaticiAdi { get; set; }

>   public string SaticiAdresi { get; set; }

>   public string SaticiTelefonu { get; set; }

>   public string SaticiMersisNo { get; set; }

>   public string SaticiEPosta { get; set; }

>   public string SaticiKargoSirketi { get; set; }

}

Sonrasında, Chrome web tarayıcısının otomatik bir şekilde açılmasını sağlayacak
Selenium driver’ı oluşturdum.

IWebDriver driver = new ChromeDriver();

Chrome web tarayıcısı driver tarafından otamatik bir şekilde açıldıktan sonra,
N11 web sitesine giriş yapabilmek için, driver’ı giriş sayfasına yönlendirdim.
Bu sayfada, kullanıcı adı ve şifresini driver’ın girebilmesi için gerekli kodu
yazdım. Driver’ın dolduracağı elementleri bulabilmesi için, daha önceki
uygulamalardan öğrendiğim şekilde, kaynak kodu inceledim. Kullanıcı adı ve
şifresinin yazıldığı input elementinin name özelliğinden yararlanarak driver’ın
otomatik olarak formu doldurmasını sağladım.

driver.Navigate().GoToUrl("https://www.n11.com/giris-yap");

IWebElement query = driver.FindElement(By.Name("email"));

query.SendKeys("mailim\@mail.com");

query = driver.FindElement(By.Name("password"));

query.SendKeys("şifrem");

query.Submit();

Uygulama her çalıştırıldığında driver tarafından Chrome web tarayıcısı açılıyor,
N11 giriş sayfasına gidiyor, buradaki formu doldurup sisteme giriş yapmış
oluyor.

Daha sonra, N11’deki tüm mağazaların listelendiği web sayfasına baktığımda
mağazaların harf harf ayrıldığını ve ul elementinin içinde gösterildiğini
gördüm. Burada driver’a yaptırmam gereken, ul elementinin içindeki mağazaları
listeye ekletip, span elementlerinde gösterilen harflerden bir sonraki harfe
tıklamasını sağlatmaktı.

![9](https://3.bp.blogspot.com/-1NODkTUaChU/XM7baS2PR2I/AAAAAAAAC30/TOBIaILadGQMc28VYtbsS37A-1cvqOahQCLcBGAs/s1600/b269263448676e58823bb6a760836d95.png)

Şekil 5.2. N11’deki tüm mağazaların listelendiği web sayfası

*Karşılaştığım zorluklar:* Bir harften diğer harfe geçmek için driver’ı sonraki
harfe tıklattırdığımda geriye NoSuchElementException hatası döndü. Karşılaştığım
diğer bir zorluk ise, örneğin A harfindeki mağazaların profil url adreslerini
listeye kaydettikten sonra, driver’a B harfine tıklatıp buradaki mağazalar için
de listeye kaydetmeye çalıştığımda, alt sıralardaki mağazalar listeye
kaydolmadı.

*Çözüm:* Driver’ın tıklayamadığı harfin span elementine tıklayabilmesi için web
tarayıcısı scroll’unun o elementin hizasına getirilmesi gerektiğini ve driver’a
parametre olarak verilen herhangi bir elementin web sayfasında yüklenebilmesi
için driver’ın bekletilebildiğini öğrendim [10].

Actions actions = new Actions(driver);

actions.MoveToElement(driver.FindElement(By.XPath("//\*[\@id=\\"contentSellerList\\"]/div/div[2]/div/div[2]/div[4]")));

actions.Perform();

kodlarını kullanarak web tarayıcı scroll’unun, driver’ın tıklayamadığı mağaza
harflerine gelmesini sağladım.

WebDriverWait waitForElement = new WebDriverWait(driver,
TimeSpan.FromSeconds(beklemeSuresi));

waitForElement.Until(ExpectedConditions.ElementIsVisible(By.Id("contentSellerList")));

kodlarını kullanarak driver’ı yeterli bir süre beklettiğimde, tüm mağazaların
profil sayfasına ulaşabileceğim url adreslerini listeye kaydettirebildim.

for (int magazaSirasi = 0; magazaSirasi \< MagazaUrlListesi.Count;
magazaSirasi++)

{

>   driver.Navigate().GoToUrl("https://www.n11.com/magaza/" +
>   MagazaUrlListesi[magazaSirasi].MagazaAdi);

>   driver.Navigate().GoToUrl(driver.FindElement(By.XPath("//\*[\@id=\\"view\\"]/ul/li[1]/div/div/a")).GetAttribute("href"));

...

kodlarını kullanarak, for döngüsü ile, listede depolanan her bir mağazanın
profil url adresini alıp, driver’ı bu web sayfasına yönlendirerek bu web
sayfasından driver’ın ilk ürünün web sayfasına girmesini sağlatmam oldu.

Devamında, açılan ürün web sayfasında driver’ın *“Hemen Al”* butonuna
tıklamasını sağladım.

...

driver.FindElement(By.XPath("//\*[\@id=\\"instantPay\\"]")).Click();

sepetiBosaltmali = true;

Ardından açılan sayfada, driver’ın *“Ödemeye Geç”* butonuna tıklamaya çalışması
sonucu bazen geriye NullReferenceException hatasının dönmesinden dolayı,
driver’ı yeterince beklettikten sonra butona tıklamasını sağladım. Aynı
işlemleri, driver’ın ödeme sayfasındaki mesafeli satış sözleşmesine tıklaması
içinde uyguladım.

...

waitForElement.Until(ExpectedConditions.ElementIsVisible(By.Id("js-goToPaymentBtn")));

driver.FindElement(By.Id("js-goToPaymentBtn")).Click();

waitForElement.Until(ExpectedConditions.ElementIsVisible(By.XPath("//\*[\@id=\\"checkoutForm\\"]/div/div[2]/div/label/span[2]")));

driver.FindElement(By.XPath("//\*[\@id=\\"checkoutForm\\"]/div/div[2]/div/label/span[2]")).Click();

waitForElement.Until(ExpectedConditions.ElementIsVisible(By.XPath("//\*[\@id=\\"contractLister\\"]/div/div[2]/div/div[2]/table/tbody/tr[2]/td/ul/li")));

Bu şekilde, bir mağaza için driver’ın otomatik olarak Chrome’da mağaza iletişim
bilgilerini göstermesini sağladım.

...

string[] magazaBilgileri =
driver.FindElement(By.XPath("//\*[\@id=\\"contractLister\\"]/div/div[2]/div/div[2]/table/tbody/tr[2]/td/ul/li")).Text.Split('\\n');

MagazaBilgilerListesi mList = new
MagazaBilgilerListesi(MagazaUrlListesi[magazaSirasi].MagazaAdi.Replace("'", ""),
magazaBilgileri[0].Split(':').Last().Trim().Replace("'", ""),
magazaBilgileri.Trim().Replace("'", ""), magazaBilgileri
[2].Split(':').Last().Trim().Replace("'", ""), magazaBilgileri
[3].Split(':').Last().Trim().Replace("'", ""), magazaBilgileri
[4].Split(':').Last().Trim().Replace("'", ""), magazaBilgileri
[5].Split(':').Last().Trim().Replace("'", ""));

List\<MagazaBilgilerListesi\> MagazaBilgileriListesi = new
List\<MagazaBilgilerListesi\>();

MagazaBilgileriListesi.Add(mList);

kodunu kullanarak elde ettiğim mağaza iletişim bilgilerini veri tabanına
kaydedebilmek için ayrıştırıp, ayrı bir listeye kaydettim.

Ardından, iletişim bilgilerini kaydettiğim listeyi kullanarak, bu bilgileri
Sqlite veri tabanına kaydetmek için ayrı bir sınıfta iki metot yazdım.

public MagazaPerformansBilgilerVeritabani(List\<MagazaBilgilerListesi\> liste,
string VeritabaniPath, string VeritabaniAdi, string VeritabaniTabloAdi, int
VeritabaniId)

{

>   this.liste = liste;

>   this.VeritabaniPath = VeritabaniPath;

this.VeritabaniAdi = VeritabaniAdi + ".sqlite";

this.VeritabaniTabloAdi = VeritabaniTabloAdi;

this.VeritabaniId = VeritabaniId;

>   VeritabaniOlustur();

>   VeritabaniInsert();

}

Bu metotlardan birincisi, Sqlite veri tabanını oluşturan metot iken

private void VeritabaniOlustur()

{

>   if (!File.Exists(VeritabaniPath + VeritabaniAdi))

>   {

>   SQLiteConnection.CreateFile(VeritabaniPath + VeritabaniAdi);

>   SQLiteConnection baglanti = new SQLiteConnection("Data Source=" +
>   VeritabaniAdi + ";Version=3;");

>   baglanti.Open();

>   string sorgu = "CREATE TABLE " + VeritabaniTabloAdi + " (MagazaAdi TEXT
>   PRIMARY KEY NOT NULL, SaticiAdi TEXT, SaticiAdresi TEXT, SaticiTelefon TEXT,
>   SaticiMersisNo TEXT, SaticiEposta TEXT, SaticiKargo TEXT)";

>   komut = new SQLiteCommand(sorgu, baglanti);

>   komut.ExecuteNonQuery();

>   baglanti.Close();

>   }

}

ikincisi, listedeki iletişim bilgilerini veri tabanına kayıt eden metot oldu.

private void VeritabaniInsert(string VeritabaniPath, string VeritabaniAdi,
string VeritabaniTabloAdi)

{

>   SQLiteConnection baglanti = new SQLiteConnection("Data Source=" +
>   VeritabaniAdi + ";Version=3;");

>   baglanti.Open();

>   string sorgu = "INSERT INTO " + VeritabaniTabloAdi + " (MagazaAdi,
>   SaticiAdi, SaticiAdresi, SaticiTelefon, SaticiMersisNo, SaticiEposta,
>   SaticiKargo) VALUES (";

>   sorgu += "'" + liste[0].MagazaAdi + "', '" + liste[0].SaticiIsmi_Unvani +
>   "', '" + liste[0].SaticiAdresi + "', '" + liste[0].SaticiTelefonu + "', '" +
>   liste[0].SaticiMersisNo + "', '" + liste[0].SaticiEPosta + "', '" +
>   liste[0].SaticiKargoSirketi + "')";

>   komut = new SQLiteCommand(sorgu, baglanti);

>   komut.ExecuteNonQuery();

>   baglanti.Close();

}

Bu şekilde iletişim bilgilerini kayıt ettikten sonra, driver’ın N11’de kullanıcı
sepeti web sayfasına girerek, sepetteki ürünü silmesini sağladım ve ardından for
döngüsünde bir sonraki mağaza için aynı işlemleri yapmasını sağladım. Driver’ın
herhangi bir hata ile karşılaşması durumunda, hatayla karşılaştığı mağazayı
atlamasını sağladım.

...

driver.Navigate().GoToUrl(Ayarlar.UrlMagazaSepet);

waitForElement.Until(ExpectedConditions.ElementIsVisible(By.XPath("//\*[\@id=\\"newCheckout\\"]/div/div[1]/div[2]/div[1]/section/table[2]/tbody/tr/td[1]/div[3]/div[2]/span[1]")));

driver.FindElement(By.XPath("//\*[\@id=\\"newCheckout\\"]/div/div[1]/div[2]/div[1]/section/table[2]/tbody/tr/td[1]/div[3]/div[2]/span[1]")).Click();

sepetiBosaltmali = false;

![10](https://4.bp.blogspot.com/-YBSz_KNS6rE/XM7bWkO6xhI/AAAAAAAAC3Q/Y0zMD28DVxA_p3Z02IOYNsQTfPhOmAxXwCLcBGAs/s1600/42504aaeb4e0783108026c493902abb8.png)

Şekil 5.3. Uygulamanın çalışma sürecinden bir ekran görüntüsü (web tarayıcısı
tarafı)

![11](https://2.bp.blogspot.com/-A5mW2AM9O_k/XM7bXSGAJxI/AAAAAAAAC3c/feYLoEypjnQxCt0IV4-tKoppWvqAb90qgCLcBGAs/s1600/6e6a184ebefb36bf21cf986400355b28.png)

Şekil 5.4. Uygulamanın çalışma sürecinden bir ekran görüntüsü (konsol tarafı)

Kodladığım bu kitaplık ile N11’deki 28817 adet mağazanın her birinin iletişim
bilgilerini elde etmek günler süreceği için bu uygulamayı, kuruluşta
kullanılmayan başka bir bilgisayarda işlemin bitmesi için çalıştırdım.

1.  **Sonuç**

Bazı mağazaların her hangi bir ürününün olmaması ve uygulamanın çalışma
sürecinin tamamlanmasına kadar geçen sürede bazı mağazaların kapanmasından
dolayı 28817 adet mağazadan 28352 mağazanın iletişim bilgilerini elde etmiş
oldum.

![12](https://1.bp.blogspot.com/-04MfP7LM9-Y/XM7bXItFLnI/AAAAAAAAC3Y/Qc_BGGNhjzUVL2JKDXRkADuE79iLI7uTwCLcBGAs/s1600/6404bf09cd2e9be70327a0e45512b53e.png)

Şekil 5.5. Uygulama çalışma süreci sonunda elde edilen veri tabanı tablosunun
bir ekran görüntüsü

Kodladığım kitaplık ve benden istenen sql formatındaki veri tabanı tablo
dosyasını, bana rehberlik yapan bilgisayar mühendisine teslim ettim.

**4.  BİRFATURA API TESTİ**

**Amaç**

Staj yaptığım kuruluş, bilgisayar mühendisi iş alımı için iş ilanına
başvuranlardan:

-   Asp.Net Web Api projesi olacak, C\# ile yazılacak

-   <http://ubirfatura-testapibirfatura.azurewebsites.net/token> bu adresten
    öncelikle token alınacak

-   Token için gerekli kullanıcı adı:”test\@test.com”, şifre:”Test123.”

-   Web Api token yapısı ile ilgili
    <http://www.gokhan-gokalp.com/asp-net-web-api-token-based-authentication/>
    bu adres incelenebilir

-   Alınan token ile
    <http://ubirfatura-testapibirfatura.azurewebsites.net/api/test/SatislarGetir>
    e post yapılarak dönen değerler bir List’e atılacak

-   List, bir Grid ile ekranda gösterilecek (satılan ürünler ile birlikte)

-   Grid’de her siparişin yanında bir faturalandır butonu olacak. Bu butona
    basınca, tasarımı önemli değil, sadece o siparişteki bilgileri içen bir pdf
    dosyası oluşturulacak

-   Pdf için ITextSharp kitaplığı kullanılacak

şeklinde bir web api projesi yapmalarını bekliyordu ve benden de bu projeyi
yapmam istendi [11].

Bu projedeki amaç, beni de staj yaptığım kuruluşun bilgisayar mühendisi iş alımı
sürecine katmak ve BirFatura entegrasyon sisteminin temelde ne yaptığını bana
göstermekti.

**Kullandığım Araçlar**

Bu projeyi geliştirirken, aşağıdaki araç ve kitaplıkları kullandım:

**Diller:** C\#, Json

**Geliştirme Ortamı:** Visual Studio 2017

**Kitaplıklar:** ITextSharp, Newtonsoft.Json

**Diğer:** Postman, json2csharp.com

**Uygulama**

Web api token yapısı ile ilgili verilen url adresini incelemeye başladım. Bu web
sayfasında, tokenin nasıl oluşturulacağı ve oluşturulan token ile istenilen
bilgilerin nasıl elde edileceği anlatılıyordu [12]. Burada anlatıldığı üzere,
Postman programını yükledim. Post metodu ile grant_type = password, username =
test\@test.com ve password = Test123. parametrelerini göndererek geri dönen Json
verisinden token bilgilerini elde edebildim.

![13](https://3.bp.blogspot.com/-qGkZvlfaaMY/XM7bVN2wegI/AAAAAAAAC3I/3n2v5umfC_QhArcGalBOliiFncZ5JqNfACLcBGAs/s1600/1e7a9e3af1dda9bfe1f600681bcf1a6f.png)

Şekil 6.1. Postman kullanarak token’in elde edilmesi

Elde ettiğim bu token bilgilerini kullanarak, yine post metodu ile geri dönen
Json verisinden listeye kaydedeceğim verileri elde edebildim. Böylece
oluşturacağım custom list yapısını da öğrenmiş oldum.

![14](https://3.bp.blogspot.com/-l9-kbc95tCY/XM7bc3lbzHI/AAAAAAAAC4Q/RLU1XxU699kjEYBbZW2H3_6_7dPW0bJJgCLcBGAs/s1600/ff3d3c92ad8c6d3f0d3f3f3f0cff8d95.png)

Şekil 6.2. Postman kullanarak token’den veri elde edilmesi

Daha sonra, kodlayacağım web api projesinde de aynı şekilde bilgileri elde
edebilmek için Visual Studio’da Asp.Net Web Application (.Net Framework)’den Web
Api ve Web Forms’u seçerek yeni bir proje oluşturdum.

![15](https://2.bp.blogspot.com/-0Facg0giV08/XM7bU6D4fCI/AAAAAAAAC3E/52bL996TnrIVXoTkHUUmpdX_BYewfkQQACLcBGAs/s1600/239caf831fd53d4121cd7e9615f685b7.png)

Şekil 6.3. BirFatura Api Testi projesinin oluşturulması

İlk önce, Postman’den elde ettiğim Json verisinin list yapısını oluşturabilmek
için, json2csharp.com web sitesini kullanarak, bu Json verisini sınıfa
dönüştürdüm ve projemde oluşturduğum Liste adında bir sınıf dosyasına kaydettim.

public class SatilanUrunler

{

>   public int UrunID { get; set; }

>   public string UrunAdi { get; set; }

>   string StokKodu { get; set; }

>   public string SatisAdeti { get; set; }

>   public string KDVOrani { get; set; }

>   string KDVDahilBirimFiyati { get; set; }

}

public class RootObject

{

>   public int FaturaID { get; set; }

>   public string MusteriAdi { get; set; }

>   public string MusteriAdresi { get; set; }

>   public string MusteriTel { get; set; }

>   public string MusteriSehir { get; set; }

>   public string MusteriTCVKN { get; set; }

>   public string MusteriVergiDairesi { get; set; }

>   public List\<SatilanUrunler\> SatilanUrunler { get; set; }

}

Daha sonra, projemde token bilgisini kullanabilmek için yine Postman’de geri
dönen token Json verisini json2csharp.com web sitesini kullanarak bunu da sınıfa
dönüştürdüm ve oluşturduğun Veri adında yeni bir sınıf dosyasına kaydettim.

private class RootObject

{

public string access_token { get; set; }

public string token_type { get; set; }

public int expires_in { get; set; }

}

Token bilgisini projemde elde edebilmek için internette araştırma yaptım.
Google’da yaptığım *“c\# get access token using post method”* aramasından
bulduğum bir web sitesinde, burada sorulan bir soruya verilen ilk çözümden [13]
yararlanarak token’ı elde edebileceğim bir metot yazdım.

private RootObject root;

private void TokenAl()

{

>   string encodedUserCredentials =
>   Convert.ToBase64String(ASCIIEncoding.ASCII.GetBytes("user:password"));

>   string userData =
>   "username=test\@test.com\&password=Test123.&grant_type=password";

>   HttpWebRequest request =
>   (HttpWebRequest)WebRequest.Create("http://ubirfatura-testapibirfatura.azurewebsites.net/token");

>   request.Accept = "application/json";

>   request.ContentType = "application/x-www-form-urlencoded";

>   request.Headers.Add("Authorization", "Basic " + encodedUserCredentials);

>   StreamWriter requestWriter = new StreamWriter(request.GetRequestStream());

>   requestWriter.Write(userData);

>   requestWriter.Close();

>   var response = request.GetResponse() as HttpWebResponse;

>   String responseString;

>   using (Stream stream = response.GetResponseStream())

>   {

>   StreamReader reader = new StreamReader(stream, Encoding.UTF8);

>   responseString = reader.ReadToEnd();

>   }

>   root = JsonConvert.DeserializeObject\<RootObject\>(responseString);

}

Yazdığım bu metot ile elde edeceğim token’ı kullanarak, listeye kaydedeceğim
veriye ulaşabilmek için internette araştırma yaptım ve araştırmalarım sonucu
yine sorulan bir soruya verilmiş ilk cevaptan [14] yararlanarak verileri elde
edebileceğim bir metot yazdım.

public string VeriAl()

{

>   TokenAl();

>   ServicePointManager.ServerCertificateValidationCallback += (sender, cert,
>   chain, sslPolicyErrors) =\> true;

>   using (var client = new HttpClient())

>   {

>   if (!string.IsNullOrWhiteSpace(root.access_token))

>   {

>   client.DefaultRequestHeaders.Clear();

>   client.DefaultRequestHeaders.Add("Authorization", "Bearer " +
>   root.access_token);

>   }

>   var myContent = JsonConvert.SerializeObject(root);

>   var buffer = Encoding.UTF8.GetBytes(myContent);

>   var byteContent = new ByteArrayContent(buffer);

>   byteContent.Headers.ContentType = new
>   MediaTypeHeaderValue("application/json");

>   var response =
>   client.PostAsync("http://ubirfatura-testapibirfatura.azurewebsites.net/api/test/SatislarGetir",
>   byteContent).Result;

>   return response.Content.ReadAsStringAsync().Result;

>   }

}

Token’i kullanarak elde ettiğim verileri, önceden oluşturduğum list yapısını
kullanarak, bir listeye kaydettim.

public List\<RootObject\> Listele()

{

>   Veri veri = new Veri();

>   JsonString = veri.VeriAl();

>   return JsonConvert.DeserializeObject\<List\<RootObject\>\>(veri.VeriAl());

}

Bilgileri kullanarak pdf dosyası oluşturabilmek için ITextSharp kitaplığı
hakkında internette araştırma yaptım ve elde ettiğim bilgilere göre [15] pdf
dosyası oluşturacak şu metodu yazdım:

public void PdfOlustur(string faturaAdi, string icerik)

{

>   Document document = new Document();

>   PdfWriter.GetInstance(document, new FileStream(\@"C:\\Users\\PC\\Desktop\\"
>   + faturaAdi + ".pdf", FileMode.Create));

>   BaseFont arial = BaseFont.CreateFont("C:\\\\windows\\\\fonts\\\\arial.ttf",
>   BaseFont.IDENTITY_H, BaseFont.EMBEDDED);

>   Font font = new Font(arial, 12, Font.NORMAL);

>   if (document.IsOpen() == false)

>   {

>   document.Open();

>   document.Add(new Paragraph(icerik, font));

>   document.Close();

>   }

}

Daha sonra projede WebForm1 adında yeni bir form dosyası oluşturdum. Bu form
dosyasına, araç kutusunu kullanarak, GridView ekledim ve gerekli düzenlemeleri
yaptım.

![16](https://3.bp.blogspot.com/-Z2mGd-DbiEg/XM7bXvZVnzI/AAAAAAAAC3g/aXt0xUAvb1080cv0LH_bbwHvLCHjeOAdgCLcBGAs/s1600/77643607cbeae25acde9f3ae87f4e319.png)

Şekil 6.4. GridView yapısı

GridView’de her satırın sağında Faturalandır butonu oluşturmak için internette
bulduğum çözümden yararlanarak [16] Columns etiketi içerisine şu kodları yazdım:

\<asp:TemplateField ShowHeader="False"\>

>   \<ItemTemplate\>

>   \<asp:Button ID="Button" runat="server" CausesValidation="false"
>   CommandName="Faturalandir" Text="Faturalandır" CommandArgument='\<%\#
>   Eval("FaturaID") %\>' /\>

>   \</ItemTemplate\>

\</asp:TemplateField\>

Oluşturduğum GridView’da verileri gösterebilmek için ObjectDataSource olarak,
oluşturduğum listeyi seçtim.

\<asp:ObjectDataSource ID="ObjectDataSource1" runat="server"
SelectMethod="Listele"
TypeName="BirFaturaApiTest.Models.Liste"\>\</asp:ObjectDataSource\>

Herhangi bir Faturalandır butonuna tıklandığında PdfOlustur metodunu kullanarak
pdf dosyası oluşturulması için WebForm1 formunun sınıf dosyasında şu metodu
yazdım:

protected void GridView1_OnRowCommand(object sender, GridViewCommandEventArgs e)

{

>   string icerik = "";

>   int id = Convert.ToInt32(e.CommandArgument) - 1;

>   Liste liste = new Liste();

>   for (int i = 0; i \< liste.liste[id].SatilanUrunler.Count; i++)

>   {

>   icerik += liste.liste[id].SatilanUrunler[i].UrunID;

>   icerik += liste.liste[id].SatilanUrunler[i].UrunAdi;

>   icerik += liste.liste[id].SatilanUrunler[i].StokKodu;

>   icerik += liste.liste[id].SatilanUrunler[i].SatisAdeti;

>   icerik += liste.liste[id].SatilanUrunler[i].KDVOrani;

>   icerik += liste.liste[id].SatilanUrunler[i].KDVDahilBirimFiyati + "\\n";

>   }

>   liste.PdfOlustur("Fatura" + ++id, icerik);

}

Karşılaştığım hataları da düzelterek, böylece bu projeyi de tamamlamış oldum.

**Sonuç**

En son şekliyle projemi çalıştırdığımda GridView’da bilgilerin düzgün bir
şekilde görüntülendiğini ve herhangi bir satırdaki Faturalandır butonuna
tıkladığımda, o satırla ilgili sipariş bilgilerinin masaüstünde pdf dosyası
olarak oluşturulduğunu gözlemledim.

![17](https://4.bp.blogspot.com/-Jd2Xv8x8R9U/XM7baQpkWuI/AAAAAAAAC34/WIywyNIndC48s8SpzduFFPJdMeM7AvFjwCLcBGAs/s1600/dc46720ae9dcb7b88a27aaf181eb83f7.png)

Şekil 6.5. Uygulamanın çalışma sürecinden bir ekran görüntüsü

![18](https://3.bp.blogspot.com/-61cnOryRSpU/XM7bX8f-KGI/AAAAAAAAC3k/XZG0g-h5uvonFMSwD8LZAi-PdcrxpWx2gCLcBGAs/s1600/864056ac6f0d417fb26a7e287cb7372f.png)

Şekil 6.6. GridView’daki üçüncü satır için oluşturulan pdf dosyasının içeriği

Projeyi tamamlamamdan sonra, bana rehberlik yapan bilgisayar mühendisine
sunumumu yaptım. Her ne kadar projede MVC yapısının olmaması gerekiyorken,
Asp.Net yapısı ile ilgili herhangi bir tecrübemin olmamasından dolayı, bu
şekilde projemi oluşturmuştum. Buna rağmen sunumumu yaptığım bilgisayar
mühendisi projem için olumlu görüşte bulundu.

**5.  KAYNAKLAR**

1.  İnternet: Türkiye'de E-Ticaret Firmaları İçin Hazırlanmış Tek Entegre Fatura
    Sistemi. <https://birfatura.com/hakkimizda>

2.  İnternet: Html Agility Pack – API. <http://html-agility-pack.net/api>

3.  İnternet: Getting Started With HTML Agility Pack.
    <https://www.c-sharpcorner.com/UploadFile/9b86d4/getting-started-with-html-agility-pack/>
    (2015).

4.  İnternet: Json.Net Documentation.
    <https://www.newtonsoft.com/json/help/html/Introduction.htm>

5.  İnternet: Json2csharp, Generate C\# Classes From Json.
    <http://json2csharp.com/>

6.  İnternet: Dns Recon & Research, Find & Lookup Dns Records.
    <https://dnsdumpster.com/>

7.  İnternet: Selenium WebDriver.
    <https://www.seleniumhq.org/projects/webdriver/>

8.  İnternet: Selenium Documentation. <https://www.seleniumhq.org/docs/>

9.  İnternet: PyPros, Get Search Engine Results with Python.
    <https://www.youtube.com/watch?v=EELySnTPeyw> (2016).

10. İnternet: Explicit Wait for findElements in Selenium Webdriver.
    <https://stackoverflow.com/questions/34397608/explicit-wait-for-findelements-in-selenium-webdriver>

11. İnternet: Birfatura Api Testi.
    <http://ubirfatura-testapibirfatura.azurewebsites.net/>

12. İnternet: Asp.Net Web API – Token Based Authentication.
    <http://www.gokhan-gokalp.com/asp-net-web-api-token-based-authentication/>
    (2015).

13. İnternet: Get An Access Token from Sign Now REST APIs?
    <https://stackoverflow.com/questions/29294892/get-an-access-token-from-sign-now-rest-apis>

14. İnternet: Token Based Authentication in Web API Without Any User Interface.
    <https://stackoverflow.com/questions/38661090/token-based-authentication-in-web-api-without-any-user-interface>

15. İnternet: ITextSharp Kütüphanesi Kullanarak Pdf Dosyası Oluşturmak.
    <http://www.csharpnedir.com/articles/read/?id=1114> (2010).

16. İnternet: How To Add Button Field In Gridview For Each Row In Asp.Net Using
    C\#.
    <https://www.codeproject.com/Questions/619834/how-to-add-button-field-in-gridview-for-each-row-i>

17. İnternet: Microsoft .Net Nedir?
    <https://www.teknologweb.com/microsoft-net-nedir> (2013).

18. İnternet: Tümleşik Geliştirme Ortamı.
    <https://tr.wikipedia.org/wiki/Tümleşik_geliştirme_ortamı>

19. İnternet: HTML DOM Tanım. <http://htmldom.uzerine.com/index.jsp?objid=4473>

20. İnternet: XPath Nedir? <http://www.yusufsezer.com.tr/xpath-nedir/> (2017).

21. İnternet: JSON'a Giriş. <https://www.json.org/json-tr.html>

22. İnternet: Web Mining.
    <https://ahmetsedef.wordpress.com/tag/web-yapi-madenciligi/> (2014).

23. İnternet: WebSocket Nedir? <https://kodcu.com/2016/11/websocket-nedir-2/>
    (2016).

24. İnternet: Web API. <https://en.wikipedia.org/wiki/Web_API>

25. İnternet: JSON Web Token – JWT.
    <http://www.bayramucuncu.com/json-web-token-jwt/>

26. İnternet: Microsoft Visual Studio.
    <https://en.wikipedia.org/wiki/Microsoft_Visual_Studio>

27. İnternet: AJAX (Programlama).
    <https://tr.wikipedia.org/wiki/AJAX_(programlama)>

28. İnternet: Web Scraping with HTML Agility Pack.
    <https://wayfare.ro/web-scraping-html-agility-pack/>

29. İnternet: Selenium Nedir? Nasıl Test Case Yazılır?
    <https://medium.com/@ahmetaslan_78691/selenium-nedir-nas%C4%B1l-test-case-yaz%C4%B1l%C4%B1r-e31129a8986c>
    (2017).

**6.  EK – 1. Terimler**

**.Net Ortamı:** Microsoft şirketinin, programlama dilinden ve çalıştırılacak
sistemden bağımsız olarak uygulama geliştirmeyi amaçlayan platformudur. Pek çok
programlama dili ile uygulama geliştirmeye imkân sağlayan bir ortamdır. Uygulama
geliştirecekler için arka planda çalışan bir araçtır. Platformun desteklediği
programlama dillerinden birisi ile Visual Studio’yu kullanarak programlar veya
web uygulamaları geliştirilir [17].

**Integrated Development Environment (IDE):** IDE, yazılımcıların hızlı ve rahat
bir şekilde program geliştirebilmesini amaçlayan, içerisinde barındırdığı
geliştirme sürecini organize edebilen birçok araçlarla birlikte geliştirme
sürecinin verimli kullanılmasına katkıda bulunan araçların tamamını içerisinde
barındıran yazılım türüdür. Programlama diline göre sözdizimi renklendirmesi
yapabilen, kod dosyalarının hiyerarşik olarak görülebilmesi amacıyla hazırlanmış
gerçek zamanlı, tümleşik bir derleyici, yorumlayıcı ve hata ayıklayıcı [18].

**Document Object Model (DOM):** DOM, tüm dosya elemanları için nesneleri,
özellikleri ve metotları için erişim tanımlayan bir ara yüzdür. W3C (World Wide
Web Consortium) tarafından tanımlanmış bir standarttır. Genel olarak HTML ya da
XML gibi dosyalara erişim için standartlar getirir ve 3 seviye olarak
kullanılır. Bunlar; Core DOM (her hangi bir yapısal dosya için kullanılan
standart model), XML DOM (XML için kullanılan standart model) ve HTML DOM (HTML
için kullanılan standart model)’dir [19].

**XPath:** XPath, XML dokümanındaki bilgiyi bulmak için kullanılan, içindeki
etiketleri ve onlara ait özellikleri incelemeyen bir dil, W3C tarafından
geliştirilen bir standarttır [20].

**JavaScript Object Notation (Json):** Json, veri değişim formatıdır. İnsanların
okuyup yazabilmesi ve makinaların tarayıp, oluşturabilmesi kolaydır. Json,
tamamen programlama dillerinden bağımsızdır, ancak C türevi dillere (C, C++,
C\#, Java, JavaScript, Perl, Python), yazılış bakımından çok benzeyen bir veri
tanımlama formatıdır. Bu özellikler, Json'u veri alışverişi için ideal hale
getirir [21].

**Web Madenciliği (Web Scraping):** Web madenciliği, web’de bulunan verilerden
faydalı bilgiye ulaşmaktır. Çeşitli yapıdaki web sayfası dokümanlarını,
içeriklerini, link yapılarını ve kullanım bilgilerini incelemek, bunlardaki
anlamlı bilgileri elde etmek için veri madenciliği tekniklerinin kullanılmasıdır
[22].

**WebSocket:** Http istek/cevap protokolüdür. Sunucuda bir değişiklik olduğunda,
sunucu bunu istemciye bildiremez. Bu değişikliği algılayabilmek için WebSocket
gibi yapılar kullanılır. WebSocket tek bir TCP bağlantısı üzerinden çift yönlü
ve full duplex mesajlaşmayı sağlar. İstemciden istek gelmesine gerek kalmadan
sunucudaki değişiklikler istemciye iletilebilir hale gelir. Http protokolüne
uygun olmayan gerçek zamanlı web uygulamalarındaki karmaşık yapının
basitleştirilmesini sağlar. İstemci ve sunucu arasında kurulan kalıcı bağlantı
sayesinde her iki taraf birbirine veri gönderebilir hale gelir [23].

**Web Api (Web Application Programming Interface):** Web Api, bir web sunucusu
veya bir web tarayıcısı için uygulama programlama ara yüzüdür [24].

**Dinamik Bağlantı Kitaplığı (DLL):** Aynı anda birden fazla program tarafından
kullanılan verileri içeren bir kitaplıktır.

**Bot:** En temel anlamıyla, verilen görevi yerine getiren programlara verilen
isimdir.

**Token:** Web içeriğine erişmek için kullanılan bir bilet, içeriğe erişmek
isteyen kullanıcı uygulamanın doğrulanmış ve yetkili olup olmadığını tanıtan bir
kart gibi düşünülebilecek erişim bilgisidir [25].

**Visual Studio:** Visual Studio, Microsoft tarafından geliştirilen bir tümleşik
geliştirme ortamıdır (IDE). Microsoft Windows, .NET Framework tarafından
desteklenen tüm platformlar için yönetilen kod ile birlikte yerel kod ve Windows
Forms uygulamaları, web siteleri, web uygulamaları ve web servisleri (web api)
ile birlikte konsol ve grafiksel kullanıcı ara yüzü (GUI) uygulamaları
geliştirmek için kullanılır. Hata ayıklayıcısı, hem kaynak hem de makine
seviyesinde çalışır. GUI uygulamaları, web, sınıf ve veri tabanı şema
tasarımcıları oluşturmak için form tasarımcısı içerir. Visual Studio, çeşitli
programlama dillerini ve şablonları destekler. Bunlar; C, C++, C++/CLI, Visual
Basic .Net, C\#, F\#, JavaScript, TypeScript, XML, XSLT, HTML ve CSS’dir. Ayrıca
Python, Ruby, Node.js gibi dilleri de eklentiler aracılığıyla destekler [26].

**Ajax (Asynchronous JavaScript and XML):** Web sayfalarında JavaScript ve
XMLHttpRequest kullanımı ile etkileşimli uygulamalar oluşturmaya yarayan
tekniktir. En yaygın kullanım alanı, sayfayı yeniden yüklemeye gerek
kalmaksızın, sayfada görünür değişiklikler yapmaktır. XMLHttpRequest
kullanılarak birden fazla bağımsız işlem yapılabilir [27].

**HtmlAgilityPack:** Sunucuda bulunan HTML belgesini web dışına ayrıştırmak
amacıyla okuma veya yazma yapılabilen bir DOM oluşturan, düz XPath ve XSLT’yi
destekleyen HTML ayrıştırıcısı (parser) olarak kullanılan bir .Net kitaplığıdır.
Oluşturduğu nesne modeli (DOM), HTML belgeleri ile çok benzerdir [28].

**Newtonsoft.Json:** Json verisini deserialize yapıp oluşturulan sınıflara
atayarak, bu sınıflar ile Json verisini kolaylıkla kullanabilmeyi amaçlayan bir
.Net kitaplığıdır.

**Selenium.WebDriver:** Web tarayıcı otomasyon aracıdır. Bir web siteniz
üzerindeki bazı adımları belirleyerek web tarayıcısı üzerinde otomatik testler
yapar. Örneğin bir web sitesinde kullanıcı giriş alanının çalışıp çalışmadığını
test ederek, tek bir komutla bunların testini yapıp rapor sunması sağlanabilir.
Çeşitli programlama dilleri ile kullanılabilir [29].

**ITextSharp:** Pdf dosyası oluşturmayı amaçlayan bir .Net kitaplığıdır.
