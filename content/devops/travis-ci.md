---
title: "Travis-CI ile deployment"
tags: ["devops", "travis", "deployment"]
date: 2019-12-02T10:48:36+03:00
---

Yazılım sadece kod yazmaktan ibaret değil.
Kendi bilgisayarımızda çalışan bir uygulamanın ötesine geçebilmek için, uygulamamızı kullanıcıların erişimine açmamız gerekir.
Bu yazıda CI/CD nedir basit bir örnek üzerinden anlatıyorum.
[Daha önceki yazılarımda](https://blog.selcukcihan.com/web-development/twheat-map/) bahsi geçen [twheat-map](https://twheat-map.selcukcihan.com/) uygulamam için [sunucu tarafına](https://github.com/selcukcihan/twheat-map-backend) CI/CD süreci eklemek istiyorum.

## CI/CD

Son yıllarda CI/CD lafını çok duymaya başlamışsınızdır. CI, *continuous integration*'ın kısaltması olup ideal bir uygulama geliştirme sürecinde ilk düşünülmesi gereken safhadır. CI, *test* ve *build* (kodun derlenmesi diyelim) otomasyonu yardımıyla yapılan geliştirmelerin sürekli olarak doğrulanmasını ve farklı ortamlara taşınabilir (*deployment*) hale getirilmesini hedefler. CD ise bazen *continuous delivery* bazen de *continuous deployment* anlamlarına gelebilmektedir. İdeal uygulama geliştirme sürecinde ikinci basamak olan CD, yapılan değişikliklerin kolayca müşterilerin kullanımına sunulabilmesini sağlar. CD sürecinde eğer kastedilen *continuous deployment* ise, *pipeline*'ınız (üretim bandı diyebilirsiniz) varacağı son noktaya varmış demektir, tüm geliştirmeler sürekli olarak testlerden geçip nihayetinde üretim ortamına (*production*) taşınıyordur.

## Basit bir CI/CD pipeline

Bir CI/CD pipeline yapabilmemiz için bazı ihtiyaçlarımız var:

* kodumuzu build edebilmeliyiz
* testlerimizi çalıştırabilmeliyiz
* build edilen kodu deploy edebilmeliyiz

### Travis-CI

Bu ihtiyaçların hepsini kendi makinamızı kullanarak halledebiliriz, fakat istiyorum ki bu işi kitabına uygun olarak yapalım.
Bu yüzden [travis-ci](https://travis-ci.org/getting_started) adlı SAAS ürününü kullanmayı düşünüyorum.
Open-source projeler için ücretsiz ve sınırsız sayıda build ile üç tane eş-zamanlı iş çalıştırmayı destekliyor (mesela üç farklı proje için build aynı anda başlayabilir, dördüncü geldiğinde kuyruğa girip bu üçünden birinin bitmesini bekleyecek).
Github hesabınızla üye olabilirsiniz.

### Test

CI/CD sürecini gerçekleştirmek için elbette çok sıkı testlere ihtiyacımız var.
Düşünsenize, *continuous deployment* yapıyorsunuz ve commit'lediğiniz her değişiklik otomatik olarak prod'a taşınıyor.
Bu durumda testlerinizin çok çok iyi olması gerekir.
Mevcut haliyle [sunucu tarafında](https://github.com/selcukcihan/twheat-map-backend) hiç test yok.
En azından basitçe, uygulamamız için kritik olan [maps.js](https://github.com/selcukcihan/twheat-map-backend/blob/master/src/business/maps.js) dosyasının testlerini yazabiliriz.
Javascript test *framework*'lerinden en popüler ikisi [mocha](https://mochajs.org/) ve [jest](https://jestjs.io/).
Npm'deki indirilme sayılarına ve github'daki yıldızlara bakılırsa jest biraz daha popüler gibi duruyor.
Ben de jest kullanmaya karar verdim.

#### Jest

Kurmak için sunucu kodunun en üst klasöründe `yarn add --dev jest` komutunu çalıştırdım.
Testlerinizi istediğiniz klasöre koyabilirsiniz ama bazı varsayılan tercihler var jest'te.
Mesela testlerin adını `(dosya adı).test.js` şeklinde bekliyor.
Eminim bu varsayılan tercihler değiştiriliyordur, fakat uğraşmadım ve ben de testleri bu şekilde adlandırdım.
Tüm testlerimi `src` ile aynı seviyede oluşturduğum `test` klasörüne koyacağım.
İlk test dosyamı şöyle oluşturdum: `touch test/business/maps.test.js`
Test etmek istediğimiz metod `getCoordinates`.
Bunu test ederken birkaç basit senaryo oluşturdum:
1. "İstanbul, Türkiye" için İstanbul'un koordinatlarına yakın olacak şekilde [41.0082, 28.9784] aralığında bir değer dönsün.
2. "Seattle, WA, USA" için Seattle kentinin koordinatları olan [47.6062, -122.3321].
3. "Bangalore" için Hindistan Bangalore koordinatları olan [12.9716, 77.5946].
4. "Sydney, Australia" için Sidney'in koordinatları [-33.8688, 151.2093°].
Tabi bu değerlerin aynısını beklemek doğru olmaz, +-1 kadar bir esneme payı tanıyacağım.

```
maps = require('business/maps');

const locations = {
    Istanbul: {
        TestValue: "İstanbul, Türkiye",
        ExpectedCoordinates: [41.0082, 28.9784]
    },
    Seattle: {
        TestValue: "Seattle, WA, USA",
        ExpectedCoordinates: [47.6062, -122.3321]
    },
    Bangalore: {
        TestValue: " India, Bangalore ",
        ExpectedCoordinates: [12.9716, 77.5946]
    },
    Sydney: {
        TestValue: "Sydney Australia",
        ExpectedCoordinates: [-33.8688, 151.2093]
    },
};

function isClose(actual, expected) {
    return (Math.abs(actual[0].lat - expected[0]) < 1
        && Math.abs(actual[0].lng - expected[1]) < 1);
}

describe('maps', () => {
    for (let l in locations) {
        test(l, () => {
            expect(isClose(
                maps.getCoordinates([locations[l].TestValue]),
                locations[l].ExpectedCoordinates))
            .toBeTruthy();
        });
    }
});
```

Testi çalıştırmak için `yarn test` komutu yeterli:

```
➜  twheat-map-backend git:(master) yarn test
yarn run v1.12.3
$ jest --config=jest.config.js
 PASS  test/business/maps.test.js
  maps
    ✓ Istanbul (4ms)
    ✓ Seattle
    ✓ Bangalore (1ms)
    ✓ Sydney (15ms)

Test Suites: 1 passed, 1 total
Tests:       4 passed, 4 total
Snapshots:   0 total
Time:        1.013s
Ran all test suites.
✨  Done in 3.17s.
➜  twheat-map-backend git:(master) 
```

Jest konfigürasyon dosyam `jest.config.js` şu içeriğe sahip:
```
module.exports = {
    // An array of directory names to be searched recursively up from the requiring module's location
    moduleDirectories: ["node_modules", "src"],
    // The test environment that will be used for testing
    testEnvironment: "node"
};
```

`yarn test` komutunun çalışabilmesi için `package.json` dosyasına şunu eklemeliyiz:
```
    "scripts": {
        "test": "jest --config=jest.config.js"
    },
```

`maps.test.js` dosyasının en başında `maps` modülünü *require* ederken adresini `business/maps` olarak verdik.
Bunu yapabilmemizi sağlayan şey, `jest.config.js` içerisine eklediğimiz `moduleDirectories: ["node_modules", "src"]` ayarı.
Bu sayede jest'e, test dosyalarımızdan *require* ettiğimiz modülleri önce `node_modules` akabinde de `src` klasörlerinde aramasını söyledik.

#### Linter

Javascript projelerimizde ve elbette diğer tüm teknolojilerde, kod kalitemizi artıracak bir diğer husus da bir *linter* kullanmak.
Linter dediğimiz şey statik kod analizi yapan, olası yazım hatalarını ve kod stilini denetleyen bir araç.
Javascript'te en popüler *linter*'lardan biri [eslint](https://eslint.org/).
*Eslint*'i projemize eklemek için `yarn add eslint --dev` komutunu çalıştırıyoruz.
Daha sonra `npx eslint --init` komutuyla *linter*'in ayarlarını yapıyoruz, ben şöyle ayarladım:

```
➜  twheat-map-backend git:(master) ✗ npx eslint --init   
? How would you like to use ESLint? To check syntax and find problems
? What type of modules does your project use? CommonJS (require/exports)
? Which framework does your project use? None of these
? Does your project use TypeScript? No
? Where does your code run? Node
? What format do you want your config file to be in? JavaScript
Successfully created .eslintrc.js file in /Users/selcukcihan/twheat-map-backend
```

Kod stilini isterseniz ayarlayabilirsiniz, uğraşmamak için bu projede kod stilini kontrol etmemeyi tercih ettim.
Sonuçta oluştan `.eslintrc.js` config dosyam şu:

```
module.exports = {
    "env": {
        "commonjs": true,
        "es6": true,
        "node": true
    },
    "extends": "eslint:recommended",
    "globals": {
        "Atomics": "readonly",
        "SharedArrayBuffer": "readonly"
    },
    "parserOptions": {
        "ecmaVersion": 2018
    },
    "rules": {
    }
};
```

*Linter*'i çalıştırıp kodu kontrol ettirmek için `npx eslint src` komutunu kullanıyorum.
Bu işlemi `package.json`'a eklemek için ise:

```
    "scripts": {
        "test": "eslint src && jest --config=jest.config.js",
    },
```

### Pipeline

Pipeline için *boru hattı* şeklinde bir çeviri var ama CI/CD bağlamında pek de anlamı karşılamıyor.
Bunun yerine üretim bandı metaforunu kullanarak, *CI/CD pipeline* kelimesini yazılım üretim bandı olarak çevirdim.
Kişisel projem için yapmak istediğim üretim bandım basitçe şu işlemleri yapacak:

1. Github repo'mdaki master branch'ime commit geldiği zaman tetiklenecek.
2. Kodu *eslinter*'dan geçirecek: `yarn build
3. Testleri çalıştıracak: `yarn test`
4. Kodu AWS'ye *deploy* edilmek üzere paketleyecek: `yarn install`
5. Kodu *deploy* edecek: `sls deploy`

Tüm bunları travis-ci kullanarak otomatize etmek istiyorum.
Bunun için öncelikle travis-ci üzerinden repo'yu aktive etmem gerekiyor:
![Travis activate repo][1]

Daha sonra AWS IAM servisinden travis'in deployment sırasında kullanabilmesi için bir kullanıcı oluşturdum:
![AWS travis user][2]

Kullanıcıya lambda, api gateway ve ssm gibi bazı yetkiler verdim.
Bu yetkiler deployment sırasında gerekecek.
![AWS travis user permissions][3]

Tüm bu ayarları yaptıktan sonra, bir de travis-ci otomasyonu için `.travis.yml` adında bir config dosyasına ihtiyaç var.
NodeJS projeleri için [kendi dokümanlarını](https://docs.travis-ci.com/user/languages/javascript-with-nodejs/) takip ederek basit bir config dosyası oluşturmak mümkün:

```
language: node_js
node_js:
  - 12
cache:
  yarn: true
  directories:
    - node_modules
script:
  - yarn test
  - npm install -g serverless
  - yarn deploy
```

Dikkat ettiyseniz, son olarak `yarn deploy` komutu çalıştırılıyor.
Bu komutu henüz `package.json` dosyamıza eklemedik, şimdi bunu yapalım:

```
    "scripts": {
        "test": "eslint src && jest --config=jest.config.js",
        "deploy": "sls deploy"
    },
```

Asıl *deployment* işlemini yapan komut `sls deploy`.
Komutla ilgili detaylara [serverless framework](https://serverless.com/framework/docs/providers/aws/cli-reference/deploy/) dokümanından ulaşabilirsiniz.
Bu komutun *travis-ci* tarafından oluşturduğumuz AWS kullanıcısı adına çalıştırılması lazım.
Travis'in AWS'den aldığımız *access key id* ve *secret access key* anahtarlarına ihtiyacı var.
Bu anahtarların değerlerini doğrudan github'daki koda eklemek yanlış, çünkü anahtarlara erişen kimseler AWS hesabımıza erişmiş olurlar.
Bu yüzden *travis-ci*'nin sunduğu [şifrelenmiş ortam değişkenlerini](https://docs.travis-ci.com/user/environment-variables/) kullanacağız.
[Travis komut satırı uygulamasını](https://github.com/travis-ci/travis.rb) kurun.
Daha sonra şu iki komutu, AWS anahtarlarınızı içine yerleştirerek çalıştırın:

```
 travis encrypt "AWS_ACCESS_KEY_ID=BURAYA_ACCESS_KEY_ID_GELECEK" --add env.global
 travis encrypt "AWS_SECRET_ACCESS_KEY=BURAYA_SECRET_ACCESS_KEY_GELECEK" --add env.global
```

Şimdi `.travis.yml` dosyasına bakarsanız iki adet *secret* eklendiğini göreceksiniz.
Travis sizin projeyi deploy ederken, bu iki *secret*'i çözümleyip AWS anahtarlarınızı kullanabilecek.

## Deneme

Ayarlarımızı yaptığımıza göre, ilk denemeyi yapalım.
`git push` dedim ve *travis* tanımladığım işleri çalıştırmaya başladı:

```
$ sls deploy
Serverless: Packaging service...
Serverless: Excluding development dependencies...
 
  Serverless Error ---------------------------------------
 
  User: arn:aws:iam::XXXXXXXXXX:user/travis-twheat-map-backend is not authorized to perform: cloudformation:DescribeStackResource on resource: arn:aws:cloudformation:us-east-1:XXXXXXXXXX:stack/twheat-map-backend-dev/XXXXXXXXXX
 
  Get Support --------------------------------------------
     Docs:          docs.serverless.com
     Bugs:          github.com/serverless/serverless/issues
     Issues:        forum.serverless.com
 
  Your Environment Information ---------------------------
     Operating System:          linux
     Node Version:              12.13.1
     Framework Version:         1.58.0
     Plugin Version:            3.2.5
     SDK Version:               2.2.1
     Components Core Version:   1.1.2
     Components CLI Version:    1.4.0
 
error Command failed with exit code 1.
info Visit https://yarnpkg.com/en/docs/cli/run for documentation about this command.
The command "yarn deploy" exited with 1.
cache.2
store build cache
```

Deployment hata aldı, hata detayına bakarsak IAM yetkileriyle alakalı.
IAM yetkilerini tanımlarken, [CloudFormation](https://aws.amazon.com/cloudformation/) için yetki tanımlamayı unutmuştum.
Bunun için *AWSCloudFormationFullAccess* iznini ekledim:
![AWS CF permissions][4]

Travis üzerinden "Restart build" diyerek deployment'ı tekrar denenmek üzere tetikledim ve deployment başarılı oldu:

```
$ sls deploy
Serverless: Packaging service...
Serverless: Excluding development dependencies...
Serverless: Uploading CloudFormation file to S3...
Serverless: Uploading artifacts...
Serverless: Uploading service twheat-map-backend.zip file to S3 (11.15 MB)...
Serverless: Validating template...
Serverless: Updating Stack...
Serverless: Checking Stack update progress...
.......................
Serverless: Stack update finished...
Service Information
service: twheat-map-backend
stage: dev
region: us-east-1
stack: twheat-map-backend-dev
resources: 17
api keys:
  None
endpoints:
  GET - https://r6ssdb9382.execute-api.us-east-1.amazonaws.com/dev/heatmap
functions:
  authenticate: twheat-map-backend-dev-authenticate
  heatmap: twheat-map-backend-dev-heatmap
layers:
  None
Serverless: Removing old service artifacts from S3...
Serverless: Run the "serverless" command to setup monitoring, troubleshooting and testing.
Done in 35.59s.
The command "yarn deploy" exited with 0.
cache.2
store build cache
Done. Your build exited with 0.
```

Son olarak, projenin `readme.md` dosyasına build durumunu gösteren bir imaj ekledim:
```
[![Build Status](https://travis-ci.org/selcukcihan/twheat-map-backend.svg?branch=master)](https://travis-ci.org/selcukcihan/twheat-map-backend)
```

![Build badge][5]

[1]: /img/travis-ci/travis_activate_repo.png
[2]: /img/travis-ci/aws_travis_user.png
[3]: /img/travis-ci/aws_travis_user_permissions.png
[4]: /img/travis-ci/aws_cf_permissions.png
[5]: /img/travis-ci/build_badge.png
