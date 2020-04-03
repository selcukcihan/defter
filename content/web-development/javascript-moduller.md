---
title: "JavaScript: Modüller"
tags: ["javascript", "modül", "module", "import", "export", "node", "nodejs", "ecmascript", "require", "cjs", "es6", "esm"]
date: 2020-03-31T13:41:29+03:00
draft: false
images: ["/img/javascript-moduller/cube.jpg"]
---

JavaScript kullanıyorsanız, ister ön-yüz ister sunucu tarafı olsun, modül kullanımına rastlamış ve kullanmışsınızdır.
"import" ve "require" ifadeleri ne işe yarar, farklı kullanım şekillerinin birbirinden farkı nedir, gibi soruları merak ediyorsanız okumaya devam edin.
JavaScript'e nispeten yeni bulaşmış biri olarak, bu soruların cevabını sadece bulmakla kalmayıp anlamaya da çalıştım ve bu süreci istifadenize sunmak istedim.

![Cube house][1]
<a style="background-color:black;color:white;text-decoration:none;padding:4px 6px;font-family:-apple-system, BlinkMacSystemFont, &quot;San Francisco&quot;, &quot;Helvetica Neue&quot;, Helvetica, Ubuntu, Roboto, Noto, &quot;Segoe UI&quot;, Arial, sans-serif;font-size:12px;font-weight:bold;line-height:1.2;display:inline-block;border-radius:3px" href="https://unsplash.com/@rc_scout?utm_medium=referral&amp;utm_campaign=photographer-credit&amp;utm_content=creditBadge" target="_blank" rel="noopener noreferrer" title="Richard Ciraulo'ya ait fotoğraflar için tıklayın."><span style="display:inline-block;padding:2px 3px"><svg xmlns="http://www.w3.org/2000/svg" style="height:12px;width:auto;position:relative;vertical-align:middle;top:-2px;fill:white" viewBox="0 0 32 32"><title>unsplash-logo</title><path d="M10 9V0h12v9H10zm12 5h10v18H0V14h10v9h12v-9z"></path></svg></span><span style="display:inline-block;padding:2px 3px">Fotoğraf Unsplash'ta Richard Ciraulo'ya aittir.</span></a>

# Terminoloji
Detaylara girmeden evvel terminolojiyi oturtmakta fayda var, çünkü **JavaScript dünyasında kavram karmaşasına çok sık rastlanıyor**.

