# Intro

https://habr.com/ru/post/280488/
Elasticsearch — поисковый движок с json rest api, использующий Lucene и написанный на Java. Описание всех преимуществ
этого движка доступно на официальном сайте. Elasticsearch использует индексы Lucene для хранения данных и поиска

Подобные движки используются при сложном поиске по базе документов. Например, поиск с учетом морфологии языка\
или поиск по geo координатам.

ES автоматически создал индекс blog и тип post. Можно провести условную аналогию: индекс — это база данных, а тип —
таблица в этой БД. Каждый тип имеет свою схему — mapping, также как и реляционная таблица. Mapping генерируется
автоматически при индексации документа:

Получим mapping всех типов индекса blog:

```curl -XGET "$ES_URL/blog/_mapping?pretty"```

### Распределенный индекс

Ок, для хранения данных и поиска мы будем использовать инстанс Lucene. Но ранее мы решили, что для обеспечения
горизонтального масштабирования нам необходимо иметь возможность размещать данные на разных машинах. В действительности,
какая разница как данные хранятся физически? Важно чтобы мы имели единое логическое хранилище. Каждый инстанс Lucene
должен стать частью одного большого индекса, или осколком(shard) разбитого индекса. Шард будет выполнять непосредственно
операции по поиску и записи данных.

Shard в Elasticsearch — это логическая единица хранения данных на уровне базы, которая является отдельным экземпляром
Lucene.

Index — это одновременно и распределенная база и механизм управления и организации данных, это именно логическое
пространство. Индекс содержит один или более шардов, их совокупность и является хранилищем.

Классическое сравнение индекса с другими базами выглядит примерно так. Elasticsearch SQL MongoDB Index Database Database
Mapping/Type Table Collection Field Column Field Object(JSON)    Tuple Object(BSON)

## Как Еластик хранит данные

Объекты в ЕЛ мы можем воспринимать как отдельные страницы, файлы, карточки, все это можно назвать некими документами.
Поэтому такая модель хранения данных называется документоориентированной. Elasticsearch это документоориентированная
база данных

## Инвертированые индексы

## Типы данных

https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-types.html
```
short 
boolean 
long 
text 
object - any JSON object (may be nested)
float
integer 
double 
date 
nested 
keyword used for exact matching of values (filtering aggregations sorting)
NO SUCH TYPE AS ARRAY!
```

Apache Lucene does not have JSON type so all the fields are transformed to raw equivalent For nested object we have
behaviours:

```
1) {name="Alex";child:{name}}
    will become
    {name, child.name}
   
   The {name="Alex";childs:{name},{name}}
   Will become:
   {name, childs=[name,name]}
2) Nested data type
if we want to save "the independent object" search possibility (we should use 
nested query then)
nested objects are stored as hidden documents internaly 
 ```


# Stemming and stop words

making words in root form and deleting words in analysis process

# Analyzers and search queries
