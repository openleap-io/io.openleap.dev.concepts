# BFF Contract: F-PPS-IM-001 — Post Goods Receipt

> **Template:** [BFF Contract](../../../concepts/artifacts/platform/bff-contract.md)
> **Feature:** [F-PPS-IM-001](feature-spec.md)
> **Derived from:** Feature Spec §5 (Backend Dependencies) + §5.2 (View-Model Shape)

---

## Feature Identity

| Field | Value |
|-------|-------|
| featureId | F-PPS-IM-001 |
| name | Post Goods Receipt |

---

## Aggregated Services

These service calls are orchestrated by the BFF to compose the view-model for this feature.

| # | Service | Tier | Endpoint | Method | Purpose | isMutation | Required |
|---|---------|------|----------|--------|---------|-----------|----------|
| 1 | pps-im-svc | T3 | /goods-receipts | POST | Create new GR | ✓ | ✓ |
| 2 | pps-im-svc | T3 | /goods-receipts/{id}/post | PUT | Post (confirm) GR | ✓ | ✓ |
| 3 | pps-im-svc | T3 | /goods-receipts/{id} | GET | Load existing GR | | ✓ |
| 4 | pps-pur-svc | T3 | /purchase-orders/{id} | GET | Load PO for verification | | ✓ |
| 5 | pps-pd-svc | T3 | /materials/{id} | GET | Material details | | ✓ |
| 6 | bp-svc | T2 | /parties/{id} | GET | Supplier name | | Optional |
| 7 | si-svc | T1 | /units/{code} | GET | Unit validation | | Optional |

---

## View-Model Shape

The BFF composes this response from multiple service calls. This IS the contract between BFF and frontend.

```jsonc
// GET /bff/pps-im/goods-receipt/{id}
{
  "goodsReceipt": {
    "id": "uuid",
    "referenceType": "PURCHASE_ORDER",
    "referenceId": "PO-2026-00431",
    "status": "RECEIVED",
    "receivedAt": "2026-03-29T08:15:00Z",
    "receivedBy": "user:clerk-01",
    "totalValue": 12500.00,                  // computed by BFF
    "fourEyeRequired": true,                 // computed: totalValue > threshold
    "lines": [
      {
        "lineId": "uuid",
        "material": {
          "id": "MAT-001",
          "name": "Steel Rod M12",           // ← pps-pd-svc
          "sku": "SR-M12-500",
          "unit": "pcs",                     // ← si-svc
          "isRegulated": true                // ← pps-pd-svc
        },
        "expectedQuantity": 500,             // ← pps-pur-svc (PO line)
        "actualQuantity": 500,
        "lotNumber": "LOT-2026-0329-A",
        "qualityStatus": "PASSED",
        "variance": 0                        // computed by BFF
      }
    ]
  },
  "supplier": {
    "id": "SUP-001",
    "name": "Acme Corp"                      // ← bp-svc
  },
  "purchaseOrder": {
    "id": "PO-2026-00431",
    "status": "PARTIALLY_RECEIVED"           // ← pps-pur-svc
  },
  "extensionFields": {                       // ← ext.customFieldsOnReceipt
    // Product-specific, populated by extension route
  }
}
```

---

## Feature-Gating Rules

| Product Mode | BFF Behavior |
|-------------|-------------|
| **FULL** | All service calls enabled. POST/PUT mutations allowed. Full view-model returned. |
| **READ_ONLY** | GET calls only. POST/PUT endpoints blocked (BFF returns 403). View-model shows existing GRs but no create/post actions. |
| **EMBEDDED** | Reduced view-model (header only, no lines). Suitable for widget display in another feature. |
| **EXCLUDED** | BFF does not register routes for this feature. No calls made. |

---

## Extension Routing

| Extension Point | Product Config Decides | Default Route | Custom Route Example |
|----------------|----------------------|--------------|---------------------|
| ext.qualityCheckHook | Where quality checks are performed | Inline (frontend-only pass/fail) | `pps-qm-svc → POST /api/pps/qm/v1/checks` |
| ext.customFieldsOnReceipt | Additional fields on GR form | None (zone hidden) | `custom-fields-svc → GET/POST /custom-fields/goods-receipt/{id}` |

---

## Failure Modes

| Service | Failure | BFF Behavior |
|---------|---------|-------------|
| pps-im-svc | Unavailable | **Block** — feature cannot function. Show error, retry button. |
| pps-pur-svc | Unavailable | **Degrade** — allow manual PO number entry, skip verification. Warning shown. |
| pps-pd-svc | Unavailable | **Degrade** — show material ID without name/details. Warning shown. |
| bp-svc | Unavailable | **Degrade** — show supplier ID without name. No warning (non-critical). |
| si-svc | Unavailable | **Degrade** — accept unit input without validation. |
| ext.qualityCheckHook | Unavailable | **Block for regulated materials** — cannot post without quality status. |
| ext.customFieldsOnReceipt | Unavailable | **Degrade** — hide extension zone (absent-rule: collapse-up). |

---

## Caching Hints

| Service Call | Cacheable | TTL | Invalidation |
|-------------|-----------|-----|-------------|
| pps-pd-svc /materials/{id} | ✓ | 1 hour | Event: pps.pd.product.released |
| bp-svc /parties/{id} | ✓ | 1 hour | Event: bp.bp.party.updated |
| si-svc /units/{code} | ✓ | 24 hours | Rarely changes |
| pps-pur-svc /purchase-orders/{id} | ✗ | — | Changes frequently (partial receipts) |
| pps-im-svc /goods-receipts/{id} | ✗ | — | Mutable during receipt process |
