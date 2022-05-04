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

Генерируем pem

        openssl x509 -inform der -in FiddlerRoot.cer -out <old_hash>.0

Стягиваем репо, перемещаем <old_hash>.0 в system/etc/security/cacerts

Запаковываем в zip и устанавливаем так:

        adb push module.zip /data/local/tmp
        adb shell su -c magisk --install-module /data/local/tmp/module.zip

Включить proxy для WSA

        adb shell settings put global http_proxy <fiddler_ip>:8888

Выключить proxy для WSA

        adb shell settings put global http_proxy :0