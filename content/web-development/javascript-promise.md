---
title: "Javascript: Promise ve asenkron çalışma mantığı"
tags: ["javascript", "promise", "event", "async", "concurrency", "asenkron"]
date: 2019-12-28T14:41:29+03:00
draft: false
---

Javascript'te asenkron işler nasıl yapılır, Promise ne işe yarar ve kodumuzu nasıl daha okunaklı hale getirir? Promise zincirleri nasıl oluşturulur?

## Giriş

Javascript ilk çıktığında, kullanım amacı dinamik web sayfalarıydı.
Fakat günümüzde web servislerden (*backend*) tutun da mobil uygulamalara kadar pek çok yerde javascript ile uygulama geliştirebiliyoruz.
Bu makalede javascript'i herhangi bir **runtime özelinde ele almayacağım**.
Javascript ile backend (mesela nodejs) veya frontend (angular, react, vue, jquery, sade javascript vs.) geliştirmek isteyen yazılımcılar anlattıklarımdan istifade edebilirler.

Javascript "single-threaded" bir dil.
Kodunuz çalışırken aynı anda sadece bir koldan ilerliyor diye düşünebilirsiniz.
Böyle iken, bir web servis çağırdığınızı düşünün, servisten gelen yanıtı boş boş bekleyecek miyiz?
Hayır, javascript'te asenkron çalışma mantığı sayesinde I/O tarzı bloklayan işleri beklerken başka işler yapabiliyoruz.
Mesela, servisten cevap gelene kadar kullanıcıya hareketli bir animasyon gösterebiliyoruz.

Asenkron çalışma mantığı "callback" veya "promise" yapılarıyla yönetiliyor.
Callback yapısında, asenkron işi yapacak olan metod parametre olarak bir fonksiyon alıyor ve iş bittiğinde bu fonksiyonu çalıştırarak işin bittiğini haber vermiş oluyor.
Promise ise biraz daha farklı bir soyutlama.
Promise nesnesi, asenkron bir işin bittiği zamanki halini içeriyor diyebiliriz.
Mesela promise "resolve" edildiğinde, eğer işlem başarılı bir şekilde tamamlandıysa, servisin cevabını içerecektir.
Bir başka deyişle, promise gelecekteki bir değeri temsil ediyor.

```
var promise = new Promise(function(resolve, reject) {
        setTimeout(function() {
            resolve("Beş saniye sonradan geliyorum.");
        }, 5000);
    });
```
Bu kodu çalıştırıp promise değerini ara ara kontrol ettiğinizde, beş saniye sonra değerin geldiğini göreceksiniz.
![Promise gelecek][1]

Bu ve aşağıda paylaştığım diğer kodları çalıştırmak için, tarayıcınızın "developer tools" gibi bir özelliğini veya online javascript interpreter'lerini kullanabilirsiniz.
Ben Chrome'un "Developer Tools" uygulamasını kullanıyorum.

Koda dalmadan önce şunu belirtmeliyim ki, *promise*'lerle yapıp da *callback*'lerle yapamayacağınız bir şey yok, güç olarak eşitler.
Fakat *promise* kullanarak çok daha okunaklı ve kolay anlaşılır kodlar yazabilirsiniz.

## Hedef

Tersten başlayalım.
Önce varmak istediğim yapıyı ortaya koyup, daha sonra yavaş yavaş oraya nasıl ulaşacağımı detaylandıracağım.

```
let promise = log("A servisi", callServiceA)("test")
    .then(log("B servisi", callServiceB))
    .then(log("C servisi", callServiceC))
    .then(log("D servisi", callServiceD))
    .catch(console.log);
```

Zincirleme olarak farklı promise'ler birbirine bağlandığından, yukarıdaki kod yapısına "promise chaining" deniyor.
Gerçekleştirmek istediğim senaryo şu:

* Sırayla A, B, C ve D servislerini çağır.
* Bir servisten gelen cevabı, sıradakine besle.
* Tüm servisler için loglama yap (servis çağırılmadan önce ve servisten cevap geldikten sonra araya girmek istiyorum).

Bir servisten gelen cevabı bir sonraki serviste kullandığım için her adım bir önceki adıma bağımlı.
Kodun tamamı aşağıdaki gibi:
<script src="https://gist.github.com/selcukcihan/34f2050d956943e4dd85ab53b2b25af4.js"></script>

Çalıştırdığınızda aşağıdaki gibi bir çıktı üretecektir:
![Promise chain output][2]

## Dekorasyon
Uygulamanızda harici servisleri çağırdığınız noktaları loglamak ve ölçmek çok önemli.
Böylece, olası bir hatada, hatanın hangi servisten geldiğini kolayca anlayabilirsiniz.
Servislerin cevap dönme süresini (latency) de ölçüp loglarsanız, hangi servisin uygulamanızı yavaşlattığını bulmanız kolaylaşır.
Tüm bu sebeplerden, çağırdığım her servis için standart yaptığım bazı işler var:

1. Servisi çağırmadan önce saati bir kenara not et
2. Servisi çağırdığın parametreleri logla
3. Servisten gelen cevabı logla
4. Servisten cevap geldikten sonra, başlangıçta not ettiğimiz saati de kullanarak cevabın ne kadar sürede geldiğini logla

Promise yapısında bunu gerçekleştirmek için, ```log``` metoduna parametre olarak promise geçtim.
Promise'in öncesine ve sonrasına bazı kod parçacıkları zerk ettim (inject :smile:).
Bu kullanıma "decorator" da deniyor.
Dekore ettiğimiz şey bir fonksiyon.
Fonksiyonu alıp biraz dekorasyondan sonra yeni bir fonksiyon olarak geri veriyoruz.
Bu örnekte dekorasyon malzemesi loglama.

