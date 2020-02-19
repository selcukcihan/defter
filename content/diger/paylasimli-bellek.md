---
title: "Paylaşımlı bellek"
tags: ["concurrency", "parallelism", "iletişim", "numpy", "nlp", "machine learning", "gunicorn", "flask",
       "senkronizasyon", "synchronization", "process", "memory", "bellek", "python", "paylaşımlı",
       "big data", "büyük veri", "makine öğrenmesi"]
date: 2020-02-19T04:41:29+03:00
draft: false
images: ["/img/paylasimli-bellek/petronas.jpg"]
---

Big data, machine learning ve yapay zeka son dönemin popüler konuları.
Bir taraftan çeşitli ar-ge çalışmaları ve prototip uygulamalar yapılıyor.
Diğer taraftan Google, Facebook ve Amazon gibi devler faydalarını devşirmeye çoktan başladı.
Fakat henüz emekleme aşamasındayız; aşılmamış pek çok engel var ve çözülmemiş - hatta henüz keşfedilmemiş - pek çok problem var. Bir teknolojinin veya konseptin araştırma safhası ile, uygulama ve gerçek ortamda kullanım aşamaları arasında büyük bir boşluk bulunur.
Mühendislik, bu boşluğu doldurma sanatıdır.
Bir başka deyişle, ar-ge aşamasındaki bir konsepti alıp, teoriden pratiğe dökmektir.

![Petronas][1]
<a style="background-color:black;color:white;text-decoration:none;padding:4px 6px;font-family:-apple-system, BlinkMacSystemFont, &quot;San Francisco&quot;, &quot;Helvetica Neue&quot;, Helvetica, Ubuntu, Roboto, Noto, &quot;Segoe UI&quot;, Arial, sans-serif;font-size:12px;font-weight:bold;line-height:1.2;display:inline-block;border-radius:3px" href="https://unsplash.com/@alexazabache?utm_medium=referral&amp;utm_campaign=photographer-credit&amp;utm_content=creditBadge" target="_blank" rel="noopener noreferrer" title="Alex Azabache'ye ait fotoğraflar için tıklayın."><span style="display:inline-block;padding:2px 3px"><svg xmlns="http://www.w3.org/2000/svg" style="height:12px;width:auto;position:relative;vertical-align:middle;top:-2px;fill:white" viewBox="0 0 32 32"><title>unsplash-logo</title><path d="M10 9V0h12v9H10zm12 5h10v18H0V14h10v9h12v-9z"></path></svg></span><span style="display:inline-block;padding:2px 3px">Fotoğraf Unsplash'ta Alex Azabache'ye aittir.</span></a>

# Girizgâh
Freelancer olarak ne zaman ne tür bir işle karşılaşacağınız belli olmuyor.
Kader ağlarını ördü ve [iş icabı](/hakkinda), büyük ölçekli veri kümesi üzerinde **gerçek zamanlı** (real-time) makine öğrenmesi yapan bir web servis hazırlamakla görevlendirildim.
Bu web servisin en büyük özelliği, gelen tüm istekler için oldukça büyük bir veri üzerinde işlem yapması.
Ben de kendime sordum,

>Çok büyük bellek kullanımı olan bir uygulamayı, aynı makine üzerinde, aynı anda birden fazla kullanıcıya bir web servis olarak nasıl sunabilirim?

## Büyük veri - Big data
Şuna bir açıklık getirelim.
Big data sihirli bir kelime veya yepyeni bir konsept gibi kullanılıyor.
Big data, kelimenin tam anlamıyla "büyük veri" demektir.
Ve veriler işlenirken belleğe yüklenmek zorundadır, çünkü işlemci bellek üzerinden veriye erişebilir.
Önünde sonunda o veri parça parça da olsa işlemciden geçecek.
Kısaca big data, bildiğimiz *data*'nın büyüğü.

## Problem

[Bir önceki yazıda](/diger/es-zamanli-calisan-kodlar) bu problemden biraz bahsedip, çözümünde kullanılabilecek bazı yapılar ve biraz da terminolojiden dem vurmuştum.
Sıra problemi çözmeye geldi.
Öncelikle problemi detaylı bir şekilde tanımlayalım.

