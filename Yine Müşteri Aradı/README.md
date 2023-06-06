Öncellikle programı sanal makinede çalıştırıp procceslerinde çalışan uygulamaları buldum python gördükten sonra direk aklıma decompile etmek geldi

https://github.com/extremecoders-re/pyinstxtractor

Burdan indirdiğim toolla decompile ettim 

![image](https://github.com/muhammetolg/KAPSUL-HACKME-2023-CTF/assets/75451957/80014e8e-7bf7-4a8b-8355-0afac2867cdd)

Kodlar bu şekilde decomple edildi 

![image](https://github.com/muhammetolg/KAPSUL-HACKME-2023-CTF/assets/75451957/d63ee05b-353c-4f73-a292-d3ef7ac40e50)

Daha sonra "UNCOMPYLE6" toolunu kullanarak kaynak kodlarına ulaştım 
![image](https://github.com/muhammetolg/KAPSUL-HACKME-2023-CTF/assets/75451957/0294f1be-e310-4d65-a393-92ad0a7a0b71)

Program bulamazsanız hazır olarak burada : https://github.com/extremecoders-re/uncompyle6-builds/releases/tag/v3

          # uncompyle6 version 3.9.0
          # Python bytecode version base 3.7.0 (3394)
          # Decompiled from: Python 3.10.8 (main, Nov  6 2022, 02:23:43) [MSC v.1929 64 bit (AMD64)]
          # Embedded file name: 1001010110111101111110010101010010101.py
          import random as r, string, datetime as g1011011001010101001010101011101010101010101, uuid, requests
          from Crypto.Cipher import AES as a
          from Crypto.Util.Padding import pad
          import os, subprocess as v

          def a1011011001010101001010101011101010101010101(f1011011001010101001010101011101010101010101, h1011011001010101001010101011101010101010101, x1011011001010101001010101011101010101010101):
              command = f'reg add "{f1011011001010101001010101011101010101010101}" /v "{h1011011001010101001010101011101010101010101}" /d "{x1011011001010101001010101011101010101010101}" /f'
              v.run(command, shell=True)


          def b1011011001010101001010101011101010101010101(file_path, y10101010101010100110101010101010111110001010):
              y10101010101010100110101010101010111110001010 = y10101010101010100110101010101010111110001010.encode('utf-8')
              y10101010101010100110101010101010111110001010 = y10101010101010100110101010101010111110001010[:32].ljust(32, b'\x00')
              iv = os.urandom(16)
              cipher = a.new(y10101010101010100110101010101010111110001010, a.MODE_CBC, iv)
              with open(file_path, 'rb') as (file):
                  plaintext = file.read()
              ciphertext = cipher.encrypt(pad(plaintext, a.block_size))
              encrypted_file_path = file_path + '.encrypted'
              with open(encrypted_file_path, 'wb') as (file):
                  file.write(iv + ciphertext)
              print('Dosya şifreleme tamamlandı. Şifreli dosya: ', encrypted_file_path)


          def d1011011001010101001010101011101010101010101(seed):
              r.seed(seed)
              key_length = r.randint(5, 10)
              y10101010101010100110101010101010111110001010 = ''.join((r.choice(string.ascii_lowercase) for _ in range(key_length)))
              return y10101010101010100110101010101010111110001010


          def d1011011001010101001010101011101010101010101s(num_keys):
              keys = []
              seed = g1011011001010101001010101011101010101010101.datetime.now().strftime('%Y%m%d%H%M%S')
              for _ in range(num_keys):
                  y10101010101010100110101010101010111110001010 = d1011011001010101001010101011101010101010101(seed)
                  keys.append(y10101010101010100110101010101010111110001010)
                  seed = y10101010101010100110101010101010111110001010

              return keys


          def main():
              f1011011001010101001010101011101010101010101 = 'HKEY_CURRENT_USER\\Software\\HACKME_HATIRA'
              h1011011001010101001010101011101010101010101 = 'HACKME!'
              x1011011001010101001010101011101010101010101 = 'Bu !zararsız! dosyayı kendi bilgisayarımda çalıştırdığım için özür dilerim. Bir daha böyle !zararsız! dosyaları kendi bilgisayarımda çalıştırmayacağım ;)'
              try:
                  a1011011001010101001010101011101010101010101(f1011011001010101001010101011101010101010101, h1011011001010101001010101011101010101010101, x1011011001010101001010101011101010101010101)
              except:
                  pass

              num_keys = 1
              keys = d1011011001010101001010101011101010101010101s(num_keys)
              file_path = 'fffxxssdaxdas.hackme'
              y10101010101010100110101010101010111110001010 = str(keys[0])
              b1011011001010101001010101011101010101010101(file_path, y10101010101010100110101010101010111110001010)


          if __name__ == '__main__':
              choice = int(input('Merhaba, bu dosyayı kendi bilgisayarında çalıştırmamalıydın. Sana ufak bir hatıra bıraktım ;) Yine de kendi bilgisayarında çalıştırmaya devam etmek istersen 1 yaz ve gönder:\n'))
              if choice == 1:
                  main()
          # okay decompiling 1001010110111101111110010101010010101.pyc

Kaynak kodundan elde ettiğimiz bilgiye göre AES şifrelemeeyi kullanıyor ve Iv de değerini il 16 byte'a yazıyor dosyanın geri kalan kısmı şifreleniyor.

Bunu çözebilmem için chatgbt den script yardımı istiyorum fakat verdiği script tam anlamıyla çözemiyor şifreyi tekrardan istiyor. 

1 Saat aradıktan sonra dosyanın oluşturulma tarihine bakıp o anki şifreleri çekeceğimi öğreniyorum daha sonra scripte bunu ekleyip şifreyi denemesini söylüyorum bulduğu script yine işe yaramayınca 
https://github.com/jackalkarlos kullanıcısının scriiptini deneyerek çözüyorum 
          
            GNU nano 7.2                                                                                                     aesdecode.py                                                                                                               
          import string
          import random
          import os
          from Crypto.Cipher import AES
          from Crypto.Util.Padding import unpad
          import datetime

          def generate_key(seed):
              random.seed(seed)
              key_length = random.randint(5, 10)
              key = ''.join(random.choice(string.ascii_lowercase) for _ in range(key_length))
              return key


          def decrypt_file(file_path, seed):
              key = generate_key(seed)
              key = key.encode('utf-8')
              key = key.ljust(32, b'\x00')
              iv_size = 16
              with open(file_path, 'rb') as file:
                  ciphertext = file.read()
              iv = ciphertext[:iv_size]
              ciphertext = ciphertext[iv_size:]
              cipher = AES.new(key, AES.MODE_CBC, iv)
              decrypted_data = unpad(cipher.decrypt(ciphertext), AES.block_size)
              decrypted_file_path = file_path + '.decrypted'
              with open(decrypted_file_path, 'wb') as file:
                  file.write(decrypted_data)
              print('çözdüm he: ', decrypted_file_path)


          seed_list = ['202305220141{:02d}'.format(second) for second in range(60)]

          for seed in seed_list:
              try:
                  decrypt_file("fffxxssdaxdas.hackme.encrypted", seed)
              except:
                  pass
                 
                 
Daha sonra Flag dosyası decrypted bir şekilde çıkıyor. 

Flag : HACKME{s3ms1p4s4p4s4j1nd4ctfcoz3s1c3l3r:)}

