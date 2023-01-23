<span style="font-size: 65px; color: #7e56c2">**SQLAlchemy Filterset**</span>

<p align="left">
    <em>An easy way to filter, sort, paginate SQLAlchemy queries</em>
</p>

[![codecov](https://codecov.io/gh/sqlalchemy-filterset/sqlalchemy-filterset/branch/main/graph/badge.svg)](https://codecov.io/gh/sqlalchemy-filterset/sqlalchemy-filterset)
[![CodeQL](https://github.com/sqlalchemy-filterset/sqlalchemy-filterset/actions/workflows/codeql.yml/badge.svg)](https://github.com/sqlalchemy-filterset/sqlalchemy-filterset/actions/workflows/codeql.yml)


---
**Documentation**: <a href="https://sqlalchemy-filterset.github.io/sqlalchemy-filterset/" target="_blank">https://sqlalchemy-filterset.github.io/sqlalchemy-filterset</a>

**Source Code**: <a href="https://github.com/sqlalchemy-filterset/sqlalchemy-filterset" target="_blank">https://github.com/sqlalchemy-filterset/sqlalchemy-filterset</a>

---
The library provides a convenient and organized way to filter your database records.
By creating a `FilterSet` class, you can declaratively define the filters you want to apply to your `SQLAlchemy` queries.
This library is particularly useful in web applications, as it allows users to easily search, filter, sort, and paginate data.

The key features are:

* [X] Declaratively define filters.
* [X] Keep all of your filters in one place, making it easier to maintain and change them as needed.
* [X] Construct complex filtering conditions by combining multiple simple filters.
* [X] It offers a standard approach to writing database queries.
* [X] Reduce code duplication by reusing the same filters in multiple places in your code.
* [X] Sync and Async support of modern SQLAlchemy.

## Installation

```bash
pip install sqlalchemy-filterset
```
Requirements: `Python 3.7+` `SQLAlchemy 1.4+`


## Basic Usage


In the example provided, a `FilterSet` called `ProductFilterSet` is defined to filter records from a
`Product` database model. The `ProductFilterSet` class has several attributes, each of which
is an instance of a `Filter` object. Each `Filter` object is associated with a field in the `Product`
model and a lookup expression function, which specifies how the filter should be applied to the query.

For example, the id attribute is a `Filter` object that is associated with the id field in the
`Product` model. The title attribute is a `Filter` object that is associated with the title field
in the `Product` model.

When the `ProductFilterSet` is applied to a query, it modifies the query by adding
WHERE clauses to it based on the filters that have been specified.
For example, if an `is_active` attribute is specified as `True`, the query would be modified to only
return records where the `is_active` field is `True`.

The declarative style allows users to easily specify criteria for filtering the records that are
returned from the database by simply setting the attributes of the `ProductFilterSet` class.
This can be more convenient and easier to understand than writing raw SQL queries, which
can be more error-prone and difficult to maintain.

### Define a FilterSet

In a declarative style, we describe the attributes that will participate in filtering the query in the database:
```python
from sqlalchemy_filterset.filtersets import FilterSet
from sqlalchemy_filterset.filters import Filter, RangeFilter, BooleanFilter

from myapp.models import Product


class ProductFilterSet(FilterSet):
    id = Filter(Product.id)
    price = RangeFilter(Product.price)
    is_active = BooleanFilter(Product.is_active)
```
### Define a FilterSchema
```python
import uuid
from pydantic import BaseModel


class ProductFilterSchema(BaseModel):
    id: uuid.UUID | None
    price: tuple[float, float] | None
    is_active: bool | None
```

### Usage
```python
# Connect to the database
engine = create_engine("postgresql://user:password@host/database")
Base.metadata.create_all(bind=engine)
SessionLocal = sessionmaker(bind=engine)
session = SessionLocal()

# Create the filterset object
filter_set = ProductFilterSet(session, select(Product))

# Define the filter parameters
filter_params = ProductFilterSchema(
    price=(10, 100),
    is_active=True,
)

# Apply the filters to the query
filtered_products = filter_set.filter(filter_params)
```
#### This example will generate the following query:
```sql
SELECT product.id, product.title, product.price, product.is_active
FROM product
WHERE product.price >= 10
  AND product.price <= 100
  AND product.is_active = true
```


## License

This project is licensed under the terms of the MIT license.
