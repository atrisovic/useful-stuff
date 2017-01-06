# Code

```
from projects.models import Project
from projects.models import Platform


def main():
    f = open("softconfdb.json")
    content = f.read()
    f.close()
    rel = eval(content)

    for el in rel:
        n1 = el[0]
        r = el[1]
        n2 = el[2]

        a = Project()
        b = Project()
        p = Platform()
        try:
            a = Project.nodes.get(name=n1[0], version=n1[1])
        except Project.DoesNotExist:
            a = Project(name=n1[0], version=n1[1])
            print(str(a))
            a.save()

        if r == "REQUIRES":
            try:
                b = Project.nodes.get(name=n2[0], version=n2[1])
            except Project.DoesNotExist:
                b = Project(name=n2[0], version=n2[1])
                b.save()
            a.requires.connect(b)
        elif r == "PLATFORM":
            try:
                p = Platform.nodes.get(platform=n2)
            except Platform.DoesNotExist:
                p = Platform(platform=n2)
                p.save()
            a.platform.connect(p)
        else:
            print("DEBUG this shouldn't be here: " + r)


if __name__ == "__main__":
    main()

```

# Error

```
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/usr/local/lib/python2.7/site-packages/neomodel/signals.py", line 29, in hooked
    val = fn(self, *args, **kwargs)
  File "/usr/local/lib/python2.7/site-packages/neomodel/core.py", line 155, in save
    self.id = self.create(self.__properties__)[0].id
  File "/usr/local/lib/python2.7/site-packages/neomodel/core.py", line 258, in create
    node, _ = db.cypher_query(query, {'create_params': item})
  File "/usr/local/lib/python2.7/site-packages/neomodel/util.py", line 28, in wrapper
    return func(self, *args, **kwargs)
  File "/usr/local/lib/python2.7/site-packages/neomodel/util.py", line 103, in cypher_query
    raise UniqueProperty(ce.message)
neomodel.exception.UniqueProperty
```

# Solution

```
DROP CONSTRAINT ON (<label_name>)
ASSERT <property_name> IS UNIQUE
```
https://www.tutorialspoint.com/neo4j/neo4j_drop_unique_constraint.htm
