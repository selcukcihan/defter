---
title: "Twitter Blog Duyurucu"
tags: ["javascript", "serverless", "twitter", "blog", "lambda", "apigateway", "nodejs", "aws", "jwt"]
date: 2020-05-13T00:41:29+03:00
draft: false
images: ["/img/twitter-blog-duyurucu/megafon.jpg"]
---

Blog'a her yazı ekledikten sonra Twitter'dan duyuru yapıyorum.
Bu işi hızlıca nasıl otomatize edebilirim diye düşündüm.
Cevabını da bir yazı olarak paylaşıp, **serverless** teknolojilerle neler yapılabileceğine ve az zamanda çok iş çıkarılabildiğine vurgu yapmak istedim.

![Announcement][1]
<a style="background-color:black;color:white;text-decoration:none;padding:4px 6px;font-family:-apple-system, BlinkMacSystemFont, &quot;San Francisco&quot;, &quot;Helvetica Neue&quot;, Helvetica, Ubuntu, Roboto, Noto, &quot;Segoe UI&quot;, Arial, sans-serif;font-size:12px;font-weight:bold;line-height:1.2;display:inline-block;border-radius:3px" href="https://unsplash.com/@jeremyyappy?utm_medium=referral&amp;utm_campaign=photographer-credit&amp;utm_content=creditBadge" target="_blank" rel="noopener noreferrer" title="Jeremy Yap'a ait fotoğraflar için tıklayın."><span style="display:inline-block;padding:2px 3px"><svg xmlns="http://www.w3.org/2000/svg" style="height:12px;width:auto;position:relative;vertical-align:middle;top:-2px;fill:white" viewBox="0 0 32 32"><title>unsplash-logo</title><path d="M10 9V0h12v9H10zm12 5h10v18H0V14h10v9h12v-9z"></path></svg></span><span style="display:inline-block;padding:2px 3px">Fotoğraf Unsplash'ta Jeremy Yap'a aittir.</span></a>

# Amaç

