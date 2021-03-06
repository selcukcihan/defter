---
title: "Eş zamanlı çalışan kodlar"
tags: ["concurrency", "multithreading", "parallelism", "iletişim", "cache", "redis", "büyük veri", "veritabanı",
       "senkronizasyon", "synchronization", "http", "memory", "soket", "kuyruk", "dağıtık", "distributed"]
date: 2020-02-15T04:41:29+03:00
draft: false
images: ["/img/es-zamanli-calisan-kodlar/smoke.jpg"]
---

Bir web uygulaması yaptınız, HTTP ile konuşuyor.
Gelen istekleri dinliyor ve aynı anda birden fazla isteğe cevap verebiliyor.
Uygulamayı yaparken son çıkan şatafatlı framework'lerden birini kullanmayı ihmal etmediniz.
Web sunucunuzu seçtiniz ve uygulamanızı bir veya birden fazla makinaya *deploy* ettiniz.

Peki düşündünüz mü, nasıl oluyor da uygulamanız gelen istekleri eş zamanlı olarak işleyebiliyor?
Tabi farklı makinalara aynı anda gelen isteklerin işlenmesinde bir sır perdesi yok, benim bahsettiğim aynı fiziksel makinaya gelen istekler için.
Hiç kafa yordunuz mu?
Yoksa, "fazla kurcalamaya gerek yok, bir şekilde çalışıyor işte" mi diyorsunuz?
Eğer eş zamanlı gelen istekler ortak bir veriye erişmiyorsa, zaten hiç umrumuzda bile olmuyor.
Fakat ne zaman ki *memory*'deki bir veriyi paylaşmak gerekiyor veya aynı anda erişilmesinin problem oluşturabileceği herhangi başka bir kaynak paylaşılıyor, işte o zaman bu mevzuları biraz anlamaya çalışmak gerekiyor.

![Traffic][1]
<a style="background-color:black;color:white;text-decoration:none;padding:4px 6px;font-family:-apple-system, BlinkMacSystemFont, &quot;San Francisco&quot;, &quot;Helvetica Neue&quot;, Helvetica, Ubuntu, Roboto, Noto, &quot;Segoe UI&quot;, Arial, sans-serif;font-size:12px;font-weight:bold;line-height:1.2;display:inline-block;border-radius:3px" href="https://unsplash.com/@hugojehanne?utm_medium=referral&amp;utm_campaign=photographer-credit&amp;utm_content=creditBadge" target="_blank" rel="noopener noreferrer" title="Hugo Jehanne'a ait fotoğraflar için tıklayın."><span style="display:inline-block;padding:2px 3px"><svg xmlns="http://www.w3.org/2000/svg" style="height:12px;width:auto;position:relative;vertical-align:middle;top:-2px;fill:white" viewBox="0 0 32 32"><title>unsplash-logo</title><path d="M10 9V0h12v9H10zm12 5h10v18H0V14h10v9h12v-9z"></path></svg></span><span style="display:inline-block;padding:2px 3px">Fotoğraf Unsplash'ta Hugo Jehanne'a aittir.</span></a>

# Motivasyon
Şu an yukarıda bahsettiğim türden bir problemi çözmeye çalışıyorum, paylaştırmam gereken bir data var ve bu data

* *memory*'de durmak zorunda çünkü çok hızlı erişebilmeliyim (redis veya memcached demeyin, uymuyor)
* yaklaşık 10GB boyuta sahip, dolayısıyla her request kendi erişeceği datayı hafızaya yüklesin diyemiyorum
* arada bir güncelleniyor, read-only olmadığından bir senkronizasyon problemi de var çözmem gereken

Bu problemi çözerken, kafamdaki dağınık bilgiyi derleyip toplayıp umūmun istifadesine sunayım dedim.
Konuyu teorik değil, oldukça pratik olarak ele aldım.
Güncel web teknolojileri üzerinden örnekler verdim.
Tüm anlattıklarım web uygulamaları bağlamında olsa da, diğer uygulama çeşitleri için de geçerlidir.

# Terminoloji ve ön bilgi
**Not**: UNIX üzerinden örnekler veriyorum ama Windows ve diğer işletim sistemlerinde de aynı konseptler var.

