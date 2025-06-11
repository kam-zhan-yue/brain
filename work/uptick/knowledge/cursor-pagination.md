Cursor pagination is a pagination method that allows you to fetch data from a resource incrementally. The method involves a cursor, which is a unique identifier (e.g. timestamp) that fetches data in a specific order.

The cursor is typically included as a parameter in an API request, as is the amount of data that should be returned.

![[cursor-pagination.png]]

The response payload will include the next and previous cursors, which inform what the cursor should be for the following request (if there is data that is left to be fetched)
