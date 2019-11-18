# consomation-graphql-avec-requests

## Deux methodes s'offrent a nous 


### 1 passons pas request simple 
```python

from django.views.decorators.csrf import csrf_exempt
urlpatterns = [
   ...
    path("graphql", csrf_exempt(GraphQLView.as_view(graphiql=True,schema=schema))),

]
```
#### scrip.py 
```python
import requests


req = requests.post('http://localhost:8000/graphql',data={
    'query':'query{\
    allCategories{\
        edges{node{id,name}}\
    }\
    }'
})
print(req.status_code)
print(req.text)

    
```
### 2 avec gql legerement complexe
```python
##----import__###
from gql import gql, Client
from gql.transport.requests import RequestsHTTPTransport
##code 
 _transport = RequestsHTTPTransport(
        url='http://localhost:8000/graphql',
        use_json=True,

    )


    client = Client(
        transport=_transport,
        fetch_schema_from_transport=True,
    )
    query = gql("""
    {
    
        allCategories{
            edges{node{id,name}}
        }
    
    }
    """)
    quer = client.execute(query)
    print(quer)
```


