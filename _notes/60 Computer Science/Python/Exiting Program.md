---
banner: ""
banner_icon: 
---

---
**Tags**:: #python
**Links**::[[Python]]
**Description**::sys.exit()

---

### Ending a Program Early with sys.exit()


```python
import sys

while True:

    print('Type exit to exit.')
    response = input()
    if response == 'exit':
        sys.exit()
    print('You typed {}.'.format(response))
```
---
---
