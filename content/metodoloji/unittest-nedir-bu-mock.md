---
title: "Unittest: Nedir Bu Mock?"
tags: ["unit test", "test", "python", "mock", "arayüz", "bağımlılık", "dependency"]
date: 2016-07-12T14:41:29+03:00
draft: false
---

Unit test bağlamında, mock nesneler nelerdir? Ne işe yararlar? Testin gerçekten unit test olması için neler yapmalıyız?

* *Python 2.7 üzerinden*  
* *mock kütüphanesini her zamanki gibi kurduk*
```
pip install mock
```

Mock Kavramı
------------

Mock ile *ok arasındaki ses benzerliğini hızlıca geçiyorum.
Mock, **harici bağımlılıkları** aradan çıkarıp, testin odağını yalnızca test etmek istediğimiz metod/class üzerine toplamak için kullanılan bir yöntem ve bu yöntemi uygularken kullanılan nesnelere verilen isimdir.
Bir örnek vermek gerekirse, `WebConsole` class'ımızın `register_user` metodunda `db.register` metodu çağırılıyorsa, biz `register_user`'i unit test ederken, db'ye gerçekten kayıt yapılmasını istemeyiz.
Bunun için `db` nesnesinin yerine, **davranışını kendimizin belirlediği bir mock nesne** kullanılmasını isteriz.
Bu mock nesne üzerinden istediğimiz davranışı sergiletebilir ve dahası `db`'nin bizim metodumuzda nasıl kullanıldığını (doğru parametrelerle mi çağırılıyor?) doğrulayabiliriz.
Somut bir örnek, daha iyi anlaşılmasını sağlayacaktır.

Nasıl Kullanılır
----------------

Gerçek hayata oldukça yaklaşan bir örnek üzerinden gidelim.
Bir klasördeki, belirli bir uzantıya sahip bütün dosyaları listeleyen bir metodumuz olsa:

```
# -*- coding: utf-8 -*-

import os.path
import glob

def get_files_from(directory, extension):
    """'directory' klasöründe bulunan 'extension' uzantılı bütün dosyaları liste olarak döner."""
    path = os.path.join(directory, "*." + extension) # aranacak olan ifadeyi hazırlar
    return [os.path.basename(p) for p in glob.glob(path)] # sadece dosya ismini almak için => basename
```

Bu metod için, her zaman yaptığımız gibi bir unit test yazsak:

```
import unittest

class GetFilesFromTestCase(unittest.TestCase):

    def test_basic(self):
        """get_files_from metodu için basit unittest."""

        # metodumuzu çağıralım, bulunduğumuz klasördeki py uzantılı dosyaları istiyoruz.
        files = get_files_from('.', 'py')
        # bulunduğumuz klasörde sadece test_mock.py dosyasının olduğunu biz biliyoruz,
        # bakalım metodumuz doğru değerleri dönüyor mu.
        self.assertItemsEqual(files, ['test_mock.py'])
```

Bu kod parçacıklarını, test_mock.py dosyasında toplayın ve oluşturacağınız bu dosyanın bulunduğu klasörde başka hiçbir py uzantılı dosya olmamasına dikkat edin.
Şimdi dosyanın bulunduğu klasöre gidin ve **python -m unittest discover** komutunu çalıştırın:

```
C:\Users\SELCUKCI\python\blog_code\mocking>python -m unittest discover
.
----------------------------------------------------------------------
Ran 1 test in 0.002s

OK
```

Testimiz başarıyla geçti.
Peki biz bu test ile neyi test etmiş olduk?
Biz bu testte, *`get_files_from` metodumuzu ve içerisinde çağırdığımız bütün diğer built-in metodları test etmiş olduk*.
Çünkü metodumuzu doğrudan çağırdık, o da gitti yapması gerektiği gibi `os.path.join`'den tutun da `glob.glob`'a kadar bütün satırları çalıştırıp ilgili metodları çağırdı.
Yani **aslında unit test yapamamış olduk**; hatırlarsanız, unit test olabilmesi için test edilen kısımdaki **bütün harici bağımlılıklar (external dependencies) aradan çıkarılmış olmalıydı**.
`test_basic` bir testtir, hatta gayet makul bir testtir; fakat unit test değildir.

