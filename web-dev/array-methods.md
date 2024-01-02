# Array Methods

Array.reduce for object

```javascript
var obj = [{
    id: 5,
    count: 7
  },
  {
    id: 3,
    count: 45
  },
  {
    id: 8,
    count: 35
  },
  {
    id: 1,
    count: 15
  }
]

let res = obj.reduce((acc, next) => {
  return {
    id: acc.id + next.id,
    count: acc.count + next.count
  }

}, {
  id: 0,
  count: 0
})

console.log(res) // {count: 102,id: 17}
```

Array.sort for object

```javascript
var obj = [{
    id: 5,
    count: 7
  },
  {
    id: 3,
    count: 45
  },
  {
    id: 8,
    count: 35
  },
  {
    id: 1,
    count: 15
  }
]

let sorted = obj.sort((a, b) => {
  return a.id - b.id
})
```
