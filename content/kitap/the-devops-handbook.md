---
title: "The DevOps Handbook"
tags: ["yazılım", "süreç", "devops", "operasyon", "ci/cd", "web", "mimari"]
date: 2024-01-11T07:41:29+03:00
draft: false
images: ["/img/the-devops-handbook/the_devops_handbook.jpg"]
---

The DevOps Handbook kitabı, yazılım geliştirme süreçlerine dair yazılmış en kapsamlı ve popüler kitaplardan biri.
Kitaptan aklımda kalanları yazıya döküp kalıcı hâle getirmek istedim.

Sektörümüz, diğer iş kollarına göre oldukça yeni.
Bu konuda geçmişten gelen fazla bir birikimimiz yok.
Sürekli yeni yollar deniyoruz ve öğrenmeye devam ediyoruz.

> Every industry and company that is not bringing software to the core of their business will be disrupted.
> In previous economic eras, businesses created value by moving atoms. Now they create value by moving bits.

Günümüzde her şey çok hızlı; rakiplerinin hızına ayak uyduramayan şirketler birer birer sahneden siliniyor.
Kitap bu bakış açısıyla, yazılım süreçlerinin verimini nasıl artırabileceğimizi anlatıyor.
Çok değerli bir kılavuz bence, mutlaka okuyun.

![The DevOps Handbook][1]

### Bekleme sürelerini azaltın

Beklemeye örnek: Bir değişikliği test etmek için başka bir ekibi beklemek.
Niye o ekibe bel bağlamışız?
Bu süreci nasıl değiştirelim ki, diğer ekibe olan bağımlılığı ortadan kaldıralım?

Ekipler, yaptıkları işi en başından en sonuna kadar kendileri takip edip başka ekiplere ihtiyaç duymadan nihâyete erdirebilsinler.
İşlerin başka ekiplere devredilmesini en aza indirin, çünkü işi en iyi bilen onu üreten ekiptir.
Devredilecek ekibe göre o iş hakkında daha bilgilidir, daha ehildir.

> One of the core beliefs in the Toyota Production System is that, people closest to a problem typically know the most about it.

> Creating the conditions that enable change implementers to fully own the quality of their changes is an essential part of the high-trust, generative culture we are striving to build. Furthermore, these conditions enable us to create an ever-safer system of work, where we are all helping each other achieve our goals, spanning whatever boundaries necessary to get there.

> We strive to reduce the number of handoffs, either by automating significant portions of the work, or by building platforms and reorganizing teams so they can self-service builds, testing, and deployments to deliver value to the customer themselves instead of having to be constantly dependent on others. As a result, we increase flow by reducing the amount of time that our work spends waiting in queue, as well as the amount of non–value-added time.

### Geri bildirimleri hızlandırın

İşleri küçük parçalara bölün.
Tüm ekipler yaptıkları değişiklikleri ve geliştirmelerini olabildiğince küçük tutsun ki geri bildirim döngüsü kısalsın.

Geri bildirim sağlayacak mekanizmalar (loglar, dashboardlar, alarmlar vs.) kurun.
Değişiklik yaptığım uygulama patlamaya başladıysa, bunu başkasından öğrenmeme gerek kalmadan kendim hemen görebilmeliyim.

### Aynı anda yapılan iş sayısını sınırlandırın

Yarım kalan işler arttıkça, bunlar kafada yer etmeye devam eder, enerji ve zamanınızı çalar ve artık yeni iş yapamayacak hale gelirsiniz.
Eş zamanlı yürüttüğünüz işlerin sayısına bir sınır koyun.
Örnek: Sprint ortasında yeni iş almamak, aynı anda birden fazla işin "in-progress" olmaması.

> A significant difference between technology and manufacturing value streams is that our work is invisible. Unlike physical processes, in the technology value stream we cannot easily see where flow is being impeded or when work is piling up in front of constrained work centers. Transferring work between work centers in manufacturing is usually highly visible and slow because inventory must be physically moved.

### Kurum olarak hatâlardan ders çıkarın

