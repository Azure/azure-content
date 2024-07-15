---
title: BCP033
description: Error - Expected a value of type "{expectedType}" but the provided value is of type "{actualType}".
ms.topic: reference
ms.custom: devx-track-bicep
ms.date: 06/28/2024
---

# Bicep warning and error code - BCP033

This error occurs when you assign a value of a mismatched data type.

## Error description

`Expected a value of type "{expectedType}" but the provided value is of type "{actualType}".`

## Solution

Use the expected data type.  

## Examples

The following example raises the error because the expected data type is a string. The actual provided value is an integer:

```bicep
var myValue = 5

output myString string = myValue
```

You can fix the error by providing a string value:

```bicep
var myValue = '5'

output myString string = myValue
```

## Next steps

For more information about Bicep warning and error codes, see [Bicep warnings and errors](./bicep-error-codes.md).