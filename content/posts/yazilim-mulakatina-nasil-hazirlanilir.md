---
title: "Yazılım mülakatına nasıl hazırlanılır"
tags: ["amazon", "mülakat", "yazılım", "algoritma", "yurt dışı", "davranışsal", "teknik"]
date: 2019-11-19T14:41:29+03:00
draft: false
---


Yurtdışında çalışmaya karar verdiniz, ufkunuzu genişletmek istiyorsunuz ve dünya standartlarında iyi bir yazılımcı olmaya azmettiniz. Disiplinli ve bilinçli bir çalışmayla bunu başarmanız mümkün.

## TL;DR

*   Cracking the coding interview <sup id="fnref-25-1">[1](#fn-25-1)</sup> kitabını hatmedin
*   Mülakat tercübesi kazanın (mock interview <sup id="fnref-25-2">[2](#fn-25-2)</sup> yani yalancı mülakat veya gerçek mülakatlara girerek)
*   Davranışsal sorulara iyi hazırlanın
*   Beyaz tahtaya kod yazma pratiği yapın

### Gereksinimler

1.  ODTÜ, BOUN, Bilkent, İTÜ, YTÜ, Koç, Sabancı gibi kalburüstü bir üniversiteden mezun olmak veya çok etkileyici bir github profiline sahip olmak (ikincisi birincisinden daha zor).
2.  Çok iyi derecede İngilizce bilmek ve kendini iyi ifade edebilmek.
3.  Azim, özgüven ve bol pratik.
4.  Yenilgiye hazır olmak - pes etmek anlamında söylemiyorum, fakat mülakatta başarısız olmak dünyanın sonu değil. Pek çok kaliteli firma yazılımcı istihdam ediyor. Apple olmadıysa Google, Twitter vs. gibi başka firmaları deneyebilirsiniz. Hatta, belli bir süre sonra aynı firmaya tekrar başvurmanız bile mümkün.
5.  Yenilgiden ders çıkarabilmek. Eğer bir mülakat süreci başarısızlıkla sonuçlandıysa, bir sonrakinde aynı hataları yapmamak için evvela hatalarınızın ne olduğunu anlamanız sonra da onları düzeltmek için çalışmanız gerekiyor.

İşverenin adaylarda aradığı özellikleri iki başlıkta değerlendirebiliriz: Teknik ve davranışsal. İkisi de çok önemli özellikler ve 50/50 diyebiliriz ağırlıklarına. Doğası gereği, teknik kısımda aranan özellikler somut ve nesnel olduğundan, hazırlık süreci hem mülakata girecek kişi için hem de mülakatı yapacak kişi için oldukça net. Bu yanıyla teknik bölüm matematik sınavına, davranışsal bölüm de edebiyat sınavına benziyor diyerek artık detaylara geçelim.

## Teknik

### Algoritmalar ve problem çözme

Kendinize bir programlama dili belirleyin ve hazırlıklarınızı hep bu dilde kod yazarak yapın. Problem çözme kısmında sizden yazmanızı isteyecekleri sorular, çözümü bir beyaz tahtaya sığacak ayardadır. Eğer mülakat telefon üzerinden yapılıyorsa, beyaz tahtanın yerini collabedit <sup id="fnref-25-3">[3](#fn-25-3)</sup> tarzı bir portal alacaktır; bu durumda ise çözüm bir sayfayı geçmeyecek kadardır. _Array_, _linked list_, _doubly linked list_, _queue_, _stack_, _priority queue_ ve _tree_ gibi yapıları adınız soyadınız gibi bellemeniz lazım. Siz çözümünüzü izah ederken, "neden _stack_ kullandın?" veya "burada _linked list_ kullanmanın _array_ kullanmaya göre ne gibi dezavantajları var?" gibi soruları hiç düşünmeden cevaplayabilmelisiniz. Çözümünüzü küçük parçalara (fonksiyonlar, modüller, sınıflar vb.) ayırmayı unutmayın. Her ne kadar bir sayfalık bir kod yazıyor da olsanız, mutlaka makul şekilde ufak birimlere bölün. Büyük problemleri nasıl küçük problemler cinsinden modelleyebildiğinizi ve ne kadar anlaşılabilir kod yazdığınızı karşınızdaki yazılımcıya gösterin!

### Sistem tasarlama

Tasarım bölümünde, gerçek hayattaki bir ihtiyacı giderecek sistemi tasarlamanız gerekecek. Bol bol soru sormalısınız ki öncelikle problemin ne olduğunu, neyi çözmeye çalıştığınızı iyice anlayın.

1.  Soru sorun ve problemi daha da netleştirin - bazen bilerek ucunu açık bıraktıkları yerler olacak
2.  Sisteme giriş noktalarını, girdileri ve çıktıları belirleyin
3.  Sistemdeki aktörleri ve nesneleri modelleyin - mesela bir benzin istasyonu yazılımında pompa, kuyruk, dizel/benzin ve farklı ödeme çeşitleri olacaktır
4.  Genel hatlarıyla, fazla detaya girmeden kodlamanızı isteyebilirler

_Web service_, _database_, _partitioning/sharding_, _cache_ ve _load balancer_ gibi bileşenlerin ve mefhumların ne olduğunu ve hangi problemi çözdüklerini iyi bilin çünkü size verilen problemde de bunlardan bazılarını kullanmanız gerekecek. Örnek vermek gerekirse, benzinlik yazılımı ihtiyacını bir _web service_ ile çözdünüz diyelim. Benzin fiyatlarını her seferinde bir yerden çeker misiniz yoksa bu bilgiyi _cache_'leseniz daha mı iyi olur? Diyelim ki çözümünüz çok tutuldu ve Shell sizin çözümü kullanmak istedi, servisinizi nasıl ölçeklersiniz? Diyebilirsiniz ki bir load balancer koyarım öne, hatta her kıtada farklı bir load balancer olur veya veritabanını konuma göre _partition_'larım gibi. Bu gibi sorulara ve örnek olarak verdiğim cevaplara aşina olun. Mantık yürütmeniz istenecek, burada ezber işe yaramıyor.

## Davranışsal (_soft skills_)

Bu kısımda sizin nasıl bir çalışan olduğunuzu anlamaya çalışacaklar. Sorular mühendislik veya yazılım özelinde olmayacaktır. Birkaç misal vermek gerekirse:

*   En son ne zaman bir iş arkadaşınızla çatışma yaşadınız? Bu durumu nasıl ele aldınız ve nasıl neticelendi?
*   Yetiştirmeniz gereken projede, başka bir ekipten beklediğiniz bir özellik var ve bu olmadan ilerleyemiyorsunuz. Ne yaparsınız?

Bu soruların tek bir doğru cevabı yok arkadaşlar. Ama yanlış cevabı var :) Mesela, "iş arkadaşımla çatışma yaşadığımda ona küstüm ve o günden beri de konuşmuyoruz" derseniz yanlış bir cevap vermiş olursunuz. Doğru bir cevap örneği olarak da, "X ekibinden Y özelliğini bekliyordum, bu durumu ekip lideriyle konuştum ve istediğimiz tarihe yetişmeyeceğini söyledi. Durumu yöneticime izah ettim ve X ekibine olan bağımlılığımızı kopararak, kalan işlerimizi yapabilmemizi sağlayacağını düşündüğüm taktik bir çözümü arz ettim." diyebilirsiniz. Dikkat etmeniz gereken belki de en önemli nokta, vereceğiniz cevaplar mutlaka gerçek olsun. Yani uydurma bir hikayeyle hayal ürünü bir cevap vermeyin, müteakip sorularla bir köşeye sıkışabilirsiniz ve sonunda karşınızdaki kişi yalan söylediğinizi anlayabilir. Diğer taraftan, bu tip sorularda mülakat heyecanıyla o an gerçek bir olay aklınıza gelmeyebilir. Bu sebeple, mülakat öncesi hazırlıklarınızda davranışsal sorulara gereken vakti ayırın ve bu tarz birkaç gerçek olayın üzerinden geçerek iyice pekiştirin.

## Diğer hususlar

Bu işin sırrı pratik yapmakta. Doğru yöntemlerle ve nokta atışı çalışmalarla başarı ihtimalinizi artırmalısınız. Son olarak, ne kadar çok başvuru yapıp mülakata girerseniz, güzel bir iş sahibi olmaya o kadar yaklaşırsınız. Babam bana sınavlardan önce "stres yapmana gerek yok" derdi :) Hiçbir yardımı dokunmayan, aksiyon alamayacağınız bir tavsiye - sanki stres yapıp yapmamak tercih meselesiymiş gibi. Fakat şunu da unutmamak gerekir, stresi ve heyecanı azaltacak olan en büyük faktörlerden biri iyi hazırlanmaktır. Yeterince hazırsanız, nelerle karşılacağınızı biliyorsanız, bilinmezlik azalacak ve kendinize olan güveniniz artacak. Bunun doğal sonucu olarak da stres ve heyecan kontrol edilebilir seviyelere düşecektir.


1.  [Cracking the coding interview](https://www.amazon.co.uk/Cracking-Coding-Interview-6th-Programming/dp/0984782850) [↩](#fnref-25-1)
2.  [Mock interview](https://interviewing.io/) [↩](#fnref-25-2)
3.  [Collabedit](http://collabedit.com/) [↩](#fnref-25-3)
