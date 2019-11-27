---
title: "Twitter haritası - 1"
date: 2019-11-22T10:48:36+03:00
---

Son zamanlarda [twitter'ı](https://twitter.com/scihan) çok sık kullanmaya başladım.
Twitter, *developer'lara* kendi uygulamalarını geliştirmeleri için bazı *API* metodları sunuyor.
Bu metodları kullanan basit bir uygulama yapmak istedim.

## TL;DR

* Twitter API metodlarını *oauth 1.0a* ile herhangi bir kullanıcıymış gibi çağırabilme
* *Auth0* ile oauth 1.0a implementasyonu
* Frontend *reactjs* ile geliştirilip, Netlify üzerinde host ediliyor
* Backend *serverless framework* ile geliştirilip, AWS üzerinde host ediliyor
* Uygulamaya [twheat-map.selcukcihan.com](https://twheat-map.selcukcihan.com/) üzerinden erişebilirsiniz

## Fikir
Twitter *API* ile neler yapılabilir diye düşünürken, aklıma basit bir uygulama geldi.
Bu bir web uygulaması olacak, kullanıcı twitter hesabıyla uygulamaya giriş yapacak ve uygulama kullanıcının yerine, o kullanıcıymış gibi twitter API metodlarını çağıracak.
Uygulama, kullanıcıya takip ettiği hesapların dünya haritası üzerinde nerelerde yer aldığını bir "[heat map](https://toronto.citynews.ca/twitter-heat-map/)" ile gösterecek.
![Heat map][1]

## Gerekenler

* Twitter [API metodlarını](https://developer.twitter.com/en/docs/api-reference-index), başka bir kullanıcıymış gibi çağırabilme.
* Kullanıcının takip ettiği hesapların lokasyonunu alabilme.
* Lokasyon bilgisini koordinata çevirebilme.
* Takip edilen hesapların yerlerini, dünya haritası üzerinde heat map haline getirebilme.

## Twitter authentication

Öncelikle biraz güvenlikten bahsedelim.
Authentication dediğimiz, bir nevi kimlik ispatı gibi düşünülebilir.
Mesela polis sizi durdurup kimlik istediğinde, aslında yaptığınız şey authentication.
Bir de authorization var, yetki kontrolü diye çevirebiliriz.
Authentication olmadan, authorization'dan bahsetmek mümkün değil.
Bir kullanıcı, önce kendi kimliğini ispat edecek (authentication), daha sonra gerektiğinde o kimliğe tanımlanmış yetki kontrolü (authorization) yapılabilir.

Twitter, API metodları için iki farklı authentication türüne [sahip](https://developer.twitter.com/en/docs/basics/authentication/overview/oauth):

* [OAuth2](https://oauth.net/2/): Bu protokol ile authentication yapılınca Twitter'ın public (herkese açık) verilerine erişebiliyor.
Bu protokol bağlamında, kullanıcı yok.
Uygulama olarak, herhangi bir kullanıcı adına değil, doğrudan uygulama adına ve sadece herkese açık olan verilere erişebiliyoruz.
* [OAuth 1.0a](https://oauth.net/core/1.0/): Bir kullanıcı adına o kullanıcının verilerine erişmemizi sağlıyor.
Bizim kullanacağımız bu, çünkü kullanıcı bağlamına ihtiyacımız var, bir kullanıcı adına hareket edecek uygulamamız.

### Twitter entegrasyonu
Twitter API metodlarına erişebilmek için, [developer.twitter.com](https://developer.twitter.com/) üzerinden kendimize bir uygulama (twitter application) oluşturuyoruz.
![Twitter dev app][2]

Uygulama oluşturunca, twitter bize *consumer key* ve *consumer secret* adında iki anahtar verecek.
OAuth 1.0a akışını, bu anahtarlarla başlatacağız.
Bunlar olmadan, Twitter API metodlarını çağırmamız mümkün değil.

### OAuth 1.0a

*Not: Birazdan token'lar havalarda uçuşacak, hangi token ne için kullanılıyor daha kolay takip edebilmek için token'lara "TOKEN_A" "TOKEN_B" gibi isimler vereceğim. Bunu yaparken, bize görünmeyen arka planda kullanılan token'ları isimsiz bırakıyorum.*

OAuth bir kütüphane veya uygulama değil.
OAuth bir protokol ve Twitter API metodlarının kullanımını OAuth protokolü ile emniyete almış.

*OAuth 1.0a'yı* kabaca tarif etmek gerekirse, uygulamanızın başka bir kullanıcı adına o kullanıcının üyesi olduğu *servisi* çağırmanızı sağlıyor. Burada uygulamamız [twheat-map](https://twheat-map.selcukcihan.com/), servis dediğimiz şey ise [Twitter](https://twitter.com/). Mekanizma şöyle işliyor:

1. Uygulamamız, twitter'a kendini *consumer key* ile tanıtıyor.
Bunu yaparken Twitter'a bir de *callback* URL veriyoruz.
2. Twitter, uygulamamıza bir *token* veriyor.
3. Kullanıcıyı twitter'a yönlendirip, aldığımız bu *token'ı* imzalatıyoruz.
Kullanıcı twitter üzerinden kullanıcı adı/şifresini girdikten sonra, twitter ilk adımda vermiş olduğumuz *callback* URL'i imzaladığı *token* ile çağırıyor.
4. İmzalanan *token'ı* kullanarak, twitter'dan bir *access token* (TOKEN_A) alıyoruz. 
5. Aldığımız *access token'ı* (TOKEN_A) kullanarak, twitter'ın API metodlarını sanki o kullanıcıymışız gibi çağırıyoruz.

OAuth 1.0a akışı, sunucu tarafında gerçekleştirilmesi gerek bir iş.
İstemci (client) tarafında gerçekleştirilmemeli çünkü bize özel anahtarlarımız kullanılacak ve bu anahtarların istemciler tarafından görülmemesi lazım, aksi takdirde isteyen bunları kullanarak bizim uygulamaymış gibi davranabilir.

### Auth0

Authentication kısmı karışık gelmiş olabilir, merak etmeyin çünkü OAuth işlemlerini [Auth0](https://auth0.com/)'a devredeceğiz.
Eğer bunu yapmasaydık, sunucu tarafında kendi oauth implementasyonumuzu yapmak zorunda kalacaktık, gerek yok, yapılmışı var.
Hem de Auth0 ile gayet şık bir login ekranımız bile hazır gelecek.
Auth0 çok güzel ve kullanması inanılmaz kolay bir *identity* servisi sunuyor.
[Twitter entegrasyonu](https://auth0.com/docs/connections/social/twitter) zaten mevcut, bu demek oluyor ki **oauth ile ilgili hiç kod yazmadan** "Twitter ile login" özelliğine sahip olacak uygulamamız.

## Uygulama mimarisi

Uygulamamız, bir sunucu (backend) ve bir istemci (client) olmak üzere iki parçadan oluşacak.
Düşününce neden bir sunucuya ihtiyacımız var diyebilirsiniz, sebebi şu:
Auth0 ile twitter üzerinden kullanıcının kimlik doğrulamasını yapabiliyoruz.
Bu doğrulama sonucunda twitter Auth0'a bir *access token* (TOKEN_A) veriyor.
Fakat Auth0, twitter'ın kullanıcıya verdiği *access token'ı* (TOKEN_A) [bize doğrudan vermiyor](https://auth0.com/docs/connections/calling-an-external-idp-api).
Bu *token'a* (TOKEN_A) erişebilmek için, Auth0'ın [yönetim API metodunu](https://auth0.com/docs/api/management/v2) çağırmamız gerekiyor.
Bu metodu çağırabilmek için de Auth0'dan bir başka *token* (TOKEN_B) almamız gerekiyor, bu herkese açık olan bir API değil.
Dolayısıyla, bu metodu sadece güvenli bir yerden yani sunucudan çağırmalıyız.
Çünkü bu metodu çağırırken, Auth0'dan aldığımız "client id" ve "client secret" değerlerini kullanacağız.
Eğer istemciden çağırsaydık, herkes bu çağrıyı nasıl yaptığımızı görüp, sanki biz çağırıyormuşuz gibi Auth0 metodlarını çağırıp uygulamamızın güvenliğini delebilirdi.

Bir diğer sebep de, henüz yapmamış da olsam, sunucu uygulamasında bir cache mekanizması yapmak istemem.
Aynı kullanıcı için her seferinde gidip de arkadaş listesini çekmeye gerek yok, arkadaş listesini haftada bir güncellesem yeterli diye düşünüyorum.
Bunu yapmanın tek yolu, sunucu tarafında kullanıcının arkadaş listesini bir veritabanına yazmak.
Bu sayede Twitter API metodunu da sık sık çağırmamış olacağız, neticede onun da bir kotası ve ücreti var.

### İstemci (client)

İstemciyi [ReactJS](http://reactjs.org/) ile yazıp, netlify üzerinden host etmeyi düşünüyorum.
Benzer bir örnek için [ilgili yazıma](/web-development/reactris/) bakabilirsiniz.

#### Login ve authentication
İstemci, API authentication için [JWT](https://jwt.io/) kullanacak, bunun için Auth0'ın geniş istemci kütüphanesinde [kullanıma hazır kodlar](https://github.com/auth0-samples/auth0-react-samples/tree/master/01-Login) mevcut.
İstediğiniz platformu seçip, ilgili kod parçacığına erişebiliyorsunuz.

![Auth0 reactjs starter][11]

ReactJS de hazır kütüphaneleriyle destekledikleri platformlardan biri.

İstemci tarafında login akışı şöyle olacak:

1. İstemci Auth0'ya bir login isteği gönderecek.
2. Auth0 istemciyi Auth0'ın host ettiği kendi login ekranına yönlendirecek.
3. Kullanıcı, burada yer alan "login with twitter" butonuna tıklayacak.
![Auth0 log in with twitter][3]
4. Auth0 kullanıcıyı [api.twitter.com/oauth/authenticate](https://developer.twitter.com/en/docs/basics/authentication/api-reference/authenticate) endpoint'ine yönlendirecek
5. Twitter kullanıcı için bir *access token* (TOKEN_A) üreterek, Auth0'ın *callback* url'ini çağıracak.
6. Auth0 kullanıcının token (TOKEN_A) ve diğer bilgilerini saklayacak, bir JWT (TOKEN_C) üretecek ve kendi *callback* url'imizi çağıracak.
7. Böylece uygulamamız artık kullanıcının kimliğini doğrulamış olacak.
8. İstemci, kullanıcıya Auth0 tarafından verilen *JWT* (TOKEN_C) ile sunucumuzdaki kendi API metodumuzu çağırabilecek.

#### Harita
Kullanıcımızın kimliğini doğruladığımıza göre, şimdi kendi API metodumuzu çağırıp kullanıcının arkadaşlarının hangi koordinatlarda yer aldığını bulabiliriz.
API metodumuz bize şöyle bir liste dönecek:
<script src="https://gist.github.com/selcukcihan/277a05096f0b0240803719fe2cadf7f9.js"></script>

Haritayı gösterebilmek için ilk başta Google Maps [API metodlarını](https://developers.google.com/maps/documentation/javascript/heatmaplayer) kullanmayı düşünmüştüm, fakat o kadar detaylı bir haritaya gerek olmadığından zoom özelliği olmayan statik bir dünya haritası ile işi kotarmaya karar verdim.
Bunun için [react-simple-maps](https://www.react-simple-maps.io/) diye çok kullanışlı ve şık haritalar çizen bir react komponent kütüphanesi buldum:
![React simple map][4]

### Sunucu
Sunucu tarafını [nodejs](https://nodejs.org/en/) ile yapmaya karar verdim.
Önceleri Python ile yazılım geliştirmeyi tercih ediyordum fakat son zamanlarda javascript'e kaydım.
Nedenine gelince, javascript kullanımı iyice yaygınlaştı ve örnek kod, SDK, kütüphane vb. yardımcı araç gereç bulmak çok daha kolay.
Stackoverflow 2019 yazılımcı anketine göre, javascript [%67.8](https://insights.stackoverflow.com/survey/2019#technology-_-programming-scripting-and-markup-languages) ile en popüler programlama dili.
Yine aynı ankette, nodejs kullanımı [%49.9](https://insights.stackoverflow.com/survey/2019#technology-_-programming-scripting-and-markup-languages) ile .Net'i bile geride bırakmış durumda.

Sunucuda bir API metodumuz olacak, bu metod parametre olarak twitter ile giriş yapan kullanıcı adına Auth0'ın ürettiği bir JWT *token'ı* (TOKEN_C) alacak ve güvenliğini bununla sağlayacak.
Sadece twitter'la login olan kullanıcılar metodumuza erişebilecek.
Metodumuz sırasıyla şunları yapacak:

1. Sunucu, "client id" ve "client secret" değerlerini kullanarak Auth0'dan yönetim API'sini çağırabilmek için bir token (TOKEN_B) isteyecek.
2. Bu *token'ı* (TOKEN_B) kullanarak, sunucu Auth0 yönetim API metodunu çağırıp kullanıcı adına twitter'ın vermiş olduğu *access token'ı* (TOKEN_A) çekecek.
3. Bu *access token'ı* (TOKEN_A) kullanarak, bir kullanıcının takip ettiği hesapları çeken [twitter API metodunu](https://developer.twitter.com/en/docs/accounts-and-users/follow-search-get-users/api-reference/get-friends-list) çağıracak.
4. Takip edilen hesapların lokasyon bilgisini istemciye dönecek.

#### Twitter API'dan gelen cevap
Twitter'ın API metodundan gelen cevap şu şekilde:
<script src="https://gist.github.com/selcukcihan/56c36a72ac001490e139f7e92d364159.js"></script>

Twitter'dan gelen cevapta, her kullanıcının bir de *location* bilgisi var.
Twitter'daki lokasyon, *Free-text* bir alan, yani isteyen istediği şeyi yazabiliyor buraya.
Tabi insanlar genelde kullanım amacına uygun olarak, bulundukları şehri, ülkeyi vs. yazıyorlar.
Lokasyon bilgisini haritada gösterebilmek için koordinata çevirmemiz gerekiyor.
Bu noktada basite kaçtım ve içinde yaklaşık 13.000 adet lokasyon/koordinat bilgisi olan [ücretsiz bir data set](https://simplemaps.com/data/world-cities) buldum.
Tüm her yeri kapsamasa da, pek çok ülkeyi ve şehri kapsıyor, işimizi fazlasıyla görecektir.

#### Serverless
Sunucuyu herhangi bir fiziksel/sanal makina yönetmeden, tamamen *serverless* olarak AWS API Gateway ve Lambda ile gerçekleyeceğim.
[Serverless framework](https://serverless.com) çok kullanışlı ve AWS üzerinde sunucusuz (serverless) uygulamalar geliştirme sürecini çok hızlandıran güzide bir framework.
Eğer hala bir AWS hesabınız yoksa ve nasıl açılacağını merak ediyorsanız, [ilgili yazımı](https://blog.selcukcihan.com/bulut-bilisim/awsye-giris/) okuyarak yaklaşık beş dakika içinde AWS hesabınızı açabilirsiniz.

AWS üzerinde host edilecek olan sunucu uygulamamız basit bir uygulama olup, iki adet Lambda metodundan oluşacak.
Bunlardan biri, API Gateway ile sunacağımız asıl metodumuzu kullanmak isteyen istemcilerin authentication yani kimlik doğrulamasını yapacak olan metod.
Auth0, API Gateway custom lambda authorizer'ları nasıl yazılır anlatan güzel [bir doküman](https://auth0.com/docs/integrations/aws-api-gateway/custom-authorizers) yazmış.
Bir de tabi [AWS'nin kendi dokümanı](https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-use-lambda-authorizer.html) var.

İstemci, API Gateway üzerinde host edilecek olan `/heatmap` metodumuzu çağırırken, HTTP header'ında aşağıda gördüğünüz Authorization: Bearer parametresiyle JWT token'ı (TOKEN_C) gönderecek.
![Authorization bearer][5]

API Gateway, header'daki bu token'ı (TOKEN_C) alıp authorization lambda'mızı çağıracak.
Kimlik doğrulamasını yapacak olan lambda metodumuzun tek yaptığı şey gelen JWT token'ı (TOKEN_C) doğrulayıp, cevap olarak bir AWS IAM Policy dokümanı dönecek.
API Gateway de bu dokümana bakarak istemciden gelen isteği ya kabul edecek ya da reddedecek.
Kabul ettiği durumda ise asıl metodumuz olan ve Twitter API metodunu çağırıp bize koordinatları dönecek olan metodumuzu çağırıp sonucu istemciye dönecek.
Anlayacağınız, API Gateway aslında bir proxy görevi görüyor, yani asıl işi yapan o değil.
Elçilik görevi görüyor.
API Gateway'i kullanarak lambda metodlarınızı RESTful bir API metoduna dönüştürebilirsiniz.

### Auth0 entegrasyonu

Kendi oauth sunucumuzu yapmamak için, Auth0'ın sunduğu *SAAS* identity hizmetini kullanıyoruz.
Bunun için Auth0 üzerinde ücretsiz hesap oluşturdum.
Bir "Single Page Web Application" ve bir de "Machine to Machine Application" oluşturdum.
Bir adet API oluşturdum.
Aslında buradaki API mantıksal bir şey, yani bir metod falan değil.
Bunu kullanarak API Gateway için JWT token (TOKEN_C) üreteceğiz.

Auth0 üzerinde iki adet API'ımız var, biri kendi oluşturduğumuz ve API Gateway için JWT üretmek için kullandığımız, diğeri de Auth0'ın kendi oluşturduğu bir *management API*.

![Auth0 API'leri][6]

"Machine to Machine Application" oluşturmamızın sebebi, kendi sunucumuzun (backend) Auth0'ı çağırıp kullanıcının detaylarını çekebilmesi.
Bunun için de ilgili uygulamaya Auth0 *management API* metodlarını çağırabilmesi için bazı yetkiler vermemiz gerekiyor:

![Auth0 authorize machine to machine][8]

Tabi bu uygulamamızın bir client id ve client secret'i olacak, bu ikiliyle birlikte backend uygulamamız Auth0 API metodlarını çağırabilecek:

![Auth0 machine to machine api keys][10]

Auth0 üzerinden machine-to-machine backend app'a yetki olarak "read:users" ve "read:user_idp_tokens" verdim.
Bu sayede, artık backend aşağıda gördüğünüz *management API* metodunu çağırarak kullanıcıya Twitter tarafından verilen *access token'a* (TOKEN_A) erişebilecek.

![Auth0 management API][7]

Son olarak, henüz Auth0'ı Twitter'a bağlamadık.
Auth0'ın bize Twitter entegrasyonu verebilmesi için, Twitter'dan aldığımız *consumer api key* ve *consumer api secret* anahtarlarını Auth0'a tanıtmamız lazım.
Bunun için Auth0 yönetim konsolundan ["Connections->Social"](https://manage.auth0.com/dashboard/us/selcukcihan/connections/social) sayfasını açıp, Twitter ikonuna tıklayıp entegrasyonu başlatıyoruz.
Twitter'dan aldığımız anahtarları, aşağıdaki gibi Auth0'a ekliyoruz:

![Auth0 Twitter keys][9]

## Sonuç

Uygulamaya [twheat-map.selcukcihan.com](https://twheat-map.selcukcihan.com/) adresinden erişebilirsiniz.
İstemci projesi [github.com/selcukcihan/twheat-map](https://github.com/selcukcihan/twheat-map) üzerinde, sunucu projesi ise [github.com/selcukcihan/twheat-map-backend](https://github.com/selcukcihan/twheat-map-backend) üzerinde.

Uygulama için düşündüğüm bazı iyileştirmeler var:

* Twitter API metodundan gelen bilgileri veritabanında saklayıp her seferinde API çağırmaktansa veritabanında varsa oradan kullanmak.
Tabi arada (mesela veritabanındaki data bir haftadan eskiyse) gidip Twitter'dan tekrar güncel datayı çekmek gerekecektir.
* Sunucumuzdaki kendi API metodumuzu asenkron hale getirmek, yani istemci bana koordinatları ver dediğinde cevabı hemen beklemeyecek, onun yerine Web socket kullanarak Twitter'dan data çektikçe sunucudan istemciye koordinatları göndereceğiz.

[1]: /img/twheat-map/heat_map.png
[2]: /img/twheat-map/twitter_dev_app.png
[3]: /img/twheat-map/auth0_login_with_twitter.png
[4]: /img/twheat-map/react_harita.png
[5]: /img/twheat-map/authorization_bearer.png
[6]: /img/twheat-map/auth0_all_apis.png
[7]: /img/twheat-map/auth0_get_user_by_id_api_method.png
[8]: /img/twheat-map/auth0_authorize_machine_to_machine_scope.png
[9]: /img/twheat-map/auth0_twitter_consumer_keys.png
[10]: /img/twheat-map/auth0_backend_app.png
[11]: /img/twheat-map/auth0_starter.png
