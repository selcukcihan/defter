---
title: "Blog'larımı birleştirdim"
tags: ["Blog", "hugo", "netlify", "statik web sitesi", "go", "static web site", "deployment", "subdomain"]
date: 2019-11-19T14:41:29+03:00
draft: false
---

TLDR; farklı yerlerdeki blog yazılarımı tek bir mecraya topluyorum. Framework olarak hugo, hosting için ise netlify kullanıyorum.

## Olay ne?
Yaptığım geliştirmeleri, öğrenme süreçlerimi ve çözdüğüm problemleri bir yerlere yazmayı her zaman faydalı bulmuşumdur.
Şimdiye kadar yazılarımı, biraz [wordpress](https://selcukcihan.wordpress.com/), biraz [medium](https://medium.com/bulut-aws) ve biraz da [github pages](https://pages.github.com/) ile yönettiğim [blog sayfalarında](http://selcukcihan.com/blog) yayınlıyordum.
Bundan sonra yazdıklarımı, tüm eski yazılarımı da alıp, kendi yönettiğim tek bir blog altında yayınlamaya karar verdim.

## Neden?

Neden en baştan bu işi tek bir yerde yapmadım derseniz, biraz maymun iştahlılık, biraz da üşengeçlikten.
Temel sebep şudur ki, yazılar arttıkça, yönetmek de zorlaşıyor.
Artık bu işi sistematik bir hale getirip, mevcut yazıları da ortak bir blog'a toplamanın vakti geldi.

## Nasıl (static site generator)

Blog'um için bazı kriterlerim var:

* Bedava olmalı (bu işten para kazanmadığım için, yatırım yapmak da istemiyorum)
* Hızlıca ayağa kaldırabilmeliyim (bir yerde host edip, kendi custom subdomainimden yayınlayabilmeliyim - misal blog.selcukcihan.com)
* Yazılarımı kolayca ekleyebilmeliyim
* Hazırda seçebileceğim birden fazla güzel tema olmalı (css, html vs. ile vakit kaybetmek istemiyorum)
* Yazılarımı [markdown](https://daringfireball.net/projects/markdown/) ile yazabilmek

Kısacası ihtiyacım, kullanımı kolay bir *static site generator* ve bir de hosting.
Alan adım zaten hazır, selcukcihan.com bana ait.
Blog'u da blog.selcukcihan.com'dan yayınlayacağım.

## Tercihlerim

### Source control

Kodları ve yazılarımı git ile versiyonlayıp, [github üzerinde](https://github.com/selcukcihan/defter) saklıyorum.

### Hosting (netlify)

Blog'u bir web uygulaması olarak düşündüğümüzde, veritabanı olmayacak ve sadece statik html/js/css gibi kaynaklar kullanılacak.
Bu yüzden, basit bir web sunucusu işimi görecek.
Alternatifler arasında, AWS, GCP ve [Netlify](https://www.netlify.com/) var.
Lafı uzatmadan, bunlar arasında ihtiyacımı en kolay ve ucuza gören açık ara netlify.
Geçenlerde [reactjs](https://reactjs.org/) ile yaptığım bir [tetris uygulamasını](https://tetris.selcukcihan.com/) netlify üzerinden sunmam sadece beş dakikamı aldı.
Hugo için de [resmî tutorial'ı](https://gohugo.io/hosting-and-deployment/hosting-on-netlify/) takip ederek hallettim işimi.


### Framework (hugo)

Blog'u oluşturmak için pek çok *framework* var.
Mesela: [jekyll](https://jekyllrb.com/), [gatsby](https://www.gatsbyjs.org/) ve [hugo](https://gohugo.io/).
Bu üçlü arasında, jekyll ile bir deneyimim oldu, [selcukcihan.com](https://www.selcukcihan.com) kişisel sayfamı jekyll ile hazırladım ve [github pages](https://github.com/selcukcihan/selcukcihan.github.io) ile host ettim.
Fakat son zamanlarda hugo çok sık karşıma çıkmaya başladı, ben de karşılaştırmada hugo'ya biraz torpil geçtim.
Burada detaylı bir karşılaştırma yapmayacağım, zaten karşılaştırma yapacak kadar da hakim değilim hiçbirine.
Fakat hugo ile ilgili genel söylem şu yönde, her şey hazır geliyor ([ruby on rails mantığı](https://rubyonrails.org/doctrine/), ihtiyaç olan her şeyi dahil etmişler).
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

3. Git repo'su oluşturdum:

```
cd defter && git init
```

4. `.gitignore` dosyası oluşturdum. *public* klasörü, build klasörü gibi, bunu git'e eklemek istemiyoruz. Bu klasörde, netlfiy üzerinden sunacağımız dosyalar bulunacak. Buradaki tüm dosyalar hugo'nun otomatik olarak oluşturacağı dosyalar.

```
# .gitignore dosyasının içeriği
/public
.DS_Store
```

5. Netlify için deployment tercihlerimi netlify.toml dosyasının içine koydum:

```
[build]
publish = "public"
command = "hugo --gc --minify"

[context.production.environment]
HUGO_VERSION = "0.59.1"
HUGO_ENV = "production"
HUGO_ENABLEGITINFO = "true"

```

6. Tema kurdum:

```
git submodule add https://github.com/vividvilla/ezhil.git themes/ezhil
```

7. İlk blog yazımı ekledim:

```
hugo new posts/bloglari-birlestir.md
```Oluşturulan boş markdown dosyasını düzenleyerek, içeriğimi ekledim.

8. Kendi bilgisayarımda blog uygulamasını ayağa kaldırıp, çalıştığını gördüm:

```
hugo server -D
```

9. Değişikliklerimi git repo'ma commitledim:

```
git add . && git commit -m "Hugo ile blog ekle"
```

10. Github'da remote repo oluşturdum ve değişikliklerimi *push* ettim:
![Github'da yeni repo oluşturma][1]

```
git remote add origin https://github.com/selcukcihan/defter.git
git push -u origin master
```

11. [Netlify üzerinden](https://app.netlify.com/start), yeni siteyi oluşturmaya başladım. *Continuous deployment* olarak github'ı seçip, github'daki repo için netlify'a izin verdim.
![Netlify için github'daki repo'ya erişim izni ver][2]

12. *Deploy site* diyerek, blog'umu yayınlamaya başladım.
![Netlify üzerinden blog'u yayınla][3]

[1]: /img/bloglari-birlestir/github_create_repo.png
[2]: /img/bloglari-birlestir/github_netlify_permission.png
[3]: /img/bloglari-birlestir/netlify_deployment.png
[4]: /img/bloglari-birlestir/netlify_setup_custom_domain.png
[5]: /img/bloglari-birlestir/netlify_setup_custom_domain_2.png

13. *Set up a custom domain* ile blog.selcukcihan.com'dan yayınlanmasını sağladım:
![Github'da yeni repo oluşturma][4]
![Github'da yeni repo oluşturma][5]


## İmajlar

Markdown ile yazdığım blog yazılarımda imaj kullanmak için şunları yaptım:

1. `static` klasörü altındaki tüm dosyaları, hugo tarafından `public` klasörüne olduğu gibi kopyalanır ve imajlar için burası en uygun klasördür.
Bu klasör çok karışmasın diye, imajları `static/img/blog-yazisi` şeklinde bir klasör hiyerarşisinde saklıyorum.
Örnek vermek gerekirse, bu yazı için klasörü aşağıdaki gibi oluşturdum:

```
mkdir static/img/bloglari-birlestir
```

2. Klasör hiyerarşisi bu şekildeyken, imajlara `/img/bloglari-birlestir/imaj.png` şeklinde erişebiliyorsunuz.









