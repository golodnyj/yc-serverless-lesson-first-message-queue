# Создание функции для чтения из очереди

В предыдущих работах мы создавали функцию использующую подключение к БД, тут мы повторим этот опыт. Проверим, что нам доступны переменные для инициации подключения: `CONNECTION_ID`, `DB_USER`, `DB_HOST` мы их создавали в предыдущей работе с помощью следующих команд:

    echo "export CONNECTION_ID=<CONNECTION_ID>" >> ~/.bashrc && . ~/.bashrc
    echo "export DB_USER=<DB_USER>" >> ~/.bashrc && . ~/.bashrc
    echo "export DB_HOST=<DB_HOST>" >> ~/.bashrc && . ~/.bashrc

Также для работы с очередью нам потребуются переменные `VERBOSE_LOG`, `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, `QUEUE_URL` заданные на предыдущих шагах.

Создадим нашу функцию `function-for-url-from-mq.py`, при этом сразу зададим все необходимые переменные и сервисный аккаунт:

    zip function-for-url-from-mq function-for-url-from-mq.py requirements.txt

    yc serverless function create \
    --name  function-for-url-from-mq \
    --description "function for url from mq"

    yc serverless function version create \
    --function-name=function-for-url-from-mq \
    --memory=256m \
    --execution-timeout=5s \
    --runtime=python37 \
    --entrypoint=function-for-url-from-mq.handler \
    --service-account-id $SERVICE_ACCOUNT_ID \
    --environment VERBOSE_LOG=True \
    --environment CONNECTION_ID=$CONNECTION_ID \
    --environment DB_USER=$DB_USER \
    --environment DB_HOST=$DB_HOST \
    --environment AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID \
    --environment AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY \
    --environment QUEUE_URL=$QUEUE_URL \
    --source-path function-for-url-from-mq.zip

Протестируйте функцию. После её выполнения количество сообщений в очереди уменьшится, а в базе данных появится новая таблица с результатами тестирования доступности функции.

