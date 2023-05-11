---
id: lfd7gm77dlosle8awpii3af
title: Python
desc: ''
updated: 1683114685423
created: 1683114205251
---

### Read JSON from file
```
with open('token.json', 'r') as f:
    session_json = json.loads(f.read())
```

### Write JSON to file
```
with open('token.json', 'w') as f:
    json_to_file = json.dumps(session_json.dump())
    f.write(json_to_file)
```

