# CAP Node.js Handlers and Transactions

Implement custom behavior with CAP service handlers and transaction-aware APIs.

## Pattern

```js
const cds = require('@sap/cds')
module.exports = class CatalogService extends cds.ApplicationService {
  async init () {
    this.before('CREATE', 'Books', req => {
      if (!req.data.title) req.reject(400, 'Title is required')
    })
    this.on('submitOrder', async req => {
      const tx = cds.tx(req)
      return tx.run(SELECT.one.from('Orders').where({ ID: req.data.ID }))
    })
    return super.init()
  }
}
```

Use `req.user`, `req.data`, `req.params`, and `req.reject()` consistently. Avoid unmanaged connections and preserve atomicity for multi-step operations.