---
title: "AWS'ye giriş"
tags: ["aws", "bulut", "fatura", "hesap", "fiyatlandırma", "yetki", "iam", "sdk", "rest"]
date: 2019-10-26T14:41:29+03:00
draft: false
---

Bulut sağlayıcılarından biri olan AWS hangi hizmetleri sunar? AWS hesabı nasıl açılır? Yetki yönetimi nasıl yapılır? 

![Fotoğraf [Unsplash](https://unsplash.com/s/photos/cloud?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) üzerinde [Kaushik Panchal'a](https://unsplash.com/@kaushikpanchal?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) aittir](https://cdn-images-1.medium.com/max/7334/1*LHkOwVtsb3re6LaIcDaM1w.jpeg)*Fotoğraf [Unsplash](https://unsplash.com/s/photos/cloud?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) üzerinde [Kaushik Panchal'a](https://unsplash.com/@kaushikpanchal?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) aittir*

## Bulut sağlayıcıları

Son yıllarda bulut bilişim büyük bir ivmeyle yaygınlaşmakta, verilen hizmetlerin (veritabanı, sanal makina, dosya saklama hizmetleri vs.) çeşitliliği ve kalitesi artıp, fiyatları ise azalmakta. Bu da bilhassa ufak çaplı şirketlerin ve girişimcilerin, ürünlerini **daha çabuk ve daha az masrafla** çıkarabilmesine olanak sağlamakta. Bulut sağlayıcıları (AWS, GCP, Azure vs.) fiyatlandırmayı kullanım miktarı üzerinden yaptığından, **kullanmadığınız kaynaklar için para ödemenize gerek kalmıyor**. Böylelikle, yeni bir fikriniz varsa bunu gerçekleştirmek için altyapıyı (ta ki ürününüz gerçekten kullanılana kadar) bedavaya getirebiliyorsunuz.

AWS, bulut sağlayıcıları arasında en popüler ve en büyük olanı. [Gartner’a göre](https://www.gartner.com/en/newsroom/press-releases/2019-07-29-gartner-says-worldwide-iaas-public-cloud-services-market-grew-31point3-percent-in-2018), 2018 yılında AWS, %47.8 pazar payı ile birinci sırada yer aldı. AWS’nin [100'den fazla ürünü bulunmakta](https://aws.amazon.com/tr/products/?nc1=h_ls) ve bu ürünlerin pek çoğunun [ücretsiz kullanım kotaları](https://aws.amazon.com/tr/free/?all-free-tier.sort-by=item.additionalFields.SortRank&all-free-tier.sort-order=asc) var. Buna sanal makinalar ve veritabanları da dahil. Artık, fikrinizi gerçekleştirmek için, fiziksel bir makina satın almak gibi erken aşamada riskli bir yatırım yapmanıza gerek yok. AWS ile, bir yıl boyunca sanal bir makina ve kurulumu/bakımı AWS tarafından üstlenilen bir veritabanı sunucusunu ücretsiz kullanma hakkına sahipsiniz.

## AWS ürünleri

AWS, sunduğu ürünleri farklı kategoriler altında grupluyor. Bu kategorilerin içinde en başta gelenler şunlar:

* İşlem — sanal makinalar (EC2), lambda ve container’lar

* Veritabanı — SQL, NoSQL ve DWH çözümleri

* Depolama — dosya (S3) veya blok depolama (hard disk) çözümleri

* Analitik — gerçek zamanlı veri (Kinesis) ve big data (Athena, Redshift)

* Entegrasyon — Dağıtık kuyruk (SQS), pub/sub (SNS)

* Ağ — Route53 (DNS) ve CloudFront (CDN), VPC (size özel bir ağ tanımlar)

Birkaç servis hariç (mesela Route53), AWS’nin sunduğu hizmetler bölgeseldir. AWS altyapısını bölgelere ayırmış ve her bir **bölge** coğrafi bir yeri temsil etmektedir. Bölgeler iki veya daha fazla **erişilebilirlik alanı**ndan oluşur(AZ). Erişilebilirlik alanları, bir veya birden fazla veri merkezinden oluşur ve birbirlerinden tamamen izoledirler. Bu sayede doğal afet veya operasyonel problemler sadece belirli bir AZ’yi etkiler ve etki alanı genişlememiş olur. Dolayısıyla eğer yüksek erişilebilirlik gerektiren kritik bir uygulama kurguluyorsanız, sunucularınızı ve verinizi birden fazla AZ’ye yaymalısınız.
> AWS Cloud, dünya çapındaki 22 coğrafi bölgede 69 Erişilebilirlik Alanını kapsar. Ayrıca 9 Erişilebilirlik Alanının yanı sıra Cape Town, Jakarta ve Milano’da üç Bölgenin daha planlandığı açıklanmıştır.

[Fiyatlandırma](https://aws.amazon.com/tr/pricing/) kullanım üzerinden olmakla birlikte, veri transferi ekstra ücrete tabidir. Fiyatlandırmayla ilgili her şey şeffaf, yani hangi hizmet kaç paraya mal oluyor en ince ayrıntısına kadar belirtilmiş. Ücretler genellikle artmıyor, aksine azalıyor (tabi USD üzerinden olduğu için, TL olarak kur farkından ötürü artışlar olabilir). Biraz karmaşık olmakla beraber, iki adet fiyat hesaplama uygulaması mevcut: [https://calculator.s3.amazonaws.com/index.html](https://calculator.s3.amazonaws.com/index.html) ve [https://calculator.aws](https://calculator.aws/)

## AWS hesabı

AWS’yi kullanabilmek için, öncelikle bir [hesap oluşturmalısınız](https://portal.aws.amazon.com/gp/aws/developer/registration/index.html). Hesap oluşturmak ücretsizdir ve hesap oluşturduğunuz tarihten itibaren 12 ay boyunca ücretsiz kullanım kotanız tanımlanmış olur. Hesap oluşturmak için email adresi, telefon numarası, adres ve kredi kartı isteniyor. Bir email adresiyle birden fazla hesap oluşturulamamaktadır. Telefon numarası doğrulanacağı için, gerçek bir numara kullanmalısınız. Kredi kartı olarak düşük limitli bir sanal kart verirseniz kafanız rahat eder, yanlışlıkla ücretsiz kotanızı aşarsanız minimum hasarla kurtarmak için en basit yöntem bu. Şöyle de bir uyarı çıkacaktır, denemek için bir miktar ücret alıp geri yüklüyorlar:
> Ödeme bilgilerinizi gönderdiğinizde kartınızın geçerli olup olmadığından emin olmak için kredi kartınıza doğrulama ücreti olarak 1 USD’lik/EUR’luk ücret yansıtacağız. Doğrulama tamamlanana kadar bu tutar kredi kartınızın kullanım özetindeki bekleyen işlemler kısmında 3–5 gün boyunca görünebilir ve bu süreden sonra silinir. Doğrulama ücreti için yetki vermek üzere bankanızın internet sitesine yönlendirilebilirsiniz.

Telefonunuzu doğrulayıp üye olduktan sonra, ilk önce hangi destek planını seçmek istediğinizi soracak:

* Basic (ücretsiz)

* Geliştirici (ayda 29$)

* İş (business, ayda 100$)

Basic planı seçtiğinize emin olun :)

Son olarak, size rolünüzü ve ilgi alanlarınızı soracak, buradaki cevaplarınız AWS konsolunda size özel öneriler gelmesini sağlayacaktır, çok da önemli değil açıkçası.

Bölgelerden bahsetmiştik, AWS hesapları global olup herhangi bir bölgeye özel değildir. Fakat hesaba ait kaynaklar ve kullanılan servisler bölgeseldir. Mesela bir sanal makina başlatacaksanız, size hangi bölgede başlatmak istediğiniz sorulacaktır.

Hesabınız açıldıysa, AWS konsoluyla tanışma vakti gelmiştir: [https://console.aws.amazon.com](https://console.aws.amazon.com/) burada hesabı açarken belirttiğiniz email adresi ve parolayla giriş yapabilirsiniz. Maalesef konsolda henüz Türkçe seçeneği bulunmamakta. Sağ üst köşede hesabınızla ilgili işlemleri yapıp, AWS’de kullandığınız bölgeyi değiştirebilirsiniz. Adres çubuğunda da gördüğünüz üzere, bulunduğumuz bölge “us-east-2” yani Ohio bölgesi.

![](https://cdn-images-1.medium.com/max/5720/1*0t7KU7X73vU6U1kgbNlKuQ.png)

AWS’yi yeni kullanmaya başlayan, Türkiye’de yaşayan bir kişi için en uygun bölge “eu-west-1” yani İrlanda. Hem ülkemize yakın olduğundan ağ gecikmesi diğer bölgelere göre daha az, dolayısıyla erişimimiz daha hızlı, hem de ilk kurulan bölgelerden biri olduğundan yeni AWS hizmetlerinin bu bölgeye daha erken gelme ihtimali yüksek. Hizmetler bölgesel olduğundan, bazı hizmetler bir bölgede bulunurken başka bir bölgede henüz gelmemiş olabiliyor.

## Yetki ve erişim

Global olan ve diğer hizmetlerden oldukça farklı olan bir AWS ürünü var, “IAM”. IAM, AWS hesabınıza ve kullandığınız kaynaklara (veritabanı, sanal makina vs.) kimlerin erişebileceğini belirlediğiniz bir hizmet olup, içerisinde şu kavramları barındırmakta:

* Kullanıcı: Hesabınıza başka kullanıcılar ekleyebilirsiniz, bunları gerçek birer şahıs olarak düşünün, isterseniz onlar da konsola erişebilirler.

* Grup: Kullanıcıları gruplandırmak için kullanılır, bir kullanıcının yetkileri değerlendirilirken, üyesi olduğu gruptan gelen yetkileri de işin içine girer.

* Rol: Bunu servis kullanıcısı olarak düşünebilirsiniz, mesela sanal makinanızın S3'e erişmesini istiyorsanız, bir rol oluşturup o role S3 yetkisi verirsiniz ve daha sonra bu rolü sanal makinaya eklediğinizde artık sanal makinanız S3'e erişebilir hale gelir.

* Policy (yetki tanımı): IAM’in en ufak yapı taşıdır diyebiliriz, bunlarla yetki tanımları yapabilirsiniz. Mesela, yukarıdaki örnekte aslında S3 yetkisi için bir policy oluştururuz ve daha sonra bu yetki tanımını role ekleriz. Benzer şekilde, bir kullanıcıya yetki vereceğimiz zaman, o kullanıcıya ilgili yetki tanımlarını ekleriz. Rollere veya kullanıcılara birden fazla yetki tanımı eklenebilir ve aynı yetki tanımı birden fazla rolde veya kullanıcıda kullanılabilir.

AWS hizmetlerine erişim için iki temel yöntem var:

1. Konsol üzerinden: Mesela konsoldan S3'e girip dosyalarınızı listeleyebilirsiniz. Bu sayede, kullanımı kolay bir görsel arayüz ile muhattap olup işlemlerinizi basitçe halledebilirsiniz.

1. Kod ile: Mesela bir kod parçacığı ile AWS servislerini çağırabilirsiniz. Bunun için bir CLI (komut satırı), API (mesela [S3 REST API](https://docs.aws.amazon.com/en_us/AmazonS3/latest/API/Welcome.html)) veya kullandığınız programlama dili için AWS’nin sunduğu bir SDK kullanabilirsiniz (mesela [javascript S3 SDK](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/S3.html)).

Konsol için kimlik doğrulama, kullanıcı adı ve şifreyle olmakta. Buna bir de MFA yani ekstra bir güvenlik aşaması daha ekleyebilirsiniz (mesela cep telefonunuza gelecek bir şifre gibi). Kod ile erişim ise ya kullanıcınıza ait erişim anahtarıyla veya yukarıda bahsettiğimiz rollerle gerçekleşmektedir. Tavsiye edilen her zaman rolleri kullanmaktır çünkü bu sayede erişim anahtarıyla uğraşmamış olursunuz. Erişim anahtarını kullanmak bir güvenlik zaafiyetine yol açabilir çünkü erişim anahtarı başkasının eline geçerse, sizin adınıza AWS servislerini çağırıp bütün altyapınızı darmadağın edebilirler.

Güvenlik açısından bir diğer önemli husus da, ister kullanıcı olsun ister rol olsun mutlaka size yeten, yani işinizi görecek olan en küçük yetki kümesini kullanın. Mesela S3'e erişim için bir rol oluşturacaksanız, tutup o role başka servis yetkileri de vermeyin. Bu sayede olası bir hack’lenme durumunda, etki alanını daraltmış olursunuz.

AWS hizmetlerini kullanmayı düşünüyor ve yetkinliğinizi geliştirmek istiyorsanız, [diğer makalelerimizi](/tags/aws) de okuyun. Bunun yanında, kurumsal ve startup firmalara sağladığımız [eğitim hizmetine](https://www.bulutla.com/) bir göz atmayı da unutmayın!
