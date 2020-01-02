---
title: "Yazılımcılar için Amazon mülakat süreci"
tags: ["amazon", "mülakat", "yazılım", "algoritma", "yurt dışı", "davranışsal", "teknik"]
date: 2018-07-29T14:41:29+03:00
draft: false
images: ["/img/yazilimcilar-icin-amazon-mulakat-sureci/whiteboard.jpg"]
---

Amazon'da yazılım mühendisi olmak için, zorlu bir mülakat sürecinden geçmeniz gerekiyor. Bu yazıda, başarı ihtimalinizi artırmak için neler yapmanız gerektiğini anlattım.

*Burada yazdıklarım şahsi fikir ve yorumlarım olup, Amazon firmasını bağlamamaktadır.*

![White board][1]
<center>*Photo by Paul Hanaoka on Unsplash*</center>

## Genel bilgilendirme

### Sizden istenen
Amazon dünyanın en büyük teknoloji firmalarından biri [^1]. Çok başarılı bir hikayesi var. Kendine has bir kültüre sahip ve firmayı başarılı kılan temel sebeplerden biri de bu kültür. Firma kültürü, üzerinde bilinçli olarak ısrarla durulan bir husus ve öyle doğal bir sonuç veya rastgele oluşmuş bir şey değil. Üzerinde kafa yorulmuş, başarıya götüren bir anahtar olarak görülen bir dizi ilkeden [^2] inşa edilmiş. Bahsi Amazon'un liderlik ilkelerine getirmemin sebebi de bu; **adaylarda asıl aradıkları şey, belirledikleri ilkelere yatkınlıkları**.

### SDE I, II ve III
Değerlendirildiğiniz pozisyonun seviyesine göre mülakat süreci değişebiliyor, burada bahsettiğim süreç SDE II mülakatı. Az tecrübeli yazılımcılar SDE I olarak değerlendiriliyor, senior denilen pozisyon ise SDE III oluyor. Bu arada, senior'lıktan kasıtları ülkemizdeki gibi yıl endeksli değil, tamamen ne kadar iyi bir yazılım mühendisi olduğunuzla alakalı. Çıta epey yüksek, dolayısıyla ilk etapta senior'lığa heves etmeye gerek yok, gerçekçi bir hedef belirlemeniz lazım. Mülakat safhalarına geçmeden önce son bir husus, Amazon çok büyük bir firma fakat her ekip adeta bir start-up gibi hareket ediyor. O yüzden firmadaki süreçler dışına çıkılmayan katı birer kanun gibi değil, oldukça dinamik ve esnek. Bu bakımdan, anlattıklarımdan farklı bir mülakat süreci yaşamanız da mümkün. Fakat yine de genel hatları ve sizde aradıkları özellikler değişmeyecektir.

## Süreç

### Nasıl başvuracağım?

Ben iki kere başvurdum Amazon'a. İlk başvurumu 2015 yılında, Dublin'deki bir AWS pozisyonuna, Amazon'un iş ilanları sayfasından [^3] yaptım ve kabus gibi geçen bir mülakatlar serisinden sonra reddedildim. Yenilen pehlivan güreşe doymazmış, ertesi sene tekrar mülakata girdim fakat bu sefer onlar bana ulaştı. Amazon İK'dan biri bana email attı ve İstanbul'da gerçekleşecek işe alım organizasyonuna davet etti. Buradan da anlayacağınız üzere, belirli aralıklarla işe alım organizasyonları düzenliyorlar, ama bunun haricinde benim ilk başvurumdaki gibi siz kendiniz belli bir pozisyona müstakil olarak da başvurabilirsiniz.