* **memory**: Bildiğimiz RAM, en hızlı depolama yeri (evet tamam register'lar falan da var, o kadar detaya girmiyorum). Fakat kalıcı değil, fişi çekersen uçar.
* **process**: İşletim sisteminin koşturduğu bir iş parçacığı, bir ID'ye sahiptir (PID) ve halden hale girer (beklemede, hazır, koşuyor, ölü vs.). Kendine ait *memory*'si vardır ve başka *process*'ler buna erişemez.
* **thread**: Bir *process* bağlamında yer alırlar ve o *process*'in *memory*'sine içindeki tüm *thread*'ler erişebilir.
* **senkronizasyon**: Paylaşılan bir veriye erişimin nasıl koordine edileceği, mesela sırayla erişmek veya aynı anda erişmek gibi.
* **race condition**: Senkronizasyonu doğru yapmadığınızda ortaya çıkan berbat bir durum.
Mesela global bir değişkeni aynı anda güncellemeye/okumaya çalışan iki *thread* var, kimin güncellemesi geçerli olacak?
* **web sunucusu**: Apache, nginx veya IIS gibi bir uygulama.

# Web sunucuları
Web uygulamanızı yazdığınızda, bunu bir web sunucusu üzerinden kullanıma açarsınız.
Uygulamanız daima bir web sunucusu bağlamında çalışır.
Windows'ta "task manager" ile bakarsanız çalışan *process* IIS'tir mesela.
Sizin uygulamanız bu *process* tarafından çalıştırılır.

Uygulamanıza gelen isteklerin eş zamanlı işlenebilmesini sağlayan web sunucularıdır.
Bunu kendiniz de yapabilirdiniz ama oturup baştan bir web sunucusu yazmaya gerek yok :smile:
Eğer uygulamanız aynı anda sadece bir istek işleyebilseydi, çok yavaş bir istemci uygulamanızı kullanılamaz hale getirebilirdi.
Web dünyasında vaktin çoğu kablolarda (network, ağ) geçtiğinden, aynı anda birden fazla bağlantı kabul edebilmek elzem.

Web sunucuları, çoklu *process* (çokoprens), çoklu *thread*, ya da her ikisini birden kullanarak uygulamanızın aynı anda birden fazla isteğe cevap verebilmesini sağlıyor.
Hatta isterseniz tek bir process ve thread'le bile web sunucusu yapılabilir, bakınız [select](http://man7.org/linux/man-pages/man2/select.2.html).
Select ile aynı anda birden fazla istemciye hizmet verebilirsiniz.
Tabi gerçek anlamda eş zamanlı olmuyor, ama sunucunuza dışarıdan bakan biri için aynı anda birden fazla istemcinin istekleri işleniyor gibi gözüküyor.

# Reverse proxy
*Reverse proxy*, uygulamanızla dış dünya arasına oturan ayrı bir uygulamadır.
Mesela [nginx](https://nginx.org/en/) bir reverse proxy olarak kullanılabilir.
Reverse proxy kullanmanın gerektiği durumlar ve çeşitli faydaları var, ama ben burada "eş zamanlı çalışan kodlar" kapsamındaki faydasına değineceğim.

Çoğunlukla, sunucunuzun hizmet vereceği istemciler üzerinde hiçbir kontrolünüz yok.
Bu istemciler çok yavaş ağlardan gelip size ulaşıyor olabilir.
Veya istemciler, yani sunucunuza bağlanan uygulamalar, kötü niyetli olabilir.
Sunucunuza bağlanan çok yavaş bir istemci, eğer uygulamanızı dikkatli kurgulamadıysanız diğer istemcilerin hizmet kalitesini düşürebilir.

Reverse proxy sayesinde, istemci doğrudan sizin uygulamanıza değil de reverse proxy'ye bağlandığı için, istek ağ üzerinden tamamen iletilene kadar uygulamanıza henüz giriş yapmamış oluyor.
Bu da demektir ki, istemci yavaş bir ağdan da gelse, gelen istek sizin uygulamanıza girdiği anda tamamı okunmuş oluyor ve uygulamanız ağ kaynaklı yavaşlıklardan etkilenmiyor.
Aynı şekilde isteğe verilen cevabı iletme görevi de uygulamanıza değil, reverse proxy'ye düşüyor ve yine ağdaki yavaşlıklar sizi etkilemiyor.

Diğer taraftan bakacak olursak, eğer reverse proxy kullanmamış olsaydık neler olurdu?
Olayı daha da dramatik hale getirmek için tek bir process ve thread ile implement edilmiş bir uygulamamız olsun.
A istemcisi uygulamamıza doğrudan bağlanacak ve elimizdeki tek thread'i, onun isteğini okuyup işleyip cevabını yazmaya kullanacağız.
Bu sırada B istemcisi uygulamaya bağlanmaya çalışınca onun bağlantısını alamayacağız, A'nın işlemi bitmeden B'ye hizmet verememiş olacağız ve A çok yavaş bir ağ üzerindeyse B uzun bir süre beklemek zorunda kalacak.

# Eş zamanlı çalışma modelleri
Her teknolojinin kendine has bazı kısıtları ve püf noktaları var, biraz da bunlardan bahsedelim.

## NodeJS
[NodeJS](https://nodejs.org/) tek bir *thread*'de çalışıyor (Javascript'in doğası gereği).
Asenkron çalışma modeli sayesinde, yazdığınız kod daima bir "callback" bağlamında çalışıyor.
Yani NodeJS vakti gelince sizin koda koşma şansı veriyor diyelim ve kodunuz işini bitirene kadar çalışıyor.
Bu yüzden, NodeJS'te *multi-threading* kaynaklı *race condition* durumu oluşamıyor.
Diğer taraftan, tek bir *thread* olduğu için eğer kodunuz asenkron olmayan bir iş yapıyor ve bu uzun sürüyorsa (diyelim ki sonsuz bir döngü yazdınız), NodeJS uygulamanız kilitlenmiş oluyor.

## Python
Python'da (CPython) [GIL](https://wiki.python.org/moin/GlobalInterpreterLock) (global interpreter lock) denen bir şey var, kısaca söylemek gerekirse *multi-threading* yapamıyorsunuz.
Daha doğrusu, I/O işleri için *multi-threading* işe yarar fakat GIL yüzünden aynı anda sadece tek bir *thread* sizin kodunuzu çalıştıracak, eş zamanlılık yok.
Eş zamanlı olmayan *multi-threading* de ne bileyim yani.
Python ile aynı anda birden fazla iş yapmak için [multi processing](https://docs.python.org/3.6/library/multiprocessing.html) kullanmak gerek.
Birden fazla *process* *fork*'layıp eş zamanlı işler çalıştırabilirsiniz.
Fakat *process* ağır bir yapı, kendi *memory*'si var demiştik.
Uygulamanızın bir klonu olarak düşünebilirsiniz.

## Java
Java da .Net gibi, *multi-threading* yapabiliyorsunuz.
Mutex ve semaphore gibi yapılar ile *thread*'lerin senkronizasyonunu sağlayabiliyorsunuz.
Böylece paylaştıkları *memory*'de birbirlerinin ayağına basmalarını ve *race condition*'lar oluşmasını engelleyebiliyorsunuz.

# İletişim modelleri

Eş zamanlı çalışan kod parçacıkları birbiriyle nasıl iletişim kuracak, kaynakları nasıl paylaşacak öğrenelim.
Sırayla birbirine en yakın birimlerden başlayıp, araları en açık olana doğru gideceğiz.
Bu sıralamada, bir sonraki için geçerli olan iletişim biçimi ondan önce gelen tümü için de kullanılabilir.

## Multi-threading
Burada söylenecek fazla bir şey yok.
Thread'ler arası iletişim gayet basit, *memory* tüm *thread*'ler tarafından görülebiliyor.
Global bir değişkeniniz varsa, tüm *thread*'ler bunu okuyup yazabilir.
Dikkat edilmesi gereken husus **race condition**'lar ve **senkronizasyon**.

## Multi-processing
Aynı makina üzerindeki iki *process*'in birbiriyle iletişim kurabileceği farklı yollar var:

* **pipe**: Bir taraftan yazılır diğer taraftan okunur
* **shared memory**: İsminden belli; *memory*, *process*'ler arasında paylaştırılabiliyor
* **dosya**: İki *process* ortak bir dosya kullanabilir
* **memory mapped file**: Bu da özellikle büyük dosyalar üzerinde performanslı bir iletişim yöntemi

## Dağıtık iletişim (distributed, farklı makinalar arası)
Ağ (internet, intranet vb.) üzerinden, farklı makinalardaki uygulamalar iletişim kurabilirler (bakınız WWW).

* TCP/UDP gibi alt seviye bir protokolle iletişim kurabilirsiniz.
* HTTP gibi daha üst seviye bir protokolle iletişim kurabilirsiniz.
* Dağıtık kuryruklar (*distributed queue*) veya *stream*'ler kullanabilirsiniz, mesela *AWS SQS* ve *Kinesis*, *Apache Kafka* veya *RabbitMQ* gibi.
* *Distributed cache*'ler kullanabilirsiniz, mesela *Redis* veya *memcached* gibi.

## Anti-pattern
Bunu yapmayın diyeceğim tek şey: **Veritabanını iletişim için kullanmayın**.
Veritabanlarının asıl amacı veri saklamak ve sunmak, A'dan B'ye iletmek değil.
A uygulaması iş üretecek olsa, B uygulaması da bu işleri alıp gerçekleştirecek olsa ve bu işler veritabanı üzerinden iletilse; B'nin tek şansı sürekli veritabanını

* "iş var mı"
* "iş var mı"
* "iş var mı"

diye sorgulamak olacaktır.
İşler çoğaldıkça yeni A ve B makinaları koymanız gerekecek ve veritabanı can çekişecek.
Veritabanınız uygulamanızın ölçeklenmesinde bir **darboğaz** oluşturacak.
Bunun yerine, dağıtık bir kuyruk doğru seçim olacaktır.

# Tercih

Çözmeye çalıştığım probleme en yatkın iletişim yöntemi *shared memory* (paylaşımlı bellek).
Neden bu yöntemi seçtiğimi ve problemi nasıl çözdüğümü [başka bir yazıda](/diger/paylasimli-bellek) anlattım.

[1]: /img/es-zamanli-calisan-kodlar/smoke.jpg
