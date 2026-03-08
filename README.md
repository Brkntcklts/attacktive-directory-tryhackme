# Attacktive Directory – TryHackMe Pentest Çalışması

Bu repo, TryHackMe platformunda bulunan **Attacktive Directory** laboratuvarı üzerinde gerçekleştirdiğim sızma testi çalışmasının teknik dokümantasyonunu içermektedir.

Bu çalışmada Windows Active Directory ortamına yönelik keşif, kimlik doğrulama saldırıları, parola kırma ve domain ele geçirme teknikleri uygulanmıştır.

---

# Lab Bilgileri

Platform: TryHackMe  
Lab: Attacktive Directory  
Ortam: Windows Active Directory Domain  
Rapor Türü: Sızma Testi Analizi  

Hazırlayan: **Berkant Çakıltaş**

---

# Kullanılan Araçlar

Bu çalışmada aşağıdaki penetration testing araçları kullanılmıştır:

- Nmap
- Enum4linux-ng
- Kerbrute
- Impacket
- Hashcat
- John The Ripper
- CrackMapExec
- smbclient
- Evil-WinRM
- SecretsDump

---

# Saldırı Aşamaları

## 1. Servis Keşfi (Nmap)

nmap -sC -sV -Pn 10.81.186.123

Hedef sistem üzerinde çalışan servisleri belirlemek için Nmap taraması yapılmıştır.

Tespit edilen önemli servisler:

- Kerberos (88)
- LDAP (389)
- SMB (445)

Bu servisler sistemin **Active Directory Domain Controller** olduğunu göstermektedir.


# 2. Enumeration (Enum4linux-ng)

Active Directory ortamı hakkında bilgi toplamak için enum4linux-ng aracı kullanılmıştır.

python3 enum4linux-ng.py 10.81.186.123

Bu aşamada:

- Domain adı
- SMB paylaşımları
- Kullanıcı bilgileri

gibi önemli bilgiler elde edilmiştir.

# 3. Kullanıcı Enumeration (Kerbrute)

Kerberos servisi üzerinden domain kullanıcılarını keşfetmek için Kerbrute kullanılmıştır.

kerbrute userenum -d DOMAIN userslist.txt 10.81.186.123

Bu işlem sonucunda domain üzerinde aktif kullanıcılar tespit edilmiştir.

# 4. AS-REP Roasting Saldırısı

Impacket araç setindeki **GetNPUsers** kullanılarak AS-REP roasting saldırısı gerçekleştirilmiştir.

impacket-GetNPUsers DOMAIN/ -usersfile users.txt -dc-ip 10.81.186.123

Bu saldırı sonucunda bazı kullanıcılar için Kerberos hash değerleri elde edilmiştir.

# 5. Hash Analizi ve Kırma

Elde edilen hash değerleri analiz edilmiş ve parola kırma işlemi gerçekleştirilmiştir.

Hash kırma için kullanılan araçlar:

- Hashcat
- John the Ripper

Örnek komut: hashcat -m 18200 hash.txt wordlist.txt

Bu aşamada kullanıcı parolası başarıyla elde edilmiştir.

# 6. Kimlik Doğrulama Testi

Elde edilen kullanıcı bilgileri CrackMapExec kullanılarak doğrulanmıştır.

crackmapexec smb TARGET_IP -u username -p password

Bu işlem sonucunda sistem üzerindeki SMB erişimi doğrulanmıştır.

# 7. Dosya Paylaşımı ve Veri Keşfi

SMB servisleri üzerinden sistemdeki paylaşımlar incelenmiştir.

smbclient -L 10.81.186.123

Bu aşamada bazı hassas dosyalara erişim sağlanmıştır.

# 8. Domain Veritabanının Ele Geçirilmesi

Active Directory veritabanı **SecretsDump** aracı kullanılarak dump edilmiştir.

impacket-secretsdump DOMAIN/user:password@10.81.186.123

Bu işlem sonucunda tüm domain kullanıcılarına ait NTLM hash değerleri elde edilmiştir.

# 9. Administrator Erişimi

Son aşamada Evil-WinRM kullanılarak sistem üzerinde administrator erişimi sağlanmıştır.

evil-winrm -i 10.81.186.123 -u Administrator -p password

Bu aşama sistemin tamamen ele geçirildiğini göstermektedir.


Bu aşama sistemin tamamen ele geçirildiğini göstermektedir.

---

# Sonuç

Bu çalışma kapsamında aşağıdaki saldırı teknikleri başarıyla uygulanmıştır:

- Active Directory Enumeration
- Kerberos User Discovery
- AS-REP Roasting
- Hash Cracking
- SMB Enumeration
- NTDS Dump
- Privilege Escalation
- Domain Compromise

Sonuç olarak hedef sistem üzerinde **Domain Administrator yetkileri ile tam erişim elde edilmiştir.**


# Yazar

Berkant Çakıltaş  

Cyber Security Analyst 

CEH 