Aslında bizim test etmek istediğimiz nokta, `glob` metodunu *doğru parametreyle çağırıyor muyuz çağırmıyor muyuz* olmalı.
Hatta daha da öteye gidersek, biz unit testlerimizi çalıştırırken, her seferinde `glob` metodunu gerçekten çağırıp ilgili klasördeki dosyaları listeletmek ister miyiz?
Hayır, istemeyiz. Derdimiz dosyaların listelenmesi değil ki, derdimiz dosyaları listelemek için doğru adımları atıp atmadığımız.
Bu kadar laf ebeliği yeter, bir resim bin kelimeye bedel diyerek koda geçelim:


```
    # GetFilesFromTestCase class'ına aşağıdaki metodu ekliyoruz

    import mock
    @mock.patch('test_mock.glob.glob')
    def test_get_files_from_with_mock_fail(self, mock_glob):
        """get_files_from metodu için mock kullanan hatalı unittest."""

        files = get_files_from('', 'py')
        mock_glob.assert_called_with('*.py')

        self.assertItemsEqual(files, ['test_mock.py'])
```

>*[Mock kütüphanesi](https://docs.python.org/dev/library/unittest.mock.html), Python 3.3'ten itibaren `unittest` modülüne dahil edildi. Öncesinde harici bir kütüphane olarak sunuluyordu. Biz Python 2.7 üzerinden gittiğimiz için, import'larda farklılıklar göreceksiniz.*

Yeni test metodumuzu `@mock.patch` ile dekore ettik.
Böylece, modülümüzde (`test_mock`) import ettiğimiz `glob` modülünün içerisindeki `glob` metodunu "*mock'tan*" bir nesneyle yamamış olduk.
Orjinal metod gitti, yerine tamamen kontrolü bizde olan ve biz ne söylersek onu yapacak olan emrimize amade bir metod geldi.
Bu mock metod sayesinde, glob metoduna istediğimiz davranışı dikte edebileceğiz.
Şimdilik herhangi bir davranış bildirmeyip, sadece glob metodunu doğru parametreyle çağırıyor muyuz çağırmıyor muyuz kontrol etmek için bir assert yazdık :`mock_glob.assert_called_with('*.py')`.

Dosyaya ilgili eklemeyi yaptıktan sonra, **python -m unittest discover** komutunu tekrar çalıştırın:

```
C:\Users\SELCUKCI\python\blog_code\mocking>python -m unittest discover
.F
======================================================================
FAIL: test_get_files_from_with_mock_fail (test_mock.GetFilesFromTestCase)
get_files_from metodu i?in mock kullanan hatal² unittest.
----------------------------------------------------------------------
Traceback (most recent call last):
  File "C:\Python27\lib\site-packages\mock\mock.py", line 1305, in patched
    return func(*args, **keywargs)
  File "C:\Users\SELCUKCI\python\blog_code\mocking\test_mock.py", line 32, in test_get_files_from_with_mock_fail
    self.assertItemsEqual(files, ['test_mock.py'])
AssertionError: Element counts were not equal:
First has 0, Second has 1:  'test_mock.py'

----------------------------------------------------------------------
Ran 2 tests in 0.005s

FAILED (failures=1)
```

Testlerimizin biri hata aldı, hata mesajına bakılırsa yeni yazdığımız metodda `AssertionError` almışız.
Yani iddia ettiğimiz eşitlik tutmamış, ['test_mock.py'] listesinde bir eleman varken, `files` listesinde hiç eleman yokmuş.
**Ne değişti de, bir önceki testimizde dorğu sonuç veren `self.assertItemsEqual(files, ['test_mock.py'])` iddiamız yanlış oldu?**
Değişen şey, bir öncekinde davranışını kendi belirlediğimiz `glob` yerine, orjinal `glob` çalışıyor ve bize bulunduğumuz klasördeki 'test_mock.py' dosyasını listeliyordu.
Oysa şimdi, mock'tan metodumuz `glob` bizi yanılttı ve bize beklediğimiz sonucu vermedi.
Burada onun hiç kabahati yok, unutmayın ki o emrimize amade bir emir kulu, ne denirse onu yapar.
**Ona beklediğimiz sonucu vermesini söylemedik ki!!!**
Beklediğimiz sonucu vermesini şöyle söyleyeceğiz: `mock_glob.return_value = ['test_mock.py']`. Hatalı test metodumuzu silip, şu yeni haliyle tekrar ekleyelim:

```
    @mock.patch('test_mock.glob.glob')
    def test_get_files_from_with_mock(self, mock_glob):
        """get_files_from metodu için mock kullanan doğru unittest."""

        mock_glob.return_value = ['test_mock.py'] # bu satırı ekledik
        files = get_files_from('', 'py')
        mock_glob.assert_called_with('*.py')

        self.assertItemsEqual(files, ['test_mock.py'])
```

Düzeltmeden sonra, tekrar testlerimizi çalıştırıyoruz:

```
C:\Users\SELCUKCI\python\blog_code\mocking>python -m unittest discover
..
----------------------------------------------------------------------
Ran 2 tests in 0.003s

OK
```

Kıbrıslı Türk kardeşlerimizin deyimiyle: **YESSS BE ANNEM!**

Kapanışı yapmadan evvel, dikkat edilmesi gereken bir nokta daha var.
Mock nesnemize bize ['test_mock.py'] listesini return etmesini, `get_files_from` metodundan önce söylememiz gerekiyor.
Aksi takdirde, metodu çağırdıktan sonra davranışı değiştirmek işimize yaramayacaktır, olan olmuştur çünkü.
Tıpkı, `assert_called_with` metodunu `get_files_from` metodundan önce çağıramayacağız gibi: Daha metodu çağırmadan, ne şekilde çağırıldığını nasıl iddia edebiliriz ki?

* Davranışa yönelik ayarlamalarımızı (bkz. return_value), asıl metodumuzu çağırmadan önce yapmalıyız.
* İddialarımızı (bkz. assert'ler), asıl metodumuzu çağırdıktan sonra yapmalıyız.

Bütün Kod
---------
```
# -*- coding: utf-8 -*-

import os.path
import glob

def get_files_from(directory, extension):
    """'directory' klasöründe bulunan 'extension' uzantılı bütün dosyaları liste olarak döner."""
    path = os.path.join(directory, "*." + extension) # aranacak olan ifadeyi hazırlar
    return [os.path.basename(p) for p in glob.glob(path)] # sadece dosya ismini almak için => basename

import unittest

class GetFilesFromTestCase(unittest.TestCase):

    def test_basic(self):
        """get_files_from metodu için basit unittest."""

        # metodumuzu çağıralım, bulunduğumuz klasördeki py uzantılı dosyaları istiyoruz.
        files = get_files_from('.', 'py')
        # bulunduğumuz klasörde sadece test_mock.py dosyasının olduğunu biz biliyoruz,
        # bakalım metodumuz doğru değerleri dönüyor mu.
        self.assertItemsEqual(files, ['test_mock.py'])

    import mock
    @mock.patch('test_mock.glob.glob')
    def test_get_files_from_with_mock(self, mock_glob):
        """get_files_from metodu için mock kullanan doğru unittest."""

        files = get_files_from('', 'py')
        mock_glob.assert_called_with('*.py')

        self.assertItemsEqual(files, ['test_mock.py'])

    @mock.patch('test_mock.glob.glob')
    def test_get_files_from_with_mock(self, mock_glob):
        """get_files_from metodu için mock kullanan doğru unittest."""

        mock_glob.return_value = ['test_mock.py'] # bu satırı ekledik
        files = get_files_from('', 'py')
        mock_glob.assert_called_with('*.py')

        self.assertItemsEqual(files, ['test_mock.py'])
```
