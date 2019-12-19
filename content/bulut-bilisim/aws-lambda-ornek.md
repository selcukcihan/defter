---
title: "AWS Lambda - Sesi yazıya dönüştürme"
tags: ["aws", "bulut", "lambda", "cloud", "faas", "serverless", "örnek",
       "transcribe", "s3", "yapayzeka", "ses", "email", "boto3", "python"]
date: 2019-12-18T14:41:29+03:00
draft: false
---

AWS Lambda ile asenkron çalışma modelini kullanan basit bir örnek yapmak istiyorum.
Ses dosyalarını yazıya çevirip (text dosyası) email olarak gönderen basit bir uygulama yapacağım.

## Problem tanımı
[Bir önceki yazıda](/bulut-bilisim/aws-lambda-giris) AWS Lambda hizmetini tanımıştık.
Gerçek bir problem üzerinden giderek lambda'yı daha iyi anlayabiliriz.
Örnek problemimiz *speech-to-text*, yani sesi yazıya dönüştürme.
Girdi olarak herhangi bir wav veya mp3 dosyasını alıp, çıktı olarak text dosyası üreteceğiz.
Daha sonra, oluşturduğumuz text dosyasının içeriğini email olarak göndereceğiz.

## Bileşenler ve çözüm
Sesi yazıya çevirmek için [AWS Transcribe](https://aws.amazon.com/transcribe/) hizmetini kullanacağız.
Transcribe, Türkçe dahil pek çok dili [destekliyor](https://docs.aws.amazon.com/transcribe/latest/dg/what-is-transcribe.html).
İngilizce, Fransızca ve İspanyolca için ayrıca *streaming* desteği de var, mesela bunu kullanarak canlı bir yayın için anlık olarak altyazı koyabilirsiniz.

Hemen hemen tüm yapay-zeka algoritmaları gibi, sesi yazıya dönüştürmek de işlemciyi yoran ve zaman alan bir iştir.
Bu yüzden çözümümüzü senkron yapmak yerine, asenkron yapıp sonucun oluşmasını beklemeyeceğiz.
İşleyiş kabaca şu sırayla olacak:

1. Ses dosyası [S3'te](/bulut-bilisim/s3-statik-hosting/) belirlediğimiz *sesler* adlı *bucket'a* yüklenir
2. "sesler" *bucket'ına* tanımlamış olduğumuz S3 *event* bildirimi sayesinde, çeviriyi yapacak olan *yapayzeka* lambda fonksiyonumuz tetiklenir
3. Lambda, transcribe servisini çağırarak sesten yazıya dönüşüm işlemini asenkron olarak başlatır (bu noktada lambda sonlanır)
4. Transcribe, sonucu S3'teki başka bir *bucket'a* (turkce-yazi) yazar
5. "turkce-yazi" *bucket'ına* tanımlamış olduğumuz diğer bir S3 *event* bildirimi sayesinde, email gönderecek olan *mailgonder* lambda fonksiyonumuz tetiklenir
6. Lambda, S3'teki Türkçe yazıyı alır ve [AWS SES](https://aws.amazon.com/ses/) hizmetini kullanarak maili gönderir.

Örneğimizde kullandığımız tüm servisler, AWS'nin ücretsiz kullanım kotasına dahil.
Faturamızda sürprizlerle karşılaşmadan, öğrenme amacıyla ve kafamız rahat bir şekilde kurcalayabiliriz bu servisleri.

Programlama dili olarak python kullanacağım.
AWS'nin python için geliştirdiği bir *client library* var, [boto3](https://boto3.amazonaws.com/v1/documentation/api/latest/index.html).
Boto3'ü kullanarak hem transcribe, hem S3, hem de SES servislerini çağıracağız.

## S3

S3 üzerinde iki tane *bucket* oluşturun, varsayılan ayarları kullanabilirsiniz.
Detaylar için [ilgili yazımı](/bulut-bilisim/s3-statik-hosting) okuyabilirsiniz.
Benim oluşturduğum *bucket'lar* "sesler" ve "turkce-yazi".
S3'teki *bucket* isimleri DNS isimlendirme standardına uymak zorunda ve global olarak benzersiz olmak zorunda.
Yani benim seçtiğim isimleri siz kullanamayacaksınız, hayal gücünüzü çalıştırıp güzel isimler bulun.

![S3 buckets][5]

Test amaçlı oluşturduğum [mp3 dosyasını](/audio/speech_20191219095654760.mp3) "sesler" *bucket'ına* ekledim.
Siz de istediğiniz bir ses kaydını kullanabilirsiniz.

## Transcribe
Koda girişelim, ilk önce yapayzeka kısmını AWS transcribe hizmetiyle halledecek olan *yapayzeka* lambda fonksiyonunu oluşturalım.
Bunun için AWS konsola'a giriyoruz ve [lambda'yı](https://console.aws.amazon.com/lambda/home?region=us-east-1#/functions) açıyoruz.
"Create function" butonuna basarak ilk fonksiyonumuzu oluşturalım.
![Create function][1]

Fonksiyona bir isim verip, *runtime* olarak "Python 3.8" seçtim.
AWS'deki tüm servislerin yetki yönetimini yapan IAM servisinden daha önce [bahsetmiştik](/bulut-bilisim/aws-ilk-adimlar).
Fonksiyonumuza bir IAM rolü atamamız gerekiyor.
Şimdilik default seçenekle ilerleyebiliriz, "Create function" butonuna tıklayıp devam edin.
Bu şekilde ilerleyince, lambda'mıza yeni bir IAM rolü oluşturup ekliyor.
Daha sonra IAM üzerinden fonksiyonumuza S3 ve Transcribe servislerini kullanabilmesi için yetki vereceğiz çünkü default ayarlar bu servislerin yetkisini içermiyor.
![Create function more][2]

yapayzeka lambda'mızın kodu şu şekilde:
<script src="https://gist.github.com/selcukcihan/4ad4623f4d6e5c8bdc2fbc8c37f3b0ab.js"></script>

Bunu test etmek için, boto3 dokümanlarından bulduğum [örnek bir S3 event'ini](https://docs.aws.amazon.com/lambda/latest/dg/with-s3.html) alıp kendime uyarladım:
<script src="https://gist.github.com/selcukcihan/a602bfa0380523f409febc0dfe84a7ff.js"></script>

Lambda konsolunda kodu editöre yapıştırdıktan sonra, sağ üstteki "Test" butonuna tıklayın.
![yapayzeka test][3]

Daha sonra test datasını ekleyip "Create" butonuna basın.
![yapayzeka test event][4]

Şimdi IAM rolüne ilgili yetkileri verme vakti geldi, AWS konsoldan IAM servisini seçip *Roles'a* tıklayın.
Lambda'nızın rolünü `yapayzeka-role-****` gibi bir isimde göreceksiniz, buna tıklayın ve "Attach policies" butonuna tıklayarak yetkileri eklemeye başlayın:
![Yapayzeka IAM rolü][8]

Şimdi "Test" butonuna basıp, lambda'yı test edebiliriz.
Aşağıda "Execution results" penceresinde şuna benzer bir log göreceksiniz:

```
Response:
{
  "statusCode": 200,
  "body": "\"Sesten yaz\\u0131ya \\u00e7evirme i\\u015flemi ba\\u015flat\\u0131ld\\u0131.\""
}

Request ID:
"9fdf12c1-4219-47d3-9830-46b64d8a66ab"

Function Logs:
START RequestId: 9fdf12c1-4219-47d3-9830-46b64d8a66ab Version: $LATEST
[INFO]  2019-12-19T13:24:33.681Z    9fdf12c1-4219-47d3-9830-46b64d8a66ab    sesler bucket'ındaki speech_20191219095654760.mp3 objesi işleniyor.
[INFO]  2019-12-19T13:24:34.53Z 9fdf12c1-4219-47d3-9830-46b64d8a66ab    Transcribe durumu: "IN_PROGRESS"
END RequestId: 9fdf12c1-4219-47d3-9830-46b64d8a66ab
REPORT RequestId: 9fdf12c1-4219-47d3-9830-46b64d8a66ab  Duration: 375.71 ms Billed Duration: 400 ms Memory Size: 128 MB Max Memory Used: 74 MB  Init Duration: 261.70 ms    
```

Lambda *transcribe* servisini çağırdığında, sesten yazıya dönüşüm işini başlatmış oluyor.
Fakat bu işin bitmesini beklemiyoruz, *transcribe* bize cevap olarak bir id ve işin durumu gibi detayları dönüyor.
Servisi boto3 ile çağırdığımız için, aldığı parametrelere ve cevap olarak döndüğü değerlere [boto3'ün dokümanlarından](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/transcribe.html#TranscribeService.Client.start_transcription_job) bakabiliriz.

Şimdi AWS konsol üzerinden *transcribe* hizmetini açın ve başlattığınız dönüşüm işini görmelisiniz:
![Transcribe][6]

İş "Completed" statüsünde görünüyorsa, S3'teki "turkce-yazi" *bucket'ına* bakın, transcribe cevabını buraya koymuş olacak:
![Türkçe yazı S3][7]

Dosyayı indirip içeriğine bakarsanız, sesi yazıya çevirmiş olduğunu göreceksiniz:
```
{
  "jobName": "speech_20191219095654760.mp3_1576761873",
  "results": {
    "transcripts": [
      {
        "transcript": "on yılı aşkın bir süredir yazılım mühendisi olarak çalışıyorum. Topta üzerinden bir eylemci olarak dünyanın dört bir yanındaki müşteriler için uygulama geliştirme ve danışmanlık hizmetleri veriyorum. Yazmam daki Ahmad bildiklerini ve öğrendiklerimi pekiştirmek bunun yanında yazma çözdüm. Bir probleme benzer başka bir problemle karşılaşırsam geri dönüp neler yaptığıma bakabilme mide sağlıyor. Yazılarımdan neler bulacaksınız? Karşıma çıkan problemleri problemlere getirdiğim çözümleri mi merak ettiğim yeni bir kütüphane veya framevorkü nasıl öğrendiğimi doğrudan yazılımla alakası olmayan ama yazılımcıların fayda sağlayabileceği tavsileyelerini."
      }
    ]
  },
  "status": "COMPLETED"
}
```

## E-posta

Uygulamanın ilk ayağında, *yapayzeka* lambda'mız sesi yazıya çevirdi ve sonucu S3'e kaydettik.
Şimdi başka bir lambda oluşturup, S3'teki yazıyı alıp kendimize bir e-posta olarak göndereceğiz.
Bu lambdayı tetikleyecek olan, "turkce-yazi" *bucket'ına* sonucun eklenmesi olacak.
Hem *yapayzeka* hem de *mailgonder* lambda fonksiyonlarımız, AWS lambda [S3 event bildirimleri](https://docs.aws.amazon.com/lambda/latest/dg/with-s3.html) ile otomatik olarak tetiklenecek.
Bu bildirimleri, bir sonraki bölümde açacağız, şimdilik sadece konsoldaki "Test" butonu ile manuel test ederek fonksiyonlarımızı yazıyoruz.

*mailgonder* lambda'sını bir önceki *yapayzeka* lambda'sıyla aynı şekilde oluşturuyoruz ve IAM rolü olarak varsayılan değerlerle yeni bir rol oluşturulmasını kabul ediyoruz.

Fonksiyonumuzun kodu aşağıdaki gibi:
<script src="https://gist.github.com/selcukcihan/340d6a90dd53b227fd96e7e2c465a861.js"></script>

Bunu test etmek için, aşağıdaki test *event'ini* kullanabilirsiniz:
<script src="https://gist.github.com/selcukcihan/f3380bd290227da32deed7f7e2b8ec81.js"></script>

Boto3 SES *client'ının* [dokümantasyonuna](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/ses.html#SES.Client.send_email) bakarak ilgili parametreleri doğru bir şekilde kullandık.

Test etmeden önce, IAM üzerinden yine gerekli yetkileri vereceğiz.
Bu kez *mailgonder* lambda'sının yetkilerini açıp, "AmazonS3FullAccess" ve "AmazonSESFullAccess" yetkilerini verin.

Yetkileri verdikten sonra "Test" butonuna basın:

```
Response:
{
  "errorMessage": "An error occurred (MessageRejected) when calling the SendEmail operation: Email address is not verified. The following identities failed the check in region US-EAST-1: noreply@selcukcihan.com, omerselcukcihan@gmail.com",
  "errorType": "MessageRejected",
  "stackTrace": [
    "  File \"/var/task/lambda_function.py\", line 23, in lambda_handler\n    response = ses.send_email(\n",
    "  File \"/var/runtime/botocore/client.py\", line 272, in _api_call\n    return self._make_api_call(operation_name, kwargs)\n",
    "  File \"/var/runtime/botocore/client.py\", line 576, in _make_api_call\n    raise error_class(parsed_response, operation_name)\n"
  ]
}

Request ID:
"a081aec3-dd77-47fb-8b2e-134613f757ed"

Function Logs:
START RequestId: a081aec3-dd77-47fb-8b2e-134613f757ed Version: $LATEST
[INFO]  2019-12-19T14:14:57.90Z a081aec3-dd77-47fb-8b2e-134613f757ed    turkce-yazi bucket'ındaki speech_20191219095654760.mp3_1576761873.json objesi işleniyor.
[INFO]  2019-12-19T14:14:57.312Z    a081aec3-dd77-47fb-8b2e-134613f757ed    Email içeriği: on yılı aşkın bir süredir yazılım mühendisi olarak çalışıyorum. Topta üzerinden bir eylemci olarak dünyanın dört bir yanındaki müşteriler için uygulama geliştirme ve danışmanlık hizmetleri veriyorum. Yazmam daki Ahmad bildiklerini ve öğrendiklerimi pekiştirmek bunun yanında yazma çözdüm. Bir probleme benzer başka bir problemle karşılaşırsam geri dönüp neler yaptığıma bakabilme mide sağlıyor. Yazılarımdan neler bulacaksınız? Karşıma çıkan problemleri problemlere getirdiğim çözümleri mi merak ettiğim yeni bir kütüphane veya framevorkü nasıl öğrendiğimi doğrudan yazılımla alakası olmayan ama yazılımcıların fayda sağlayabileceği tavsileyelerini.
[ERROR] MessageRejected: An error occurred (MessageRejected) when calling the SendEmail operation: Email address is not verified. The following identities failed the check in region US-EAST-1: noreply@selcukcihan.com, omerselcukcihan@gmail.com
Traceback (most recent call last):
  File "/var/task/lambda_function.py", line 23, in lambda_handler
    response = ses.send_email(
  File "/var/runtime/botocore/client.py", line 272, in _api_call
    return self._make_api_call(operation_name, kwargs)
  File "/var/runtime/botocore/client.py", line 576, in _make_api_call
    raise error_class(parsed_response, operation_name)END RequestId: a081aec3-dd77-47fb-8b2e-134613f757ed
REPORT RequestId: a081aec3-dd77-47fb-8b2e-134613f757ed  Duration: 538.09 ms Billed Duration: 600 ms Memory Size: 128 MB Max Memory Used: 83 MB  Init Duration: 284.77 ms    
```

Bir hata aldık, bunun sebebi kullandığımız e-posta adreslerinin doğrulanmamış olması.
AWS SES hizmetini kullanmaya başlayınca, sınırlı bir ortam olan "sandbox" içerisinde bulunuyoruz.
Sandbox ortamında bazı sınırlamalar var, mesela doğrulanmamış adreslere e-posta gönderemiyorsunuz.
Sandbox'tan çıkmak için AWS'de bir "ticket" açmanız gerekiyor ve SES hizmetini ne için kullanacağınız, günde kaç e-posta göndereceğiniz gibi detayları paylaşıyorsunuz.
Sandbox ortamındaki limitler için [AWS dokümantasyonuna](https://docs.aws.amazon.com/ses/latest/DeveloperGuide/limits.html) bakabilirsiniz.
Amacımız kurcalamak ve öğrenmek olduğu için sandbox ortamı bize yeter.
Şimdi AWS konsolundan SES hizmetine ait [sayfayı](https://console.aws.amazon.com/ses/home?region=us-east-1#verified-senders-email:) açarak kullanmak istediğimiz e-posta adreslerini doğrulayalım.
"Verify a New Email Address" butonuna tıklayın ve kullandığınız e-posta'ları doğrulayın.
![SES verify][9]
Doğrulama için e-posta adresinize bir bağlantı gelecek, buna tıklayın ve adresinizi doğrulayın.

Şimdi tekrar "Test" butonuna basın ve aşağıdaki gibi bir çıktı göreceksiniz:
```
Response:
{
  "statusCode": 200,
  "body": "\"Email ba\\u015far\\u0131yla g\\u00f6nderildi.\""
}

Request ID:
"e4e049ff-565d-43b9-900e-04f88a42d5c5"

Function Logs:
START RequestId: e4e049ff-565d-43b9-900e-04f88a42d5c5 Version: $LATEST
[INFO]  2019-12-19T14:31:28.807Z    e4e049ff-565d-43b9-900e-04f88a42d5c5    turkce-yazi bucket'ındaki speech_20191219095654760.mp3_1576761873.json objesi işleniyor.
[INFO]  2019-12-19T14:31:29.41Z e4e049ff-565d-43b9-900e-04f88a42d5c5    Email içeriği: on yılı aşkın bir süredir yazılım mühendisi olarak çalışıyorum. Topta üzerinden bir eylemci olarak dünyanın dört bir yanındaki müşteriler için uygulama geliştirme ve danışmanlık hizmetleri veriyorum. Yazmam daki Ahmad bildiklerini ve öğrendiklerimi pekiştirmek bunun yanında yazma çözdüm. Bir probleme benzer başka bir problemle karşılaşırsam geri dönüp neler yaptığıma bakabilme mide sağlıyor. Yazılarımdan neler bulacaksınız? Karşıma çıkan problemleri problemlere getirdiğim çözümleri mi merak ettiğim yeni bir kütüphane veya framevorkü nasıl öğrendiğimi doğrudan yazılımla alakası olmayan ama yazılımcıların fayda sağlayabileceği tavsileyelerini.
[INFO]  2019-12-19T14:31:29.595Z    e4e049ff-565d-43b9-900e-04f88a42d5c5    Email id'si: 0100016f1e921ac4-29c193dc-e326-4122-819e-87bf8e61a1d1-000000
END RequestId: e4e049ff-565d-43b9-900e-04f88a42d5c5
REPORT RequestId: e4e049ff-565d-43b9-900e-04f88a42d5c5  Duration: 807.06 ms Billed Duration: 900 ms Memory Size: 128 MB Max Memory Used: 83 MB  Init Duration: 351.84 ms    
```

Hemen adresinizi kontrol edin ve e-postanın geldiğini görün.
Eğer e-postayı göremiyorsanız "Spam" klasörünüzü kontrol edin.

![SES email][10]

## S3 event bildirimleri

Lambda fonksiyonlarımızı yazdık, test ettik ve çalıştıklarını gördük.
Şimdi uygulamamızı uçtan uca deneme vakti geldi.
Bunun için iki *bucket'ımız* için de ayrı ayrı bildirimleri açacağız.
S3 konsolunu açın, "sesler" *bucket'ına* tıklayın.
"Properties" tabında en aşağıya scroll edip "Events" kutucuğuna tıklayın.
"Add notification" butonuna tıklayın ve aşağıdaki gibi ayarlarınızı yapın:
![Yapayzeka bildirim][11]

Aynı işlemi, bu kez "turcke-yazi" *bucket'ına* yapın, tabi bu kez *event'i* "mailgonder" lambda'sına bağlayacaksınız.

## Sonuç

Artık uygulamanızı denemeye geçebilirsiniz, bir ses dosyası bulup "sesler" *bucket'ına* ekleyin ve e-postanızı yenilemeye başlayın :smile:

[CloudWatch](https://console.aws.amazon.com/cloudwatch/home?region=us-east-1#logEventViewer:group=/aws/lambda/yapayzeka;stream=2019/12/19/[$LATEST]5d3a259d2205417f9f986d1afc757274;start=2019-12-18T14:44:19Z) üzerinden, lambda fonksiyonlarınızın loglarına bakabilirsiniz ve doğru çalıştıklarının sağlamasını yapabilirsiniz:
![Cloudwatch][12]

Yaklaşık bir dakika sonra e-posta bana ulaştı.
Gördüğünüz gibi, AWS Lambda ile uygulama geliştirmek oldukça kolay.
Daha karmaşık ve büyük uygulamalar için, [önceki yazılarımda](/web-development/twheat-map) bahsettiğim [serverless framework'ünü](https://serverless.com/) tavsiye ederim.
AWS ile ilgili tüm yazılarıma [buradan](/bulut-bilisim) ulaşabilirsiniz.

[1]: /img/aws-lambda-ornek/create_function.png
[2]: /img/aws-lambda-ornek/create_function_more.png
[3]: /img/aws-lambda-ornek/yapayzeka_test_lambda.png
[4]: /img/aws-lambda-ornek/yapayzeka_test_lambda_event.png
[5]: /img/aws-lambda-ornek/s3_buckets.png
[6]: /img/aws-lambda-ornek/transcribe_jobs.png
[7]: /img/aws-lambda-ornek/turkce_yazi_s3.png
[8]: /img/aws-lambda-ornek/yapayzeka_lambda_yetki.png
[9]: /img/aws-lambda-ornek/ses_verify.png
[10]: /img/aws-lambda-ornek/ses_email.png
[11]: /img/aws-lambda-ornek/yapayzeka_bildirim.png
[12]: /img/aws-lambda-ornek/cloudwatch.png