### CV ve ilk adım
Öncelikle iyi bir CV'niz olmalı. Bununla ilgili detayları araştırıp öğrenin. Benden tavsiye, iki sayfayı geçmesin. Amazon'un kendi iş ilanları sayfasından [^3] istediğiniz ilanı bulup başvuruyorsunuz. Başvurunuzu yaptıktan sonra bekliyorsunuz, ta ki İK'dan biri size dönüp güzel haberi verinceye kadar: *"Sizi değerlendirmek isteriz"*. Bekleme süresi bir haftadan bir aya kadar değişebiliyor, hemen ümitsizliğe kapılmayın! Hackerrank [^4] veya benzeri bir online kodlama platformundan size bir kodlama testi gönderecekler. Genellikle iki soru oluyor ve mülakat sürecindeki en kolay kısım diyebilirim.

İlk mülakat sürecimde online bir test yaptırmamışlardı ve telefon üzerinden bir saatlik teknik mülakata tabi tutulmuştum. İlk yarım saatte, *soft skills* dedikleri davranışsal özelliklerinizi öğrenmeye çalışıyorlar.

> Bir iş arkadaşınızla yaşadığınız çatışmayı/anlaşmazlığı anlatır mısınız? Bunu aşmak için ne yaptınız?
> Zaman baskısı altında yetiştirmeye çalıştığınız bir proje var, yetişmeyeceğinden eminsiniz, ne yaparsınız?

Gibi sorulara hazırlıklı olun. Bu sorulara cevap verirken mutlaka gerçekten yaşadığınız örnekler üzerinden cevap verin. Uydurma hikayeler genelde bir yerden patlak verecektir, yalan söylemek ve bu yalanı destekleyecek hikayeler düşünmek beyninizi gereksiz yere yoracaktır. Mülakat zaten stresli ve yorucu, o yüzden önceden bu tip 5-10 soru ve gerçek cevaplar düşünerek mülakata girmeniz en doğrusu olacaktır. Ayrıca bütün sorularda, cevaplarınız mutlaka kendinizle alakalı olsun. Mülakatı yapanlar sizin yeteneklerinizi merak ediyorlar, takım olarak bir şeyler yapmış bile olsanız bunu anlatırken kendi katkılarınızı ön planda tutun. Özneniz "ben" olsun, "biz" değil.

Son yarım saatte ise, istediğiniz herhangi güncel bir programlama dilinde, basit sayılabilecek bir problemi çözmenizi istiyorlar. Collabedit [^5] tarzı bir portalde, her ikinizin de eş-zamanlı görüp üzerinde değişiklik yapabildiği bir text editör üzerinde bu problemi çözüyorsunuz. Siz kodu yazarken, karşıdaki kişi aynı anda yazdıklarınızı görüyor. Burada dikkat etmeniz gereken husus, her ne kadar ortada bir IDE olmasa da doğru syntax'a ve derli toplu yazmaya özen göstermek. Elbette ufak tefek yazım yanlışlarını veya unutulan bir parantez işaretini görmezden geliyorlar. Neticede karşınızdaki kişi de bir yazılımcı ve yazdığınız kodu her yönüyle değerlendirecektir. Dönüp baktığınızda gurur duyacağınız bir kod yazmaya çabalayın. Tabii kodu yazmak da yetmiyor, kodunuzun nasıl çalıştığından tutun da *time complexity*, *space complexity* ve benzeri mefhumlarını da izah etmeniz bekleniyor. Ayrıca, "bunu nasıl test ederdiniz" veya "bu fonksiyon hangi durumlarda çalışmaz" gibi sorulara hazırlıklı olun.

### *ON-SITE* VEYA VIDEO KONFERANS

