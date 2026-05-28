# Solutions

## Follow Right

```
WHILE NOT EXIT (
  RIGHT
  IF CLEAR(
    FORWARD 1
  )ELSE(
    LEFT
    IF CLEAR(
      FORWARD 1
    )ELSE(
      LEFT
      IF CLEAR (
        FORWARD 1
      ) ELSE (
        LEFT
        FORWARD 1
      )
    )
  )
)
```