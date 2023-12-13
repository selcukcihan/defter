---
title: "Don't Make Me Think"
tags: ["UX", "UI", "design", "tasarım", "usability", "web", "kitap"]
date: 2023-12-13T07:41:29+03:00
draft: false
images: ["/img/dont-make-me-think/dont_make_me_think.jpg"]
---

Yazılımcıların belki de en eksik kaldığı taraf kullanıcı deneyimi.
Bir şekilde önyüze bulaşıyoruz, fakat işin özüne hakim olmadığımız için kullanımı zor ve anlaşılmaz ürünler ortaya çıkarıyoruz.
Bu eksikliğimi bildiğim için, üzerine gitmek adına [bu kitabı](https://www.amazon.com/Dont-Make-Think-Revisited-Usability/dp/0321965515) okudum.
Ama kitap pek de beklediğim gibi çıkmadı.
UX ile ilgili nokta atışı tavsiyeler arıyordum, ama maalesef bunlar az sayıda.
Daha çok genel bir anlayış oturtmaya çalışıyor kitap, bu anlayış penceresinden bakıp ona göre ürünler tasarlama kısmı bize kalıyor.
Kitap on yıl evvel yazılmış olsa da güncelliğini koruyor.

![Don't Make Me Think][1]

## Kullanıcı Deneyimi

Bir ürünün tüm kullanıcılara hitap etmesi çok zor.
Bu yüzden, kendimize şunu sormalıyız: Nasıl bir sayfa olmalı ki, herkes için kullanımı kolay olsun?

> If something requires a large investment of time—or looks like it will—it’s less likely to be used.
> A person of average (or even below average) ability and experience can figure out how to use the thing to accomplish something without it being more trouble than it’s worth.

Bir web sayfasında kafa kurcalayan tüm detayların üzerine eğilip, kullanıcıların kafasındaki soru işaretlerini gidermeliyiz.
Hedefimiz, basit ve gürültüden uzak, kullanıcının dikkatini dağıtmadan onu amacına ulaştıran tasarımlar olmalı.

> The point is that every question mark adds to our cognitive workload, distracting our attention from the task at hand. The distractions may be slight but they add up, especially if it’s something we do all the time like deciding what to click on.
> The most important thing you can do is to understand the basic principle of eliminating question marks.

Bazı işler doğası gereği karmaşık olabilir, basitleştiremeyebiliriz.
Bu durumda da yapılacak olan şey, kullanıcıların kendi kendilerine işin içinden çıkabilmelerini sağlayacak ipuçları ve yönlendirmeler sağlamak.

> Here’s the rule: If you can’t make something self-evident, you at least need to make it self-explanatory.

## Hedef Kitle

Web sayfalarını, kafamızda belirli bir hedef kitlesi olsa bile, herkes için kolay ve anlaşılır yapmalıyız.
Bunu yaparken, insanlarla ilgili şu gerçekleri hatırlamak gerek:

1. Sayfaları baştan sona okumuyoruz, hızlıca tarayıp geçiyoruz. Bu yüzden, kullanıcıların sayfada kolayca göz gezdirip, yönlerini bulabilmelerini sağlamak mühim.

    > FACT OF LIFE #1: We don’t read pages. We scan them.

2. Bir işi yaparken veya karar verirken mükemmelliği aramıyoruz, idare edecek bir yol bulduğumuzda hemen peşine düşüyoruz.

    > FACT OF LIFE #2: We don’t make optimal choices. We satisfice.

    Buna örnek olarak, itfaiyeciler üzerinde yapılan bir araştırma verilmiş.
Acil durumlarda, itfaiyeciler ellerindeki tüm opsiyonları değerlendirmek yerine, akıllarına yatan ilk kararı uyguluyorlarmış.

    > As it turned out, the fire commanders didn’t compare any options. They took the first reasonable plan that came to mind and did a quick mental test for possible problems. If they didn’t find any, they had their plan of action.

3. Sistematik bir öğrenme yerine, deneme yanılma ile ilerliyoruz.

    > FACT OF LIFE #3: We don’t figure out how things work. We muddle through.

4. Bir web sitesi için anlaşılır olmak, tutarlı olmaktan daha mühim. Sitenizdeki bir özelliği daha anlaşılır kılmak için, sitenin geri kalanıyla çok da uyuşmayan şeyler deneyebilirsiniz, tutarlı olmak ikinci planda.

    > CLARITY TRUMPS CONSISTENCY If you can make something significantly clearer by making it slightly inconsistent, choose in favor of clarity.

## Tüyolar

1. Sayfalarınızda görsel hiyerarşiyi sonuna kadar kullanın; farklı "heading"ler, boşluklar ve font varyasyonları ile kullanıcının sayfayı kolay tarayabilmesini sağlayın.

    > A good visual hierarchy saves us work by preprocessing the page for us, organizing and prioritizing its contents in a way that we can grasp almost instantly.
    > Use plenty of headings. Well-written, thoughtful headings interspersed in the text act as an informal outline or table of contents for a page.

2. Kullanıcılarınızın fazla vakti ve dikkati yok. Dikkat çekmesi gereken şeyleri sırf şıklık veya estetik kaygılarla saklamayın, keşfedilmesini zorlaştırmayın.

    > Too-subtle visual cues are actually a very common problem. Designers love subtle cues, because subtlety is one of the traits of sophisticated design. But Web users are generally in such a hurry that they routinely miss subtle cues.

3. Mutlaka kullanılabilirlik testi yapın, erken başlayın, sık sık yapın. Kendi hislerinize, deneyimlerinize ve tercihlerinize güvenmek yerine, kullanıcılardan geribildirim alın ki ürününüz saçma sapan yerlere doğru evrilmesin. Sizin kafanızdaki kullanıcı profili ile gerçek hayat arasında çok büyük farklar olduğunu unutmayın.

    > Testing one user early in the project is better than testing 50 near the end. 

4. Bilgi saklamayın: Destek hattı, ücretlendirme, adres, yasal bilgiler vs. açıkça belirtilsin ve gerekli bağlamlarda kolayca bulunabilir olsun.

    > Things that diminish goodwill Here are a few of the things that tend to make users feel like the people publishing a site don’t have their best interests at heart: Hiding information that I want. The most common things to hide are customer support phone numbers, shipping rates, and prices.

5. Kullanıcıyı, sizin istediğiniz gibi davranmadığı için cezalandırmayın. Mesela, telefon numarası girerken mutlaka belirli bir formatta girmek zorunda kalmasın, sürtünme katsayısını azaltın, copy/paste çalışsın.

    > Punishing me for not doing things your way. I should never have to think about formatting data: whether or not to put dashes in my Social Security number, spaces in my credit card number, or parentheses in my phone number. Many sites perversely insist on no spaces in credit card numbers, when the spaces actually make it much easier to type the number correctly. Don’t make me jump through hoops just because you don’t want to write a little bit of code.

6. Kullanıcıdan lüzumsuz bilgi istemeyin: Herkesin acelesi var, kimse kimseye güvenmiyor. Bu şartlarda, ekstra bilgi istemek çok mantıklı değil.

    > Asking me for information you don’t really need. Most users are very skeptical of requests for personal information and find it annoying if a site asks for more than what’s needed for the task at hand.

[1]: /img/dont-make-me-think/dont_make_me_think.jpg
