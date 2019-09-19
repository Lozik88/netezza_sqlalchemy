SQLAlchemy Netezza  dialect
===========================

SQLAlchemy dialect for the Netezza database.

This is a fork from deontologician project with small changes to make it compatible to python 3.

Thanks a lot to deontologician! Original github project [here](https://github.com/deontologician/netezza_sqlalchemy).

tested with `Python 3.7`, `pandas 0.25.1` and `SQLAlchemy 1.3.7`.

## Installation
Add the `netezza_dialect.py` to your project folder.

## Usage

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
import netezza_dialect
from sqlalchemy import create_engine

engine = create_engine(netezza://ODBCDataSourceName)

df.to_sql("YourDatabase", 
          engine,  
          if_exists='append',
          index=False,
          dtype=your_dtypes,
          chunksize=1600,
          method='multi')
```

A little explaination to the `to_sql()` parameters:

It is *essential* that you use the `method='multi'` parameter if you do not want to take pandas for ever to write in the database. Because without it it would send an INSERT query per row. You can use `'multi'` or you can define your own insertion method. Be aware that you have to have at least `pandas v0.24.0` to use it. [See the docs](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.to_sql.html?highlight=to_sql) for more info.

When using `method='multi'` it can happen (happend at least to me) that you exceed the parameter limit. In my case it was 1600 so I had to add `chunksize=1600` to avoid this.

## Note

If you get a warning or error like the following:

```shell script
C:\Users\USER\anaconda3\envs\myenv\lib\site-packages\sqlalchemy\connectors\pyodbc.py:79: SAWarning: No driver name specified; this is expected by PyODBC when using DSN-less connections
  "No driver name specified; "
```

```shell script
pyodbc.InterfaceError: ('IM002', '[IM002] [Microsoft][ODBC Driver Manager] Data source name not found and no default driver specified (0) (SQLDriverConnect)')
```

Then you propably treid to connect to the database via

```python
engine = create_engine(netezza://usr:pass@address:port/database_name)
```

You have to set up the database in the ODBC Data Source Administrator tool from Windows and then use the name you defined there.

```python
engine = create_engine(netezza://ODBCDataSourceName)
```

Then it should have no problems to find the driver.
