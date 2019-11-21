---
title: "ReactJS ile tetris"
tags: ["react", "tetris", "javascript", "statik web sitesi", "oyun", "reactjs"]
date: 2019-11-21T14:41:29+03:00
draft: false
---

ReactJS en popüler frontend framework'lerinden biri.
React öğrenmek için bazı tutorialları okusam da, bir işi öğrenmenin en iyi yolu uygulama yapmaktır diyerek basit bir tetris oyunu yapmaya karar verdim.
Yazıyı okumadan önce, nasıl bir oyun ortaya çıktığını görmek için: https://tetris.selcukcihan.com/

## Önkoşullar ve varsayımlar

* Örneği macOS üzerinde yaptım.
* Paket yöneticisi olarak [yarn](https://yarnpkg.com/lang/en/) kullandım.
* Klasörleri ağaç şeklinde göstermek için, [tree](https://linux.die.net/man/1/tree) komutunu kullandım.

## Güncel teknoloji

[React](http://reactjs.org/) oldukça popüler, [2019 stackoverflow anketinde](https://insights.stackoverflow.com/survey/2019#technology-_-web-frameworks) "Web framework" kategorisinde JQuery'den %31.3 ile en popüler tercih. Arkasında facebook var ve popülerliği her geçen gün artıyor. Buna üyesi olduğum [Toptal](https://www.toptal.com/) üzerinde de şahit oldum, react gerektiren işlerin sayısı oldukça fazla.

## Başlayalım

React ile SPA (single page application) yapabiliyorsunuz. İşe kolayca koyulabilelim ve boiler-plate dedikleri projenin çıplak halini hızlıca oluşturabilelim diye, [create-react-app](https://github.com/facebook/create-react-app) diye bir komut hazırlamışlar. Öncelikle [npm](https://www.npmjs.com/get-npm) kurmanız gerekiyor, dikkat edin versiyonu 5.2 veya daha yüksek olsun.
```
npx create-react-app reactris
```

komutunu çalıştırdığımızda, şöyle bir proje oluşacak:
```
➜  reactris git:(master) tree -a -I ".git|node_modules" .
.
├── .gitignore
├── README.md
├── package.json
├── public
│   ├── favicon.ico
│   ├── index.html
│   ├── logo192.png
│   ├── logo512.png
│   ├── manifest.json
│   └── robots.txt
├── src
│   ├── App.css
│   ├── App.js
│   ├── App.test.js
│   ├── index.css
│   ├── index.js
│   ├── logo.svg
│   └── serviceWorker.js
└── yarn.lock
```

Bu haliyle projeyi ayağa kaldırıp, demo sayfasına bakabilirsiniz. Bunun için
```
cd reactris && yarn start
```
komutunu çalıştırın.
Eğer her şey yolunda gittiyse, şöyle bir çıktı üretip development web sunucusu ayağa kalkacak:
```
Compiled successfully!

You can now view reactris in the browser.

  Local:            http://localhost:3000/
  On Your Network:  http://192.168.0.11:3000/

Note that the development build is not optimized.
To create a production build, use yarn build.
```
http://localhost:3000/ adresine gidin ve uygulamanıza bakın.
App.js üzerinde basit bir değişiklik yapın ve kaydedin.
Değişiklik otomatik olarak algılanıp, hemen ekrana yansıyacaktır.
Bu sayede, geliştirme süreci oldukça hızlanıyor, yaptığınız değişikliklerin etkisini hemen gözlemleyebiliyorsunuz.
*Package.json* dosyasına bakarsanız, scripts altında hangi komutların mevcut olduğunu görebilirsiniz.

## Deployment
Yaptığınız geliştirmelerin nasıl çalıştığını görmek için *start* komutunu, uygulamayı deploy edeceğimizde ise *build* komutunu kullanıyoruz, mesela:

```
yarn build
```

```
➜  reactris git:(master) yarn build
yarn run v1.12.3
$ react-scripts build
Creating an optimized production build...
Compiled successfully.

File sizes after gzip:

  39.87 KB  build/static/js/2.f8146030.chunk.js
  770 B     build/static/js/runtime-main.2f676038.js
  608 B     build/static/js/main.96a1a00b.chunk.js
  417 B     build/static/css/main.b100e6da.chunk.css

The project was built assuming it is hosted at the server root.
You can control this with the homepage field in your package.json.
For example, add this to build it for GitHub Pages:

  "homepage" : "http://myname.github.io/myapp",

The build folder is ready to be deployed.
You may serve it with a static server:

  yarn global add serve
  serve -s build

Find out more about deployment here:

  https://bit.ly/CRA-deploy
```

Bu komut, build klasörünü oluşturup içine uygulamamıza ait dosyaları [webpack](https://webpack.js.org/) ile küçültüp (minify) paketleyerek (bundle) taşıyacak:
```
➜  reactris git:(master) tree build   
build
├── asset-manifest.json
├── favicon.ico
├── index.html
├── logo192.png
├── logo512.png
├── manifest.json
├── precache-manifest.b8046172bd35cc43cc30b357fd0a862a.js
├── robots.txt
├── service-worker.js
└── static
    ├── css
    │   ├── main.b100e6da.chunk.css
    │   └── main.b100e6da.chunk.css.map
    ├── js
    │   ├── 2.f8146030.chunk.js
    │   ├── 2.f8146030.chunk.js.map
    │   ├── main.96a1a00b.chunk.js
    │   ├── main.96a1a00b.chunk.js.map
    │   ├── runtime-main.2f676038.js
    │   └── runtime-main.2f676038.js.map
    └── media
        └── logo.25bf045c.svg
```

Default webpack ayarları, [react-scripts](https://www.npmjs.com/package/react-scripts) paketinden geliyor.
İsterseniz bu ayarları değiştirebilirsiniz, *eject* komutuna bir bakın.
Bu komut, create-react-app tarafından gelen default ayarları değiştirmek isteyenler için bu ayarları ilgili paketlerden çıkarıp kendi paketinize ekliyor.

Eğer [source map](https://developer.mozilla.org/en-US/docs/Tools/Debugger/How_to/Use_a_source_map) dosyalarını istemiyorsanız, package.json içerisindeki build script'ini şununla güncelleyin:
```
"build": "GENERATE_SOURCEMAP=false react-scripts build"
```

## Kodlamaya geçelim

ReactJS, JSX adı verilen ve html ile javascript'i harmanlayan bir syntax kullanıyor.
Aslında bu syntax javascript'e bir ek ve ReactJS ile uygulama geliştirirken oldukça pratik.
İsterseniz ReactJS'i JSX olmadan saf javascript olarak da kullanabiliyorsunuz fakat gerçekten hayatınızı o kadar kolaylaştırıyor ki, kullanmamak ayıp olur.
[Şu sayfadaki](https://reactjs.org/docs/react-without-jsx.html) JSX'li ve JSX'siz iki örneğe bakın ve siz karar verin.

ReactJS'te temel yapı taşı *component*lerdir.
Component denilen şey, *render* metoduna sahip bir sınıf veya bir fonksiyon olabilir.
İdeal olarak, bir javascript dosyası (modül de deniyor) bir component içermeli.
Bu sayede, projeniz büyüdükçe yönetmek zorlaşmaz.
Tüm component'lerinizi tek bir dosyaya da koyabilirsiniz ama işler çığrından çıkacaktır.

Component bir metod veya bir sınıf olabiliyor dedik, peki ReactJS kütüphanesi yazdığınız component'ten ne bekliyor?
Basitçe, bir React element'i dönmesini bekliyor diyebiliriz.
*public/index.html* dosyasına bakarsanız, içinde id'si *root* olan bir *div* göreceksiniz.
*src/index.js* dosyasında ise, *root* elemanı `document.getElementById` ile [DOM'dan](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model/Introduction) alınarak, içine *App* component'i render ediliyor.
*App* component'i ise *src/App.js* içerisinde tanımlanmış bir fonksiyon, döndüğü şey ise JSX ile kodlanmış bir react elemant'i.

## Tetris tasarımı

Oyunun tasarımı oldukça basit, 20x10'luk bir [display: grid;](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Grid_Layout) div'imiz var.
Bunun içine 1x1'lik kutucuklar koyuyoruz.
Her bir kutucuk da bir *div*:
```
<div class="game-grid-container">
    <div class="tile empty"></div>
    <div class="tile empty"></div>
    <div class="tile empty"></div>
    <div class="tile occupied t-shape"></div>
    <div class="tile occupied square-shape"></div>
    <div class="tile occupied square-shape"></div>
    .
    .
    .
```
Kutucukların rengi, "*...-shape*" ile biten class'tan geliyor.
Chrome *Developer Tools* ile bakınca, şöyle gözüküyor:
![Tetris dev tools][1]
Koordinatlarımız, (0, 0) sol üst ve (9, 19) sağ alt şeklinde.
Tetris parçaları toplamda 7 tane ve [src/game/Shape.js](https://github.com/selcukcihan/reactris/blob/master/src/game/Shape.js) içerisinde *Shape* sınıfından türetiliyor.
Mesela L şeklindeki parçanın adı *LShape*.
Her bir parçada, css class'ının ismi, başlangıçta grid'in yatayda hangi kutucuğundan başlayacağı (dikeyde en tepeden başladığı için y koordinate 0 olarak başlıyor).
*orientations* array'inde, bir şeklin sahip olabileceği tüm varyasyonlar hazır tutuluyor.
Yani oyuncu şekli döndürmek istediğinde, hangi koordinatlara sahip bir şekil olacağını baştan hesaplamıyoruz.
Bunun yerine hazır hesaplanmış *orientations* içinden sırayla bir şeklin bir sonraki halini alıyoruz.
Yine bu array'e dikkatli bakarsanız, şekilleri her zaman saat yönünde dönecek biçimde ayarladığımı görürsünüz.
Şekillerden en basit olanı kare şekli olup, sadece bir hale sahip, yani döndürseniz de aynı halde göründüğü için, döndürme işlemi kare şekli üzerinde herhangi bir etkiye sahip değil.

Herhangi bir anda, oyunda bir aktif şekil yukarıdan aşağıya doğru iniyor.
Bir sonraki şekil de aşağıda gösteriliyor ve aktif şekil artık hareket edemeyecek hale gelince, sıradaki şekil aktif şekil oluyor.
Ana grid'e ek olarak, her bir hamlede sıfırdan oluşturulan bir ikincil grid var.
İkincil grid, aktif şekli üzerinde taşıyor ve aktif şekil artık hareket edemeyecek hale geldiğinde ikincil grid'den alınıp ana grid'e ekleniyor ve nihai yerini buluyor (ta ki patlatılıncaya kadar diyelim).
Ana grid'e eklenen şekiller, patlama hallerinde orjinal şekillerini elbette yitiriyorlar, fakat yine de kaldığı kadarıyla orjinal şekille aynı renge sahip oluyor.
Bunu sağlayan, ana grid'deki her bir kutucuğa orjinal şeklin referansını vermiş olmam.
Bu referans sayesinde, orjinal şeklin css sınıf ismini alıp aynı o şeklin renginde boyayabiliyorum.

Ana grid, [src/game/Board.js](https://github.com/selcukcihan/reactris/blob/master/src/game/Board.js) içerisinde, *this.board* ile ulaştığımız iki boyutlu 20x10luk array'de tutuluyor.

## Hareketler

Oyuncudan gelen direktifleri, [src/App.js](https://github.com/selcukcihan/reactris/blob/master/src/App.js) içerisindeki *handleKeyPress* (klavyeden yön tuşları ve çubuk) ve *handleTouchEnd* (mobil cihazlardan ekrana dokunma) metodları karşılıyor.
Bir hamlenin gerçekleşebilmesi için, *src/game/Shape.js* içinde, o şekil o hamleyi boş bir grid'de yapabiliyor mu diye bakıyoruz (mesela dik çubuk şekli, en kenardayken dönemez).
Daha sonra, ana grid'le karşılaştırıp, ana grid'de o noktada başka bir şekilden kalan bir parça var mı yok mu, yani o nokta boş mu değil mi diye kontrol ediyoruz.
Eğer boşsa, hamle gerçekleşiyor.

Oyuncudan gelen direktifler haricinde, aktif şekil her bir saniyede bir seviye aşağı kayıyor.
Patlamalar (veya nihai konumunu bularak oturmalar) sadece aşağı kayma hareketinden sonra oluyor (ister otomatik kaysın süreden ötürü, ister kullanıcı aşağı ok tuşuna bassın).

## ReactJS

Uygulamanın React kullanılan kısmı, *src/App.js* olup, sadece bir component'e sahiptir (*App componenti*).
Dikkat ettiyseniz, *src/game* altında yer alan dosyalar saf javascript, react kullanmıyor.

*Component'teki* *render* metodu, mevcut *board'u* ve aktif şekli çizmeye kodlanmış durumda.
*Board'da* bir değişiklik, yani bir hamle veya otomatik aşağı inme olduğunda, bu değişiklik *react'a* *setState* metodu ile bildiriliyor ve *react* kendi sanal *DOM'unu* bu sırada güncelliyor.
Sonrasında ise eğer gerek görürse, *react'ın DOM'u* *HTML DOM'unu* güncelliyor.
Burada bir optimizasyon var, eğer react *HTML DOM'unda* bir değişiklik olmayacağını anlarsa, boşuna *DOM'u* güncellememiş oluyor.

[1]: /img/reactris/tetris_dev_tools.png
