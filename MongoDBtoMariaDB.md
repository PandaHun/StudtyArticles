# MongoDB to MariaDB 교체



### 단순 Select

```python
# MongoDB
find_results = self.db.find()
# Maria DB (sqlAlchemy)
find_result = self.session.query(entity).all()
```

### 조건 절

```python
# MongoDB
find_results = self.db.find(
            filter={
                "id": id,
                "name": name
            }
        )
# MariaDB
find_results = self.session.query(entity).filter(
            entity.id== id,
            entity.name== name
        ).all()
```

### 데이터 Inserts

```python
# MongoDB
self.db.insert_one(document=document)
# MariaDB
self.session.add(entity)
self.session.commit()
```

### Update

```python
self.db.update_one(
            filter={
                "id": id,
                "name": name,
                "count": count
            },
            update={"$set": update_info}
        )

self.session.query(entity).filter_by(
            id=id,
            name=name,
            count=count
        ).update(**update_info)
self.session.commit()
```

### Delete

```python
self.db.delete_one(
            filter={
                "id": id,
                "name": name,
                "count": count
            }
        )

self.session.delete(find_result)
self.session.commit()
```

### $or, $regex, options

```python
self.db.count_documents(
            filter={
                "id": id,
                "name": name,
                "count": count,
                "status": status,
                "$or": [
                    {"title": {"$regex": "{}".format(query), "$options": "i"}},
                    {"description": {"$regex": "{}".format(query), "$options": "i"}}
                ]
            }
        )

self.session.query(entity).filter(
            entity.id== id,
            entity.name== name,
            entity.count== count,
            entity.status== status
        ).filter(
            entity.title.ilike(query) or
            entity.description.ilike(query)
        ).count()
```