# Array Methods

## Array.reduce for object

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

## Array.sort for object

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

## Slice and Splice

### Slice

Think of "slicing" as cutting a portion out of an array without modifying the original array.
slice returns a shallow copy of a portion of an array, specified by a start and end index.

```javascript
const originalArray = [1, 2, 3, 4, 5];
const newArray = originalArray.slice(1, 4); // Returns [2, 3, 4]
```

### Splice

Think of "splicing" as modifying or changing the original array.
splice is used to add or remove elements from an array at a specified index.


```javascript
Copy code
const originalArray = [1, 2, 3, 4, 5];
originalArray.splice(2, 1); // Removes 1 element at index 2
// originalArray is now [1, 2, 4, 5]
```

Remember the keyword associations: "slice" for creating a new sliced array, "splice" for modifying the original array by splicing in or out elements.

In summary:

- slice does not modify the original array and returns a new array.
- splice modifies the original array by adding or removing elements.