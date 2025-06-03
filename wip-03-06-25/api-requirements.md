### API requirements from the "cloud" implementation

1. **Blocking API Calls with Client-Controlled Timeout**

   * API calls must be blocking from start to finish. They may be long-running.
   * The client (via the Request resource) specifies the timeout.
   * The API server must honor the blocking behavior, not return early.

2. **Idempotent `Create` / `Update` Operations**

   * If a `create` or `update` action is **already in progress** for the same resource:

     * Return **`409 Conflict`**.
     * Repeated calls during the pending operation must continue to return 409.
   * Once the pending operation finishes:

     * If the resource exists (create) or is up-to-date (update), return **`200 OK`**.

3. **Consistent JSON Responses**

   * All API responses must include the full resource object serialized as JSON in the response body.