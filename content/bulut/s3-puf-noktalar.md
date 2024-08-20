---
title: "AWS S3: Püf Noktalar"
tags: ["aws", "s3", "storage", "database", "file", "dosya"]
date: 2020-05-09T13:41:29+03:00
draft: false
images: ["/img/s3-puf-noktalar/kitaplar.jpg"]
---

AWS'nin en kullanışlı ve en kendini ispatlamış servislerinden biri S3.
İsviçre çakısı gibi olan S3, uygulamanızdaki pek çok ihtiyacı karşılayabiliyor.
Yeri geldiğinde bir veritabanı veya [statik hosting](/bulut-bilisim/s3-statik-hosting) çözümü olabiliyor.
Fakat S3'ten azami verim alabilmeniz için bilmeniz gereken bazı detaylar var.

![Kitaplar][1]
<a style="background-color:black;color:white;text-decoration:none;padding:4px 6px;font-family:-apple-system, BlinkMacSystemFont, &quot;San Francisco&quot;, &quot;Helvetica Neue&quot;, Helvetica, Ubuntu, Roboto, Noto, &quot;Segoe UI&quot;, Arial, sans-serif;font-size:12px;font-weight:bold;line-height:1.2;display:inline-block;border-radius:3px" href="https://unsplash.com/@technobulka?utm_medium=referral&amp;utm_campaign=photographer-credit&amp;utm_content=creditBadge" target="_blank" rel="noopener noreferrer" title="Stanislav Kondratiev'a ait fotoğraflar için tıklayın."><span style="display:inline-block;padding:2px 3px"><svg xmlns="http://www.w3.org/2000/svg" style="height:12px;width:auto;position:relative;vertical-align:middle;top:-2px;fill:white" viewBox="0 0 32 32"><title>unsplash-logo</title><path d="M10 9V0h12v9H10zm12 5h10v18H0V14h10v9h12v-9z"></path></svg></span><span style="display:inline-block;padding:2px 3px">Fotoğraf Unsplash'ta Stanislav Kondratiev'a aittir.</span></a>

# Terminoloji

* S3 bir *key-value store*.
* Burada bahsi geçen *value*'ya **obje** demişler (*dosya* olarak düşünebilirsiniz).
* Dosyalar kova (*bucket*) denen yapılarda saklanıyor (*klasör* diyebiliriz).
* S3'te her dosyanın bir anahtarı var ve bu anahtar o klasör içinde ilgili dosyaya erişmenizi sağlıyor.
* S3 düz bir hiyerarşiye sahip: Bir klasör içindeki tüm dosyalar aynı seviyede bulunuyor, klasör içinde klasör gibi bir kavram yok (konsolda gösterim olarak var, buna değineceğim).
* Klasör adları tüm AWS hesaplarında benzersiz olmak zorunda. Eğer bir klasör oluştururken hata alıyorsanız, o klasör ismi başkası tarafından kullanılıyor olabilir:

![S3-hata][2]

> AWS hesabı nasıl açılır merak ediyorsanız, [ilgili yazıyı](/bulut-bilisim/awsye-giris) okumanızı tavsiye ederim.

# Anahtar seçimi

Anahtarlarla ilgili bazı kısıtlamalar var, mesela boyut olarak 1024 bayttan büyük olamazlar (UTF-8).
Anahtar formatını nasıl seçmeliyiz?
Bu sorunun cevabı, dosyalara nasıl erişeceğinize göre değişiyor.
Örnek olarak, instagram türü bir uygulama yapmış olsanız ve her kullanıcının yüklediği fotoğrafları S3'te saklayacak olsanız, şöyle bir anahtar seçimi iş görebilir: `{user-id}/foto.jpeg`.
Bu şekilde bir anahtar seçimi yaptığınızda, 123 id'li bir kullanıcıya ait tüm fotoğrafları listelemek için [ListObjects](https://docs.aws.amazon.com/AmazonS3/latest/API/API_ListObjectsV2.html) API metodunu `prefix='123'` ve `delimiter='/'` parametreleriyle çağırıp sadece o kullanıcının fotoğraflarına erişebilirsiniz.

Dikkat ettiyseniz **delimiter** olarak "`/`" kullandık, aslında isterseniz ayraç olarak başka bir karakter de kullanabilirsiniz.
Fakat "`/`" özel bir karakter ve bunu kullandığınızda AWS S3 konsolunda iç içe klasör görünümüne sahip olursunuz.
Dolayısıyla **delimiter** olarak "`/`" güvenli ve doğru (genellikle) bir tercih olacaktır.
Aşağıdaki ekran görüntülerine bakarsanız ne dediğimi daha iyi anlarsınız.

