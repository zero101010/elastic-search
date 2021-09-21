# elastic-search

## Valores para a inserção de dados

https://raw.githubusercontent.com/elastic/elasticsearch/master/docs/src/test/resources/accounts.json#

## Comando para inserir essa massa de dados
curl -H "Content-Type: application/json" -XPOST "localhost:9200/bank/_bulk?pretty&refresh" --data-binary "@accounts.json"


## Buscas
#### Buscas de todos os indices banc de forma ascendente
```
GET /bank/_search
{
  "query": {"match_all": {}}
  , "sort": [
    {
     "account_number": "asc"
    }
  ]
}
```

#### Busca por contains de termos individuais
- Essa busca é feita por aproximação, ou seja, ele busca no address um contains por termos individuais e retorna no max_score a acurácia dessas pesquisa
```
GET /bank/_search
{
  "query": {"match": {
    "address": "mill lane"
  }}
}
```


#### Busca exata de um valor
- Essa busca é feita por aproximação, ou seja, ele busca no address um contains por termos individuais e retorna no max_score a acurácia dessas pesquisa
```
GET /bank/_search
{
  "query": {"match_phrase": {
    "address": "mill lane"
  }}
}
```

#### Busca de valores com must e must_not para definir o que deve ter aquele valor ou não
- Nessa busca iremos descrever o tipo de dado que tem que ter dentro da busca e a que não deve ter dentro da busca


```
GET /bank/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "age": "40"
          }
        }
      ],
      "must_not": [
        {
          "match": {
            "state": "ID"
          }
        }
      ]
    }
  }
}
```

## Busca usando o filtro
- Essa busca faz um filtro de range entre os valores da chave balance
```
GET /bank/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match_all": {}
        }
      ],
      "filter": [
        {
          "range": {
            "balance": {
              "gte": 2000,
              "lte": 3000
            }
          }
        }
      ]
    }
  }
}
```

## Agregações
### Agregação 
- Faz a agregação dos dados para servir como um count
```
GET /bank/_search
{
  "size": 0,
  "aggs": {
    "group_by_state": {
      "terms": {
        "field": "state.keyword"
      }
    }
  }
}
```