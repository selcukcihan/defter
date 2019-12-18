---
title: "AWS Lambda - Giriş"
tags: ["aws", "bulut", "lambda", "cloud", "faas", "serverless"]
date: 2019-12-17T14:41:29+03:00
draft: false
---

Serverless oldukça revaçta olan bir konsept.
AWS ve Google gibi bulut servis sağlayıcıları, sizi herhangi bir fiziksel veya sanal makina ile uğraştırmadan, yazdığınız kodu  çalıştırabiliyor.
Bu yazıda; AWS Lambda nedir, lambdayı kullanarak neler yapılabilir ve lambda ile uygulama geliştirmenin incelikleri nelerdir öğreneceğiz.

## Serverless
*Serverless'ı* basitçe gözünüzde canlandırmak için, yazdığınız bir fonksiyonu bulut'a yüklediğinizi ve çeşitli *event'lere* bağladığınızı düşünün.
*Event'ler* geldikçe, fonkisyonunuz girdi olarak *event'leri* alıp üzerinde işlemler yapıp, farklı servisleri çağırıp bir sonuç üretecek.
Buna "Function As A Service" *FAAS* da deniyor.
AWS'nin FAAS çözümü ise 2014 yılında duyurulan [Lambda](https://aws.amazon.com/lambda/).
Elbette Lambda'ya yüklediğiniz kodlar sanal makinalar üzerinde çalışıyor, fakat bu makinaları siz yönetmiyorsunuz.
İşte *serverless*'tan kasıt bu, bir makina yönetmek zorunda olmamanız.
Mesela linux için yeni bir güvenlik yaması çıktı diyelim, makinayı siz yönetiyor olsanız bu yamayı kendiniz kurmalısınız.
Fakat *serverless* teknolojilerinde, fiziksel veya sanal makinalar sizden soyutlanmış durumda, bakımı ve yönetimi tümüyle AWS tarafından yapılmakta.

## Temel bilgiler
### Lambda neler yapabilir?

Hemen hemen her şeyi yapabilirsiniz, elbette bazı işler için çok uygun olmamakla beraber (mesela uzun süren *batch job* türü işler), genel olarak aklınıza gelen tüm problemler için çözümün bir parçası olabilir.

Lambda ile

