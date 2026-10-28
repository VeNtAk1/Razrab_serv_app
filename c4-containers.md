C4Context
    title Маркетплейс цифровых товаров — C4 Container

    Person(buyer, "Покупатель", "Просматривает и покупает цифровые товары")
    Person(seller, "Продавец", "Загружает и продаёт цифровой контент")
    Person(admin, "Администратор", "Модерация, тарифы, аналитика")

    System_Boundary(mp, "Маркетплейс цифровых товаров") {
        Container(web, "Web SPA", "React / Vue", "Интерфейс для пользователей")
        Container(api, "API Gateway", "NestJS / FastAPI", "REST API, бизнес-логика")
        Container(auth, "Auth Service", "Keycloak / JWT", "Аутентификация и роли")
        Container(orderSvc, "Order & Payment Service", "Сервис заказов", "Заказы, расчёт комиссии")
        Container(downloadSvc, "Download Service", "Сервис выдачи ссылок", "Одноразовые ссылки, TTL")
        
        ContainerDb(db, "PostgreSQL", "Основная БД", "Пользователи, товары, заказы")
        ContainerDb(s3, "Object Storage", "S3 / MinIO", "Приватные файлы товаров")
        Container(queue, "Message Broker", "RabbitMQ / Kafka", "События заказов и оплат")
    }

    System_Ext(payment, "Платёжный провайдер", "Stripe / ЮKassa")
    System_Ext(email, "Email-сервис", "Отправка ссылок")

    Rel(buyer, web, "Использует", "HTTPS")
    Rel(seller, web, "Использует", "HTTPS")
    Rel(admin, web, "Использует", "HTTPS")

    Rel(web, api, "REST/JSON", "HTTPS")
    Rel(api, auth, "Проверка токена")
    Rel(api, orderSvc, "Создание заказа")
    Rel(api, downloadSvc, "Запрос ссылки")
    
    Rel(orderSvc, payment, "Инициирует оплату")
    Rel(orderSvc, db, "Читает/пишет")
    Rel(orderSvc, queue, "Публикует payment.confirmed")
    
    Rel(downloadSvc, s3, "Генерирует URL")
    Rel(downloadSvc, db, "Сохраняет токены")
    Rel(downloadSvc, queue, "Слушает события")
    Rel(downloadSvc, email, "Отправляет ссылку")
    
    Rel(api, db, "Читает данные")
    Rel(seller, s3, "Загружает файлы")
