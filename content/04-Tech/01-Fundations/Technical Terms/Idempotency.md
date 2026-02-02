
**Idempotency** means: doing the same operation multiple times has the same effect as doing it once.


### IHTTP / APIs (common interview + system design topic)
- Usually idempotent
	- **GET** `/users/123`  Read-only. Repeating doesn’t change state.
	- **PUT** `/users/123` with the full user object, replace the resource with the same content each time → same final state.
	- **DELETE** `/users/123`  Deleting again still ends up “deleted” (even if the server responds differently).
- Usually _not_ idempotent
	- **POST** `/orders` (create a new order)  
		- retrying can create **multiple orders** unless you protect it.


### How to make a “create” operation safe: **Idempotency Key**
For non-idempotent actions like `POST /payments`, clients send a unique key:
- Header: `Idempotency-Key: 7f2c...`
- Server stores: key → result (or key → created resource id)
- If the same key is seen again, server returns the **same result** instead of creating a new one.

This is how payment APIs (e.g., **Stripe**) avoid double-charging.