* backend yazabilirsiniz, AWS [API Gateway](https://aws.amazon.com/api-gateway/) ile veya [ELB](https://aws.amazon.com/elasticloadbalancing/) (Elastic Load Balancing) ile bağlayarak REST API'ler gerçekleyebilirsiniz
* S3'e yüklenen nesneler üzerinde işlemler yapabilirsiniz, mesela yüklenen imajları thumbnail haline getirmek gibi
* zamanlanmış işler için kullanabilirsiniz, CloudWatch üzerinden yapacağınız [cron tanımına göre](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/Create-CloudWatch-Events-Scheduled-Rule.html) lambdanız belirlenmiş zamanlarda çalıştırılabilir
* [dynamodb stream'lerini](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Streams.html) işleyebilirsiniz, mesela tablonuza gelen her yeni kaydı elasticsearch indexine eklemek gibi

### Lambda hangi dilleri destekler?

Lambda popüler dillerin pek çoğunu [destekliyor](https://docs.aws.amazon.com/lambda/latest/dg/lambda-runtimes.html).
Lambda'da *runtime* adı verilen konsept ile farklı programlama dillerini kullanımınıza sunuyor, bunlar:

* Node.js (12, 10, 8.10)
* Python (3.8, 3.7, 3.6, 2.7)
* Ruby (2.5)
* Java (11, 8)
* Go (1.x)
* .Net (Core 2.1)

Runtime'lar sadece belirli versiyonları destekliyor ve günü geldiğinde eski bir versiyon için verilen destek kaldırılabiliyor.

### Kodu yüklemek için
Lambda üzerinde fonksiyon oluşturmak için birkaç farklı yöntem var:

* [AWS Lambda konsolu](https://console.aws.amazon.com/lambda/home?region=us-east-1) üzerinden "Create function" butonuna tıklayıp, Lambda konsolun kendi editörünü kullanarak yeni bir fonksiyon oluşturabilirsiniz. Bu yeni başlarken işinize yarayabilir, ama "hello world"'ün ötesine geçmek istiyorsanız konsoldaki editör ihtiyacınızı görmeyecektir.
![Lambda designer][1]
* Yine konsol üzerinden, bu kez "Serverless Application Repository" yani [*SAR*](https://aws.amazon.com/serverless/serverlessrepo/) kullanıp, hazır uygulamalardan birini seçerek (mesela microservice-http-endpoint ile basit bir http backend servisi yazabilirsiniz) kullanabilirsiniz.
* AWS'nin sunduğu [komut satırı uygulamalarını](https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-tools.html) kullanarak lambda API'lerine erişebilir ve kodunuzu yükleyebilirsiniz.
* AWS CloudFormation ve AWS S3 hizmetlerini birlikte kullanarak fonksiyon tanımınızı CloudFormation'da yaptıktan sonra kodu zipleyip S3'e atarak da lambdanızı [oluşturabilirsiniz](https://docs.aws.amazon.com/lambda/latest/dg/services-cloudformation.html).
* [Serverless framework](https://serverless.com/) ile lambdanızı tanımlayıp AWS'ye yükleyebilirsiniz.

### Lambda'nın limitleri

AWS Lambda servisini kullanırken, servis üzerinde AWS'nin koymuş olduğu [limitlerden](https://docs.aws.amazon.com/lambda/latest/dg/limits.html) haberdar olmak önemli.
Mesela, lambda fonksiyonunuzun çalışması çok uzun sürmemeli: AWS 15 dakika sonra fonksiyonunuzu bölüp *timeout* verir.
Bazı kritik limitler şunlar:

* Tüm fonksiyonların en fazla 15 dakika çalışma süresi olmak zorunda. 15 dakikayı aşan fonksiyonlar hata verip kesilecektir.
* Bir AWS bölgesinde hesabınıza tanımlı tüm fonksiyonlar için varsayılan eş-zamanlı çalıştırma kapasitesi 1000, yani aynı anda en fazla 1000 fonksiyon çağırabilirsiniz. Örnek vermek gerekirse, A ve B adlı iki fonksiyonunuz varsa ve ikisi de bir saniye sürüyorsa, bir saniye içinde 500 kere A ve 500 kere de B'yi çağırabilirsiniz; veya sadece A'yı ya da sadece B'yi 1000 kere çağırabilirsiniz.
* Kodunuzun sıkıştırılmamış hali 250 MB'tan az olmalı (konsol üzerinden oluşturuyorsanız, 3 MB'lık bir limit var)

## Lambda fonksiyon çalıştırma modelleri

Lambda'nın iki çeşit fonksiyon çalıştırma modeli var:

* [Async](https://docs.aws.amazon.com/lambda/latest/dg/invocation-async.html) (asenkron olarak, lambda'nın tetikleyicisi gelecek olan cevabı beklemez)
* [Sync](https://docs.aws.amazon.com/lambda/latest/dg/invocation-sync.html) (senkron olarak, lambda'yı tetikleyen kod parçası gelecek olan cevabı bekler)

Basit iki örnekle açıklamak gerekirse, eğer API Gateway ile bir REST API yapıyorsanız, arkaya bağladığınız lambda fonksiyonu API Gateway tarafından senkron olarak çağırılır, gelecek olan cevabı beklemek zorunda ki *client'a* cevap dönebilsin.
Asenkron modelde ise, fonksiyon çağırıldığında bir *event* oluşturulup sizden soyutlanmış olan bir "queue" yani sıraya sokulur ve zamanı geldiğinde *event* lambda tarafından işlenir.
Asenkron modelde, lambda fonksiyonunuz hata alırsa otomatik olarak iki kere daha dener.
Asenkron modelde, lambda'nın cevabını almak için [SQS](https://aws.amazon.com/sqs/), [SNS](https://aws.amazon.com/sns/), başka bir Lambda veya [EventBridge](https://aws.amazon.com/eventbridge/) kullanabilirsiniz.
Mesela fonksiyonunuz hata aldığında, hatayı yönetmek için yazdığınız başka bir lambda metodunuzun otomatik olarak tetiklenmesini sağlayabilirsiniz.
Veya hata aldığında bu hatayı daha sonrasında incelemeniz için SQS'e [gönderebilirsiniz](https://docs.aws.amazon.com/lambda/latest/dg/invocation-async.html#dlq).

## Ücretlendirme

Mühendislikte zamanla birlikte en önemli kaynaklardan biri de elbette para.
AWS'nin ücretsiz kullanım kotası kapsamında, bir sene boyunca ayda 1M adet fonksiyon çalıştırma hakkı ve 400.000 GB-saniye işlem gücünü bedavaya getiriyorsunuz.
Ücretlendirme, fonksiyonların tetiklenme sayısı ve fonksiyonlara tanımladığınız memory ile fonksiyonlarınızın kaç saniye çalıştığıyla ilgili olarak değişiyor.
Detaylar için [lambda fiyatlandırma sayfasına](https://aws.amazon.com/lambda/pricing/) bir göz atın.

## Kapanış

AWS Lambda sayesinde sadece yazdığınız koda odaklanıp, kodun nasıl çalıştırılacağını, hangi makinada koşacağını, nasıl güncelleneceğini unutabilirsiniz.
Serverless ve lambda, klasik uygulama geliştirmeden oldukça farklı.
İçselleştirmeniz ve benimsemeniz gereken en büyük fark, her şeyin *event* kaynaklı olması.
Geleneksel uygulamalarda, uygulamayı ayağa kaldırırsınız ve çalışmaya başlar.
Oysa lambda buluttaki *event'lere* cevap vermek üzere tasarlanmış bir servis.
Bir diğer önemli husus da, ssh yapıp loglarına bakacağınız bir makina olmaması.
Uygulamanızda bir hata olduğunda bunu kolayca bulabilmek için AWS'nin sunmuş olduğu CloudWatch ve [X-Ray](https://aws.amazon.com/xray/) gibi servislerinden faydalanmanız gerekiyor.
Lambda'yı bir karakutu olmaktan çıkarmak ve nasıl davrandığını anlayabilmek için [Twitter haritası - 2](/web-development/twheat-map-2) yazımda değinmiş olduğum *observability* mefhumuna önem vermeniz gerekiyor.

[1]: /img/aws-lambda-giris/lambda_designer.png
