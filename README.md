# Отладка Fiddler'ом приложений в Windows Subsystem for Android


В первую очередь, устанавливаем WSA+Magisk(+gapps) от сюда https://github.com/LSPosed/MagiskOnWSA

Запускаем Fiddler.
В его настройках должно быть включено:

            HTTPS -> Capture HTTPS CONNECTs -> Decrypt HTTPS traffic
                                            -> Ignore server certificate errors

            Connections -> Allow remote computers to connect

---

Скачиваем сертификат:

        wget http://localhost:8888/FiddlerRoot.cer

---

Генерируем old hash:

        openssl x509 -inform PEM -subject_hash_old -in FiddlerRoot.pem | head -1

---

Генерируем pem:

        openssl x509 -inform der -in FiddlerRoot.cer -out <old_hash>.0

---

Стягиваем репо, перемещаем <old_hash>.0 в system/etc/security/cacerts:

        git clone https://github.com/emergy/wsafiddler.git
        cd wsafiddler
        mkdir -p system/etc/security/cacerts
        mv ../<old_hash>.0 system/etc/security/cacerts/

---

Запаковываем в zip и устанавливаем так (в настройках WSA должен быть включен режим разработчика):

        adb connect localhost:58526
        adb push wsafiddler.zip /data/local/tmp
        adb shell su -c magisk --install-module /data/local/tmp/wsafiddler.zip

---

Перезапустить WSA

---

Проверить наличие сертификата, можно тут:

        /system/etc/security/cacerts/

---

Включить proxy для WSA:

        adb shell settings put global http_proxy <fiddler_ip>:8888

---

Выключить proxy для WSA:

        adb shell settings put global http_proxy :0