# Создание Функции

Ранее мы использовали рантайм `python37`, однако с версии рантайма `python39` библиотеки необходиомо указывать явно. Для работы с `requirements.txt` есть удобная python библиотека `pipreqs`. Для генерации `requirements.txt` с помощью `pipreqs` достаточно указать рабочий каталог. В большинстве интерпритаторов linux для указания текущего каталога есть переменная `$PWD`. Если файл `requirements.txt` уже есть и его нужно актуализировать то используется флаг `--force`, например:  

    pip install pipreqs
    pipreqs $PWD --print
    pipreqs $PWD --force

Для создания функции зададим ряд переменных:
* `VERBOSE_LOG` указывает на то, чтобы функция писала в журнал детали своего выполнения.
* `AWS_ACCESS_KEY_ID` — значение «Идентификатор ключа» из сервисного аккаунта, который мы сделали ранее.
* `AWS_SECRET_ACCESS_KEY` — значение «Секретный ключ» из того же сервисного аккаунта.
* `QUEUE_URL` — URL на очередь, его можно получить на обзорной странице созданной ранее очереди.

    echo "export VERBOSE_LOG=true" >> ~/.bashrc && . ~/.bashrc
    echo "export AWS_ACCESS_KEY_ID=<AWS_ACCESS_KEY_ID>" >> ~/.bashrc && . ~/.bashrc
    echo "export AWS_SECRET_ACCESS_KEY=<AWS_SECRET_ACCESS_KEY>" >> ~/.bashrc && . ~/.bashrc
    echo "export QUEUE_URL=<QUEUE_URL>" >> ~/.bashrc && . ~/.bashrc

Находясь в директории с исходными файлами, упакуем их в zip-архив. Создадим нашу функцию `my-url-receiver-function.py`, при этом сразу зададим все необходимые переменные и сервисный аккаунт:
    
    zip my-url-receiver-function my-url-receiver-function.py requirements.txt

    yc serverless function create \
    --name  my-url-receiver-function \
    --description "function for url"

    yc serverless function version create \
    --function-name=my-url-receiver-function \
    --memory=256m \
    --execution-timeout=5s \
    --runtime=python37 \
    --entrypoint=my-url-receiver-function.handler \
    --service-account-id $SERVICE_ACCOUNT_ID \
    --environment VERBOSE_LOG=$VERBOSE_LOG \
    --environment AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID \
    --environment AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY \
    --environment QUEUE_URL=$QUEUE_URL \
    --source-path my-url-receiver-function.zip

Проверим работоспособность функции: 

    yc serverless function version list --function-name my-url-receiver-function
    yc serverless function invoke --name my-url-receiver-function


## Тестирование функции

В веб-консоли находясь в вашем рабочем каталоге перейдите в раздел `Cloud Functions`, выберете ранее созданную функцию `my-url-receiver-function`. Перейдите в раздел `Тестирование` в боковом меню, выберите шаблон `HTTP-вызов` и замените раздел `queryStringParameters`: 
 
    "queryStringParameters": {
        "a": "2",
        "b": "1",
        "url": "https://ya.ru/"
    },    

на аналогичный то только с параметром `url` с любым сайтом. Важно указывать ссылку целиком.
    
    "queryStringParameters": {
        "url": "https://ya.ru/"
    },    

Нажмите кнопку `Запустить тест`. Если вы всё сделали правильно, то увидите код статуса `200`.