Eğer online testte veya telefon üzerinden yapılan teknik mülakatta başarılı olduysanız, ilk mülakattan takriben 1-2 ay sonrasına asıl mülakat için gün veriyorlar. Bu safhada yaklaşık 4-5 kişi ile ayrı ayrı birer saatlik teknik mülakatlara giriyorsunuz. Mülakatların hepsi aynı gün oluyor ve peş peşe gerçekleşiyor. Bazı durumlarda arada bir saatlik boşluğunuz olabiliyor. Bazen de bir mülakata iki kişi girebiliyorlar, iki kişiden biri asıl mülakatı yapan kişi oluyor diğeri de onu izleyip nasıl mülakat yaptığını gözlemliyor - yani eğitim amaçlı. Duruma göre ya sizi Amazon'un bir ofisine davet ederek yüz yüze (on-site) ya da video konferans üzerinden online olarak görüşüyorlar. Benim tavsiyem, eğer video konferans yapacağız derlerse mutlaka "on-site yapabilir miyiz, video konferans üzerinden kendimi yeterince iyi gösteremeyeceğimden endişe ediyorum" şeklinde bir yoklamanız. Tabii siz video konferansı tercih ediyor da olabilirsiniz, fakat şahsi tecrübem yüz yüze görüşmenin daha rahat ve etkili olduğu. Eğer işe alım organizasyonu üzerinden başvuru yaptıysanız, zaten on-site olacaktır mülakatınız.

Benim ilk başvuru sürecim video konferans üzerinden devam etmişti ve 5 farklı yazılımcı Kaliforniya, Seattle, Dublin ve Edinburgh'dan beni fevkalade zorlamışlardı. Hani eski türk filmlerinde, adamı ortaya alıp bir o tarafa bir bu tarafa tokatlayarak paslaştıkları sahneler vardır ya... Öyle ki, son mülakatta artık dermanım kalmamış ve pes etmiştim, kendimi hiç verememiştim. Video konferansın en kötü yanı havaya girememeniz ve iletişimin kısıtlı olması. Mülakatlar yine aynı ilk teknik mülakat tarzında iki bölüme ayrılabiliyor: davranışsal ve teknik. Yazılımcılardan biri *bar raiser* dedikleri, o gruptaki en yetkin ve sizi muhtemelen en çok terletecek kişi oluyor. Bunu resmi olarak size söylemiyorlar ama mülakatlar bittikten sonra hangisi olduğundan emin oluyorsunuz :).

Eğer yüz yüze mülakattaysanız, iki kişi bir odada olacaksınız. Odada en az bir masa, iki sandalye ve bir beyaz tahta olacak. Mülakatı yapan kişinin laptop'u açık olacak ve mülakat boyunca sizinle alakalı notlar alacak. Mülakat bittikten sonra da derledikleri notları birbirleriyle paylaşarak sizin kritiğinizi yapacaklar ve bir karara varacaklar. Aynı şeyler video konferans için de geçerli, yani siz bir yandan kod yazarken veya anlatırken karşınızdaki kişi bilgisayarına notlar alacak. Bu bazen sinir bozucu olabiliyor veya dikkat dağıtabiliyor, elinizden geldiğince tıkır tıkır klavye sesine aldırmamaya çalışın ve hakkınızda olumlu şeyler yazdığını düşünün.

Kodu beyaz tahtaya yazmanız gerekecek, işte aslında en büyük ipucu burada arkadaşlar. Yani beyaz tahtada kodlayarak sizce ne kadar karışık bir problem çözülebilir? Önce bir class'ı komple kodlayıp sonra tahtayı silip diğer class'ı yazmak diye bir şey olamayacağına göre, bütün problemi tek sayfada çözmeniz gerekiyor. Bu neye işaret ediyor biliyor musunuz? Sorular oldukça kısa çözümlere sahip, dallanıp budaklanmıyor ve süreyi de dikkate alırsanız yarım saat içinde çözülebilecek problemler. Mesela, size hiçbir zaman bana bir red-black tree [^6] yaz demeyecekler. Fakat aynı online kodlamada olduğu gibi, seçtiğiniz dilin syntaxına bağlı kalarak ve mümkün olduğunca *production kalitesinde* dönüp baktığınızda göğsünüzü gererek "ben yazdım" diyebileceğiniz şekilde kodlamanız gerekiyor.

