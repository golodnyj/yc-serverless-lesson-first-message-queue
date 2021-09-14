# Обновление спецификации API Gateway

Наша функция готова, но по умолчанию она не является публичной. Предоставим доступ к этой функции с помощью API-шлюза. Для этого нам необходимо обновить ранее созданную спецификацию `hello-world.yaml`. Возможно ее нет у вас под рукой, тогда выгрузите её из облака:

    yc serverless api-gateway get-spec \
    --name hello-world >> hello-world-new.yaml

Внесите изменения, добавив секцию о ранее созданной функции (подсмотреть можно в файле `hello-world.yaml`):

    /check:
        get:
            x-yc-apigateway-integration:
                type: cloud-functions
                function_id: <идентификатор функции>
                service_account_id: <ID сервисного аккаунта>
            operationId: add-url

Обновите конфигурацию  

    yc serverless api-gateway update \
    --name hello-world \
    --spec=hello-world-new.yaml 
    
Для тестирования выполните вызов функции в браузере:

    https://<API Gateway ID>.apigw.yandexcloud.net/check?url=https://ya.ru/

После каждого запроса количество сообщений в очереди будет увеличиваться на одно.