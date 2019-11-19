---
title: "Blogları birleştir"
date: 2019-11-19T14:41:29+03:00
draft: false
---

## Olay ne?
Yaptığım geliştirmeleri, öğrenme süreçlerimi ve çözdüğüm problemleri bir yerlere yazmayı her zaman faydalı bulmuşumdur.
Şimdiye kadar yazılarımı, biraz wordpress, biraz medium ve biraz da kendim yönettiğim blog sayfalarında yayınlıyordum.
Bundan sonra yazdıklarımı, tüm eski yazılarımı da alıp, kendi yönettiğim tek bir blog altında yayınlamaya karar verdim.

## Neden?

Neden en baştan bu işi tek bir yerde yapmadım derseniz, biraz maymun iştahlılık biraz da üşengeçlikten.
Temel sebep şudur ki, yazılar arttıkça, yönetmek de zorlaşıyor.
Artık bu işi sistematik bir hale getirip, mevcut yazıları da ortak bir blog'a toplamanın vakti geldi.

## Nasıl (static site generator)

Blog'um için bazı kriterlerim var:

* Bedava olmalı (bu işten para kazanmadığım için, yatırım yapmak da istemiyorum)
* Hızlıca ayağa kaldırabilmeliyim (bir yerde host edip, kendi custom subdomainimden yayınlayabilmeliyim - blog.selcukcihan.com)
* Yazılarımı kolayca ekleyebilmeliyim
* Hazırda seçebileceğim birden fazla güzel tema olmalı (css, html vs. ile vakit kaybetmek istemiyorum)
* Yazılarımı [markdown](https://daringfireball.net/projects/markdown/) ile yazabilmek

Yani kısacası, ihtiyacım kullanımı kolay bir *static site generator* ve bir de hosting.
Alan adım zaten hazır, selcukcihan.com bana ait.
Blog'u da blog.selcukcihan.com'dan yayınlayacağım.

## Tercihlerim

### Source control

Kodları ve yazılarımı git ile versiyonlayıp, github üzerinde saklıyorum.

### Hosting (netlify)

Blog'u bir web uygulaması olarak düşündüğümüzde, veritabanı olmayacak ve sadece statik html/js/css gibi kaynaklar kullanılacak.
Bu yüzden, basit bir web sunucusu işimi görecek.
Alternatifler arasında, AWS, GCP ve [Netlify](https://www.netlify.com/) var.
Fazla uzatmadan, bunlar arasında ihtiyacımı en kolay ve ucuza gören açık ara netlify.
Geçenlerde [reactjs](https://reactjs.org/) ile yaptığım bir [tetris uygulamasını](http://tetris.selcukcihan.com/) netlify üzerinden sunmam sadece beş dakikamı aldı.
Hugo için de [şuradaki](https://gohugo.io/hosting-and-deployment/hosting-on-netlify/) tutorial'ı takip ederek hallettim işimi.


### Framework (hugo)

Blog'u oluşturmak için pek çok *framework* var.
Mesela: [jekyll](https://jekyllrb.com/), [gatsby](https://www.gatsbyjs.org/) ve [hugo](https://gohugo.io/).
Bu üçlü arasında, jekyll ile bir deneyimim oldu, [selcukcihan.com](https://www.selcukcihan.com) kişisel sayfamı jekyll ile hazırladım ve github pages ile host ettim.
Fakat son zamanlarda hugo çok sık karşıma çıkmaya başladı, ben de karşılaştırmada hugo'ya biraz torpil geçtim.
Burada detaylı bir karşılaştırma yapmayacağım, zaten karşılaştırma yapacak kadar da hakim değilim hiçbirine.
Fakat hugo ile ilgili genel söylem şu yönde, her şey hazır geliyor (ruby on rails mantığı, ihtiyaç olan her şeyi dahil etmişler).
Mesela gatsby'de bir şeyler yapmak için mutlaka ek plugin'ler kurmak gerekiyor, fakat hugo'da en baştan beri ihtiyaç duyduğunuz her şey pakete dahil geliyor.

## Hello world

Hugo ile *hello world* diyebileceğimiz çıplak blog'u aşağıdaki gibi oluşturdum.

1. Hugo'yu kurdum:

```
brew install hugo
```

2. Blog'u oluşturdum:

```
hugo new site defter
```

3. Git repository'si oluşturdum:

```
cd defter && git init
```

4. Tema kurdum:

```
git submodule add https://github.com/vividvilla/ezhil.git themes/ezhil
```

5. Kendi bilgisayarımda blog uygulamasını ayağa kaldırıp, çalıştığını gördüm:

```
hugo server -D
```

6. Değişikliklerimi git repository'me commitledim:

```
git add . && git commit -m "Hugo ile blog ekle"
```

7. Github'da remote repository oluşturdum:

SS_1

```
git remote add origin https://github.com/selcukcihan/defter.git
```

8. 

hugo new posts/bloglari-birlestir.md