* **ECMAScript**: Bir programlama dili spesifikasyonu, güncel versiyonuna [buradan](https://www.ecma-international.org/publications/standards/Ecma-262.htm) ulaşabilirsiniz.
* **JavaScript** (JS): ECMAScript spesifikasyonuna uygun bir programlama dili
* **JavaScript engine** (JS motoru): JS kodunu çalıştıran bir uygulama olarak düşünebilirsiniz.
Mesela tarayıcınızın bir JS motoru vardır, Google'ın [V8 motoru](https://v8.dev/) veya Mozilla'nın [SpiderMonkey](https://developer.mozilla.org/en-US/docs/Mozilla/Projects/SpiderMonkey) motoru gibi. 
* **Node.js**: V8 motorunu kullanan bir JS *runtime'ı*.
* **JS modülleri** (native): ES modülleri veya ECMAScript modülleri olarak da bilinir. ESM kısaltmasıyla da anılıyor. Spesifikasyona [buradan](https://www.ecma-international.org/ecma-262/6.0/#sec-modules) ulaşabilirsiniz.
* **Node.js modülleri**: Native JS modülleri çıkmadan önce, Node.js [modül](https://nodejs.org/api/modules.html) yapısına sahipti (bkz. *require*).
* **CommonJS** (CJS): En popüler modül sistemi diyebiliriz, Node.js'te kullanılmasıyla çok yaygınlaştı.
* **ES6**: ECMAScript [2015 spesifikasyonu](https://www.ecma-international.org/ecma-262/6.0/).

# Tarihçe

Bizi ilgilendiren iki tür modül var, kronolojik olarak:

1. *Native* olmayan modüller (mesela Node.js'te kullanabildiğimiz [CommonJS](http://www.commonjs.org/) ve [AMD](https://github.com/amdjs/amdjs-api/blob/master/AMD.md) gibi)
2. *Native* modüller, yani JS modülleri (ES6'daki gibi).

JS modülleri nispeten yeni bir özellik.
Geç gelen bir özellik olduğundan, öncesinde boşluğunu dolduracak başka modül API'leri çıkmış.
[Modüller](https://nodejs.org/api/modules.html#modules_modules), yukarıda açıkladığım gibi, Node.js'te daha önce de vardı - fakat *native* olarak değil.

# Modüller

Her dosya bir modül diye tasavvur edebilirsiniz.
Öncelikle modüllere neden ihtiyaç olduğunu düşünelim.
İster bir ön-yüz (react, angular, vue veya vanilla (düz) js) ister bir sunucu uygulaması olsun (nodejs), **kod parçalarını birbirinden ayırmak kodun okunabilirliğini artıran en önemli husustur**.
Bütün uygulamanızı 10.000 satırlık tek bir dosya olarak tutmak pek akıl kârı değil.

Genelde sunucu uygulamaları yaptığımdan **örneklerimi Node.js ile veriyorum**.
Basit tutalım, örneğimiz;

* bir "utility" modülü olsun
* içinde bir "sleep" metodu olsun, belirttiğiniz süre kadar uyuyan bir Promise dönecek
* bir de "default" uyku süresini belirten bir sabit değer olsun

## ES Modülleri (native modüller)

Node.js ile [ES modülü](https://nodejs.org/api/esm.html) oluşturmak isterseniz temelde iki yönteminiz var:

* Dosyanın uzantısını `.mjs` yapmak
* Dosyanın uzantısı `.js` olup, package.json dosyasında `type` alanını `module` yapmak.

İkinci yöntemi uygulayan bir örnek:

<script src="https://gist.github.com/selcukcihan/86d80594edf72b3709987ccff4ca33ec.js"></script>

Örneği çalıştırmak için dosyaları bir klasöre indirip, `node index.js` komutunu kullanabilirsiniz.

Modülden iki değer "export" ediliyor, biri `DEFAULT_SLEEP_MILLISECONDS` diğeri de `sleep` fonksiyonu.
Modülü kullanacak olan kod (`index.js`) istediği değerleri "import" ediyor ve böylece bu değerlere aynı isimlerle erişebiliyoruz.
Bu örnekte "named export" kullandık, yani export'larımıza birer isim verdik.

Bir de "default" export'lar var:

<script src="https://gist.github.com/selcukcihan/4c6e6964ce16992c24f07ec1c7fff867.js"></script>

Bir modülü import ederken eğer süslü parantez (`{}`) kullanmazsanız, o modüldeki `default` export'u import etmiş oluyorsunuz.

İsim çakışmalarını engellemek için, import ettiğiniz değerlere yeni isimler verebilirsiniz.
Mesela:

```
import {DEFAULT_SLEEP_MILLISECONDS, sleep as sleepMilliseconds} from './utils.js'
```

Bu sayede, `sleep` metodunu, `sleepMilliseconds` ismiyle kullanabileceksiniz.
İsim karmaşasının önüne geçmek için bir yöntem daha var, o da import'ları bir modül nesnesine bağlamak:

```
import * as utils from './utils.js'
```

Böylece, fonksiyona `utils.sleep()` şeklinde ulaşabiliyorsunuz ve import edilen isimlerin çakışma ihtimalini sıfırlamış oluyorsunuz.

## Native olmayan modüller (aka. CJS, require)

Basit örneğimizi *native* olmayan modüllerle oluşturacak olursak:

<script src="https://gist.github.com/selcukcihan/f92fe7f70123be3b9e91be2e960512ff.js"></script>

Tıpkı native modüllerden geri kalmayacak biçimde, kafamız yeterince karışmamış gibi, CJS modüllerinde de **farklı export türleri var**.
Bir diğer kullanım ise `module.exports` değerini yeni bir değerle ezmek.
Bu kullanım için aşağıdaki örneğe bakalım:

<script src="https://gist.github.com/selcukcihan/75fbca9c0238aad3eca6e256ab0ae03b.js"></script>

`exports` aslında `module.exports`'a işaret eden bir referans olduğundan, bu iki kullanımın etkisi aynı.

`exports` referansını daha iyi anlayabilmek için, aşağıdaki modülü inceleyelim.
Bu modül sanki export ediyor gibi görünse de, aslında hiçbir şeyi export etmiyor.
```
function sleep(milliseconds) {
  return new Promise(resolve =>
    setTimeout(resolve, milliseconds)
  );
}

exports = sleep;
```

Bu modülü `require` ettiğinizde, elinize bir adet `{}` geliyor.
Sleep metodunu export **edememiş** oluyorsunuz.

Bir de şöyle bir kullanıma rastlayabilirsiniz: `module.exports = exports = falanfilan;`.
Bunun `module.exports = falanfilan;`'a üstünlüğü nedir derseniz, aşağıdaki türden hatalar yapma ihtimalinizi azaltıyor:

```
const DEFAULT_SLEEP_MILLISECONDS = 1000;

function sleep(milliseconds) {
  return new Promise(resolve =>
    setTimeout(resolve, milliseconds || DEFAULT_SLEEP_MILLISECONDS)
  );
}

module.exports = {
  DEFAULT_SLEEP_MILLISECONDS: DEFAULT_SLEEP_MILLISECONDS
};
exports.sleep = sleep; // Geçmiş olsun, bu modülü "require" eden kod sleep'e ulaşamayacak.
```

Bu modülü `require` ettiğinizde, `sleep` fonksiyonunun gelmediğini göreceksiniz.
Çünkü `module.exports`'a yeni bir değer atadık, fakat `exports` hala eski değere işaret ediyor.
İşte bu hatalara düşmemek ve modül dosyasında farklı noktalarda exports'a bir şeyler ekleyebilmek için, `module.exports`'a yeni bir değer atarken, `exports`'u da güncellemek gerekiyor.
Bu da `module.exports = exports = ...` kullanımını doğuruyor.

## Ön-yüz
Native modüllere ön-yüz örneği olarak, `sleep` metodumuzu kullanan basit bir html sayfası yapalım.

<script src="https://gist.github.com/selcukcihan/1530aaa56b9c189eab8487a5402fbf89.js"></script>

Burada dikkat edilecek husus, modülleri sayfaya eklerken `script` *tag*'inin `module` tipinde tanımlanması.
HTML dosyasını tarayıcıda doğrudan açmaya çalışırsanız, [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)'tan ötürü JS modüllerinin yüklenmediğini göreceksiniz.
Bu engeli aşmak için bir http sunucusu ayağa kaldırmanız gerekiyor, mesela `python -m http.server` gibi.

# Çıkarılacak dersler

* JavaScript tam bir keşmekeş.
* Modüller de buna dahil.
* Native modüller import/export ile kullanılıyor.
* Node.js modülleri `require` ve `module.exports` ile kullanılıyor.
* Node.js'te native modül kullanabiliyorsunuz.
* Deneyerek öğrenin, copy-paste ettiğniz kodun çalışmasıyla yetinmeyin, nasıl çalıştığını da anlayın.
* Node.js'te `console.log(module);` komutunun çıktısını inceleyin ve üzerine düşünün :smile:

[1]: /img/javascript-moduller/cube.jpg
