Netezza SQLAlchemy
==================

SQLAlchemy dialect for the Netezza database.

This is a fork from deontologician project with small changes to make it compatible to python 3.

Thanks a lot to deontologician! Original github project [here](https://github.com/deontologician/netezza_sqlalchemy).

Usage:

```python
import netezza_dialect
from sqlalchemy import create_engine, Table, Column, INT

engine = create_engine('netezza://<username>:<password>@<dsn>')
existing_table = Table('existing_name', reflect=True)
new_table = Table('new_table',
    Column('column_a', INT, nullable=False),
    Column('column_b', netezza_dialect.ST_GEOMETRY(100)),
    Column('column_c', netezza_dialect.BYTEINT),
    distribute_on='column_a'
)

# And you're off to the races...

new_table.create()

engine.execute(existing_table.insert().from_select(...))
```

## Example how to write directly into netezza from pandas dataframe
```python
df.to_sql("YourDatabase", 
          engine,  
          if_exists='append',
          index=False,
          dtype=your_dtypes,
          chunksize=1600,
          method='multi')
```
