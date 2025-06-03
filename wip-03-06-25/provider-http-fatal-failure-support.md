# 📌 Enhancement: Fatal Failure Detection for `provider-http`

## Overview

This proposal introduces a mechanism to detect **fatal errors** during `CREATE` or `UPDATE` actions in the `Request` resource of the Crossplane `provider-http`. The goal is to avoid unnecessary remote API calls by `Observe()` after an unrecoverable error has already been detected.

## Motivation

Currently, `isUpToDate()`—invoked by `Observe()`—uses JQ-based checks like `expectedResponseCheck` and `isRemovedCheck` to determine if a resource requires an update or has been removed. However, there's no mechanism to short-circuit further observations if a previous action (e.g., a bad request) has already failed irrecoverably.

By detecting such **fatal failures**, the controller can:

* Avoid redundant remote calls
* Prevent misleading status
* Surface errors that require manual intervention more clearly

---

## Proposed Field: `fatalFailureCheck`

An optional JQ-based field under `spec.forProvider`, similar to existing checks:

```yaml
fatalFailureCheck:
  type: CUSTOM
  logic: |
    if .response.statusCode == 400 
      and .response.body.error == "Invalid email format"
      then true 
      else false 
    end
```

### Field Description

| Field   | Description                                                                 |
| ------- | --------------------------------------------------------------------------- |
| `type`  | `DEFAULT` (future use) or `CUSTOM` for user-defined JQ logic                |
| `logic` | A JQ expression evaluated on the response of a `CREATE` or `UPDATE` request |

If this logic returns `true`, the `Request` will be marked with a `FatalFailure` condition and future `Observe()` calls will skip the HTTP request.

---

## Status Example

```yaml
status:
  conditions:
    - type: Ready
      status: False
      reason: FatalFailure
      message: "Fatal error detected in Create response: Invalid email format"
    - type: FatalFailure
      status: True
      reason: FatalErrorDetected
      message: "Create response indicated fatal error per .spec.fatalFailureCheck"
```

---

## Call Flow

```text
[Create()/Update()]
   |
   |--> Send HTTP request
   |
   |--> Run `fatalFailureCheck.logic` if defined
         |
         |-- true  --> Set status.conditions["FatalFailure"]
         |-- false --> Continue normally
         
[Observe()]
   |
   |--> Check status.conditions["FatalFailure"]
         |
         |-- true  --> Skip HTTP call, return early
         |-- false --> Run isUpToDate()
                          |
                          |--> determineIfRemoved()
                          |--> determineIfUpToDate()
```

---

## Benefits

* Reduces unnecessary HTTP calls for known-failure cases
* Clearly surfaces unrecoverable errors in resource status
* Aligns with existing, familiar configuration style (JQ logic)
* Fully optional and non-breaking for existing configurations