Siz soruyu çözerken, karşınızdaki mülakatı yapan şahıs sizin soruya nasıl yaklaştığınızdan tutun da problemde eksik/belirsiz gördüğünüz noktalarla alakalı nasıl sorular sorduğunuza dair çeşitli noktalarda notlar alacak. O yüzden, soruyu çözerken mutlaka kafanızdan geçenleri ona anlatın. Bir karar verdiğinizde o karara nasıl vardığınızı aktarın: "Burada bir *hash map* kullanıyorum çünkü elemanlara hızlı ulaşmak istiyorum ve sıralamanın hiçbir önemi yok çünkü elimde ulaşmak istediğim elemanın *key'i* var" gibi. Soru sormak çok önemli, size bazen kasıtlı olarak problemle alakalı eksik bilgi verecekler (yanlış veya yanıltıcı bilgi vermezler, fakat bir noktanın ucunu açık bırakırlar veya o hususa hiç değinmeyebilirler) ve bu eksikliği fark edip tamamlamak için kendilerine soru yöneltmenizi bekleyecekler. Amazon'da günlük iş hayatında oldukça sık ihtiyaç duyduğunuz bir özellik olacak bu. Çünkü, gerçek hayatta karşınıza çıkacak hemen hemen her problemde belirsizlikler olacak ve sizden beklenen bu belirsizlikleri gidermeniz veya makul varsayımlar yapabilmeniz.

Bir diğer önemli husus; arada bazı detayları bilinçli olarak atlamanız gerekebilir. Mesela, benim ikinci başvuru sürecimdeki on-site mülakatta, problemi çözdüm fakat ilk aklıma gelen şekilde kodlamaya geçtim ve 15 dakika sonra aslında bunun çok daha performanslı ve şık başka bir çözümü olduğu aklıma geldi. Hemen durumu izah ettim, ben bunu şöyle çözebileceğimi düşünüyorum ve şu şu sebepten çok daha kaliteli bir çözüm olacak dedim. Daha sonra tahtayı komple silip baştan başladım, ama tabii vakit iyice daraldı ve kodun bir kısmını atlamak zorunda kaldım. Atladığım kısmı mülakatçıya detaylıca tasvir ettim ve içi boş bir fonksiyon olarak tahtaya ekledim. Bu mülakat sürecim başarıyla sonuçlanmıştı; her şeyin mükemmel ve eksiksiz olması gerekmiyor. Makul ölçüde ve gerekçelerini izah ederek bazı varsayımlarda bulunabilirsiniz.

Konuşmak çok önemli. Şöyle düşünün, karşınızda bir yazılımcı var ve bir saat içinde sizinle ilgili bir kanaate varmaya çalışıyor. Ona ne kadar *data point* sunarsanız, kararını o kadar rahat verecektir. Hatta sizi konuşmaya teşvik etmek için arada sufle bile verebilirler veya sizi bilinçli olarak bir yönde ilerlemeye teşvik edebilirler. Size verdikleri tavsiyeleri ve ipuçlarını sakın yabana atmayın ve dikkatlice dinleyin, işinizi kolaylaştırır. Amaçları sizi kandırmak veya akıl oyunları yapmak değil. Tek dertleri, sizin potansiyelinizi ölçebilmek ve Amazon'a ne kadar uygun olduğunuzu anlayabilmek. İşe alım organizasyonlarında adaylar birbirleriyle yarışmıyorlar. Yani eğer bütün adaylar başarılı olsa, hepsine iş teklifi yapılacak. Sadece kendinizle yarıştığınızdan emin olabilirsiniz.

