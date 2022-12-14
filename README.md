# Google Drive P2P Proxy
#### Приложение для преодоления лимита по скачиванию файлов через хранилище Google Drive.

## Запуск проекта
#### ...

## Текущий прогресс
### https://github.com/users/danyaisyourhomie/projects/1

## Документация по REST API
### https://www.postman.com/partnadem/workspace/google-drive-proxy/overview

<br>

## Принцип работы
1. Пользователь авторизуется в приложении через OAuth в своём Google Drive аккаунте
2. Пользователь выбирает из списка файлов тот, который хочет расшарить
3. Приложение делает копию этого файла в специальную служебную директорию внутри диска пользователя
4. Редактирует настройки приватности файла для доступа по ссылке
5. Сохраняет ссылку внутри базы данных
6. Генерирует новую служебную ссылку и отдает пользователю

## Генерация ссылки на файл

Приложение хранит в базе оригинальные ссылки на файл (которые ведут непосредственно на Google drive). Но в качестве результата отдаёт другую, служебную ссылку, которая приложением редиректится на диск.

Когда пользователь (отличный от автора файла) переходит по ссылке (например https://p2pdrive.com/abc123) приложение считает это потенциальным скачиванием файла и обновляет счетчик скачиваний у ссылки. Только после этого приложение редиректит на сам файл, лежащий в Google Drive. 

Если счетчик приблизился к лимиту скачиваний, приложение на диске автора файла создает новую копию файла и обновляет ссылку на оригинальный файл. Таким образом квота по файлам, позволяя не думать об установленном сервисе лимитом.

## Счётчик скачиваний файла

В планируемой реализации мы сталкиваемся с необходимостью считать количество посещений файла на диске, чтобы заранее создать новую копию файла и избежать ограничение на скачивания.

## Сброс счетчика

Каждая ссылка имеет дату создания. Если обращение к файлу происходит спустя 24 часа после создания этой самой ссылки - счётчик скачиваний сбрасывается, а значит новая копия файла не делается.

## Жизненный цикл копий файла

Создание копии файла - способ избежать достижения лимита. Если пользователь пытается перейти по ссылке, ограничение скачиваний которой было достигнуто - приложение выполняет следующее: создает новую копию оригинального файла; обновляет ссылку на файл; удаляет предыдущую копию; возвращает актуальную ссылку.

Таким образом копия файла будет существовать до тех пор, пока все обращения к ней будут в рамках выставленного лимита. 