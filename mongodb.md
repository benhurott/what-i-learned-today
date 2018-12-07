# MongoDB

## Queries

### Quering depends on string length in an object inside an array

```js
db.getCollection('mydata').find({ 
    "items.schools.gradYear": { "$exists": true }, 
    "items.schools": {
        $elemMatch: {
                gradYear: /^\w{1,2}$/ // 1-min, 2-max
        }
     }
})
```

### Quering based on a REF property

```js
db.getCollection('posts')
.aggregate(
    [{
        $lookup: {
            from: 'users', // the collection to join
            localField: 'user', // the field that have the id to join
            foreignField: '_id', // the field in the users that will be used to join
            as: 'user' // the name of the field that will be mapped with the user result
        }
    }, 
    {
        $match: {
            'user.name': 'Osmar, The Mith' // the filter
        }
    }]
)
```

### Query based on array length

```js
db.getCollection('users').find({ 'roles.1': { $exists: true } })
```

Here we are searching for a collection with size 1 or less.
