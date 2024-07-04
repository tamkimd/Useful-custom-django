# Useful-custom-django
Explore custom Django lookup classes to extend query capabilities beyond Django ORM's native support. Enhance Django ORM queries with specialized lookups designed for complex filtering and aggregation scenarios.
## Custom Lookup for Django ORM

### IExactIn Lookup for Django ORM
The `IExactIn` lookup extends Django ORM's querying capabilities to perform case-insensitive exact matches against multiple values.
#### Implementation 1:

```python
@Field.register_lookup
class IExactIn(Lookup):
    lookup_name = 'iexactin'

    def as_sql(self, compiler, connection):
        lhs, lhs_params = self.process_lhs(compiler, connection)
        rhs, rhs_params = self.process_rhs(compiler, connection)
        rhs_params = list(rhs_params)
        sql = f"UPPER({lhs}) IN (SELECT UPPER(unnest(array[{', '.join(rhs_params)}])))"
        params = list(lhs_params)
        return sql, params
```
#### Implementation 2:
```python
@Field.register_lookup
class IExactIn(Lookup):
    lookup_name = 'iexactin'
    def as_sql(self, compiler, connection):
        lhs, lhs_params = self.process_lhs(compiler, connection)
        rhs, rhs_params = self.process_rhs(compiler, connection)
        rhs_params = list(rhs_params)
        sql = f"{lhs} ILIKE ANY(array[{', '.join(rhs_params)}])"
        params = list(lhs_params)
        return sql, params
```

#### Usage Example:
Assuming a Django model MyModel with a field name, you can use the IExactIn lookup as follows:
```python
from myapp.models import MyModel
query = MyModel.objects.filter(name__iexactin=['John', 'Doe'])
```