On-site mülakatların bir diğer güzel yanı (eğer Türkiye'deki bir işe alım organizasyonu değilse) bedava yurtdışı seyahati yapacak olmanız. İkinci serüvenimde, İstanbul'daki işe alım organizasyonuna davet edilmiştim. Fakat maalesef kısa bir süre sonra 28 Haziran Atatürk Havalimanı terör saldırısı gerçekleşti ve Amazon güvenlik gerekçesiyle organizasyonu Belgrad'a kaydırdı. Belgrad'a gidiş dönüş uçak biletim, oradaki konaklama masrafım (şehir merkezinde mülakatın yapılacağı yere yürüme mesafesindeki 4 yıldızlı bir otel ayarlamışlardı) ve taksi (mesela İstanbul'da evinizden havalimanına ve Belgrad'da havalimanından otelinize), yeme/içme gibi ek masraflarımı karşılamışlardı. Günlük sınır var tabii, sınırsız harcama yapamıyorsunuz ama sınırlar oldukça geniş. Ben mutedil bir seyahat sonrası, harcama hakkımın yarısını bile kullanmamıştım. Uçak biletini direkt Amazon alıyor, diğer masrafları önce kendi cebinizden yapıyorsunuz daha sonra Amazon'a fişleri gönderip (fotoğrafını tabii ki) ödemeyi alıyorsunuz. Ben mülakat günümden bir gün önce Belgrad'a gidip, iki gece konaklayıp dönmüştüm. İsteğinize göre bir gün veya iki gün konaklayabiliyorsunuz - üç güne izin verirler mi bilmiyorum :) Eğer mülakata gideceğiniz ülke vize istiyorsa, vize masraflarınızı da karşılıyorlar.

Teknik mülakatın bir bölümünde de size tasarım yaptıracaklar. Örnek bir problem üzerinden, o problemi çözecek sistemi ve bileşenlerini tasarlayacaksınız; mesela veri modelini ve varsa API kontratını belirlemek gibi. İçinde en çok belirsizlik içeren bölüm de genelde tasarım bölümü oluyor, o yüzden bolca soru sorun. Tasarım kısmında pek kod yazmak gerekmiyor, sistemin davranış detaylarından daha çok veri modeli ve high-level design'ı [^7] tahtaya/editöre yazmanız gerekiyor.

## Netice

Bu mülakatlar safhası bittikten sonra birkaç gün içinde, henüz size dair izlenimleri taze iken, kararlarını verecekler. Başarısızlıkla sonuçlanan ilk denememde, video konferans mülakatları sonrası 4 gün içinde cevabım gelmişti:

> Merhaba Selcuk,
> Amazon'a gösterdiğin ilgi ve bize ayırdığın değerli zamanın için teşekkür ederim. (burada anlıyorsunuz ters bir şeyler olduğunu)
> CV'n ve mülakatların AWS takımlarımızca dikkatle değerlendirildi. Her ne kadar yeteneklerinize saygı duysak da, bu pozisyon için çok da uygun olmadığın sonucuna vardık.
> Bu hayal kırıcı olabilir, fakat Amazon sürekli büyüyor ve umarım ileride doğabilecek yeni fırsatlar için iletişim detaylarını saklamamızın bir sakıncası yoktur. Bu arada, iş ilanları sayfamızı sık sık kontrol edip sana uygun olabileceğini düşündüğün pozisyonlara bakabilirsin.
> Bir kaç teselli cümlesi daha ve saygılar...

Fazla beklentim olmamasına rağmen, bu emaili okuduktan sonra canımın çok sıkıldığını hatırlıyorum. Sırasıyla şu duyguları yaşadım:

0. Of yaaa ne güzel olacaktı, keşke geçebilseydim keşke daha sıkı hazırlansaydım.
1. Sanki çok da matah bir yer, beni ne şirketler istedi de ben gitmedim.
2. Depresyon, ne yapacağım ben şimdi, hiçbir şeye hevesim yok.
3. Hayat devam ediyor, yenilgiyi kabulleniyorum ve önümdeki maçlara bakıyorum.

Kitabına göre üzüleyim derseniz, Kübler-Ross modelini [^8] bir inceleyin :) Daha sonraki süreçte Amazon'a başka başvuru yapmadım. Ta ki ertesi sene onlar beni İstanbul'daki işe alım organizasyonuna davet edinceye kadar. O zaman çok daha sıkı hazırlanmış ve Belgrad dönüşü şöyle bir email almıştım:

