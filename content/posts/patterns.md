---
title: "Pattern'ler"
tags: ["desen", "python", "pattern", "dinamik dil", "arayüz", "interface", "soyutlama", "abstraction"]
date: 2016-07-15T14:41:29+03:00
draft: false
---


Pattern (Kalıp) Nedir?
----------------------------

Pattern kelimesini *desen* olarak çeviriyorlar, fakat bence bu anlamı karşılamıyor.
Desen deyince kafamda şekiller falan canlanıyor.
Şablon veya kalıp sanki daha iyi karşılıyor *pattern* anlamını.
Yazılım pattern'leri, **sık rastladığımız işleri çözmek için tekrar tekrar kullanılabilecek** *(benzer problemleri çözmekte istifade edebileceğimiz)* **usullerdir**.
Örgü örerken uygulanan tekniklere benzetebiliriz, aynı teknikle pek çok farklı şey örülebilir.
Yazılımda da, aynı pattern'i benzeşen problemleri çözerken uygulayabiliriz.

Yazılım patternleri, belirli bir dile özgü olmayıp, **dilden bağımsız** düşünülebilir.
Biz hep pratikten gittiğimiz için, tabi ki Python özelinde ve somut örnekler üzerinden bakacağız.
Python dünyasında patternleri uygulamak, mesela C++ veya Java'ya göre, çok daha zahmetsizdir.
Bunu bir üstünlük anlamında söylemiyorum, çünkü bir patterni farklı diller arasında kıyaslamak, elmayla armutu kıyaslamaya benzer.
Python **dinamik bir dil olduğu için** (bkz. dynamic typing, dynamic binding), doğası gereği birçok pattern zaten dilin kendi bünyesinde mevcuttur ve diğerlerini de eklemek **çocuk oyuncağıdır**.
Zaten önemli olan hiçbir zaman patterni gerçeklemek değil ki, mühim olan **doğru patterni doğru probleme uygulayabilmektir**.
Tamirci (yazılımcı), kaç numaralı anahtarı (pattern) kullanacağını bilmelidir.

Temel Prensipler
----------------

* Arayüzle (soyutlamayla) muhattap olun, implementasyonla değil
* Nesne kompozisyonunu (nesneleri birleştirmek), inheritance'a (kalıtım diyelim) tercih edin

Yazdığınız metodları ve sınıfları, daima bir arayüze bağımlı kılın.
Asla belirli bir implementasyona bağlanmayın.
Mesela `WebApplication` sınıfınızda loglama yapıyorsunuz ve kodunuzda bir logger'ınız var.
Bu logger'ı illa dosyaya yazan `FileLogger` türünden olmaya zorlamayın.
Sizin için `log` metodu olan her şey `logger` olabilsin, sizi `log` metodunun varlığı ilgilendirir - ne tür bir `logger` olduğuna (`DatabaseLogger`, `ConsoleLogger`...) karışmayın ve umursamayın ve belirli bir logger'ı zorunlu tutmayın.

