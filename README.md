MongoDB
=======

Empty query result list iterator bug
------------------------------------

Testcase demonstrating a bug with empty query result iterator on MongoDB
datastore.

Calling `hasNext()` method on an empty lazy-loaded query result iterator
returns true while `next()` method returns `null`. Consequently, iterating
over such a list results in a single `null` element being processed.

The problem will not show up if DataNucleus is forced to evaluate the list
prior to invoking the iterator, for example by:
 * calling list `size()` method
 * calling one of `makeTransient()`, `makePersistent()`, etc PM methods

The reason for this strange behavior is `QueryResultIterator`'s `hasNext()`
relying on the existence of at least one candidate result, failing to
check if candidate's cursor contains at least one object.
However, if one of the above methods is invoked, `LazyLoadQueryResult`
class ends up calling `getNextObject()` method, removing empty result candidate
cursors.