Bir web servis yapmam gerekiyor.
Bu web servis parametre olarak bir yazı (herhangi bir text olabilir) alıp, [NLP](https://en.wikipedia.org/wiki/Natural_language_processing) (natural language processing) ile işledikten sonra, repertuvarındaki benzer yazıları bulacak.
Örnek vermek gerekirse, blog'umdaki bu yazıyı web servise verdiğim zaman, bana benzer konulardan bahseden başka yazıları getirecek.

## Kısıtlar
* Web servis **senkron** olmak zorunda, yani istemci bir istek gönderdiğinde cevabını o an geri almalı.
Bunun anlamı, arkada NLP ile ne yapacaksam o an yapacağım, bir kuyruğa atıp istemciye bir id verip "al bu id'yi, arada bir gel sor bakalım bitmiş mi" şeklinde asenkron kurgulayamam.
* Arama yaptığım alan çok büyük ve çok boyutlu, dolayısıyla elimdeki datayı herhangi bir veritabanına koyup sorguları oradan alamıyorum. Gelen her istek için elimdeki datanın her yerini aramam gerekiyor.
* Data bellekte durmak zorunda ve boyutu makinenin tüm belleği kadar.

## Kabul kriteri
* NLP uygulaması, doğası gereği, hem bellek hem de işlemciyi yoğun bir şekilde kullanıyor.
Bu yüzden makinedeki tüm çekirdekleri aktif kullanmam gerekiyor ve aynı anda birden fazla isteği eş zamanlı olarak işleyebilmeliyim.
* Bellek kullanımı, aynı anda ne kadar istek gelirse gelsin sabit olmalı.
* Senkron bir API olacağından, gelen isteklere makul bir sürede cevap verebilmeli (ağda geçen zaman hariç, mesela 1 saniye).

# Çözüm
NLP tarafı Python'da yazıldı, web servisi de Python üzerinden implement etmemek için bir sebebim yok.
Testlerimi dört çekirdekli 16GB RAM olan bir makinede yapacağım.

**Sual**: Web servisi, aynı anda dört isteği işleyebilmesi için, dört ayrı process'te çalıştırıp bellek kullanımını nasıl sabit tutarım?

**Cevap**: [Paylaşımlı bellek](https://en.wikipedia.org/wiki/Shared_memory) (shared memory), farklı process'lerin aynı bellek alanını kullanmasına yarayan ve işletim sisteminin sunduğu bir yapı.
Hem Unix hem de Windows'ta var.
Veriyi paylaşımlı belleğe atacağım.

## Flask ve Gunicorn
Web servisi [flask](https://palletsprojects.com/p/flask/) framework'ü ile yapıyorum, çünkü kullanımı oldukça basit.
Flask kendi web sunucusu ile geliyor fakat *production* için kullanılması tavsiye edilmiyor.
Bu yüzden bir WSGI HTTP sunucusu ile beraber kullanacağım, bunun için de [gunicorn'u](https://gunicorn.org/) seçtim.
Bunların detayını bilmenize gerek yok, kısaca:

* Flask ile endpoint'lerimi tanımlıyorum, yani kodu flask ile yazıyorum diyelim.
* Gunicorn ile kodu çalıştırıyorum, yani [top](http://man7.org/linux/man-pages/man1/top.1.html) veya [ps](http://man7.org/linux/man-pages/man1/ps.1.html) ile baktığınızda process olarak "python" veya "flask" değil de "gunicorn" görülüyor.

Makinede dört çekirdek olduğundan, gunicorn'u dört "worker" process ile başlatıyorum.
Gunicorn ilk başladığında bir "master" process içinden çalışıyor diye düşünürsek, benim ayarlarımla toplam beş process oluşacak fakat bunlardan sadece dört tanesi gelen istekleri işlemekte kullanılacak.
Worker sayısı için ```(çekirdek sayısı * 2 + 1)``` tavsiye ediliyor, fakat benim uygulamam "CPU-bound" yani işlemciyi yoğun kullanan bir uygulama olduğundan çekirdek sayısı kadar worker kullanmayı tercih ettim.
Aksi takdirde, çekirdek sayısı sınırlayıcı faktör olduğundan, worker'lar verimli çalışamayacaktı.
Ve gunicorn'daki benim için en önemli ayar olan [preload_app](https://docs.gunicorn.org/en/stable/settings.html#preload-app) ayarını "True" yapıyorum, bu sayede gunicorn "worker" process'leri başlatmadan önce uygulama kodunu yüklüyor.
Bu sayede, worker process'ler başlamadan önce, paylaşımlı belleği hazırlamış oluyorum.

## *shared_memory* modülü
Flask metodlarının dışında, uygulama ayağa kalkarken, yani gunicorn "master" process'i uygulamayı yüklediğinde, Python'ın standart [shared_memory](https://docs.python.org/3/library/multiprocessing.shared_memory.html) modülünü kullanarak tüm datayı paylaşımlı belleğe atıyorum.
Bu modül Python 3.8'le beraber geldi, öncesinde yok.
Fakat paylaşımlı bellek Python'a ait bir yapı değil, işletim sisteminin sağladığı bir güzellik.
[CPython modül koduna](https://github.com/python/cpython/blob/3.8/Lib/multiprocessing/shared_memory.py) bakarsanız, arkada [mmap](https://docs.python.org/3.6/library/mmap.html) kullanıyor ve tüm güncel Python sürümlerinde mevcut.
En altta ise, olay işletim sisteminin [mmap](http://man7.org/linux/man-pages/man2/mmap.2.html) metodunda bitiyor.

Peki worker process'lerden paylaşımlı belleğe nasıl erişiyorum?
Paylaşımlı bellek oluşturulurken belleğe bir isim veriyorum (vermezseniz varsayılan olarak rastgele benzersiz bir isim atanıyor).
Daha sonra belleğe erişeceğim zaman bu isimle "SharedMemory" objesini oluşturup kullanıyorum.

Denemek için iki tane terminal açıp Python 3.8'i çalıştırın (numpy da kurulu olsun).
Bu iki terminal, iki farklı process'i yani iki farklı worker'ı temsil ediyor.
İlk terminale şunları girin
```
import tracemalloc
tracemalloc.start()

from multiprocessing import shared_memory
import numpy as np
# orjinal datayı oluşturalım
data = np.ones(50 * 1024 * 1024, dtype=np.int8)

# paylaşımlı hafızayı oluşturalım
shared_mem = shared_memory.SharedMemory(name="paylasimli_bellek", create=True, size=data.nbytes)

# orjinal datayı paylaşımlı belleğe kopyalamak için, paylaşımlı bellekle desteklenmiş bir başka array kullanıyoruz.
shallow_copy = np.ndarray(data.shape, dtype=data.dtype, buffer=shared_mem.buf)

# burada orjinal datayı paylaşımlı belleğe yazmış oluyoruz.
shallow_copy[:] = data[:]

snapshot = tracemalloc.take_snapshot()
top_stats = snapshot.statistics('lineno')
print("[ Top 10 ]")
for stat in top_stats[:10]:
    print(stat)
```

* Bellek kullanımını takip etmek için [tracemalloc](https://docs.python.org/3/library/tracemalloc.html) kullandık.
* 50MB'lık bir numpy array'i oluşturduk ve bunu paylaşımlı belleğe attık.

Çıktı olarak şuna benzer bir şey görmelisiniz:
```
[ Top 10 ]
>>> for stat in top_stats[:10]:
...     print(stat)
... 
/Users/selcukcihan/miniconda3/envs/py38/lib/python3.8/site-packages/numpy/core/numeric.py:207: size=50.0 MiB, count=3, average=16.7 MiB
<frozen importlib._bootstrap_external>:580: size=4660 KiB, count=31387, average=152 B
<frozen importlib._bootstrap>:219: size=928 KiB, count=6481, average=147 B
/Users/selcukcihan/miniconda3/envs/py38/lib/python3.8/site-packages/numpy/core/overrides.py:180: size=348 KiB, count=3276, average=109 B
<__array_function__ internals>:3: size=185 KiB, count=948, average=200 B
/Users/selcukcihan/miniconda3/envs/py38/lib/python3.8/site-packages/numpy/core/function_base.py:511: size=110 KiB, count=187, average=602 B
/Users/selcukcihan/miniconda3/envs/py38/lib/python3.8/abc.py:85: size=109 KiB, count=476, average=234 B
/Users/selcukcihan/miniconda3/envs/py38/lib/python3.8/site-packages/numpy/ma/core.py:902: size=101 KiB, count=52, average=1993 B
/Users/selcukcihan/miniconda3/envs/py38/lib/python3.8/site-packages/numpy/core/function_base.py:508: size=74.4 KiB, count=62, average=1229 B
/Users/selcukcihan/miniconda3/envs/py38/lib/python3.8/functools.py:50: size=69.9 KiB, count=387, average=185 B
```

En üstte, ```size=50.0 MiB``` dediği, bizim numpy array'i.

İkinci terminali başlatın ve bu kez şunları girin
```
from multiprocessing import shared_memory
shared_mem = shared_memory.SharedMemory(name="hatali_isim")
```

Ne olacak dersiniz?
Tabi ki bu isimde bir paylaşımlı bellek bulamayacak ve hata verecek:
```
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/Users/selcukcihan/miniconda3/envs/py38/lib/python3.8/multiprocessing/shared_memory.py", line 100, in __init__
    self._fd = _posixshmem.shm_open(
FileNotFoundError: [Errno 2] No such file or directory: '/hatali_isim'
```

Şimdi kodu düzeltip, numpy array'inin içindeki değerleri diğer terminalden okuyalım:
```
import tracemalloc
tracemalloc.start()

from multiprocessing import shared_memory
import numpy as np
shared_mem = shared_memory.SharedMemory(name="paylasimli_bellek")
another_copy = np.ndarray((50 * 1024 * 1024, ), dtype=np.int8, buffer=shared_mem.buf)

snapshot = tracemalloc.take_snapshot()
top_stats = snapshot.statistics('lineno')
print("[ Top 10 ]")
for stat in top_stats[:10]:
    print(stat)
```

Çıktıda 50 MB'lık herhangi bir kullanım görmüyoruz!
Çünkü veri ortak bellek alanında duruyor ve tekrar oluşturulmadan kullanılabiliyor.

```
>>> for stat in top_stats[:10]:
...     print(stat)
... 
<frozen importlib._bootstrap_external>:580: size=4660 KiB, count=31388, average=152 B
<frozen importlib._bootstrap>:219: size=928 KiB, count=6482, average=147 B
/Users/selcukcihan/miniconda3/envs/py38/lib/python3.8/site-packages/numpy/core/overrides.py:180: size=348 KiB, count=3276, average=109 B
<__array_function__ internals>:3: size=185 KiB, count=948, average=200 B
/Users/selcukcihan/miniconda3/envs/py38/lib/python3.8/site-packages/numpy/core/function_base.py:511: size=110 KiB, count=187, average=602 B
/Users/selcukcihan/miniconda3/envs/py38/lib/python3.8/abc.py:85: size=109 KiB, count=476, average=234 B
/Users/selcukcihan/miniconda3/envs/py38/lib/python3.8/site-packages/numpy/ma/core.py:902: size=101 KiB, count=52, average=1993 B
/Users/selcukcihan/miniconda3/envs/py38/lib/python3.8/site-packages/numpy/core/function_base.py:508: size=74.4 KiB, count=62, average=1229 B
/Users/selcukcihan/miniconda3/envs/py38/lib/python3.8/functools.py:50: size=69.9 KiB, count=387, average=185 B
<frozen importlib._bootstrap_external>:64: size=42.4 KiB, count=304, average=143 B
```

Bu sayede, tüm process'ler paylaşımlı belleğe erişebiliyor. Kendi kopyalarını oluşturmadıklarından **bellekten tasarruf etmiş oluyoruz**.

Bu işin sadece bir ayağı.
Bir de bu verinin güncellenmesi var.
Bir sonraki yazıda, "ortak belleğe aynı anda erişen birden fazla worker process varken, datayı nasıl güncelleyebiliriz" sorusuna cevap vermeye çalışacağım.
Tahmin edeceğiniz gibi, **mutex** tarzı bir senkronizasyon yapısı kullanmam gerekecek.
Ne dersiniz, zevkli konular değil mi? :smile:

[1]: /img/paylasimli-bellek/petronas.jpg
