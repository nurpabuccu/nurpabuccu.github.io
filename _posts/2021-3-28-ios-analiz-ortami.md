---
layout: article
title: iOS Uygulama Analiz Ortamı
key: page-single
---

Bu yazıda iOS uygulama pentesti veya iOS zararlı analizi yaparken kullanılabilecek bazı araçlardan bahsedeceğim. 

Bir iOS uygulamasını test etmek için fiziksel bir cihaza ve MacOS'a gereksiniminiz var. Malesef emülatörle root yetkileri alınıp (jailbreak) istenilen işlemler gerçekleştirilemiyor.

## iOS Jailbreak

Android sistemlerde cihazı rootlamak diye bir kavram var. Bunun iOS karşılığı da jailbreak, Apple tarafından belirlenen sınırları aşmaya yarar.

checkra1n, iOS jailbreak için kullanabileceğiniz çok güzel bir araç. Cihazınıza uygun olan sürümü https://checkra.in/ adresinden indirebilirsiniz. Bazen uygun sürüm olsa da jailbreak gerçekleşmeyebiliyor bu durumda chekra1n'in önceki sürümlerini deneyebilirsiniz. Uygulamayı açtığınızda bilgisayara cihazı bağlamanızı isteyecek. Adımları gerçekleştirdikten sonra cihazınız jailbreaklenmiş olacak.

--Cihaz her kapanıp açıldığında, normal haline geri dönüyor ve jailbreak işlemini tekrarlamanız gerekiyor.--

## Cydia

Cydia uygulaması, jailbreak uygulamaları, modları ve başka iOS içerikleri ile ilk resmi olmayan iPhone appstore'udur. Saurik (aka Jay Freeman) tarafından geliştirilmiştir ve sadece jailbreakli iPhone ve iPadlere yüklenebilir. checkra1n jailbreak işleminden sonra cihaz Cydia App Store yüklü geliyor.

## Jailbreak Detection Bypass

Uygulamanın cihazın jailbreakli olduğunu anlamaması için Liberty Lite uygulamasını Cydia'dan indirmek gerekiyor. Önce source kısmından http://ryleyangus.com/repo adresini ekleyin ve arama ksımından Liberty Lite'i bulup indirin. Sistem ayarlarından Liberty'i bulun ve hedef uygulama için jailbreak detectionı blocklayın.

## SSH Bağlantısı

Cydia'dan OpenSSH'ı indirin. Ayarlarda Wifi bilgilerinden cihazın IP adresini öğrenin. Default (ön tanımlı) kullanıcı root ve parolası alpine olarak geliyor. Artık IP adresine bu bilgilerle ssh bağlantısı gerçekleştirebilirsiniz.

## SSL Pinning Bypass

Bir mobil uygulama bir sunucuyla iletişim kurduğunda, iletilen verileri kurcalamaya ve gizlice dinlemeye karşı korumak için SSL kullanır. SSL pinning ile uygulama, önceden tanımlanmış bir veya daha fazla sertifika hariç tüm sertifikaları reddedecek şekilde tasarlanmıştır. Uygulama bir sunucuya bağlandığında, sertifikayı pinlenmiş sertifika ile karşılaştırır. Eşleşme olduğunda, sunucuya güvenilir ve SSL bağlantısı kurulur. Şimdi bunun için bir bypass yöntemini deneyeceğiz.

Cydia'da Filza uygulamasını indirin. Sonra cihazdan https://github.com/nabla-c0d3/ssl-kill-switch2/releases adresine giderek son sürümden deb scriptini indirin. Filza'yı açıp indirdiğiniz .deb dosyasını açın. İndirme bitince sistem ayarlarında SSL Kill Switch 2'yi bulun "Disable Certificate Validation” ayarını açın. Burp Suite'te Proxy Listener ekleyip cihazınızın Wifi ayarında HTTP Proxy bölümünden Manuel seçip Server ve Port alanlarını Burp Suit'e eklediğiniz listenerla aynı yapın. Artık hedef uygulamanın trafiğini izleyebilirsiniz.

## Frida

Cydia'da source ekleme kısmından https://build.frida.re yı ekleyip arama kısmından Frida'yı aratıp indirebilirsiniz. Bilgisayarınız ve cihazınızdaki frida versiyonlarının aynı olmasına dikkat edin. Cihaza indirdikten sonra ssh ile bağlanıp frida-server processini görebilirsiniz.

``` bash
$ ps aux | grep frida
```

Client tarafı için de bilgisayarınıza pip ile indirebilirsiniz.

``` bash
$ pip install frida-tools
```

Github üzerinde birçok frida scripti bulabilirsiniz. Örn: https://github.com/interference-security/frida-scripts/tree/master/iOS