## Promise

Callback kullanmaya aşina olanlar için, aynı işi yapan iki farklı kod parçacığı yazdım.
İlkinde callback metodları kullandım:

```
function uzaklardanDosyaOku(callback) {
    ...
    ...
}

function callback(res, err) {
    if (err) {
        console.error("Hata: " + err);
    } else {
        console.log(res);
    }
}

// asenkron metodu çağırıp, callback metodumuzu parametre olarak veriyoruz
uzaklardanDosyaOku(callback);
```

İkincisinde ise promise kullandım:

```
function uzaklardanDosyaOku() {
    // bir promise döndüğünü varsayıyoruz
    return new Promise()...
}

function successCallback(res) {
    console.log(res);
}

function errorCallback(err) {
    console.error("Hata: " + err);
}

uzaklardanDosyaOku().then(successCallback).catch(errorCallback);
```

### Resolve ve Reject

Callback yapısına baktığınızda, callback fonksiyonu genellikle iki parametre bekler: *success* ve *error*.
Böylece, hem hata durumunu hem de başarılı olma durumunu tek fonksiyonla yönetirsiniz.
Diğer bir yaklaşım da, iki tane callback fonksiyonu kullanmak olabilir; bir fonksiyon başarı durumu için, bir fonksiyon hata durumu için.

Promise'ler de benzer biçimde, başarı durumunu *resolve* ile hata durumunu ise *reject* ile karşılıyor:

```
let myPromise = new Promise(function(resolve, reject)) {
    let result = doStuff();
    if (result.error) {
        reject(result.error);
    } else {
        resolve(result);
    }
};
```

### Promise zinciri

Promise nesnelerinin *then*, *catch* ve *finally* metodlarını kullanarak, farklı promise nesnelerini birbirine bağlayabilirsiniz.
Bu sayede, bir promise'den gelen cevabı diğer bir promise'e aktarmış olursunuz.

"then" metodu, promise'e başarı ve hata durumlarında (hata opsiyonel) çalıştırılmak üzere callback metodu ekler.
"catch" metodu, promise'e *reject* edilme durumunda çalıştırması gereken bir callback ekler.
"finally" metodu ise hem başarılı hem de hatalı durumlar için, promise artık gerçekleştiğinde çalışıtırlmak üzere callback metodu ekler.
Bu üç metod da yeni bir promise döner.
Bu sayede
```
promise.then(res => callServiceA(res)).then(res => callServiceB(res))
```
gibi bir zincir oluşturmak mümkün oluyor.

### Promise kullanmanın avantajları

Callback yapısında birden fazla asenkron işi sırayla yapmaya kalktığınızda, kodunuz şu şekli almaya başlar:

```
asyncFunc1(function(res, err) {
    if (err) {
        handleError(err);
    } else {
        asyncFunc2(function(res, err) {
            if (err) {
                handleError(err);
            } else {
                asyncFunc3(function(res, err) {
                    if (err) {
                        handleError(err);
                    } else {
                        // res ile birşeyler yap...
                        // nihayet bitti!
                    }
                })
            }
        })
    }
});
```

Kod sağa doğru kaymaya başladı, takip edip ne yaptığını anlamak çok zor.
Bir de şuna bakın, şiir gibi değil mi?

```
asyncFunc1()
    .then(res => asyncFunc2(res))
    .then(res => asyncFunc3(res))
    .catch(handleError);
```

Callback'lerle kıyasladığımızda, promise'lerin diğer farkları şunlar:

* Javascript'in "event loop" adı verilen bir işleyiş mekanizması var.
Promise kullandığınız zaman, callback metodlarınız o anki *event loop iterasyon'u* bitmeden asla çalışmayacaktır.
Kodla anlatmak daha kolay:
```
new Promise(function(resolve, reject) {
        resolve("Promise return değeri");
})
.then(res => console.log("Ben biraz sonra çalışırım: " + res));
console.log("Event loop sebebiyle önce ben çalışacağım!");
```
![Promise loop][3]
* "then" ile eklediğiniz callback'ler, asenkron metod çoktan işini (başarılı veya başarısız) bitirmiş olsa bile gerektiği gibi çalıştırılacaktır.
Bir başka deyişle, "resolve" veya "reject" olmuş bir promise üzerinde "then" metodunu çağırırsanız, promise'in o anki değeri kullanılarak verdiğiniz callback metodu çalıştırılacaktır.

## Özet

Eğer Javascript kullanıyorsanız, zaten "Promise" yapısıyla çoktan karşılaşmışsınızdır.
Her zaman olduğu gibi, öğrenmenin en iyi yolu denemekten geçer.
Takıldığınız yerler olursa, başvurmanız gereken en güzel kaynak [mozilla](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises).
Bu kaynağın maalesef henüz Türkçe'si yok.
Promise kullanımına gerçek bir örnek görmek isterseniz, [twheat-map](http://twheat-map.selcukcihan.com/) uygulamasının *backend* [kodlarını](https://github.com/selcukcihan/twheat-map-backend/blob/2752c07cf6538d20510bf70d78a793bf3c375db0/src/business/heatmap.js#L64) inceleyebilirsiniz.

[1]: /img/javascript-promise/promise_gelecek.png
[2]: /img/javascript-promise/promise_chain_output.png
[3]: /img/javascript-promise/promise_loop.png