Prod mu patladı?
Birilerini suçlamadan, süreçlere odaklanarak bu olayın köküne inin ve bir daha tekrarlamaması için tedbirler alın.
Öğrendiklerinizi tüm şirketle şeffaf bir şekilde paylaşın ki, yarın aynı hatâyı başka birinin yapma ihtimali azalsın veya hatâ tekrar ederse önceden nasıl çözüldüğünü kolayca görüp benzer çözümü yine uygulayabilsinler.
Örnek: blameless post-mortem alışkanlığı getirin.
Hatâları şeffaflıkla inceleyin, hiçbir şeyi halının altına süpürmeyin.
Bu işi resmî bir süreç hâline getirin.

> We’re trying to prepare for a future where we’re as stupid as we are today. In other words, it is not acceptable to have a countermeasure to merely “be more careful” or “be less stupid”—instead, we must design real countermeasures to prevent these errors from happening again.

### Operasyonel yükü azaltın

Operasyonel işler, ürün geliştiren ekipler için kısıtlayıcı faktör veya ayak bağı olmasın.
Bunun için, operasyonel işleri self-service hale getirmek şart.

> One way to enable market-oriented outcomes is for Operations to create a set of centralized platforms and tooling services that any Dev team can use to become more productive, such as getting production-like environments, deployment pipelines, automated testing tools, production telemetry dashboards, and so forth. By doing this, we enable Dev teams to spend more time building functionality for their customer, as opposed to obtaining all the infrastructure required to deliver and support that feature in production.

Operasyonel yükü ve acil durumları azaltacak adımlar atın.
Plansız işleri en aza indirin.
Ekipleri asıl yapmaları gereken işten uzaklaştırmak çok maliyetlidir.
İnsanlara odaklanacakları ortamları sağlayın ve dikkatlerini dağıtacak her şeyi engellemeye çalışın.

> Disruptions in manufacturing are also highly visible and costly. They often require breaking the current job and scrapping any incomplete work in process in order to start the new job. This high level of effort discourages frequent disruptions. However, interrupting technology workers is easy because the consequences are invisible to almost everyone, even though the negative impact to productivity may be far greater than in manufacturing. For instance, an engineer assigned to multiple projects must switch between tasks, incurring all the costs of having to reestablish context, as well as cognitive rules and goals. 

### Sistemlerinizi takip edin

Tüm sistemler kolayca ulaşıp bakabileceğiniz metrikler üretsin.
Bununla da kalmayın, bu metrikler üzerinden çeşitli kriterlerle tetiklenen alarmlar kurun.
Örnek:

- Siparişlerle ilgilenen web servis son 10 dakika içinde birden fazla 500 internal server hatâsı verirse, ilgili ekibe bildirim gitsin.
- Son bir saat içinde hiç yeni müşteri oluşmadıysa, CRM ekibine bildirim gitsin.

> By detecting and correcting problems earlier, we can fix them while they are small and easy to fix with fewer customers impacted. Furthermore, after every production incident, we should identify any missing telemetry that could have enabled faster detection and recovery; or, better yet, we can identify these gaps during feature development in our peer review process.

### İşlerin bitme kriterini keskin hatlarla belirleyin

Bir değişiklik veya yeni özellik geliştirilmesi işine "bitti" diyebilmek için şu kriterler sağlanmalı:

- İlgili geliştirmenin prod ortamına taşınmış olması
- Aktif olarak kullanıldığının ve tasarlandığı işi yaptığının görülmesi, yani üzerinden gerçek trafik geçmesi (test değil) ve beklenen neticeyi oluşturması
- Operasyonel olarak büyük bir ek yük veya planda olmayan ek iş çıkarmaması (mesela yeni özellik veritabanını çok yorup ara sıra patlamasına yol açıyorsa, bu işe "bitmiş" dememeliyiz)

### Bağımlılıkları azaltın

Büyük bir kurumsanız ve kalabalık yazılım ekipleriniz varsa, yazılım mimarinizi ekiplerin birbirinden bağımsız çalışabilmesine izin verecek şekilde tasarlayın: Mesela monolitik bir yapı yerine, web servisler kullanmanız gibi.

> Ideally, our software architecture should enable small teams to be independently productive.

[1]: /img/the-devops-handbook/the_devops_handbook.jpg
