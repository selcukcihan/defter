---
title: "Sanal makinanız EC2"
tags: ["aws", "bulut", "ec2", "sanal makina", "sanal", "web uygulaması", "sunucu", "server", "ssh", "ami", "t2.micro", "ücretsiz"]
date: 2019-11-05T14:41:29+03:00
draft: false
images: ["/img/sanal-makinaniz-ec2/matrix.jpg"]
---

AWS sanal makinalarını EC2 adı verilen hizmet ile sunuyor. Anlık olarak sanal makina başlatıp, sadece kullandığınız miktar kadar ücret ödüyorsunuz. [Ücretsiz kullanım kotası](https://aws.amazon.com/tr/free/?all-free-tier.sort-by=item.additionalFields.SortRank&all-free-tier.sort-order=asc) kapsamında, bir yıl boyunca “t2.micro” türündeki bir makinayı kullanma hakkına sahipsiniz. "t2.micro" makinaları, basit web uygulamaları için kullanabilirsiniz.

![Matrix][1]
<center>*Fotoğraf Unsplash'ta Markus Spiske'ye aittir.*</center>

AWS, farklı [türlerde sanal makinalar](https://aws.amazon.com/tr/ec2/instance-types/) ile, değişen ihtiyaçlara uygun çok çeşitli donanımlar sunuyor. Sadece “t2.micro” türündeki makinalar ücretsiz kullanım kotasına dahil olduğu için, biz şimdilik bunu kullanacağız. Mesela GPU ihtiyacı olan bir uygulama yapacak olsaydık, “p3.8xlarge” türündeki bir sanal makinayı kullanmayı düşünebilirdik.

## Sanal makinamızı başlatalım

Öncelikle AWS konsolundan, EC2 hizmetinin olduğu [sayfayı](https://eu-west-1.console.aws.amazon.com/ec2/home?region=eu-west-1#Instances:sort=instanceId) açalım ve “Launch Instance” butonuna tıklayalım.

![Şimdilik hiç sanal makinamız olmadığından, liste boş gelecektir.](https://cdn-images-1.medium.com/max/2544/1*pxf-igScAeKmVFLgJUsnhw.png)*Şimdilik hiç sanal makinamız olmadığından, liste boş gelecektir.*

İlk adımda bir “AMI” seçmemiz istenecek. AMI, açılımı “Amazon Machine Image” olup, sanal makinanızda koşacak olan yazılımı (işletim sistemini de diyebiliriz) belirler. Yanındaki “Select” butonuna basarak “Amazon Linux 2 AMI (HVM), SSD Volume Type” türünü seçin. Bir sonraki ekranda, “t2.micro” seçtiğinizden emin olup, “Next: Configure Instance Details” butonuna basın.

![Henüz “Launch” butonuna basmayın, daha ayarlayacağımız detaylar var.](https://cdn-images-1.medium.com/max/2528/1*JaVPyuKB6qOzr8J_43_L6g.png)*Henüz “Launch” butonuna basmayın, daha ayarlayacağımız detaylar var.*

Bir sonraki ekranda, hiçbir seçimi değiştirmeden sol alttaki “Next: Add Storage” butonuna basın. Burada da değişiklik yapmanıza gerek yok, sanal makinanızın 8GB SSD diski olacak (aslında 30GB’a kadar ücretsiz kotanızın hakkı var).

“Next: Add Tags” butonuna basıp, sanal makinanıza bir etiket verin. Şimdilik sadece bir sanal makinanız olacağından, etiket vermeseniz de olur ama genel olarak etiketlerle AWS kaynaklarınızı takip etmek çok kolaylaşıyor o yüzden her zaman etiketleri kullanmaya çalışın. Yüzlerce sanal makinanız olduğunda, hangi makina ne için kullanılıyordu karıştırmamak imkansız.

![“key, value” çiftleri kullanılarak istediğiniz etiketlendirmeyi yapabilirsiniz. Bir makinaya birden fazla etiket de verebilirsiniz, mesela her makinanın bir “isim” bir de “departman” şeklinde etiketi olabilir. Böylece hangi makina hangi departmana ait kolayca görebilirsiniz.](https://cdn-images-1.medium.com/max/2514/1*xz2tFlEPWRvtEYPen4rJoA.png)*“key, value” çiftleri kullanılarak istediğiniz etiketlendirmeyi yapabilirsiniz. Bir makinaya birden fazla etiket de verebilirsiniz, mesela her makinanın bir “isim” bir de “departman” şeklinde etiketi olabilir. Böylece hangi makina hangi departmana ait kolayca görebilirsiniz.*

“Next: Configure Security Group” butonuna tıklayarak, sanal makinanızın hangi portlarına kimler erişebilir detaylıca ayarlayabildiğiniz güvenlik grubu seçimi ekranına geçin. Şimdilik güvenlik gruplarının detayına girmeyeceğiz, fakat makinaya SSH yapabilmemiz için, 22 nolu port otomatik olarak dışarıya açılacak şekilde ayarlı geliyor.

![[CIDR block](http://cidr.xyz/) 0.0.0.0/0 olan IP’lerden (bu durumda, tüm IP’ler) 22 nolu porta bağlantı isteklerine izin verir.](https://cdn-images-1.medium.com/max/2520/1*jTx3QySKhwviZ19Qlayu2w.png)*[CIDR block](http://cidr.xyz/) 0.0.0.0/0 olan IP’lerden (bu durumda, tüm IP’ler) 22 nolu porta bağlantı isteklerine izin verir.*

“Review and Launch” butonuna tıklayıp seçimlerinizi gözden geçirdikten sonra “Launch” butonuna tıklayın. Karşınıza gelen ekranda, sanal makinaya ssh yapabilmek için bir güvenlik anahtarı oluşturmanız veya mevcut anahtarınızı kullanmanız istenecek. “Create a new key pair” seçip, yeni oluşturacağınız anahtarınıza bir isim verin ve “Download Key Pair” butonuna tıklayarak pem dosyasını **bilgisayarınıza indirin** ve güvenli bir şekilde saklayın, bu dosya olmadan makinanıza bağlanmanız imkansız! Anahtar dosyasını indirdiyseniz, artık “Launch Instances” butonuna tıklayarak **sanal makinanızı ayağa kaldırabilirsiniz.**

![“Download Key Pair” butonuna tıklamayı unutmayın.](https://cdn-images-1.medium.com/max/2000/1*C7kTB8G9sJ_4YuEi7d6qVw.png)*“Download Key Pair” butonuna tıklamayı unutmayın.*

Sanal makinanın hazır hale gelme süresi birkaç saniye ila bir iki dakika arasında değişebilir. Yeşil kutucuğun içindeki linke tıklayarak sanal makinanızın durumuna bakabilirsiniz, veya [ec2 konsolunun](https://eu-west-1.console.aws.amazon.com/ec2/home?region=eu-west-1#Instances:sort=instanceState) ana sayfasına gelip “Instances” kısmından tüm ec2 makinalarınızı görebilirsiniz. “Instance State” olarak yeşil işaretli “running” yazısını gördüyseniz, sanal makinanız **bağlantı kurulmaya hazır** demektir.

![Sanal makinanızın bir ID’si var bir de ismi var, eğer bir önceki adımda “name” adında bir etiket ekleseydik, bu sayfadaki ilk kolonda “name” etiketinin değerini görecektik. Sanal makinanızı durdurmak, başlatmak veya tamamen silmek gibi işlemleri bu sayfadaki “Actions” butonu ile gerçekleştirebilirsiniz.](https://cdn-images-1.medium.com/max/2526/1*VneBgeBVDz4VlvF1C5K5bQ.png)*Sanal makinanızın bir ID’si var bir de ismi var, eğer bir önceki adımda “name” adında bir etiket ekleseydik, bu sayfadaki ilk kolonda “name” etiketinin değerini görecektik. Sanal makinanızı durdurmak, başlatmak veya tamamen silmek gibi işlemleri bu sayfadaki “Actions” butonu ile gerçekleştirebilirsiniz.*

Sanal makinanıza harici bir IP (public IP) ve bir DNS atanacaktır, ister IP ile ister DNS ile makinaya bağlantı kurabilirsiniz. Makinaya bağlanmak için istediğiniz SSH *client*’ını veya AWS konsoldaki tarayıcı SSH *client*’ını kullanabilirsiniz. En kolayı olan tarayıcıdan bağlanmak için, üstteki “Connect” butonuna tıklayın ve açılan pencerede “EC2 Instance Connect” seçin.

![Tarayıcıdan bağlanırken, ssh anahtarını istemiyor.](https://cdn-images-1.medium.com/max/2000/1*SH9Cwno0TtZQeMgHY__mqw.png)*Tarayıcıdan bağlanırken, ssh anahtarını istemiyor.*

![Sanal makinanıza hoş geldiniz.](https://cdn-images-1.medium.com/max/2000/1*4FpfvLZB-oyOXcVcM2C45Q.png)*Sanal makinanıza hoş geldiniz.*

Sanal makinanıza bağlandınız, kullanıcı adınız seçtiğimiz *AMI*’dan gelen “ec2-user” şeklinde. İlk iş olarak, sudo yum update komutu ile paketleri güncelleyelim. Makinamızı bir web sunucusu olarak kullanmak için, “httpd” paketini kuralım: sudo yum install -y httpd . Sonrasında, sudo service httpd start ile web sunucusunu başlatalım. Web sayfamıza ait dosyaların barındığı “/var/www/html” klasörüne girelim: cd /var/www/html . İsterseniz kendi tasarladığınız index.html sayfasını bu klasöre atabilirsiniz. Uğraşmak istemiyorsanız, benim hazırlamış olduğum dosyayı kullanmak için, şu komutu çalıştırın:

```
sudo wget https://gist.githubusercontent.com/selcukcihan/9e6b2589f5dceb88cb2f5fb0558e4fde/raw/c0619c3add90f2ad644d1ecab99d6373966ce04f/index.html
```

Ve hemen web sitenizi ziyaret etmeyi deneyin. Bağlantı zaman aşımına uğrayacak. Güvenlik gruplarını hatırladınız mı? Nasıl ki ssh bağlantısı için, 22 nolu portu dışarı açmıştık. Şimdi de web sunucumuz için, 80 nolu portu dışarı açacağız ki gelen http bağlantıları sanal makinamıza ulaşabilsin.

![Bir problem var gibi!](https://cdn-images-1.medium.com/max/2530/1*LWH1qDJ4OJ_JSfRtok_OBw.png)*Bir problem var gibi!*

Bunun için, AWS konsolunda, ec2 makinamızın listelendiği ekranda “Security Group” kısmında bulunan “launch-wizard-1” bağlantısına tıklayın.

![](https://cdn-images-1.medium.com/max/2118/1*tXjw_QmKBWye8WP6YFyREg.png)

Açılan ekranda, “Inbound” tabını seçerek “Edit” butonuna basın.

![Şu haliyle, sadece 22 nolu port dışarıya açık.](https://cdn-images-1.medium.com/max/2116/1*rlOfruZijggxtyemYQRmSA.png)*Şu haliyle, sadece 22 nolu port dışarıya açık.*

“Add Rule” butonuna tıklayarak, “Type” sütununda yer alan menüden “HTTP” seçin ve “Save” butonuna tıklayarak değişiklikleri kaydedin. Hiç vakit kaybetmeden, tarayıcıdan web sitenizi yenileyin ve artık çalıştığını görün.

![Güvenlik grubundaki değişiklikleriniz anında etkisini gösterir.](https://cdn-images-1.medium.com/max/2536/1*pwF3VTuZbONOAAs5SjYCSA.png)*Güvenlik grubundaki değişiklikleriniz anında etkisini gösterir.*

Web sunucunuz hayırlı olsun. Sanal makinanızı durdurmak için, menüden “Stop”a tıklayın.

![Lüzumsuz ise söndürün.](https://cdn-images-1.medium.com/max/2532/1*bVIHgEFbGearVW5MczKMRw.png)*Lüzumsuz ise söndürün.*

AWS hizmetlerini kullanmayı düşünüyor ve yetkinliğinizi geliştirmek istiyorsanız, [diğer makalelerimizi](/tags/aws) de okuyun. Bunun yanında, kurumsal ve startup firmalara sağladığımız [eğitim hizmetine](https://www.bulutla.com/) bir göz atmayı da unutmayın!

[1]: /img/sanal-makinaniz-ec2/matrix.jpg