![S3-klasorler][3]
![S3-klasorler-detay][4]

Aynı resme bir de [AWS CLI](https://aws.amazon.com/cli/) aracılığıyla bakalım:

`aws s3api list-objects-v2 --bucket kullanicilar`

```
{
  "Contents": [
    {
      "Key": "123/",
      "LastModified": "2020-05-09T21:01:54.000Z",
      "ETag": "\"d41d8cd98f00b204e9800998ecf8427e\"",
      "Size": 0,
      "StorageClass": "STANDARD"
    },
    {
      "Key": "123/baska-foto.png",
      "LastModified": "2020-05-09T21:03:41.000Z",
      "ETag": "\"ae25c06dacc06bd191b24ff28ca52ce5\"",
      "Size": 31453,
      "StorageClass": "STANDARD"
    },
    {
      "Key": "123/foto.jpg",
      "LastModified": "2020-05-09T21:02:44.000Z",
      "ETag": "\"10f7e69e54361d612d4a326165876884\"",
      "Size": 115774,
      "StorageClass": "STANDARD"
    },
    {
      "Key": "987/",
      "LastModified": "2020-05-09T21:03:54.000Z",
      "ETag": "\"d41d8cd98f00b204e9800998ecf8427e\"",
      "Size": 0,
      "StorageClass": "STANDARD"
    },
    {
      "Key": "987/foto.jpg",
      "LastModified": "2020-05-09T21:04:07.000Z",
      "ETag": "\"10f7e69e54361d612d4a326165876884\"",
      "Size": 115774,
      "StorageClass": "STANDARD"
    }
  ]
}
```

Burada dikkatinizi çekmesi gereken nokta, `123` ve `987` konsolda klasör olarak gözükseler de, aslında bunlar da birer obje.
Boyutları sıfır bayt.
S3 bir güzellik yapıp, normalde hiyerarşik olmayan bir altyapı üzerine bilgisayarınızdaki klasör yapısı gibi bir görünüm sunmuş.

Eğer hangi klasörler var diye listelemek isterseniz **delimiter** parametresini kullanmalısınız:

`aws s3api list-objects-v2 --bucket kullanicilar --delimiter "/"`

```
{
  "CommonPrefixes": [
    {
      "Prefix": "123/"
    },
    {
      "Prefix": "987/"
    }
  ]
}
```

Bir kullanıcıya ait dosyaları listelemek için **prefix** parametresini kullanmalıyız:

`aws s3api list-objects-v2 --bucket kullanicilar --prefix "123/"`

```
{
  "Contents": [
    {
      "Key": "123/",
      "LastModified": "2020-05-09T21:01:54.000Z",
      "ETag": "\"d41d8cd98f00b204e9800998ecf8427e\"",
      "Size": 0,
      "StorageClass": "STANDARD"
    },
    {
      "Key": "123/baska-foto.png",
      "LastModified": "2020-05-09T21:03:41.000Z",
      "ETag": "\"ae25c06dacc06bd191b24ff28ca52ce5\"",
      "Size": 31453,
      "StorageClass": "STANDARD"
    },
    {
      "Key": "123/foto.jpg",
      "LastModified": "2020-05-09T21:02:44.000Z",
      "ETag": "\"10f7e69e54361d612d4a326165876884\"",
      "Size": 115774,
      "StorageClass": "STANDARD"
    }
  ]
}
```

# Consistency modeli

S3, SQS, SNS vb. bulut hizmetleri, dağıtık ve [multi AZ](https://aws.amazon.com/about-aws/global-infrastructure/regions_az/) (birden fazla farklı veri merkezinde replikasyon) mimarilere sahip olduğundan, farklı bazı davranışlar sergileyebiliyorlar.
S3 için bilinmesi gerekenler:

* S3'te yeni bir dosya oluşturduktan sonra, o anahtarın işaret ettiği dosyayı istediğinizde yazmış olduğunuz dosyayı aynen bulursunuz.
Bir başka deyişle, ilk kez yazdığınız bir dosyayı ilk kez okuduğunuzda dosyayı bulamama gibi bir ihtimal yok!
* S3'te hâlihazırda bulunan bir dosyayı güncelledikten sonra hemen okumaya çalışırsanız, dosyanın güncel halini alamayabilirsiniz (buna *eventual consistency* deniyor).
* S3'te bir dosyayı silerseniz ve sonra okumaya çalışırsanız, dosyayı hâlen okuyabilirsiniz, henüz silinmemiş olabilir.

Burada bahsi geçen durumlar milisaniye bazında geçerli de olsa (mesela genelde dosyayı güncelledikten birkaç saniye sonra artık eski versiyonunu okumak gibi bir durum olmuyor), en kötü ihtimale hazır olmalısınız.
Uygulamalarınızı kurgularken, güncellediğiniz dosyanın birkaç saniye sonra bile eski versiyonunu okuma ihtimaliniz olduğunu bilin.

Daha iyi anlamak için bir senaryo üzerinden gidelim.
Bir dosyamız olsun S3'te.
S3 bir veri merkezi arızası ihtimaline karşı, dosyanızı en az üç farklı merkezde depolar (A, B ve C diyelim bunlara).
Dosyayı güncellediğinizde, güncelleme bir veri merkezinden başlayıp diğerlerine asenkron olarak tekrarlanır.
Mesela A'daki dosya güncellenmiş olup bu güncelleme B ve C'ye henüz ulaşmamış olabilir.
Tam bu esnada S3'ten dosyayı okumaya çalışıp, S3 size dosyayı B veya C'den verirse işte o zaman dosyanın eski halini okumuş olursunuz.
Buradaki gecikme birkaç saniye ile sınırlı olsa da, dikkat etmeli ve uygulamanızı bunu göz önünde bulundurarak tasarlamalısınız.

# Bildirimler

S3'ü diğer AWS servisleriyle entegre etmenin en kolay yollarından biri bildirimler.
S3, gerçekleşen bazı olaylar için, belirlediğiniz servislere bildirim gönderebiliyor.
Bunlardan en öne çıkanlar:

* Yeni bir dosya oluşturulması
* Dosya silinmesi

S3 bildirimlerini gönderebileceğiniz servisler ise şunlar:

* [Lambda](/bulut-bilisim/aws-lambda-ornek)
* [SNS](https://aws.amazon.com/sns/)
* [SQS](https://aws.amazon.com/sqs/)

Bildirimleri tüm dosyalar için değil de, **prefix** ve **suffix** kullanarak belirli dosyalar için geçerli kılabilirsiniz.
Maalesef **prefix** ve **suffix** içerisinde *wildcard* (mesela `*`) kullanılamıyor.
Örnek olarak, `kullanici/{id}/metadata.json` şeklinde tüm kullanıcılar için birer dosya tutuyorsanız ve sadece bu tür dosyalar için bildirim almak istiyorsanız "`prefix=kullanici/`" ve "`suffix=metadata.json`" olarak belirlemelisiniz.
Bir başka kısıt ise, birden fazla bildirim tanımladığınızda, **prefix** ve **suffix** ikilileri tamamen ayrık olmalı.
Mesela "`prefix=kullanici/`" ve "`prefix=kullanici/123`" şeklinde iki tanım bir arada yer alamaz.

# Versiyonlama

Klasör (*bucket*) bazında versiyonlama aktifleştirilebiliyor.
Versiyonlama sayesinde, aynı dosyanın farklı versiyonlarını saklayabiliyorsunuz.
Versiyonlamanın faydası, yanlışlıkla bir dosyanın silinmesini veya üzerine yazılmasını engellemesidir.
Versiyonlama aktifken bir dosyayı sildiğinizde aslında dosya silinmiyor, bunun yerine dosyanın yeni bir versiyonu oluşturuluyor ve bu işaretleniyor.
Böylece dosya sanki silinmiş gibi görülüyor ama istediğinizde dosyanın eski versiyonlarını geri alabiliyorsunuz.
Windows'taki geri dönüşüm kutusu gibi düşünebilirsiniz.

Örnek üzerinde gösterecek olursak, diyelim ki versiyonlama aktif olan bir klasörde `123/foto.jpg` dosyasını sildik.
Eğer dosyanın versiyonlarını listelersek ilk versiyonu ve silinme işaretini göreceğiz:

`aws s3api list-object-versions --bucket kullanicilar --prefix "123/foto.jpg"`

```
{
  "Versions": [
    {
      "ETag": "\"10f7e69e54361d612d4a326165876884\"",
      "Size": 115774,
      "StorageClass": "STANDARD",
      "Key": "123/foto.jpg",
      "VersionId": "null",
      "IsLatest": false,
      "LastModified": "2020-05-09T21:02:44.000Z",
      "Owner": {
        "DisplayName": "omerselcukcihan",
        "ID": "da14db3b6ffbc36f6261fa2eb6af8d9ca2f26a075328f619aa51b1345a878e8f"
      }
    }
  ],
  "DeleteMarkers": [
    {
      "Owner": {
        "DisplayName": "omerselcukcihan",
        "ID": "da14db3b6ffbc36f6261fa2eb6af8d9ca2f26a075328f619aa51b1345a878e8f"
      },
      "Key": "123/foto.jpg",
      "VersionId": "chM_qvFv7zrt0JX2xaKmfqILZ5ecdGqq",
      "IsLatest": true,
      "LastModified": "2020-05-09T23:27:58.000Z"
    }
  ]
}
```

Bu dosyayı okumak istediğimizde 404 hatası alırız:

```
aws s3api head-object --bucket kullanicilar --key "123/foto.jpg"

An error occurred (404) when calling the HeadObject operation: Not Found
```

Eğer dosyanın silinmeden önceki versiyonunu okursak, dosyamıza ulaşabiliriz:

```
aws s3api head-object --bucket kullanicilar --key "123/foto.jpg" --version-id null

{
    "AcceptRanges": "bytes",
    "LastModified": "Sat, 09 May 2020 21:02:44 GMT",
    "ContentLength": 115774,
    "ETag": "\"10f7e69e54361d612d4a326165876884\"",
    "VersionId": "null",
    "ContentType": "image/jpeg",
    "Metadata": {}
}
```

`VersionId` **null** olarak görünüyor, çünkü bu dosyayı oluşturduğumuzda henüz klasörde versiyonlama aktif değildi.
Aynı anahtarla tekrar bir dosya yüklersek, S3 dosyaya bir `VersionId` atayacaktır.
Versiyonlamanın aktif olduğu bir klasörde, dosyayı tamamen silmek için tüm versiyonlarını silmek gerekir.
Bu işlemi sadece klasör sahibi yapabilir.
Dosyaların yanlışlıkla silinmesinin önüne geçmek için S3'ün aldığı bir önlem bu.

# Metadata

S3'te saklayabildiğiniz veri sadece dosyanın kendisiyle sınırlı değil.
İsterseniz her dosyaya bir de *metadata* ekleyebilirsiniz.
S3 zaten her dosyaya kendi birtakım *metadata* ekliyor.
Bunlardan bazıları:

* Tarih
* Boyut
* Content-Type (`image/png` veya `application/json` gibi)
* Son değiştirilme tarihi
* MD5 hash

Kendi oluşturmak istediğiniz `metadata` için toplam 2KB'lık bir sınır var.
Instagram örneğinden gidecek olursak, kullanıcının fotoğrafını saklarken `metadata` olarak yer bilgisini göndermek isteyebilirsiniz.

# Çıkarımlar

* **S3 bir dosya sistemi değil**, mesela dosya sisteminizdeki bir dosyanın sonuna ekleme yapabilirsiniz ama S3'teki dosyalarda bu işlemi yapamazsınız.
Aynı etkiyi, dosyayı yeniden yükleyerek elde edebilirsiniz elbette.
* S3 bildirimlerini kullanarak, Lambda, SQS ve SNS ile **sunucusuz** (*serverless*) uygulamalar geliştirebilirsiniz.
* **Consistency model** her zaman aklınızın bir köşesinde dursun.
* **Versiyonlama** sayesinde, dosyalarınızın geçirdiği safhaları ayrı ayrı görebilirsiniz.
* **Anahtar** seçiminizi yaparken, dosyalara nasıl erişeceğinizi iyice düşünün, çünkü sonradan değiştirmek çok masraflı olabilir.

S3 kullanarak geliştirdiğiniz ve anlatmaktan gurur duyacağınız uygulamalarınız varsa, yorumlarda paylaşabilirsiniz :smile:

[1]: /img/s3-puf-noktalar/kitaplar.jpg
[2]: /img/s3-puf-noktalar/s3_hata.png
[3]: /img/s3-puf-noktalar/s3_klasor.png
[4]: /img/s3-puf-noktalar/s3_klasor_detay.png
