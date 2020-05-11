---
title: "S3 ile statik websitesi host etmek"
tags: ["aws", "bulut", "s3", "statik web sitesi", "html", "web uygulaması", "depolama", "kova", "bucket", "yetkilendirme", "public", "ücretsiz"]
date: 2019-10-31T14:41:29+03:00
draft: false
---

AWS’nin en eski hizmetlerinden biri olan S3 ile websitesi host etmek oldukça kolay. Birkaç basit ayardan sonra, websiteniz ziyaretçilerinizin kullanımına hazır hale gelecek.

![Fotoğraf [Unsplash](https://unsplash.com/s/photos/web-hosting?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) üzerinde, [Genta Mochizawa'ya](https://unsplash.com/@gentam?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) aittir](https://cdn-images-1.medium.com/max/5280/1*MhWkE5F0Tw9C8NvEQTBNLQ.jpeg)*Fotoğraf [Unsplash](https://unsplash.com/s/photos/web-hosting?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) üzerinde [Genta Mochizawa'ya](https://unsplash.com/@gentam?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) aittir*

## S3 nedir ve ne işe yarar?

AWS 14 Mart 2006'da S3 hizmetini başlatmıştı. Günümüzde, **10 yılı aşkın** bir süredir hayatta olan S3, AWS’nin en oturmuş ve en kullanışlı hizmetlerinden biri. S3, adından da anlaşılacağı üzere (Simple Storage Service) oldukça basit bir amaca hizmet etmekte: *dosya saklama*. S3 ile her türlü dosyayı saklayıp istediğinizde erişebiliyorsunuz. Dosya saklamanın yanı sıra, S3'ün bir diğer kullanışlı özelliği de **statik websitesi** host edebilmek. S3'ü kullanarak statik websitemizin dosyalarını (html, js, css, media dosyaları vs.) yükledikten sonra basit birkaç ayarla birlikte websitemizi kullanıma açabiliriz.

## Bazı bilgiler

* S3 jargonunda, dosyalarınıza “nesne” (object), dosyalarınızın barındığı klasörlere ise “kova” (bucket) denilmekte.

* Kovalar belirli bir bölgede bulunuyor fakat kovanıza verdiğiniz ismin tüm AWS genelinde benzersiz olması gerekiyor, aynı isimde iki kova farklı AWS hesapları veya bölgelerinde bile olsanız oluşturulamıyor.

* S3 kullanım ihtiyacınıza göre bazı [depolama sınıflarına](https://aws.amazon.com/tr/s3/storage-classes/?nc1=h_ls) bölünmüş durumda ve farklı sınıfların farklı fiyatlandırmaları var.

* Sizin şu aşamada **depolama sınıflarını bilmenize gerek yok**, S3 standard sınıfı genel amaçlı olup bizim de işimizi görecek olan varsayılan depolama sınıfıdır.

* S3, AWS’nin **ücretsiz** kullanım kotasına dahil ettiği hizmetlerden biridir ve acaba ücret yansıtırlar mı diye düşünmeden gönül rahatlığıyla kurcalayabilirsiniz çünkü kullanım kotası [oldukça cömert](https://aws.amazon.com/tr/s3/pricing/?nc=sn&loc=4), biraz zor aşarsınız kotayı (kotanızda 5GB depolama alanı ve 2000 dosya barındırma hakkınız var).

* S3'te iki tür yetkilendirme var, birincisi kova bazında yetkilendirme ikincisi ise dosya bazında. Bu iki tür yetkilendirme de dikkate alınarak herhangi bir kullanıcının dosyaya erişip erişemeyeceğine karar veriliyor. Her zaman olduğu gibi, burada da kısıtlayıcı yetkilendirme izinlere üstün geliyor. Mesela, dosyaya okuma yetkisi vermediyseniz kova bazında yetki olsa bile dosyaya erişemeyeceksiniz.

* [Kova bazındaki yetkilendirme](https://docs.aws.amazon.com/AmazonS3/latest/dev/example-bucket-policies.html), json formatında bir tanımla yapılıyor.

* Varsayılan davranış olarak, bir kova oluşturduğunuzda o kovadaki dosyaları sadece siz görüntüleyebiliyorsunuz. Dosyalarınızı herkesin görüntüleyebilir hale gelmesi için daha sonra bahsedeceğimiz üç aşamalı bir ayar yapmanız gerekiyor. AWS, dosyalarınızı yanlışlıkla herkesin erişimine açmayasınız diye bu işi biraz meşakkatli hale getirmiş.

## Kollarınızı sıvayın, websitemiz geliyor

AWS konsoluna giriş yapıp, S3 sayfasını açalım.

![Karşınıza gelen sayfada, hesabınızdaki kovalar listeleniyor.](https://cdn-images-1.medium.com/max/5560/1*goQRYKrNHga2C0DE2DtPuQ.png)*Karşınıza gelen sayfada, hesabınızdaki kovalar listeleniyor.*

Şimdi bir kova oluşturalım, ilk adımda kovanıza bir isim veriyorsunuz (websitenizin adresinde bu kova ismi de geçecek). “Create bucket” butonuna tıklayın. Kovanızın ismini girin ve hangi bölgede oluşturmak istediğinizi seçin. Mesela Türkiye’ye yakın bir bölge sayılabilecek olan “eu-west-1” seçilebilir.

![Kovaya istediğiniz ismi verin, bazı isimler önceden alınmış olabilir, bu durumda başka bir isim seçmekten başka çareniz yok.](https://cdn-images-1.medium.com/max/4324/1*ig7ovpd9U9wFl0Ty0Lyyeg.png)*Kovaya istediğiniz ismi verin, bazı isimler önceden alınmış olabilir, bu durumda başka bir isim seçmekten başka çareniz yok.*

Bir sonraki adımda, versiyonlama ve CloudWatch ile websitenize gelen isteklerin istatistiklerini kaydetme gibi bazı seçenekler sunuluyor, bu sayfada hiçbir değişiklik yapmadan “Next” butonuna basın. Bir sonraki adımda, az önce bahsettiğimiz, dosyaların herkes tarafından görülmesini engelleyen önlemi kaldırmamız gerekiyor. Websitemizi ziyaretçilerin görüntüleyebilmesi için “Block all public access” seçimini kaldırıp “Next” diyoruz ve son olarak tüm seçimlerimizin bir özetini görüp “Create Bucket” butonuna tıklıyoruz.

![“Block all public access” seçimini kaldırdığınızdan emin olun ve devam edin.](https://cdn-images-1.medium.com/max/4312/1*cFjTWStvjBeAePBCyKiNdg.png)*“Block all public access” seçimini kaldırdığınızdan emin olun ve devam edin.*

S3 konsolunun anasayfasına geri döndüğünüzde, yeni oluşturduğunuz kovayı göreceksiniz.

![Dikkat ettiyseniz, “Access” kısmında “Objects can be public” yazıyor, yani dosyalarımız herkesin erişimine açık olabilecek.](https://cdn-images-1.medium.com/max/5664/1*jCcCTfdF39VyrQYhugCjew.png)*Dikkat ettiyseniz, “Access” kısmında “Objects can be public” yazıyor, yani dosyalarımız herkesin erişimine açık olabilecek.*

Burada önemli bir ayrıma dikkat çekmek istiyorum. Henüz kovamızdaki nesnelere herkesin erişebilmesi için yetki vermedik, sadece herkesin erişebilmesini engelleyen önlemi kaldırdık. Dosyaları ekledikçe dosya bazında ayrıca yetki vermemiz gerekecek yoksa bizden başkası dosyalarımızı görüntüleyemeyecektir. Bir başka deyişle, kovamızdaki nesneleri herkesin erişimine açılabilir hale getirdik ama aslında henüz erişime açmadık. Bu önlem sayesinde, eğer bu ayarı yapmasaydık, yetki vererek bile erişime açamazdık.

Şimdi dosyamızı ekleme vakti geldi, basit bir örnek üzerinden gidip sadece bir index.html dosyası yükleyeceğiz.

<script src="https://gist.github.com/selcukcihan/9e6b2589f5dceb88cb2f5fb0558e4fde.js"></script>

Dosyayı bilgisayarınıza kaydettikten sonra, S3 konsolunda kovanıza tıklayın ve sol üstteki “Upload” butonuna basın ve sonrasında “Add files” butonuna basarak dosyanızı seçin ve sağ alttaki “Next” butonuna basın.

![index.html dosyasını eklemeye başladık, şimdi dosyayla alakalı bazı ayarlar yapmamız gerekecek. Bu yüzden hemen “Upload” butonuna **basmayıp** “Next” diyerek ek ayarlarımızı yapacağız.](https://cdn-images-1.medium.com/max/4320/1*tu9-gPzZYxrPfXKpGQ5MrQ.png)*index.html dosyasını eklemeye başladık, şimdi dosyayla alakalı bazı ayarlar yapmamız gerekecek. Bu yüzden hemen “Upload” butonuna basmayıp “Next” diyerek ek ayarlarımızı yapacağız.*

Dosya bazındaki erişim yetkilerini, websitemize herkesin erişebilmesi için uygun şekilde ayarlamalıyız. Bunun için, “Grant public read access to this object(s)” seçeneğini işaretlemeliyiz.

![Eğer dosyamızın erişimini herkese **açmazsak**, kovamız “public” dahi olsa dosyamıza ziyaretçiler **erişemeyecektir**.](https://cdn-images-1.medium.com/max/4324/1*4oLOZewhIlL5vCM5IFIB6A.png)*Eğer dosyamızın erişimini herkese açmazsak, kovamız “public” dahi olsa dosyamıza ziyaretçiler erişemeyecektir.*

Bir sonraki aşamada, dosyamızın hangi depolama sınıfında yer almasını istediğimizi seçiyoruz, varsayılan ayar olan “Standard” seçimi işimizi görecektir, “Next” butonuna tıklayın.

![Bu sayfada varsayılan seçimlerle “Next” butonuna basın.](https://cdn-images-1.medium.com/max/4292/1*WgYe2ORgktrGU2J1Odtx6Q.png)*Bu sayfada varsayılan seçimlerle “Next” butonuna basın.*

Dosyamızı yükledik, şimdi statik websitesi hosting seçeneğini açıp websitemize ait bir URL elde etmenin vakti geldi.

1. “Properties” tabına tıklayın.

1. “Static website hosting” kutucuğuna tıklayın.

1. “Use this bucket to host a website” seçeneğini işaretleyip, “Index document” kısmına index.html yazın.

1. “Endpoint” adresini kaydedin, websitenize bu adres ile ulaşacaksınız, şimdi “Save” butonuna tıklayın ve değişikliklerinizi kaydedin.

![Değişiklikleri kaydettiğinize emin olun.](https://cdn-images-1.medium.com/max/5548/1*Db9YkYkvrdrXmqpJVTDNBQ.png)*Değişiklikleri kaydettiğinize emin olun.*

Eğer bir problem yaşamadıysanız, elinizde [http://statik-web-sitem.s3-website-eu-west-1.amazonaws.com/](http://statik-web-sitem.s3-website-eu-west-1.amazonaws.com/) gibi bir URL olacak. Şimdi bu URL’ye tıklayın ve websitenizi açmaya çalışın, şöyle bir hata alacaksınız:

![Yapmamız gereken son bir ayar daha var!](https://cdn-images-1.medium.com/max/3112/1*axEv4nXNPWLC-B1aWkfBZQ.png)*Yapmamız gereken son bir ayar daha var!*

Kovamız “herkesin erişimine açık olabilir” olarak ayarlanmıştı fakat herkesin erişimine açmamıştık, sadece açık olabilmesinde bir sakınca olmadığını belirtmiştik. Bu kadar önlem size aşırı gelmiş olabilir ama S3'te yanlış erişim ayarları yüzünden erişmemesi gereken kişiler tarafından erişilen o kadar dosya oldu, o kadar facialar yaşandı ki, Netflix gibi firmaların bile bu hataya düştüğü görüldü. Araştırmak isterseniz Google’a “aws s3 leaky buckets” yazın.

Şimdi son güvenlik aşamasını da tamamlamak için, “Permissions” tabına tekrar gelin ve “Bucket policy” kısmını açıp aşağıdaki erişim yetkisi tanımını yapıştırın.

<script src="https://gist.github.com/selcukcihan/79c87952124b0c5aaeb5cf31e8f3b8db.js"></script>

![Eğer bu yetki tanımını doğru biçimde yaptıysanız, “Save” butonuna basınca artık turuncu renkte “Public” yazısını göreceksiniz.](https://cdn-images-1.medium.com/max/5260/1*ZM2yvVw8QKxMPoZeBdMKOw.png)*Eğer bu yetki tanımını doğru biçimde yaptıysanız, “Save” butonuna basınca artık turuncu renkte “Public” yazısını göreceksiniz.*

Turuncu “public” yazısını gördüyseniz, artık websiteniz ziyaretçilerin erişimine açık, önceki adımda sakladığınız endpoint URL’ine tıklayıp websitenizi açın.

![Biraz basit kaçtı galiba :) Ama vurgu yapmak istediğimiz şey S3 ile websitenizi **nasıl** host edeceğinizdi, websitenizi nasıl tasarlarsınız o apayrı bir mevzu.](https://cdn-images-1.medium.com/max/3536/1*B33GJIdzAuD6fDWGh9H4iQ.png)*Biraz basit kaçtı galiba :) Ama vurgu yapmak istediğimiz şey S3 ile websitenizi nasıl host edeceğinizdi, websitenizi nasıl tasarlarsınız o apayrı bir mevzu.*

S3 ile ilgili dikkat edilmesi gereken önemli hususlar için, [ilgili yazıyı](/bulut-bilisim/s3-puf-noktalar) okuyabilirsiniz.
S3 ve diğer AWS hizmetlerini kullanmayı düşünüyor ve yetkinliğinizi geliştirmek istiyorsanız, kurumsal ve startup firmalara sağladığımız [eğitim hizmetine](https://www.bulutla.com/) bir göz atmayı unutmayın!
