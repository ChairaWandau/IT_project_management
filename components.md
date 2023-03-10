# Компонентная архитектура
<!-- Состав и взаимосвязи компонентов системы между собой и внешними системами с указанием протоколов, ключевые технологии, используемые для реализации компонентов.
Диаграмма контейнеров C4 и текстовое описание. 
-->
## Компонентная диаграмма

```plantuml
@startuml
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Container.puml

AddElementTag("microService", $shape=EightSidedShape(), $bgColor="CornflowerBlue", $fontColor="white", $legendText="microservice")
AddElementTag("storage", $shape=RoundedBoxShape(), $bgColor="lightSkyBlue", $fontColor="white")

Person(admin, "Администратор")
Person(moderator, "Модератор")
Person(user, "Пользователь")

System_Ext(web_site, "Клиентский веб-сайт", "HTML, CSS, JavaScript, React", "Веб-интерфейс")

System_Boundary(conference_site, "Социальная сеть") {
   'Container(web_site, "Клиентский веб-сайт", ")
   Container(client_service, "Сервис авторизации", "C++", "Сервис управления пользователями", $tags = "microService")    
   Container(post_service, "Сервис стены", "C++", "Сервис управления записями на стене", $tags = "microService") 
   Container(blog_service, "Сервис сообщений", "C++", "Сервис управления сообщениями в чате", $tags = "microService")   
   ContainerDb(db, "База данных", "MySQL", "Хранение данных о сообщениях, записях на стене и пользователях", $tags = "storage")
   
}

Rel(admin, web_site, "Просмотр, добавление и редактирование информации о пользователях, публикациях на стене, сообщениях в чатах")
Rel(moderator, web_site, "Модерация контента и пользователей")
Rel(user, web_site, "Регистрация, просмотр/изменение публикаций на стене, общение в чате")

Rel(web_site, client_service, "Работа с пользователями", "localhost/person")
Rel(client_service, db, "INSERT/SELECT/UPDATE", "SQL")

Rel(web_site, post_service, "Работа с записями на стене", "localhost/pres")
Rel(post_service, db, "INSERT/SELECT/UPDATE", "SQL")

Rel(web_site, blog_service, "Работа с сообщениями в чате", "localhost/conf")
Rel(blog_service, db, "INSERT/SELECT/UPDATE", "SQL")

@enduml
```
## Список компонентов  

### Сервис авторизации
**API**:
-	Создание нового пользователя
  - Входные параметры: login, пароль, email, имя, фамилия, гендер
  - Выходные параметры: отсутствуют
-	Поиск пользователя по логину
  - Входные параметры: login
  - Выходные параметры: email, имя, фамилия, гендер
-	Поиск пользователя по маске имени и фамилии
  - Входные параметры: маска фамилии, маска имени
  - Выходные параметры: login, имя, фамилия, email, гендер

### Сервис сообщений
**API**:
- Отправка сообщения пользователю
  - Входные параметры: login отправителя, login получателя, текст сообщения, дата и время создания
  - Выходные параметры: отсутствуют
- Получение списка сообщений для пользователя
  - Входные параметры: login пользователя
  - Выходные параметры: массив сообщений, где для каждого указаны его дата и время создания, текст, login отправителя и login получателя

### Сервис стены
**API**:
- Добавление записи на стену
  - Входные параметры: автор, содержания записи, дата создания
  - Выходные параметры: идентификатор записи
- Загрузка стены пользователя
  - Входные параметры: login пользователя
  - Выходные параметры: массив с записями со стены пользователя (идентификатор, автор, содержание записи, дата создания)

