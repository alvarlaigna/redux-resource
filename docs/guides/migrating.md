# Migrating

This guide outlines how to migrate from `resourceful-redux@1.x` to `redux-resource@2.0.0`.

### Getting Started

First, install the latest version of redux-resource.

```
npm install redux-resource@2.0.0-beta1
```

### Unchanged Utilities

Several utilities are unchanged between these releases. The first thing that you can do is
update your application to use these instead of the Resourceful Redux versions.

```diff
- import { getStatus, actionTypes, requestStatuses } from 'resourceful-redux';
+ import { getStatus, actionTypes, requestStatuses } from 'redux-resource';
```

Your application should continue working as expected after these changes.

### The New Reducer

There are two breaking changes in the Resource Reducer:

- `labels` have been renamed to be `requests`
- `labels` are on longer used to track ordered lists of resources. Instead, use `lists`.

Because Redux Resource is configured per-slice, you can migrate one slice at a time,
rather than moving your entire application over at once.

First, use the new `resourceReducer` on a single slice.

```diff
- import { resourceReducer } from 'resourceful-redux';
+ import { resourceReducer } from 'redux-resource';

  export default resourceReducer('books');
```

Next, update your Action Creators to specify a `request` rather than a `label`.

```diff
  readResources({
    resourceName: 'books',
-   label: 'readBooks'
+   request: 'readBooks'
  });
```

Update all usages of `getStatus` to reference the new `requests` section of the slice,
rather than `labels`.

```diff
- getStatus(state, 'books.labels.readBooks.status');
+ getStatus(state, 'books.requests.readBooks.status');
```

If you were using `getResources` to render out the response from this request, you will want to
now also use a [list](https://redux-resource.js.org/docs/guides/lists.html). Now is also a
good opportunity to switch to the new `getResources` API. The old API still works, but it will
be removed in v3.0.0 sometime in early 2018.

```diff
  readResources({
    resourceName: 'books',
    request: 'readBooks',
+   list: 'usersBooks'
  });

-  getResources(state, 'books', 'readBooks');
+  getResources(state.books, 'usersBooks');
```

If you were using `mergeLabelIds`, be sure to change that to be `mergeListIds`:


```diff
  readResources({
    resourceName: 'books',
    request: 'readBooks',
    list: 'usersBooks',
-    mergeLabelIds: false,
+    mergeListIds: false,
  });
```

### Conclusion

That's the extent of the changes in the main library. You will want to make sure that any
plugins or libraries that you are using have also been updated to support these changes.