Blog'um netlify üzerinde host ediliyor, bunun detaylarını [başka bir yazıda](/web-development/bloglari-birlestir) paylaşmıştım.
Kodlar github'da duruyor.
İstiyorum ki, blog'a her yeni yazı eklendiğinde, [twitter'dan](https://twitter.com/scihan) otomatik olarak duyurusunu yapayım.
Bu iş için fazla vakit harcamak istemiyorum, kafamda kabaca bir plan var:

* Netlify üzerinden [deploy notifications](https://docs.netlify.com/site-deploys/notifications/) özelliğini kullanarak, her başarılı deployment'tan sonra Twitter'dan duyuru yapacak akışımı tetikleyeceğim.
* Netlify bildirimlerinin [outgoing webhooks](https://docs.netlify.com/site-deploys/notifications/#outgoing-webhooks) türünü kullanıp, AWS [APIGateway](https://aws.amazon.com/api-gateway/) ile dışarı açacağım bir *endpoint'e* bildirimleri **POST** edeceğim.
* *Endpoint*'imin arkasında bir [Lambda](/bulut-bilisim/aws-lambda-giris) olacak.
* Lambda, bildirim yapıp yapmamaya karar verip, Twitter API metodunu çağırarak bir tweet atacak.

# Tetikleme

Yukarıda bahsettiğim sürecin tetikleyicisi olan Netlify bildiriminin detayına bakmak için [hookbin.com](https://hookbin.com/) üzerinden kendime bir *endpoint* oluşturdum ve bunu Netlify'daki bildirime ekledim:

![netlify-notification-setup][2]

Netlify'dan nasıl bir bildirim geldiğini görmek için github [repo'ma](https://github.com/selcukcihan/defter) bir *commit* *push'ladım* ve [hookbin.com](https://hookbin.com/)'daki sayfayı yenileyip gelen HTTP POST isteğinin içeriğini gördüm:

*Biraz tıraşladım, ihtiyacım olan sadece commit_ref aslında*

```
{
  "state": "ready",
  "url": "https://blog.selcukcihan.com",
  "commit_ref": "b623b20cef5068651f3c622fc7f38715c65b0cdf",
  "branch": "master",
  "commit_url": "https://github.com/selcukcihan/defter/commit/b623b20cef5068651f3c622fc7f38715c65b0cdf",
  "committer": "selcukcihan",
  "manual_deploy": false
}
```

Buna ek olarak, netlify JWT *token*'ı HTTP *header*'ı ile gönderiyor.
Kullandıkları alan `x-webhook-signature`.
Bunu API Gateway üzerinde *authentication* yaparken kullanacağım.

# APIGateway

Lambda'yı bir REST API olarak açmanın en kolay yolu API Gateway.
API Gateway entegrasyonunda en kritik nokta *authentication*.
Eğer gelen istekleri olduğu gibi kabul edersem, *endpoint*'imi öğrenen herkes *POST* yapabilir ki bunu istemem :smile:
Bu yüzden API Gateway'i JWT ile koruyacağım.
API Gateway *authentication* yaptıktan sonra, gelen isteği Lambda'ya aktaracak.
Bunu yaparken Lambda'yı [senkron olarak](/bulut-bilisim/aws-lambda-giris/#lambda-fonksiyon-çalıştırma-modelleri) tetikleyecek.
Yani Lambda işini yaparken, API Gateway onu bekleyecek.
Lambda senkron olarak tetiklendiğinde, mükerrer çalışma ihtimali olmuyor (tabi netlify aynı bildirimi birden fazla kez yaparsa o zaman mükerrer çalışır, o durumu görmezden geleceğim).

API Gateway'de [custom authorizer](https://www.serverless.com/framework/docs/providers/aws/events/apigateway/#http-endpoints-with-custom-authorizers) ile gelen JWT *token*'ının validasyonunu yapıp, gerekli çalıştırma iznini vereceğim.
JWT token validasyon kodunu [şuradan](https://github.com/adnanrahic/a-crash-course-on-serverless-auth) buldum, bunu kullanacağım.

# Lambda

*Authentication* kısmını başarıyla geçen istek, lambda'ya hiç değiştirilmeden iletilecek ve yukarıda örneğini verdiğim türden bir *event*'i işlememiz gerekecek.
Yapmamız gereken iki temel iş var:

* GitHub API ile commit'te eklenen dosyaları çekmek.
* Twitter API ile duyuru twitimizi atmak.

## GitHub API v3

Elimizde bir `commit_ref` var, bunu kullanarak [GitHub API üzerinden](https://developer.github.com/v3/git/commits/) bu committeki dosyaları çekeceğiz.
Bunun için elbette GitHub'a kendimizi *authenticate* etmeliyiz, tıpkı Twitter'a yapacağımız gibi.
Bir *personal access token* ürettim, nasıl yapılacağını [güzelce anlatmışlar](https://help.github.com/en/github/authenticating-to-github/creating-a-personal-access-token-for-the-command-line).
Çekeceğimiz URI https://api.github.com/repos/selcukcihan/defter/commits/b0e604b7820bc77a03ec2f4c24503a8c1ea21724
Tabi sondaki commit hash'i her istekte değişecek, ilgili `commit_ref`'i kullanacağız.

Gelen cevapta `files` alanı bizi ilgilendiriyor.

*İhtiyacım olmayan alanları tıraşladım, ama gelen cevap şuna benziyor*
```
{
  "files": [
    {
      "sha": "34760e92bbfa06bff8681da09175e7f527e99ed4",
      "filename": "content/bulut-bilisim/s3-puf-noktalar.md",
      "status": "added",
      "raw_url": "https://github.com/selcukcihan/defter/raw/b0e604b7820bc77a03ec2f4c24503a8c1ea21724/content/bulut-bilisim/s3-puf-noktalar.md"
    }
  ]
}
```

Eğer `content/` altında `.md` uzantılı bir dosya `added` statüsünde ise, bir tweet atacağız!

## Twitter API

Twitter API'dan [POST statuses/update](https://developer.twitter.com/en/docs/tweets/post-and-engage/api-reference/post-statuses-update) metodunu kullanacağım.
Öncelikle Twitter'da bir [developer hesabı](https://developer.twitter.com/) açmanız gerekiyor.
Daha sonra bir uygulama oluşturup, API key'lerinizi alabilirsiniz.

![twitter-api-keys][3]

Twitter API metodunu çağırmak için, [twitter-lite](https://www.npmjs.com/package/twitter-lite) adlı *client* kütüphanesini [kullandım](https://github.com/selcukcihan/serverless-blog-announcer/blob/master/blog/NewPostHandler.js#L22).

# Netlify ile entegrasyon

Lambda'nın ortam değişkenlerine ilgili *key*'lerin hepsini yükledim:

* `GITHUB_ACCESS_TOKEN`
* `TWITTER_API_KEY`
* `TWITTER_API_SECRET`
* `TWITTER_ACCESS_TOKEN`
* `TWITTER_ACCESS_SECRET`
* `JWT_SECRET`

Uygulamayı `yarn deploy` komutu ile *deploy* ettim.
Her şey hazır, AWS API Gateway *endpoint*'imin adresini aldım ve bir adet JWT *secret*'ı ile Netlify'daki bildirim ayarına ekledim.

# Test

Gerçek hesabımdaki testi henüz yapmadım, birazdan bu yazıyı *commit*'leyip github'a attıktan sonra göreceğiz bakalım :smile:

Bütün bu blog yazısı ve uygulamayı yazmam yaklaşık 150 dakikamı aldı.
Bir blog yazısını Twitter'dan duyurmak 1 dakikamı alıyor olsa, 150. yazıdan sonra kâra geçmiş olacağım, verimlilik açısından çok da mantıklı bir iş yapmamış olabilirim :smile:

[1]: /img/twitter-blog-duyurucu/megafon.jpg
[2]: /img/twitter-blog-duyurucu/netlify-notification-setup.png
[3]: /img/twitter-blog-duyurucu/twitter-api-keys.png
