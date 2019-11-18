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
# graphql

```python
import graphene
from .models import *
from graphene import relay, ObjectType, Connection, Node, Int
from graphene_django import DjangoObjectType
from graphene_django.filter import DjangoFilterConnectionField
from django_filters import FilterSet, OrderingFilter
from django.contrib.auth.models import User

class ExtendedConnection(Connection):
    class Meta:
        abstract = True

    total_count = Int()
    edge_count = Int()

    def resolve_total_count(root, info, **kwargs):
        return root.length
    def resolve_edge_count(root, info, **kwargs):
        return len(root.edges)

class UserNode(DjangoObjectType):
    class Meta:
        model = User
         
        filter_fields = ['first_name','last_name','email']
          
        
      
        interfaces = (relay.Node, )
        connection_class = ExtendedConnection
class CandidatureNode(DjangoObjectType):
    class Meta:
        model = Candidature
            # 'ville': ['exact',],
        filter_fields = ['user','nanplus','contacts','genre','pays','qrcontent','userkey','userkey','location','ville','birth_date']
            # 'user': ['exact',],
            # 'nanplus': ['exact', 'icontains', 'istartswith'],
            # 'contacts': ['exact', 'icontains', 'istartswith'],
            # 'genre': ['exact', 'icontains', 'istartswith'],
            # 'pays': ['exact', 'icontains', 'istartswith'],
            # 'qrcontent': ['exact', 'icontains', 'istartswith'],
            # 'userkey': ['exact', 'icontains', 'istartswith'],
            # 'location':['exact'],
            # 'ville': ['exact',],
            # 'birth_date': ['exact'],
        
        order_by = OrderingFilter(
            fields=(
                ('date_add','date_add'),
          
            )
        )
        interfaces = (relay.Node, )
        connection_class = ExtendedConnection



class Query(ObjectType):
    Candidature = relay.Node.Field(CandidatureNode)
    all_Candidatures = DjangoFilterConnectionField(CandidatureNode)
    User = relay.Node.Field(UserNode)
    all_Users = DjangoFilterConnectionField(UserNode)
    
    ##requests
    
     req = requests.post('http://127.0.0.1:8000/graphql',data={
        'query':'query{\
        allUsers{\
            edges{node{lastName,firstName,email}}\
        }\
        }'

    })
    print(req.status_code)
    print(req.text)
    
```

