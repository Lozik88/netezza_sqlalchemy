Netezza SQLAlchemy
==================

SQLAlchemy dialect for the Netezza database.

This is far from complete, has a lot of bugs etc, but it might give you a start.

Note: I don't have access to Netezza anymore so I can't maintain this, but feel free to fork!

Usage:

```python
from netezza_sqlalchemy import BYTEINT, ST_GEOMETRY
from sqlalchemy import create_engine, Table, Column, Integer

engine = create_engine('netezza://<username>:<password>@<dsn>')
existing_table = Table('existing_name', reflect=True)
new_table = Table('new_table',
    Column('column_a', Integer, nullable=False),
    Column('column_b', ST_GEOMETRY(100)),
    Column('column_c', BYTEINT),
    distribute_on='column_a'
)

# And you're off to the races...

new_table.create()

engine.execute(existing_table.insert().from_select(...))

```
