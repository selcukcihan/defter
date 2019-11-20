---
title: "AWS - İlk adımlar"
tags: ["aws", "cli", "bulut", "fatura", "hesap", "CloudWatch", "monitoring", "alarm"]
date: 2019-10-28T14:41:29+03:00
draft: false
---

AWS kullanmaya karar verdim, ilk yapmam gerekenler neler? CLI uygulaması nasıl kullanılır? Fatura alarmı nasıl oluşturulur?

![Fotoğraf [Unsplash](https://unsplash.com/s/photos/start?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) üzerinde [Matt Duncan'a](https://unsplash.com/@foxxmd?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) aittir](https://cdn-images-1.medium.com/max/6000/1*QEe2Ts6H_h5HkMEk-Pm1Dg.jpeg)*Fotoğraf [Unsplash](https://unsplash.com/s/photos/start?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) üzerinde [Matt Duncan'a](https://unsplash.com/@foxxmd?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) aittir*

## Bir kullanıcı oluşturalım

AWS hesabını açtığınızda, zaten bir kullanıcınız oluyor fakat bu kullanıcı “root user” denilen özel bir kullanıcı ve bu kullanıcıyla işlem yapmak tavsiye edilmiyor. *Root *tüm yetkilere sahip olup, yanlış ellere geçtiğinde hesabınıza geri dönüşü olmayacak şekilde zarar verebilir.

1. Root kullanıcımızla konsola [giriş](https://console.aws.amazon.com/) yapıyoruz.

1. Sağ üst köşede, hesabımızın detaylarına girerek “Account ID”yi not edelim, yeni kullanıcımızla giriş yaparken bu ID’ye ihtiyacımız olacak.

![Bütün hesapların bir ID’si var.](https://cdn-images-1.medium.com/max/2196/1*XeRJx9-34eIdT6lAL_TfwQ.png)*Bütün hesapların bir ID’si var.*

3. Sol üst köşede “services” kısmını açıp, IAM yazıyor ve yetki yönetiminin yapıldığı AWS hizmeti olan IAM’i [açıyoruz](https://console.aws.amazon.com/iam/home).

![AWS 100'den fazla hizmet barındırmakta, bu hizmetleri kolayca bulmak için “Services” kısmını kullanabilirsiniz.](https://cdn-images-1.medium.com/max/2472/1*1SYaFe4RmvBt7ZGCoD1BFw.png)*AWS 100'den fazla hizmet barındırmakta, bu hizmetleri kolayca bulmak için “Services” kısmını kullanabilirsiniz.*

4. Karşımıza gelen ekranda, “Security Status” kısmında bazı tavsiyeler göreceksiniz, sağlam bir güvenlik için bunların her biri oldukça önemli.

5. “Users” kısmına tıklayın ve “Add user” butonuna basarak ilk kullanıcınızı oluşturun:

![İsterseniz şifreyi oluştururken kendiniz de belirleyebilirsiniz, yine isterseniz yeni kullanıcı ilk giriş yaptığında şifreyi reset’lemeyi mecbur tutabilirsiniz.](https://cdn-images-1.medium.com/max/2004/1*VWwI17VNi1O21eNxNylVuQ.png)*İsterseniz şifreyi oluştururken kendiniz de belirleyebilirsiniz, yine isterseniz yeni kullanıcı ilk giriş yaptığında şifreyi reset’lemeyi mecbur tutabilirsiniz.*

Bir sonraki adımda, kullanıcının yetkilerini belirleyeceğiz. Burada ideal olan, bir grup oluşturmak ve yetkileri gruba tanımladıktan sonra kullanıcıyı bu gruba eklemek. Fakat şimdi hızlıca, yetkiyi doğrudan kullanıcıya atayacağız. Yetki tanımları(policy) iki türdür: AWS’nin herkesin kullanımına sunduğu genel tanımlar ve kullanıcının kendi oluşturduğu tanımlar. Biz burada AWS’nin “AdministratorAccess” yani tüm yetkileri içinde barındıran yetki tanımını kullanacağız. İsterseniz daha az yetkiye sahip bir tanım kullanabilirsiniz tabi. Her zaman, ihtiyaç duyulan en küçük yetki tanımını kullanmak sizi daha sonra büyük baş ağrılarından kurtarır. AWS de bunu [tavsiye etmekte](https://docs.aws.amazon.com/en_us/IAM/latest/UserGuide/best-practices.html).

![](https://cdn-images-1.medium.com/max/2020/1*744n-GyXJc00H8bSoRkn4A.png)

Son olarak, kullanıcınızın “Access key ID” ve “Secret access key” lerinin içeren .csv uzantılı dosyayı bilgisayarınıza indirin ve güvenli bir şekilde saklayın. Bu iki anahtarı birlikte kullanarak, AWS hizmetlerini çağırabilirsiniz. Benzetme yapmak gerekirse, “Access key ID” aslında kullanıcı adınız, “Secret access key” ise şifreniz diyebiliriz. Ama bunlar kodsal erişim için geçerli, bunun haricinde zaten bir kullanıcı adı ve şifreniz var ve bir sonraki adımda mevcut “Root” kullanıcısıyla çıkış yapıp, yeni oluşturduğumuz kullanıcıyla konsola giriş yapacağız.

![](https://cdn-images-1.medium.com/max/2030/1*UGXmIIkshsRrCufK0ghfhA.png)

6. Yeni oluşturduğunuz kullanıcıyla konsola giriş yapın. İlk adımda not ettiğimiz “Account ID”yi burada kullanacağız.

![Oluşturduğunuz kullanıcıyla giriş yapmak için “Account ID” gerekiyor.](https://cdn-images-1.medium.com/max/2000/1*H8NSM1lCFXenA4_O_gFiHQ.png)*Oluşturduğunuz kullanıcıyla giriş yapmak için “Account ID” gerekiyor.*

## CLI kullanımı

AWS’ye konsol veya kod ile erişilebiliyor. Kod ile erişim ise üç farklı şekilde olabiliyor, bunlardan ilki [CLI](https://aws.amazon.com/tr/cli/) yani komut satırı uygulamasıyla. Bunun için öncelikle CLI uygulamasını [kurmalıyız](https://aws.amazon.com/tr/cli/). Uygulamayı kurduktan sonra, aws helpkomutu ile yardım sayfasını görüntüleyebilirsiniz. Şimdi, kullanıcı oluştururken bilgisayarımıza indirdiğimiz .csv dosyasını kullanarak CLI uygulamasına anahtarlarımızı tanıtmalıyız ki bu anahtarları kullanarak kendi hesabımız üzerinde işlemler yapabilelim. Bunun için aws configure komutunu kullanacağız, bu komut özelinde yardım sayfasını görüntülemek için aws configure help komutunu kullanabilirsiniz.

Önceki adımda indirdiğimiz .csv dosyasını bir text editörüyle açın ve daha sonra komut satırında şu komutu çalıştırın: aws configure --profile yeni_profil ve size sorulan sorulara .csv dosyasındaki anahtarları kullanarak cevap verin:

![](https://cdn-images-1.medium.com/max/2032/1*hEyhYzncRVXtoeSHHk_9Xw.png)

Bu işlemden sonra, “Home” klasörünüze baktığınızda .aws adında gizli bir klasör göreceksiniz, bu klasörün içerisinde az önce girdiğiniz anahtarlarınız CLI kullanımına hazır halde saklanmaktadır. CLI uygulaması, bu klasörü tarayarak ilgili profilin anahtarlarını bulur ve o servisleri profildeki kullanıcı adına çağırır.

![aws sts komutu ile istek yapan kullanıcının kimlik bilgisi öğrenilebilir.](https://cdn-images-1.medium.com/max/2000/1*x-oIVsWRAiED5PVgX8MOTA.png)*aws sts komutu ile istek yapan kullanıcının kimlik bilgisi öğrenilebilir.*

CLI key’lerini doğru ayarlayıp ayarlamadığınızı denemek için aws sts get-caller-identity --profile yeni_profil komutunu çalıştırın ve sonuç olarak bu komutu çalıştırırken hangi kullanıcı ile çalıştırıldığını gösteren bir değer dönecektir. Eğer key’leri yanlış girmiş olsaydınız şöyle bir sonuç dönecekti:

![AWS hizmetleri REST API’ler ile sunulmakta ve API’lere yapılan istekler geçerli imzalara sahip olmalı, AWS CLI uygulaması da, istek yaparken, ayarladığınız key’leri kullanarak istekleri imzalıyor. Yanlış bir key girdiyseniz, imza geçersiz olacağından hata alırsınız.](https://cdn-images-1.medium.com/max/3572/1*AH1DeJ6-Arf3ErrZr9sJ-A.png)*AWS hizmetleri REST API’ler ile sunulmakta ve API’lere yapılan istekler geçerli imzalara sahip olmalı, AWS CLI uygulaması da, istek yaparken, ayarladığınız key’leri kullanarak istekleri imzalıyor. Yanlış bir key girdiyseniz, imza geçersiz olacağından hata alırsınız.*

## İlk alarmımız

Bir uygulama, sadece sunduğu değerle veya yaptığı işle değerlendirilmemeli. İyi bir uygulamanın bakımı kolay olmalıdır. Mesela bir hata aldığında son kullanıcıya bunu bildirmenin dışında, uygulamanın sahibinin de bu durumdan haberdar olması gerekir. Bunun için alarmlar kullanmak gerekir. Mesela web sayfanız son beş dakikada 10'dan fazla hata aldıysa, demek ki bir şeyler yanlış gitmeye başladı ve bu durumdan ne kadar erken haberdar olursanız problemi de o kadar erken çözersiniz. Uygulama alarmlarını, arabalardaki uyarı lambalarına benzetebiliriz. Nasıl ki aracın yağı eksildiyse yağ lambası yanar. Siz de hemen kenara çekip kitapçığı açıp uyarının anlamına bakarsınız ve gerekeni yaparsınız. İyi bir alarm, gerçekleşmesi durumunda ne yapılması gerektiğini de söyleyen alarmdır. Mesela uygulamanızın kurulu olduğu sanal makinada depolama alanı bitmek üzereyse, bununla ilgili kurduğunuz alarm aktif hale geldiğinde size depolama alanı bitince ne yapılması gerektiğini de anlatan bir bildirim gelmesi problemi çabucak çözmek için elzemdir.

Alarm tanımını yaptıktan sonra, sırada ilk alarmımızı oluşturmak var. Elbette henüz bir uygulamamız yok fakat AWS üzerinde alarm oluşturmak için bir uygulamaya ihtiyacınız da yok. Hesabınızın faturası belirli bir sınırı aştığında email’inize otomatik olarak bildirim gönderen bir alarm kurabilirsiniz. Bunun için aşağıdaki işlemleri takip edebilirsiniz:

1. Öncelikle oluşturduğumuz kullanıcıya Fatura servisini kullanma yetkisi tanımlamalıyız. Her ne kadar kullanıcımıza “Admin” yetkisi vermiş olsak da, AWS Fatura servisinin kullanımı için hususi olarak bir izin daha vermek gerekiyor:

![](https://cdn-images-1.medium.com/max/2000/1*yI4ljwqM4w_JrVaqc5VTyQ.png)

Bunun için mevcut kullanıcıdan çıkıp, “Root” ile giriş yapmamız gerekiyor. Giriş yaptıktan sonra, [hesap sayfanıza](https://console.aws.amazon.com/billing/home?#/account) girip izni aşağıdaki gibi verebilirsiniz.

![](https://cdn-images-1.medium.com/max/2000/1*UPES9ABEBr-L2hwchUOa_w.png)

2. “Root” kullanıcısından çıkabilirsiniz, yeni oluşturduğunuz kullanıcıyla giriş yapıp alarmımızı bu kullanıcıyla oluşturalım, ne de olsa “Root” kullanıcısını günlük işlerimizi halletmek için kullanmak güvenlik açısından tavsiye edilmiyor. AWS üzerinde alarmlar “CloudWatch” adı verilen hizmet üzerinden ayarlanıyor. Konsoldan “services” kısmına “CloudWatch” yazarak [CloudWatch konsoluna](https://console.aws.amazon.com/cloudwatch/home?region=us-east-1) geçin. Sol tarafta “Alarms” altında “Billing”e tıklayın (Eğer bölgeniz “us-east-1” yani N.Virginia değilse bir uyarı çıkacak, bu durumda uyarıdaki linke tıklayıp doğru bölgeye geçiş yapın).

3. Sayfanın altında yer alan “Create alarm” butonuna tıklayarak alarmı oluşturmaya başlayalım. “Conditions” altında, hangi durumlarda alarmın aktif hale geçeceğini seçeceğiz.

![Belirlediğiniz değeri alttaki kutucuğa giriniz. Varsayılan para birimi USD’dir.](https://cdn-images-1.medium.com/max/2000/1*q1E9wD4sFxySMNVTTgEiyA.png)*Belirlediğiniz değeri alttaki kutucuğa giriniz. Varsayılan para birimi USD’dir.*

“Next”e tıklayıp bir sonraki adımda alarm halinde gönderilmesini istediğimiz bildirim türünü seçeceğiz. “Create new topic” seçerek yeni bir bildirim konusu oluşturmamız gerekiyor. Email kısmına da email adresinizi girin ve “Create topic” butonuna tıklayarak bildirim konusunu oluşturun.

![AWS bildirimler için SNS adını verdiği hizmeti kullanıyor ve SNS “topic” yani bildirim konuları ile bildirimleri gruplandırıyor. Her bildirim türü bir “topic”e ait olmak zorunda.](https://cdn-images-1.medium.com/max/2000/1*8tnGk4KXL4vuksZSRq1veA.png)*AWS bildirimler için SNS adını verdiği hizmeti kullanıyor ve SNS “topic” yani bildirim konuları ile bildirimleri gruplandırıyor. Her bildirim türü bir “topic”e ait olmak zorunda.*

4. “Next” butonuna tıklayarak bir sonraki sayfaya geçin ve alarmınıza bir isim verin ve opsiyonel olarak bir tanım ekleyin ve tekrar “Next”e basıp gelen ekranda seçeneklerinizi gözden geçirip “Create alarm” butonuna basın.

![Alarma bir isim vermek mecburi.](https://cdn-images-1.medium.com/max/2000/1*Inq2gcFe_g_PiLfq9Zhelg.png)*Alarma bir isim vermek mecburi.*

İlk alarmınız hayırlı olsun, dikkat ettiyseniz bir uyarı var, bunun sebebi bildirim gelmesini istediğiniz email adresinizin kullanılabilmesi için henüz onay vermemiş olmanız, şimdi emailinizi açın ve gelen onay mailine onay vererek süreci tamamlayın. Artık AWS faturanız belirlediğiniz rakamın üzerine çıkarsa, email adresinize bir bildirim maili gelecek. Diyelim ki deneme amaçlı bir EC2 sanal makina başlattınız ve kapatmayı unuttunuz, fatura alarmı sayesinde çok geç olmadan bu durumdan haberdar olup gerekli tedbiri alabileceksiniz.

![Alarm oluşturuldu fakat son bir adım daha var, emailinizden onay vermeniz gerekiyor.](https://cdn-images-1.medium.com/max/2000/1*5TuHgu1srh9uVRPQROe1xQ.png)*Alarm oluşturuldu fakat son bir adım daha var, emailinizden onay vermeniz gerekiyor.*

AWS hizmetlerini kullanmayı düşünüyor ve yetkinliğinizi geliştirmek istiyorsanız, [diğer makalelerimizi](/tags/aws) de okuyun. Bunun yanında, kurumsal ve startup firmalara sağladığımız [eğitim hizmetine](https://www.bulutla.com/) bir göz atmayı da unutmayın!
