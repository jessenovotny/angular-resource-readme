# $resource

## Overview

We've had a look at the awesome `$http` service that Angular provides, but there is also another awesome service under the name of `$resource`. Let's take a look at that!

## Objectives

- Describe $resource
- Use $resource methods
- Fetch data from an API
- Update data from an API

## What is $resource?

$resource is a factory that creates a resource object for us to communicate with APIs. We pass through a URL and it gives us an object back that looks like this:

```js
var User = $resource('/user/:userId');

/**
 * User = { get: function (),
 *          save: function (),
 *          remove: function ()
 *        }
 */
```

Here, we've defined our URL. Let's break that down:

`/user/:userId`

We're defining our URL as `/user/:userId` - but hold on, we can't have colons in our URLs!?

Well, with $resource, using a colon in a URL means that section is *actually* a variable. `:userId` means that we aren't actually going to put `:userId` into our URL, rather that we will be replacing `:userId` with an actual user ID. This allows us to put our data into our URL - for instance, we would end up querying `/user/1` or `/user/1231938`.

Now, our `User` variable is equal to an object containing multiple functions. We can call these to make the appropriate requests.

### Reading

When we call `.get()`, you've guessed it - it makes a GET request for us. As the first argument, we pass in an object with the data we need for request. In the case of our endpoint above, we need to pass in an object with a `userId` property, in order for Angular to replace `:userId` with the actual user's ID.

```js
User.get({userId: 3}, function (user) {
	console.log(user);
});
```

Our second argument is a callback that gets fired when the request completes. This will make a GET request to `/user/3` (as we've passed in 3 as the `userId`).

### Updating and Creating

Now, we can create new users and also update previous users using the `.$save()` function. This will issue a POST request to the same URL as above.

For existing users, we'd do any modifications inside the `.get` callback, as we already have received the user. Note: we use `$save()` inside the `.get` callback as we may have a property named `save` on the user.

```js
User.get({userId: 3}, function (user) {
	console.log(user);

	user.email = newEmailAddress;

	user.$save();
});
```

When we want to create a new user, we just simply create a new instance of `User`, and then call `.$save()` again.

```js
var user = new User();

user.name = 'Bill Gates';
user.email = 'bill@microsoft.com';

user.$save();
```

This will issue a POST request again, with details about our new user.

### Removing

We can also delete users, using `.$delete()`.

```js
User.get({userId: 3}, function (user) {
	console.log(user);

	user.email = newEmailAddress;

	user.$delete();
});
```