> Tebrikler Selçuk, Toronto'daki FBA (Fulfillment By Amazon) ekibi sana yazılım mühendisliği pozisyonu için teklif sunmak istiyorlar.

**Bir kere reddedilmeniz, bir daha başvuramayacağınız anlamına gelmiyor**. Altı ay gibi bir bekleme süresinden sonra tekrar başvurularınızı değerlendirmeye alıyorlar. Yalnız unutmayın ki, mülakat süreçlerinizle ilgili tüm detayları saklıyorlar ve müteakip başvurularınızı incelerken bunları da göz önünde bulunduruyorlar. İkinci kez başvururken mutlaka ilk seferde kendinizde eksik gördüğünüz noktalara eğilip noksanlarınızı giderin.

## Bazı detaylar

* Burada anlattığım bazı hususların ve sordukları soruların detaylarına giremiyorum, çünkü mülakat sürecinin başında bir gizlilik sözleşmesi [^9] imzalatıyorlar.
* Giyim kuşam önemli değil, ceket kravatla da gidebilirsiniz yerel kostümünüzü [^10] de giyebilirsiniz. 
* Zamanlamanıza çok dikkat edin! İster telefon mülakatı olsun, ister yüz yüze olsun, ister video konferans olsun - mutlaka dakik olun. Eğer anlaştığınız saate bile riayet edemiyorsanız, insanlar size güvenip de niye iş versinler ki.
* Bütün mülakatlarda, son beş dakikayı sizin sorularınıza ayırmaya çalışırlar. Soracak güzel sorularınız olsun, bunların cevapları sizin için önemli de olabilir önemsiz de olabilir; fakat güzel sorular sormak her zaman muhatabınızda iyi izlenim bırakır.
* Mülakatlar sonrası başarılı olur da teklif alırsanız, teklif her türlü pazarlığa açık. Maaş, hisse, taşınma yardımı gibi maddi mevzularda zaten pazarlığınızı mutlaka yapın.
* Hangi ekipte veya hangi ofiste çalışmak istediğiniz noktasında da pazarlık yapabilirsiniz. Mesela benim katıldığım işe alım organizasyonu Kanada için alım yapıyordu, ama ben teklif aldıktan sonra Londra ofisinde çalışmak istediğimi belirttim ve kabul ettiler. Unutmayın ki sizin için bunca zaman/masraf/emek harcadılar, o yüzden hiçbir zaman kestirip atmayacaklardır ve isteklerinize kulak vereceklerdir. Bu demek değil ki her istediğinizi kabul edecekler, ama en azından değerlendireceklerinden emin olun.
* "Brain teaser" denilen türden, zihni sinir sorular kesinlikle sormayacaklar!


[^1]: [Wikipedia - largest information technology companies](https://en.wikipedia.org/wiki/List_of_the_largest_information_technology_companies)
[^2]: [Amazon leadership principles](https://www.amazon.jobs/principles)
[^3]: [Amazon jobs](https://www.amazon.jobs/)
[^4]: [Hackerrank](https://www.hackerrank.com)
[^5]: [Collabedit](http://collabedit.com/)
[^6]: [Wikipedia - RB Trees](https://en.wikipedia.org/wiki/Red%E2%80%93black_tree)
[^7]: [Wikipedia - high level design](https://en.wikipedia.org/wiki/High-level_design)
[^8]: [Wikipedia - Kübler - Ross modeli](https://en.wikipedia.org/wiki/K%C3%BCbler-Ross_model)
[^9]: [Wikipedia - NDA](https://en.wikipedia.org/wiki/Non-disclosure_agreement)
[^10]: [Rize - yöresel kıyafetler](http://www.rizekulturturizm.gov.tr/TR,113098/erkek-kiyafetleri.html)

[1]: /img/yazilimcilar-icin-amazon-mulakat-sureci/whiteboard.jpg
